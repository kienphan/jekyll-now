---
layout: post
title: Tạo màn hình Loading khi call Ajax thật đơn giản
---

Có thể để ý thấy nhiều bạn trẻ khi develop 1 chức năng nào đấy cần gọi API bằng AJAX với Jquery thì rất hay để lỗi không tạo màn hình Loading để khoá action từ người dùng lại, điều này có thể dẫn đến 1 số tác hại như sau: 

- API luôn có độ trễ xử lý, enduser không hiểu chuyện gì xảy ra, ko thấy hồi đáp sẽ bấm nút submit liên tục, dẫn đến lỗi hệ thống và dữ liệu.
- Tạo cảm giác ứng dụng chạy ì ạch nặng nề, không thích thú sử dụng dẫn đến rời bỏ ứng dụng

Việc tạo màn hình Loading cũng không đến nỗi phức tạp với HTML&CSS&JQuery nên việc tạo 1 màn hình Loading khi gọi API thì sẽ đảm bảo UX tốt hơn, hạn chế lỗi và nâng cao chất lượng hệ thống hơn.

> Loading image

Kiếm 1 cái ảnh gif hiệu ứng loading, VD như này 

![_config.yml]({{ site.baseurl }}/images/ajax-loader.gif)

> HTML

Tạo 1 div trong body 

```html
<body>
    <div id="loading" style="display:none">
        <img src="./your-image-path/ajax-loader.gif" alt="Loading..."/>
    </div>
    <!-- ... MAIN HERE ... -->
</body>
```

> CSS

```css
#loading {
  background-color:white;
  position: fixed;
  display: block;
  top: 0;
  bottom: 0;
  z-index: 1000000;
  opacity: 0.5;
  width: 100%;
  height: 100%;
  text-align: center;
}

#loading img {
  margin: auto;
  display: block;
  top: calc(50% - 100px);
  left: calc(50% - 10px);
  position: absolute;
  z-index: 999999;
}
```

> JQUERY

Thêm đoạn xử lý callback khi call AJAX 

```javascript
$(document).ready(function(){
    $(document).ajaxStart(function() {
        $("#loading").show();
    });
    $(document).ajaxStop(function() {
        $("#loading").hide();
    });
});
```
