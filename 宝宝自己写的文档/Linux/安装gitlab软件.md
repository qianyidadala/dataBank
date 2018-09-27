1. 安装依赖软件

**yum -y install policycoreutils openssh-server openssh-clients postfix**

 

2.设置postfix开机自启，并启动，postfix支持gitlab发信功能

**systemctl enable postfix && systemctl start postfix**

 

1. [root@ ~]# systemctl start postfix  
2. Job for postfix.service failed because the control process exited with error code. See "systemctl status postfix.service" and "journalctl -xe" for details.  


1. 修改 /etc/postfix/main.cf的设置  
2. ​
3. inet_protocols = ipv4  
4. inet_interfaces = all 

3.下载gitlab安装包，然后安装

centos 6系统的下载地址:https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el6

centos 7系统的下载地址:https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7

 

**wget https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/gitlab-ce-10.7.3-ce.0.el7.x86_64.rpm**

[root@iZm5ejbpux25ac17foc9e7Z ~]$rpm -i soft/gitlab-ce-10.7.3-ce.0.el7.x86_64.rpm 

warning: soft/gitlab-ce-10.7.3-ce.0.el7.x86_64.rpm: Header V4 RSA/SHA1 Signature, key ID f27eab47: NOKEY
error: Failed dependencies:
​        policycoreutils-python is needed by gitlab-ce-10.7.3-ce.0.el7.x86_64

 

**yum install policycoreutils-python**

**rpm -i soft/gitlab-ce-10.7.3-ce.0.el7.x86_64.rpm **

 

4.修改gitlab配置文件指定服务器ip和自定义端口：

**vim  /etc/gitlab/gitlab.rb**

![img](https://images2015.cnblogs.com/blog/1008644/201609/1008644-20160911113500228-232680032.png)

 

注意这里设置的端口不能被占用，默认是8080端口，如果8080已经使用，请自定义其它端口，并在防火墙设置开放相对应得端口

 

 

5.重置并启动GitLab

执行：

**gitlab-ctl reconfigure**

**gitlab-ctl restart**

 

提示“ok: run:”表示启动成功

 

6.访问 GitLab页面

如果没有域名，直接输入服务器ip和指定端口进行访问

 

初始账户:root 密码:第一次登录修改密码

 

 

7.设置gitlab发信功能，需要注意一点：

发信系统用的默认的postfix，smtp是默认开启的，两个都启用了，两个都不会工作。

我这里设置关闭smtp，开启postfix

关闭smtp方法：vim /etc/gitlab/gitlab.rb

找到#gitlab_rails['smtp_enable'] = true 改为 gitlab_rails['smtp_enable'] = false

修改后执行gitlab-ctl reconfigure

另一种是关闭postfix，设置开启smtp，相关教程请参考官网https://doc.gitlab.cc/omnibus/settings/smtp.html

测试是否可以邮件通知：

登录并添加一个用户，我这里使用qq邮箱添加一个用户

 

8.GitLab服务器IP地址设置

 

**cd /opt/gitlab/embedded/service/gitlab-rails/config**

[root@iZm5ejbpux25ac17foc9e7Z config]$ll
total 192
-rw-r--r-- 1 root root  8444 May  3 06:19 application.rb
-rw-r--r-- 1 root root   611 May  3 06:19 aws.yml.example
-rw-r--r-- 1 root root   307 May  3 06:19 boot.rb
lrwxrwxrwx 1 root root    45 May  7 11:35 database.yml -> /var/opt/gitlab/gitlab-rails/etc/database.yml
-rw-r--r-- 1 root root   873 May  3 06:19 database.yml.env
-rw-r--r-- 1 root root  1188 May  3 06:19 database.yml.mysql
-rw-r--r-- 1 root root   914 May  3 06:19 database.yml.postgresql
-rw-r--r-- 1 root root 15412 May  3 06:19 dependency_decisions.yml
-rw-r--r-- 1 root root   283 May  3 06:19 environment.rb
drwxr-xr-x 2 root root  4096 May  7 11:30 environments
lrwxrwxrwx 1 root root    43 May  7 11:35 gitlab.yml -> /var/opt/gitlab/gitlab-rails/etc/gitlab.yml
-rw-r--r-- 1 root root 33762 May  3 06:19 gitlab.yml.example
drwxr-xr-x 2 root root  4096 May  7 11:35 initializers
-rw-r--r-- 1 root root  2304 May  3 06:19 karma.config.js
-rw-r--r-- 1 root root    56 May  3 06:19 license_finder.yml
drwxr-xr-x 2 root root  4096 May  7 11:30 locales
-rw-r--r-- 1 root root  1522 May  3 06:19 mail_room.yml
-rw-r--r-- 1 root root   450 May  3 06:19 no_todos_messages.yml
drwxr-xr-x 2 root root  4096 May  7 11:30 prometheus
-rw-r--r-- 1 root root  6355 May  3 06:19 README.md
-rw-r--r-- 1 root root  1160 May  3 06:19 redis.cache.yml.example
-rw-r--r-- 1 root root  1161 May  3 06:19 redis.queues.yml.example
-rw-r--r-- 1 root root  1167 May  3 06:19 redis.shared_state.yml.example
lrwxrwxrwx 1 root root    43 May  7 11:35 resque.yml -> /var/opt/gitlab/gitlab-rails/etc/resque.yml
-rw-r--r-- 1 root root  1076 May  3 06:19 resque.yml.example
drwxr-xr-x 2 root root  4096 May  7 11:30 routes
-rw-r--r-- 1 root root  2698 May  3 06:19 routes.rb
lrwxrwxrwx 1 root root    44 May  7 11:35 secrets.yml -> /var/opt/gitlab/gitlab-rails/etc/secrets.yml
-rw-r--r-- 1 root root   404 May  3 06:19 secrets.yml.example
-rw-r--r-- 1 root root  2131 May  3 06:19 sidekiq_queues.yml
-rw-r--r-- 1 root root    19 May  3 06:19 sidekiq.yml.example
-rw-r--r-- 1 root root   111 May  3 06:19 spring.rb
-rw-r--r-- 1 root root  5593 May  3 06:19 unicorn.rb.example
-rw-r--r-- 1 root root   402 May  3 06:19 unicorn.rb.example.development
-rw-r--r-- 1 root root  9649 May  3 06:19 webpack.config.js
[root@iZm5ejbpux25ac17foc9e7Z config]$

**gitlab-ctl restart**

 

经过查找终于发现了错误，原来是在服务器占用了8080端口,使GitLab的unicorn服务不能开启。 
最后在/etc/gitlab/gitlab.rb 中做出如下修改

```
unicorn['port'] = 9090
```

再`gitlab-ctl reconfigure` 重启配置，这样GitLab服务器就可以正常运行了

安装好GitLab，开启服务，发现有502错误。这是开始寻找解决办法，各种百度。 
1.找到`/var/log/gitlab/nginx`中的错误日志文件，发现有如下错误`/var/opt/gitlab/gitlab-rails/sockets/gitlab.socket failed (2: No such file or directory)`，然后用 nc命令创建了这个socket文件，最终权限设为 `srwxrwxrwx`，用户和组设置为git:git，但发现这个方法行不通。

2.这时我跑到GitLab的官网去寻找解决办法，<https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/README.md> 
ctrl+f 502 找到官方教程中说502出现的问题

> Note that on a single-core server it may take up to a minute to restart Unicorn and Sidekiq. Your GitLab instance will give a 502 error until Unicorn is up again.
>
> It is also possible to start, stop or restart individual components.
>
> **sudo gitlab-ctl restart sidekiq **
> Unicorn supports zero-downtime reloads. These can be triggered as follows:
>
> **sudo gitlab-ctl hup unicorn **
> Note that you cannot use a Unicorn reload to update the Ruby runtime.

尝试用上面两个命令解决，发现没有用。 
不断的输入`gitlab-ctl status`,发现unicorn的pid一直在变大。而其他几个服务的pid没有变化。

3.这时差不多找到了问题的所在了，应该就是unicorn的问题。然后看官方教程，可以使用`gitlab-ctl tail unicorn` 来跟踪unicorn的状态，这时候悲催的发现原来时8080端口被占用了

```
E, [2015-02-11T17:27:57.818492 #26687] ERROR -- : adding listener failed addr=127.0.0.1:8080 (in use)



E, [2015-02-11T17:27:57.818621 #26687] ERROR -- : retrying in 0.5 seconds (4 tries left)



E, [2015-02-11T17:27:58.318902 #26687] ERROR -- : adding listener failed addr=127.0.0.1:8080 (in use)



E, [2015-02-11T17:27:58.318998 #26687] ERROR -- : retrying in 0.5 seconds (3 tries left)



E, [2015-02-11T17:27:58.819309 #26687] ERROR -- : adding listener failed addr=127.0.0.1:8080 (in use)



E, [2015-02-11T17:27:58.819423 #26687] ERROR -- : retrying in 0.5 seconds (2 tries left)



E, [2015-02-11T17:27:59.319954 #26687] ERROR -- : adding listener failed addr=127.0.0.1:8080 (in use)



E, [2015-02-11T17:27:59.320076 #26687] ERROR -- : retrying in 0.5 seconds (1 tries left)
```

4.好了，终于发现了问题的所在。这时候的选择就变成了是把原来8080端口的服务给杀了还是将unicorn的端口换一个呢。这个就看自己的具体需求了。我这边是将unicorn端口换成了9090，方法就是开头讲的方法了