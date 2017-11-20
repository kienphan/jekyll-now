---
layout: post
title: Golang - A Tour of Go (Các cấu trúc điều khiển)
---

[Bài viết trước](https://kienphan.github.io/Tour-Of-Go-Basics/) đã xem qua các phần cơ bản của Golang, và để tìm hiểu tiếp về Golang, nay ta tiếp tục dựa theo [Tour Of Go](https://tour.golang.org/flowcontrol/1) để tìm hiểu các cấu trúc điều khiển

### For

- Điểm đặc biệt của Golang là nó chỉ có 1 cú pháp loop là `For` mà thôi

```go
for i := 0; i < 10; i++ {
    sum += i
}
```

- Khác với `C` hay `Javascript`, cú pháp `for` của Go không có cặp dấu `()` để bao lại và cặp dấu `{}` của block tính toán thì là bắt buộc
- Giá trị khởi tạo của biến index và biểu thức cập nhập index cũng không yêu cầu bắt buộc

```go
sum := 1
for ; sum < 1000; {
    sum += sum
}
```
- Có thể viết lại bằng cách bỏ dấu `;` đi, và giờ trông nó chẳng khác hàm nào hàm `while` trong `C`

```go
sum := 1
for sum < 1000 {
    sum += sum
}
```

### If

- Cũng như `for`, mệnh đề `if` không yêu cầu cặp `()` và block phía trong thì yêu cầu `{}`

```go
func sqrt(x float64) string {
    if x < 0 {
        return sqrt(-x) + "i"
    }
    return fmt.Sprint(math.Sqrt(x))
}
```

- Câu `if` có thể gán thêm 1 biểu thức tính toán trước khi thực hiện điều kiện

```go
func pow(x, n, lim float64) float64 {
    if v := math.Pow(x, n); v < lim {
      return v
    }
    return lim
}
```
Biến v cũng chỉ có scope ở trong block của `if`
- `If` thì sẽ đi với `else` cho điều ngược lại 

### Switch

- Là 1 cách viết ngắn và dễ đọc hơn so với `if...else`
- Khác với `C` hay `Javascript`, switch trong `Go` sẽ chỉ chạy với case được chỉ định chứ không chạy tất cả các case, do đó không cần dùng `break` ở cuối case block.
- Điểm khác biệt nữa là case value cũng ko nhất thiết phải là constant hay integer 

```go
func main() {
    fmt.Print("Go runs on ")
    switch os := runtime.GOOS; os {
    case "darwin":
      fmt.Println("OS X.")
    case "linux":
      fmt.Println("Linux.")
    default:
      // freebsd, openbsd,
      // plan9, windows...
      fmt.Printf("%s.", os)
    }
}
```

### Defer

- Câu `defer` để làm chậm quá trình thực hiện tham số truyền vào cho đến khi hàm đang chứa câu lệnh  `defer` được return.

```go
func main() {
    defer fmt.Println("world")
    fmt.Println("hello")
}
//=> hello 
//=> world
```

### Stacking Defers

- Những `defer` của function sẽ được push vào stack. Và khi hàm chứa deferer được return, thì các deferred sẽ được đẩy ra thực hiện theo trình tự Last In First Out

```go
func main() {
    fmt.Println("counting")

    for i := 0; i < 3; i++ {
      defer fmt.Println(i)
    }

    fmt.Println("done")
}
//counting 
//done
//2
//1
//0
```

### Kết luận

- Câu điều kiện, vòng lặp của `Golang` không có quá nhiều khác biệt với các ngôn ngữ khác, riêng Loop thì có phần đơn giản hơn do chỉ có đúng 1 cách thức thực hiện
- Defer là 1 cái gì đó mới, hữu dụng khi ta muốn thực hiện 1 tính toán nào đấy khi hàm được trả về, thường được sử dụng để đơn giản hóa các chức năng thực hiện các hành động dọn dẹp tài nguyên.