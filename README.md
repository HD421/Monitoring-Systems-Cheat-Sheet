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
| Cacti         | --                       | cactiuser/cactiuser          |admin/admin| --|

## Exploits ##
Admin has changed default passwords? Aww, maybe he forgot to update the system. Now check known vulnerabilities.

| NagiosXI | Version |
|-------|---------|
|[NRPE RCE](https://www.exploit-db.com/exploits/24955/)| 5.2.8<= |
|[Chained RCE](https://www.exploit-db.com/exploits/40067/)| 5.2.7<= |

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

## Postexploitation ##
You are successfully logged in and don't know what to do then? This topic is for you boiiii.
