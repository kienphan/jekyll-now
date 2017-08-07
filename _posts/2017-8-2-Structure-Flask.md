---
layout: post
title: Cấu trúc project Flask để develop web app dễ dàng hơn
---

Bài viết trước đã giới thiệu qua về cách làm tạo 1 ứng dụng Hello World với Flask App [tại đây]({{ site.baseurl }}/Hello-Flask/), hôm nay mình sẽ thử cấu trúc lại Flask App để develop Web App dễ dàng và khoa học, trông khác cái bãi rác hơn.

![_config.yml]({{ site.baseurl }}/images/flask.png)

Bắt tay vào làm luôn thôi.(Thực hiện trên Mac OS, Ubuntu cũng gần tương tự)

### Chuẩn bị môi trường

- Kiểm tra lại môi trường (Xem lại [bài viết trước](https://kienphan.github.io/Hello-Flask/))

  - `Python3`
  - `pip3`
  - `virtualenv`

Cài `virtualenv` đơn giản bằng `pip`, đây là tool để tạo môi trường Python ảo chạy độc lập và không ảnh hưởng đến hệ thống)

```bash
pip3 install virtualenv
```

### Chuẩn bị cấu trúc application

- Cấu trúc folder trước đã nào, tạm thời gọi project là `flask-starter`

```bash
cd ~/
mkdir flask-starter
```

- Như [bài trước](https://kienphan.github.io/Hello-Flask/), chúng ta cũng cần 2 thư mục là `static` và `templates`, nhưng lần này đặt trong thư mục `app`

```bash
cd flask-starter
mkdir app
mkdir app/static
mkdir app/templates
```

- Cấu trúc thư mục giờ trông sẽ như thế này 

```bash
~/flask-starter
  |__/app
     |__/statics
     |__/templates
```

- Tạo virtualenv nào 

```bash
cd ~/flask-starter
virtualenv venv
```

- Cài đặt Flask

```bash
pip3 install flask
```

- Tạo file `.env` để lưu các biến môi trường

```bash
touch .env
```

- Thêm config vào file

```bash
source venv/bin/activate

export FLASK_APP="run.py"
export SECRET=''
export APP_SETTINGS="development"
```

- Save file `.env`, để project sử dụng virtual environment:

```bash
source .env
```

- Cấu trúc thư mục giờ sẽ trông thành như thế này

```bash
~/flask-starter
  |__/app
     |__/statics
     |__/templates
  |--.env
  |__/venv
```

- Tạo Application Files: Ở đây ta sẽ tạo ra file main entrypoint và file lưu giữ config cho application

```bash
touch ~/flask-starter/run.py
touch ~/flask-starter/config.py
touch ~/flask-starter/app/__init__.py
```

- Và giờ cấu trúc thư mục thành như thế này:

```bash
~/flask-starter
  |__/app
     |__/statics
     |__/templates
     |--__init__.py
  |--.env
  |__/venv
  |--config.py
  |--run.py
```

### Module hoá với tính năng Blueprint của Flask

Flask sử dụng 1 concept gọi là [Blueprint](http://flask.pocoo.org/docs/0.12/blueprints/) để tạo những components của App, nhờ đó đối với các ứng dụng lớn, Blueprint giúp mở rộng các tính năng dễ dàng hơn. Chi tiết hơn tại [http://flask.pocoo.org/docs/0.12/blueprints/](http://flask.pocoo.org/docs/0.12/blueprints/)

Ở đây mình sẽ tạo ra module pages, mục đích sẽ không ngoài việc in `Hello World` quen thuộc. 

```bash
mkdir ~/flask-starter/app/mod_pages
touch ~/flask-starter/app/mod_pages/__init__.py
touch ~/flask-starter/app/mod_pages/controllers.py
```

- Cấu trúc thư mục hiện tại sẽ trông như thế này:

```bash
~/flask-starter
  |__/app
     |--__init__.py
     |__/statics
     |__/templates
     |__/mod_pages
        |--__init__.py
        |--controllers.py
  |--.env
  |__/venv
  |--config.py
  |--run.py
```

- Trong `controllers.py` sẽ định nghĩa các action, tương ứng đó sẽ là các view trong `templates`, ở đây là chỉ có VD Hello World với action `hello` nên sẽ tạo `hello.html` ở `templates`. Ngoài ra ta tạo thêm file `404.html` để handler khi `Not Found`
 
 ```bash
~/flask-starter
  |__/app
     |--__init__.py
     |__/statics
     |__/templates
        |--404.html
        |__/pages
           |--hello.html
     |__/mod_pages
        |--__init__.py
        |--controllers.py
  |--.env
  |__/venv
  |--config.py
  |--run.py
```

### Lắp ghép các thành phần lại với nhau:

- Chỉnh sửa `run.py`:

```python
from app import app

if __name__ == '__main__':
    app.run()
```

- Chỉnh sửa `config.py`

```python
# Statement for enabling the development environment
DEBUG = True

# Define the application directory
import os
BASE_DIR = os.path.abspath(os.path.dirname(__file__))

# Application threads. A common general assumption is
# using 2 per available processor cores - to handle
# incoming requests using one and performing background
# operations using the other.
THREADS_PER_PAGE = 2

# Enable protection agains *Cross-site Request Forgery (CSRF)*
CSRF_ENABLED     = True

# Use a secure, unique and absolutely secret key for
# signing the data. 
CSRF_SESSION_KEY = "secret"

# Secret key for signing cookies
SECRET_KEY = "secret"

# Auto reload templates
TEMPLATES_AUTO_RELOAD = True
```

- Tạo __init__ file :

```bash
touch ~/flask-starter/app/__init__.py
```

Paste vào file app/__init__.py

```python
from flask import Flask, render_template, url_for, redirect
import os

app = Flask(__name__)

app.config.from_object('config')

from app.mod_pages.controllers import mod_pages as pages_module

app.register_blueprint(pages_module)

@app.errorhandler(404)
def not_found(error):
    return render_template('404.html')
```

- Tạp `layout` cho ứng dụng:

```bash
touch ~/flask-starter/app/templates/layout.html
```

Layout chứa bộ khung HTML, đảm bảo việc DRY code, giúp maintain dễ dàng hơn:

![_config.yml]({{ site.baseurl }}/images/flask-layout.png)

- Edit file `hello.html` để có thể sử dụng `layout`:

![_config.yml]({{ site.baseurl }}/images/hello-html.png)

- Edit lại `404.html`:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Page Not Found</title>
  </head>
  <body>
    <div id="message">
      <h2>404</h2>
      <h1>Page Not Found</h1>
      <p>The specified file was not found on this website. Please check the URL for mistakes and try again.</p>
    </div>
  </body>
</html>
```

### Run và tận hưởng thành quả 

```bash
phankien@neik flask-starter *$ python3 run.py
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 118-513-369
 ```

 Truy cập `localhost:5000` ta sẽ gặp ngay trang 404 Not Found do route '/' chưa được định nghĩa. 
 Thử route `hello` ta đã định nghĩa trong `mod_pages` xem sao : `localhost:5000/pages/hello`, ta sẽ thấy render ra trang Hello Flask với màu xanh lá cây quen thuộc.

Mình cũng đã push code lên Repo cá nhân để tham khảo [tại đây](https://github.com/kienphan/flask-starter).