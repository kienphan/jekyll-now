---
layout: post
title: Thử Golang xem sao. A Tour of Go (Basics)
---

Golang - được Google giới thiệu vào năm 2009, nhưng dạo gần đây được cải thiện để nổi lên như là 1 ngôn ngữ Serverside hàng đầu do có những ưu thế về tốc độ xử lý, hiệu năng tính toán. 
1 nguyên nhân khiến Go nổi lên cũng có thể do thời gian gần đây định luật Moore đã không còn đúng đắn nữa, với tốc độ phát triển của vi xử lý không còn nhanh như trước nữa, do đó những ngôn ngữ tính toán hiệu năng cao sẽ dần lên ngôi. 

Nhắc lại chút về [định luật Moore](https://vi.wikipedia.org/wiki/%C4%90%E1%BB%8Bnh_lu%E1%BA%ADt_Moore): "Số lượng transistor trên mỗi đơn vị inch vuông sẽ tăng lên gấp đôi sau mỗi năm."

Thế nên phải tìm hiểu coi thằng Golang này có gì hay. Cũng chỉ là note lại từ hàng chính chủ [GoTour](https://tour.golang.org)

### Packages

- Mọi chương trình Go đều được tạo bởi package
- Chương trình start được đặt trong package `main`

```Go
package main

import (
    "fmt"
    "math/rand"
)

func main() {
    fmt.Println("Hello World")
}
```

### Imports

- Dùng từ khoá `Import`

### Exported names

- Trong Go, dị cái là những cái tên được export thì theo chuẩn viết hoa chữ cái đầu. VD: `math.Pi`

```Go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Pi)
}
```

### Functions

- Cũng tương tự nhiều ngôn ngữ khác

```Go
package main

import "fmt"

func add(x int, y int) int {
    return x + y
}

func main() {
    fmt.Println(add(42, 13))
}
```

- Tuy nhiên khi 2 tham số có cùng kiểu thì có thể viết ngắn lại, thay vì 
```Go
x int, y int
```

có thể 

```Go
x, y int
```

```Go
package main

import "fmt"

func add(x, y int) int {
    return x + y
}

func main() {
    fmt.Println(add(42, 13))
}
```

### Multiple results

- 1 function có thể trả về nhiều kết quả, cái này có vẻ tương tự cái `tuple` của Swift

```Go
package main

import "fmt"

func swap(x, y string) (string, string) {
    return y, x
}

func main() {
    a, b := swap("hello", "world")
    fmt.Println(a, b)
}
```

### Variables

- Biến được khai báo với `var` và type ở cuối

```Go
package main

import "fmt"

var c, python, java bool

func main() {
    var i int
    fmt.Println(i, c, python, java)
}
```

- Biến cũng có thể được khởi tạo lúc khai báo, và nó định kiểu dựa vào dữ liệu khởi tạo.

```Go
package main

import "fmt"

var i, j int = 1, 2

func main() {
    var c, python, java = true, false, "no!"
    fmt.Println(i, j, c, python, java)
}
```

- Biến còn có thể khai báo tắt với `:=` khi ở trong function. Ngoài function thì không thể

```Go
package main

import "fmt"

func main() {
    var i, j int = 1, 2
    k := 3
    c, python, java := true, false, "no!"

    fmt.Println(i, j, k, c, python, java)
}
```

### basic types

```Go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128
```

### Zero values

- Các giá trị Zero ở đây là
  -  Số 0 với Numeric
  -  false với Boolean
  - "" với String

### Type conversions

- Expression T(v) chuyển kiểu của v sang T

```Go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```

### Type inference

- Khi không chỉ định rõ type lúc khai báo biến, thì type của biến sẽ được gán theo giá trị nằm bên phải

```Go
var i int
j := i // j is an int
```

- Hoặc khi khai báo biến thì tuỳ vào độ chính xác của giá trị khai báo để định kiểu 

```Go
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128
```

### Constants

- Khai báo cũng giống biến, nhưng thay `var` bằng `const`
- Không thể khai báo với `:=`

### Kết luận

- Tìm hiểu mới ở mức cơ bản, Golang có vẻ cũng fun, bài sau sẽ tìm hiểu thêm các thành phần khác.
- Syntax đẹp đẽ, không ; cuối câu, ít từ khoá nên cũng dễ nhớ
- IDE support tốt, cộng đồng đang ngày càng phát triển.