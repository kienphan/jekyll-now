---
layout: post
title: Hello World với Python Flask
---

### Flask là cái gì?

Flask là một micro web framework khá nổi tiếng viết bởi Python. Nhanh, nhỏ nhẹ, đơn giản, nếu tập tành viết web application với Python thì Flask là 1 sự lựa chọn thích hợp.

À, mà phải biết chút Python chứ nhỉ? 
Lên [Codeacademy](https://www.codecademy.com/learn/python) dạo 1 khoá python, hoặc download 1 số ebook về python cơ bản, cảm nhận ban đầu là khá fun.
Next, bắt tay vào Hello World luôn thôi

### Hello World
- Trước tiên cần đảm bảo môi trường đã cài đặt python. Ở bài này mình dùng Mac OS với Homebrew, môi trường Linux thì hãy dùng những lệnh cài đặt tương tự.
Có 2 phiên bản Python 2.x và 3.x, thôi cứ mới mà dùng.

```bash
brew install python3
```

- Kiểm tra version python 

```bash
python3 --version   
#Python 3.6.2
```

Gói python đi kèm với `pip`(package managerment system để cài các gói của python)
, ở đây `python3` thì đi kèm với pip alias là `pip3`

- Nào cùng tạo thư mục chứa app của mình, mình sẽ gọi nó là flask-starter

```bash
$ cd ~/your_favorite_dev_path
$ mkdir flask-starter
$ cd flask-starter
```

- Cài đặt flask nào

```bash
$ pip3 install flask
```

- OK, bây giờ tạo 1 file execute run.py thử xem

```python
from flask import Flask
app = Flask(__name__)
 
@app.route("/")
def hello():
    return "Hello World!"
 
if __name__ == "__main__":
    app.run()
```

- Chạy thử run.py để xem được cái gì nào

```bash
$ python3 run.py()
* Running on http://localhost:5000/
```

- Mở trình duyệt lên, chạy [http://localhost:5000/](http://localhost:5000/) để xem thành quả.

#### Routing
- Tạo thử các route `/hello` khác thì thế nào

```python
from flask import Flask
app = Flask(__name__)
 
@app.route("/")
def index():
    return "Home Page"
 
@app.route("/hello")
def hello():
    return "Hello World!"
```

Thử lại route mới nào: [http://localhost:5000/hello](http://localhost:5000/hello)

#### Passing Variables
- Muốn truyền tham số tên thằng nào đấy vào route thì thế nào nhỉ. Thêm tiếp vào `run.py` nào:

```python
from flask import Flask
app = Flask(__name__)
 
@app.route("/")
def index():
    return "Home Page"
 
@app.route("/hello")
def hello():
    return "Hello World!"

@app.route("/hello/<string:name>/")
def hello_user(name):
    return "Hello, %s" % name
```

- Thử nào [http://localhost:5000/hello/kienphan](http://localhost:5000/hello/kienphan)
OK, cũng không khó hiểu lắm nhỉ. 

#### HTML Template & Style 

- Ơ, nếu muốn dùng template html thì sao? rồi cả css nữa. Lại phải cấu trúc tiếp thư mục thôi.

```bash
mkdir static
mkdir templates
```

- Tạo 1 file `test.html` trong templates 

```bash
touch templates/index.html
```

- Chỉnh sửa index.html cho nó có tí gọi là

```html
<h1>Hello World!!!</h1>
```

- Chỉnh lại file `run.py` import thêm method `render_redirect()` của flask, sửa lại route `/` sử dụng template `index.html`

```python
from flask import Flask, render_template

app = Flask(__name__)
 
@app.route("/")
def index():
    return render_template('index.html')
 
@app.route("/hello")
def hello():
    return "Hello World!"

@app.route("/hello/<string:name>/")
def hello_user(name):
    return "Hello, %s" % name
```

- Thế còn cái route mà có biến `name` ở route thì sao nhỉ, chắc phải truyền biến vào template bằng cách nào đó chứ nhỉ? 

- Trước tiên tạp 1 template khác, gọi là `hello.html` đi

```bash
touch templates/hello.html
```

Chỉnh sửa lại `run.py` nào

```python
from flask import Flask, render_template

app = Flask(__name__)
 
@app.route("/")
def index():
    return render_template('index.html')
 
@app.route("/hello")
def hello():
    return "Hello World!"

@app.route("/hello/<string:name>/")
def hello_user(name):
    return render_template('hello.html', name=name)
```

- Như vậy là ta đã truyền biến name vào template `hello.html`. Vậy thử chỉnh sửa `hello.html` thôi

```html
<h1>Hello, {{ name }} </h1>
```

`{{ name }}` là cú pháp của [jinja2](http://jinja.pocoo.org/) template engine, nếu ai đã từng làm việc với các template engine ở web framework khác như `jade`, `blade` thì chắc cũng không quá xa lạ với các truyền biến này.

OK, thử lại với [http://localhost:5000/hello/kienphan](http://localhost:5000/hello/kienphan) nào

Thế cái folder `static` nãy giờ chưa dùng để làm gì? Ở đây ta sẽ chứa các `asset` gồm `image`, `css`,... Thử với `css` đã.

```bash
touch static/style.css
```

- Cho tí màu vào vào `h1` lúc nãy nhỉ

```css
h1 {
    color: green;
}
```

Thế thì cũng phải sửa lại hello.html

```html
<html>
<head>
   <link rel="stylesheet" type="text/css" src="{{ url_for('static', filename = 'style.css') }}" ></script>
</head>
<body>
   <h1>Hello, {{ name }} </h1>
</body>
</html>
```

Reload lại browser thử xem sao, nhưng để không cần phải restart lại application server mỗi khi thay đổi các file `static` và `templates`, thì cần phải thêm `config` ở `run.py`

```python
if __name__ == "__main__":
    app.jinja_env.auto_reload = True
    app.config['TEMPLATES_AUTO_RELOAD'] = True
    app.run()
```

### Kết luận

- Đúng như với tên gọi mini webframework, `Flask` khá đơn giản, phải config bằng tay khá nhiều lúc ban đầu, cần gì thì cài đặt thêm cái đấy, như `ORM`, hay tự cấu trúc thư mục bằng tay. Nếu đang dev web với framework như `Rails` chuyển sang như là chạy Airblade chuyển qua đi supercub vậy, cần độ nhiều.
- Python khá fun, cũng đáng bỏ thời gian ra học hỏi chút.
- Ví dụ trên với Hello World còn khá đơn giản, để phù hợp với ứng dụng lớn hơn cần cấu trúc lại theo module hoặc MVC để dễ dàng phát triển hơn.