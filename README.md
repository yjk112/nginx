                                                          nginx金康监测脚本和高可用Keepalived



    1.机器规划
      nginx master                   192.168.189.136 
      nginx backup                   192.168.189.137



    2. 端口开放，安全机制关闭



    3.健康监测脚本，cd /lusr/local/src,vim nginx_check.sh,只部署在nginx master上，chmod +x /usr/local/src/nginx_check.sh,给予执行权限，该shell脚本中写入nginx正确的安装路径


    4.下载Keepalived，systemctl start nginx && systemctl enable nginx && systemctl status nginx ,显绿色为正常启动, vim /etc/keepalived/keepalived.conf ,在nginx master 主机的此文件写入mater的文件内容，登入backup的主机写入backup的内容都保存退出，systemctl start keepalived && systemctl enable keepalived，确保Keepalived和nginx状态正常，浏览器属入URL，显示nginx官方HTML页面即为成功


    5.健康脚本测试，停止nginx master的运行，不停止Keepalived，等待几秒，观察浏览器页面且刷新，依旧正常显示页面即为成功，Keepalived主动唤起nginx,nginx 起不来触发nginx backup 


    6.keepalived高可用测试，确保主备的nginx和Keepalived都为正常启动，如果Keepalived和nginx master都中断了的话，直接秒级切换到backup，浏览器页面正常即为成功


    7.总结：健康监测脚本监测的是一个服务即nginx服务，当nginx服务终端了，Keepalived会去唤起nginx启动，如果失败进行降权和自杀方式VIP间接漂移到backup，Keepalived高可用机制基于vrrp协议，master节点会周期性发送心跳通告，backup节点如果持续收不到通告，就判定master失效，就接管VIP，触发VIP在主备节点间的漂移，实现节点级故障转移。两者为互补关系
