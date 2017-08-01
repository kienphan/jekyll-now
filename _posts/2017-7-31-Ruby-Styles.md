---
layout: post
title: Ruby Coding Styles Rules dựa vào kinh nghiệm thực tế
---

Trong những coding convention định nghĩa ở [https://github.com/bbatsov/ruby-style-guide](https://github.com/bbatsov/ruby-style-guide) thì có những quy tắc rất hay gặp trong thực tế

- Sử dụng những syntax của Ruby thay vì dùng những syntax kiểu C, hoặc Javascript

VD:
String interpolation

Good: `str = "Aloha!, {a}"`

Bad: `str = "Aloha!, " + a`

Array

Good: `a = %w(a b c)`

Not Good: `a = ['a', 'b', 'c']`

- Dùng single quote thay vì double quotes

Good: `a = 'string'`

Bad: `a = "string"`

- Dùng `do...end` thay vì {...}

Good: 

```ruby
ary.each do |e|
  ...
end
```

Bad:

```ruby
ary.each { |e|
  ...
}
```

- Dùng {...} cho block oneliner nếu có thể oneline

Good:   `ary.map{|e| e.attr}`

- DRY, code trùng có thể chuyển vào block hoặc method mới dùng chung

- Nested condition với `if...end` tối đa là 2 bậc sâu

Good:
```ruby
if condition_1
  #...
  if condition_2
  end 
else
  #...
end
```

Bad:

```ruby
if condition_1
  #...
  if condition_2
    if conditon_3
      #...
    end
  end 
else
  #...
end
```

- Dùng case nếu phải `elsif` nhiều giá trị

Good:
```ruby
case human
when 'male'
  #...
when 'female'
  #...
when 'gay'
  #...
when 'lesbian'
  #...
end 
```

Bad:
```ruby
if human == 'male'
  #...
elsif 'female'
  #...
elsif 'gay'
  #...
elsif 'lesbian'
  #...
end 
```

- Sử dụng câu điều kiện khẳng định thay vì phủ định

Good: `if a.present?`
Bad: `unless a.nil?` hoặc `if !a.nil?`

- Hash thì dùng cú pháp mới từ Ruby ver1.9

Good: `hash = {a: 1, b: 3}`

Bad: `hash = {a => 1, b => 3}`

- Không cần dùng return trả về ở cuối method 

Good: 

```ruby
def sum(a, b)
  a + b
end
```
Bad:

```ruby
def sum(a, b)
  return a + b
end
```

- Cẩn thận với `to_i` vì có thể dẫn đến sai lầm

```ruby
nil.to_i
#=> 0
```

- method với return boolean thì nên có `?` ở cuối

Good:

```ruby
def admin?
  #...
end
```

Bad:

```ruby
def is_admin
  #...
end
```

- Dùng `try` nếu object có khả năng bị `nil` để tránh khả năng lỗi  `No method from Nil Object`

```ruby
obj.try(:method)
```


Đây đều là những kinh nghiệm chủ quan của cá nhân, có thể tham khảo thêm tại [https://github.com/bbatsov/ruby-style-guide](https://github.com/bbatsov/ruby-style-guide)