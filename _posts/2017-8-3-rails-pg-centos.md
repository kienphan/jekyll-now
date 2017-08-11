---
layout: post
title: Các vấn đề hay gặp khi cài gem 'pg' với Rails trên CentOS
---

Cài project Rails với Postgresql lên CentOS thi thoảng gặp những vấn đề nhất định

- Các yêu cầu cài đặt trước

  - CentOS
  - Ruby Environment
  - Postgresql latest(starting)

- Cài đủ các thư viện cần thiết

```bash
yum install postgresql-devel postgresql-libs
```

### Problem1: bundle install thất bại 

> Error Output:

```
$ gem install pg -v '0.18.4'

Building native extensions.  This could take a while...
ERROR:  Error installing pg:
    ERROR: Failed to build gem native extension.

    /Users/username/.rvm/rubies/ruby-2.2.1/bin/ruby -r ./siteconf20151221-23315-1tkv3fd.rb extconf.rb
checking for pg_config... no
No pg_config... trying anyway. If building fails, please try again with
 --with-pg-config=/path/to/pg_config
checking for libpq-fe.h... no
Can't find the 'libpq-fe.h header
*** extconf.rb failed ***
Could not create Makefile due to some reason, probably lack of necessary
libraries and/or headers.  Check the mkmf.log file for more details.  You may
need configuration options.

Provided configuration options:
    --with-opt-dir
    --with-opt-include
    --without-opt-include=${opt-dir}/include
    --with-opt-lib
    --without-opt-lib=${opt-dir}/lib
    --with-make-prog
    --without-make-prog
    --srcdir=.
    --curdir
    --ruby=/Users/username/.rvm/rubies/ruby-2.2.1/bin/$(RUBY_BASE_NAME)
    --with-pg
    --without-pg
    --enable-windows-cross
    --disable-windows-cross
    --with-pg-config
    --without-pg-config
    --with-pg_config
    --without-pg_config
    --with-pg-dir
    --without-pg-dir
    --with-pg-include
    --without-pg-include=${pg-dir}/include
    --with-pg-lib
    --without-pg-lib=${pg-dir}/lib

extconf failed, exit code 1

Gem files will remain installed in /Users/username/.rvm/gems/ruby-2.2.1/gems/pg-0.18.4 for inspection.
Results logged to /Users/username/.rvm/gems/ruby-2.2.1/extensions/x86_64-darwin-15/2.2.0/pg-0.18.4/gem_make.out
``` 

> Nguyên nhân: Do không tìm thấy đúng file config của postgresql để cài đặt gem

> Giải pháp

Chỉnh lại `pg_config` path cho đúng 

```bash
bundle config build.pg --with-pg-config=/usr/local/pgsql-9.4/bin/pg_config

bundle install
```

### `rake db:create` gặp lỗi permisson create database

> error output:

```
psql: FATAL: role "user" does not exist 
```

> Nguyên nhân: như clear message, role user chưa tồn tại 

> Giải pháp

Tạo role user cho postgres

```bash
sudo -u postgres createuser user
```

### `rake db:create` gặp lỗi permission với Postgres

> Error Output

```
PG::InsufficientPrivilege: ERROR:  permission denied to create database
: CREATE DATABASE "myapp_development" ENCODING = 'unicode'
.... (lots of tracing)
Couldn't create database for {"adapter"=>"postgresql", "encoding"=>"unicode", "pool"=>5, "database"=>"myapp_development", "username"=>"user", "password"=>nil}
myapp_test already exists
```

> Nguyên nhân: Do role user không có permission user để createDB

> Giải pháp

```bash
$ sudo -u postgres -i

postgres@host:~$ psql
```

Sau khi login vào Postgres:

```
postgres=# ALTER USER user CREATEDB;
```