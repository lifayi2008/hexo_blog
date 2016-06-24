---
title: CentOS6.5安装redmine
date: 2016-06-21 17:38:38
tags: redmine
categories: 基础服务
---
##### 安装依赖包

```bash
$ yum install -y gcc-c++ patch readline readline-devel zlib zlib-devel libyaml-devel libffi-devel  openssl-devel make bzip2 autoconf automake libtool bison iconv-devel ImageMagick ImageMagick-devel
```
<!-- more -->
##### 安装ruby、rubygems

```bash
$ wget https://cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.1.tar.gz

$ tar -xf ruby-2.3.1.tar.gz && cd ruby*

$ ./configure --prefix=/usr/local/ruby/ && make && make install


$ wget https://rubygems.global.ssl.fastly.net/rubygems/rubygems-2.6.4.tgz

$ tar -xf rubygems-2.6.4.tgz && cd rubygems*

$ /usr/local/ruby/bin/ruby setup.rb
```

>如上ruby安装在/usr/local/ruby、rubygems安装在/usr/local/ruby/bin/gem

##### 安装rails

```bash
$ /usr/local/ruby/bin/gem install rails

Done installing documentation for i18n, thread_safe, tzinfo, activesupport, rack, rack-test, mini_portile2, pkg-config, nokogiri, loofah, rails-html-sanitizer, rails-deprecated_sanitizer, rails-dom-testing, builder, erubis, actionview, actionpack, activemodel, arel, activerecord, globalid, activejob, mime-types-data, mime-types, mail, actionmailer, thor, railties, bundler, concurrent-ruby, sprockets, sprockets-rails, rails after 386 seconds
33 gems installed

系统提示安装了33个gems包

//安装mysql配适器
//$ /usr/local/ruby/bin/gem install mysql

将ruby bundle 加入环境变量
$ export PATH=$PATH:/usr/local/ruby/bin/
$ echo "export PATH=$PATH:/usr/local/ruby/bin/" > /etc/profile.d/ruby.sh
```

##### 在数据库中创建相关库和账号
```bash
mysql> create database redmine ;
mysql> CREATE USER 'redmine'@'localhost' IDENTIFIED BY 'my_password';
mysql> GRANT ALL PRIVILEGES ON redmine.* TO 'redmine'@'localhost';
```

##### 安装redmine

```bash
$ wget https://www.redmine.org/releases/redmine-3.3.0.tar.gz

$ tar -xf redmine-3.3.0.tar.gz -C /data/ && cd /data/redmine*
redmine放置在/data目录下

$ cp config/database.yml.example config/database.yml

$ vi config/database.yml

编辑config/database.yml文件将production中mysql的账号密码改为实际的账号密码

安装redmine的依赖
$ /usr/local/ruby/bin/bundle install --without development test

上面命令会根据本目录下的Gemfile文件来安装依赖包，redmine同时也会根据你的config/database.yml文件的内容安装响应的database adapters，所以如果更改了使用的数据库需要重新运行上面命令

注意运行上面命令的提示：
Don't run Bundler as root. Bundler can ask for sudo if it is needed, and installing your bundle as root will break this application for all non-root users on this machine.

生成一个加密用的key
$ /usr/local/ruby/bin/bundle exec /usr/local/ruby/bin/rake generate_secret_token
```

##### 建立数据库表并导入数据

```bash
$ RAILS_ENV=production bundle exec rake db:migrate
$ RAILS_ENV=production bundle exec rake redmine:load_default_data
会让你选择一种语言，请选择zh
```

##### 更改Linux环境相关目录的权限

如果你使用其他用户启动redmine，那么请保证那个用户对redmine目录下的`files` `log` `tmp` `tmp/pdf` `public/plugin_assets`目录有写权限，比如：
```bash
$ mkdir -p tmp tmp/pdf public/plugin_assets
$ sudo chown -R redmine:redmine files log tmp public/plugin_assets
$ sudo chmod -R 755 files log tmp public/plugin_assets
```

##### 测试安装

```bash
$ bundle exec rails server webrick -e production -b 192.168.1.202

192.168.1.202为你的服务器IP地址

可以使用默认用户名密码 admin/admin登陆查看
```

##### 使用其他服务器运行redmine

安装thin
```bash
编辑redmine根目录下的Gemfile文件，在全局的配置中添加两行：
gem "faye"
gem "thin"

重新进行bundle
$ /usr/local/ruby/bin/bundle install --without development test

创建thin的配置文件
mkdir /etc/thin/
```

编辑thin配置文件`/etc/thin/redmine.yml`，内容如下：
```bash
pid: tmp/pids/thin.pid
group: root
wait: 30
timeout: 30
log: log/thin.log
max_conns: 1024
require: []

environment: production
max_persistent_conns: 512
servers: 4
daemonize: true
user: root
socket: /tmp/thin.sock
chdir: /data/redmine-3.3.0 
```

启动thin
```bash
$ thin -C /etc/thin/redmine.yml start
```

配置nginx

nginx包含的配置文件`proxy.include`内容如下：

```bash
proxy_set_header   Host $http_host;
proxy_set_header   X-Real-IP $remote_addr; 
proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header   X-Forwarded-Proto $scheme;
client_max_body_size       10m;
client_body_buffer_size    128k;
proxy_connect_timeout      90;
proxy_send_timeout         90;
proxy_read_timeout         90;
proxy_buffer_size          4k;
proxy_buffers              4 32k;
proxy_busy_buffers_size    64k;
proxy_temp_file_write_size 64k;
```

nginx配置文件如下：

```bash
upstream thin_cluster {
    server unix:/tmp/thin.0.sock;
    server unix:/tmp/thin.1.sock;
    server unix:/tmp/thin.2.sock;
    server unix:/tmp/thin.3.sock;
}

server {
    listen       your.ip.address.here:80;
    server_name  your.domain.name;

    access_log  /var/log/nginx/redmine-proxy-access;
    error_log   /var/log/nginx/redmine-proxy-error;

    include sites/proxy.include;
    root /var/lib/redmine/redmine/public;
    proxy_redirect off;

    # Send sensitive stuff via https
    rewrite ^/login(.*) https://your.domain.here$request_uri permanent;
    rewrite ^/my/account(.*) https://your.domain.here$request_uri permanent;
    rewrite ^/my/password(.*) https://your.domain.here$request_uri permanent;
    rewrite ^/admin(.*) https://your.domain.here$request_uri permanent;

    location / {
        try_files $uri/index.html $uri.html $uri @cluster;
    }

    location @cluster {
        proxy_pass http://thin_cluster;
    }
}

server {
    listen       your.ip.address.here:443;
    server_name  your.domain.here;

    access_log  /var/log/nginx/redmine-ssl-proxy-access;
    error_log   /var/log/nginx/redmine-ssl-proxy-error;

    ssl on;

    ssl_certificate /etc/ssl/startssl/your.domain.here.pem.full;
    ssl_certificate_key /etc/ssl/startssl/your.domain.here.key;

    include sites/proxy.include;
    proxy_redirect off;
    root /var/lib/redmine/redmine/public;

    # When we're back to non-sensitive things, send back to http
    rewrite ^/$ http://your.domain.here$request_uri permanent;

    # Examples of URLs we don't want to rewrite (otherwise 404 errors occur):
    # /projects/PROJECTNAME/archive?status=
    # /projects/copy/PROJECTNAME
    # /projects/PROJECTNAME/destroy

    # This should exclude those (tested here: http://www.regextester.com/ )
    if ($uri !~* "^/projects/.*(copy|destroy|archive)") {
        rewrite ^/projects(.*) http://your.domain.here$request_uri permanent;
    }

    rewrite ^/guide(.*) http://your.domain.here$request_uri permanent;
    rewrite ^/users(.*) http://your.domain.here$request_uri permanent;
    rewrite ^/my/page(.*) http://your.domain.here$request_uri permanent;
    rewrite ^/logout(.*) http://your.domain.here$request_uri permanent;

    location / {
        try_files $uri/index.html $uri.html $uri @cluster;
    }

    location @cluster {
        proxy_pass http://thin_cluster;
    }
}
```

##### redmine的迁移和升级提示

redmine的备份只需要备份数据库和redmine根目录下的`files`文件夹即可；所以迁移的话只需要将备份的数据库和files文件进行覆盖

如果是升级/全新安装新的版本，则需要执行下面的步骤：
```bash
1. 安装上面的步骤进行全新安装
2. 删除redmine数据库和files文件夹（或者备份）
3. 将你的原备份的数据库和文件进行覆盖
4. 执行下面的数据库集成和清理操作

$ bundle exec rake db:migrate RAILS_ENV=production
$ bundle exec rake tmp:cache:clear tmp:sessions:clear RAILS_ENV=production

$ bundle exec rake generate_secret_token
```


参考：
[**`redmine wiki`**](https://www.redmine.org/projects/redmine/wiki/RedmineInstall)
[**`redmine thin`**](http://www.redmine.org/projects/redmine/wiki/HowTo_configure_Nginx_to_run_Redmine)
[**`redmine upgrade`**](https://www.redmine.org/projects/redmine/wiki/RedmineUpgrade)
