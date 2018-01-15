## Plugin process

- Thu thập thông số liên quan đến process 
- Theo mặc định không có cấu hình để match từng process riêng lẻ mà chỉ có các thông số thống kê chung được thu thập như : số process trong mỗi state và fork rate 
- Process matches được cấu hình bởi process và processmatch options.  
- Các thông số collectd thu thập và thống kê: 

    + size of the resident segment size (RSS)
    + user and system-time used
    + number of processes
    + number of threads
    + io data
    + context switches (under Linux)
    + minor and major pagefaults.

- Trong đó ta cần quan tâm đến 2 thông số là RSS và PS_VM

    + Tổng dung lượng virtual memory của một process được tính theo công thức như sau:
        
        `virtual memory = part in physical memory + part on disk` 
    
    + Trong đó: 

        - Phần trong physical memory là RSS

- Một số thông số khác 

    + `ps_count` hiển thị tổng số lượng process và threads 
    +  `pc_cputime` hiển thị cách sử dụng CPU của process
    +  `pg_pagefault` số lượng major và minor 

- Ví dụ tham khảo 

```sh
LoadPlugin processes
 <Plugin processes>
   ProcessMatch "keepalived" "keepalived"
 </Plugin>
```
- Kết quả 

<img src = "https://i.imgur.com/IDYD0a9.png">

Trên dashboard graphite 

<img src = "https://i.imgur.com/dboawC5.png">

- Kiểm tra trên máy local được giám sát: 

RSS

<img src = "https://i.imgur.com/2BbHOhw.png">

PS_VM

<img src = "https://i.imgur.com/p3teMLH.png">


- Một số option thêm 

`CollectContextSwitch Boolean` : Thu thập sso lượng  context switches của process đã match . Mặc định là disable 

`CollectFileDescriptor Boolean` : Thu thập số lượng của file descriptors của process đã match. Mặc định là disable 

`CollectMemoryMaps Boolean` : Thu thập số lượng memory mapped files của process. Giới hạn cho số lượng này được cấu hình thông qua `/proc/sys/vm/max_map_count` trong linux kernel.

## Plugin tail 

- Dùng để giám sát theo dõi các file log 
- Ví dụ 

```sh
LoadPlugin tail
 <Plugin "tail">
  <File "/var/log/messages">
        Instance "info"
         <Match>
          Regex " info "
          DSType "CounterInc"
          Type "counter"
          Instance "total"
        </Match>
  </File>
```

- Trong đó có các option :

    + `Instance` là tên của một trường hợp mình muốn thu thập 
    + `Match` là một khối block các option 
    + `Regex` bên trong đó là một chuỗi string mình muốn match  
    + `DSType` thiết lập kiểu thu thập các dư liệu. VD:

        - `GaugeAverage` : tính trung bình.
        - `GaugeMin` : Lấy giá trị nhỏ nhất 
        - `GaugeMax` : Sử dụng giá trị lớn nhất 
        - `GaugeLast` : Sử dụng giá trị cuối cùng được tìm thấy 
        -  `CounterInc` : Đếm số lượng các dòng được match 
        - Ngoài ra còn một vài DSType khác, mình sẽ update thêm...

    + `Type` thiết lập kiểu được sử dụng để gửi giá trị này





