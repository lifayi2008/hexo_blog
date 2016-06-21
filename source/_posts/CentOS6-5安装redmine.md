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

如果你使用其他用户启动redmine，那么请保证那个用户对redmine目录下的`files` `log` `tmp` `tmp/pdf` `public/plugin_assets`目录有写权限

比如：
```bash
$ mkdir -p tmp tmp/pdf public/plugin_assets
$ sudo chown -R redmine:redmine files log tmp public/plugin_assets
$ sudo chmod -R 755 files log tmp public/plugin_assets
```

##### 测试安装

```bash
$ bundle exec rails server webrick -e production -b 192.168.1.202

192.168.1.202为你的服务器IP地址
```

参考：
[redmine wiki](https://www.redmine.org/projects/redmine/wiki/RedmineInstall)
