# Monitoring-Systems-Cheat-Sheet #
A cheat sheet for pentesters and researchers about exploitation well-known monitoring systems.

## Exploring ##
M'kay kiddo, you found monitoring system and now think what you can do about it, right? 
My advice to you, first find out the version of the system and try to log in using the default credentials.

### Version Check ###

[Zabbix/Nagios version checker](https://github.com/HD421/Monitoring-Systems-Version-Check)

[Cacti version checker](https://github.com/worlak2/cactiVersionCheck)

### Default Credentials ###

|        | SSH Credentials     | Database Credentials                 |Web Credentials        |Port|
|------- |:-------------------:| -------------------------------------|-----------------------|-----|
| Zabbix <= 2.4 | root/zabbix zabbix/zabbix| root/zabbix zabbix/zabbix|Admin/zabbix admin/admin |10050 10051|
| Zabbix >= 3.0 | appliance/zabbix         | zabbix/zabbix            |Admin/zabbix Admin/Admin |10050 10051|
| Nagios        | root/nagiosxi            |   --                     |nagiosadmin/nagios nagiosadmin/nagiosadmin|5666|
| Cacti         | --                       | cactiuser/cactiuser          |admin/admin| 80 443 8080 | 

## Exploits ##
Admin has changed default passwords? Aww, maybe he forgot to update the system. Now check known vulnerabilities.

| NagiosXI | Version |
|-------|---------|
|[NRPE RCE](https://www.exploit-db.com/exploits/24955/)| 5.2.8<= |
|[Chained RCE](https://www.exploit-db.com/exploits/40067/)| 5.2.7<= |
|[Chained Remote Root](https://www.exploit-db.com/exploits/44560/)| 5.4.12<= |

| Zabbix | Version |
|-------|---------|
|[Command Execution](http://www.cvedetails.com/cve/cve-2009-4498)| 1.7.4<= |

| Cacti | Version |
|-------|---------|
|[SQL Injection](https://vulners.com/cve/CVE-2016-3172)| 0.8.8g<= |
|[SQL Injection](https://vulners.com/cve/CVE-2015-8604)| 0.8.8f |
|[SQL Injection](https://vulners.com/zdt/1337DAY-ID-24696)| 0.8.8f |
|[SQL Injection](https://vulners.com/cve/CVE-2015-4634)| 0.8.8d |
|[SQL Injection](https://vulners.com/cve/CVE-2015-4454)| 0.8.8c |
|[Reflected XSS](https://www.trustwave.com/Resources/Security-Advisories/Advisories/TWSL2016-007/?fid=7789)| 0.8.8b |
|[SQL Injection](https://www.trustwave.com/Resources/Security-Advisories/Advisories/TWSL2016-007/?fid=7789)| 0.8.8b |
|[Reflected XSS](https://github.com/Cacti/cacti/issues/838)| 1.1.12 |
|[Reflected XSS](https://github.com/Cacti/cacti/issues/867)| 1.1.13 |
|[Path Traversal](https://github.com/Cacti/cacti/issues/877)| 1.1.15 |
|[RCE](https://github.com/Cacti/cacti/issues/877)| 1.1.15 |
|[Reflected XSS](https://github.com/Cacti/cacti/issues/877)| 1.1.15 |
|[Reflected XSS](https://github.com/Cacti/cacti/issues/907)| 1.1.17 |
|[Stored XSS](https://github.com/Cacti/cacti/issues/918)| 1.1.17 |
|[Reflected XSS](https://github.com/Cacti/cacti/issues/1010)| 1.1.23 |
|[RCE](https://github.com/Cacti/cacti/issues/1057)| 1.1.27 |
|[AFR+RCE](https://github.com/Cacti/cacti/issues/1066)| 1.1.27 |



## Postexploitation ##
You are successfully logged in and don't know what to do then? This topic is for you boiiii.

### NagiosXI ###
[Spawning PHP Shell via component uploading](https://github.com/HD421/Monitoring-Systems-Cheat-Sheet/blob/master/php_shell_via_component_upload_NagiosXI.md)

[XSS -> RCE vector. Spawning shell via JS execution (worked on NagiosXI <= 5.4.12)](https://github.com/HD421/Monitoring-Systems-Cheat-Sheet/blob/master/xss_or_js_shell_uploading_NagiosXI.md)

[XSS -> RCE by polict (NagiosXI 5.5.10)](https://www.shielder.it/blog/nagios-xi-5-5-10-xss-to-root-rce/)

[RCE on Monitored Hosts through the NRPE(<= 2.14) plugin](https://vulners.com/metasploit/MSF:EXPLOIT/LINUX/MISC/NAGIOS_NRPE_ARGUMENTS)

[NagiosXI Vulnerability Chaining. Death By a Thousand Cuts (<= 5.4.12)](https://blog.redactedsec.net/exploits/2018/04/26/nagios.html)

### Zabbix ###
[Stealing administrator's session and creating our own privileged account (ARP-spoofing)](https://github.com/HD421/Monitoring-Systems-Cheat-Sheet/blob/master/Zabbix_session_hijacking.md)

[Spawn shell on monitored agents (Unix/Windows)](https://github.com/HD421/Monitoring-Systems-Cheat-Sheet/blob/master/Zabbix_spawn_shell_on_agents.md)

### PRTG ###

[PRTG NETWORK MONITOR PRIVILEGE ESCALATION (version 18.2.41.1652)](https://www.criticalstart.com/2018/10/prtg-network-monitor-privilege-escalation/) || [Exploit](https://github.com/Critical-Start/Section-8/blob/master/Paessler%20-%20PRTG/prtg_privesc.ps1)
