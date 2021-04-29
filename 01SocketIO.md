PHẦN 1 - SOCKET IO <Vietnamese>:

- Một phần thiết yếu trong dự án E Learning 
- Biên soạn: Tùng Hoàng

1. Gửi và nhận dữ liệu
* Cú pháp ở server và client gần như nhau

- Gửi: 
    *socket.emit(<tên_gói_dữ_liệu: string>, <dữ_liệu: any>)*

[VD]

----- Client: -----
``` Javascript:
    // Gửi dữ liệu tới server:
    socket.emit("ID", "123321")
```
----- Server: -----
``` Javascript:
    // Gửi dữ liệu tới client:
    socket.emit("name", "Tùng")
```
------------------------------------------------------------

- Nhận:

    *socket.on(<tên_gói_dữ_liệu: string>, <xử_lý_khi_nhận_được_dữ_liệu: function>)*
    * Hàm xử lý khi nhận dữ liệu có 1 tham số, tương ứng với dữ liệu nhận được

[VD]

----- Client: -----

``` Javascript:
// Nhận dữ liệu từ server:
socket.on("ID", (data) => {
    setID(data)
})
  /* Đoạn này dịch như sau:
    * Tham số data đại diện cho dữ liệu nhận được
    * Mỗi lần nhận ID từ phía máy chủ, sẽ thay đổi (set) ID từ phía client
  */
```

----- Server: -----
``` Javascript:
// Nhận dữ liệu từ client và phản hồi lại:
socket.on("msg", (data) => {
    socket.emit("send", { user: "XXX", msg: data })
})
  /* Đoạn này dịch như sau:
    * Mỗi lần nhận msg (tin nhắn) từ client, sẽ phản hồi lại client 1 object
    * Ngoài ra, ở phía server ta còn có thêm 1 vài phương thức gửi dữ liệu khác
  */
```
------------------------------------------------------------

2. Phương thức gửi dữ liệu từ server
- Có 4 phương thức gửi dữ liệu từ server, nhưng có 2 phương thức thông dụng:

a) Gửi dữ liệu cho tất cả client:
    *io.emit(<tên_gói_dữ_liệu: string>, <dữ_liệu: any>)*

b) Cho tất cả client, ngoại trừ client vừa phản hồi:
    *socket.broadcast.emit(<tên_gói_dữ_liệu: string>, <dữ_liệu: any>)*

- Xem ví dụ sau:

``` Javascript:

// Client (ReactJS):

var socket = io('http://localhost:7000') 

export default function Component() {

    const [ id, setID ] = useState('')

    useEffect(() => {
        socket.on('join', (data) => {
            setID(data)
        })

        socket.emit("change", "hello admin")
        
        socket.on("something", (data) => {
            console.log(data)
        })
    })

    return <div>
        Some thing
    </div>
}

// Server (NodeJS):
io.on("connection", (socket) => {
    socket.emit("join", socket.id)

    socket.on("change", (data) => {
        // Gửi cho tất cả client:
        io.emit("something", "Hello I am admin, someone has just sent: " + data)
    })
})

```
