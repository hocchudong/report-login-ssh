Kiểm tra tình trạng SSH
================

### TÓM TẮT
- Bài viết gồm 2 phần
```sh
A. SSH thành công hay thất bại ???
B. Phòng tránh như thế nào !
```

###A. SSH thành công hay thất bại ???
- Xuất phát từ việc hệ thống bị scan bởi các tools và boot-net, cụ thể là scan port 22 (port mặc định của giao thức SSH).
Do đó mình tổng hợp các lệnh, tổ hợp lệnh để kiểm tra xem hệ thống của bạn đã bị những IP nào dò quét, để từ đó xem xét phương 
pháp phòng tránh là gì !

### 1. Cách 1: Sử dụng lệnh `aureport`
#### 1.1. Thông tin về lệnh
```sh
root@congto:~# whatis aureport
aureport (8)         - a tool that produces summary reports of audit daemon logs
```
- Centos:  Lệnh này trong CentOS 6.6 có sẵn
- Ubuntu: Trong Ubuntu cần cài đặt gói sau `sudo apt-get -y install auditd` để có lệnh trên !

#### 1.2. Cách dùng cơ bản
##### 1.2.1. Tổng hợp báo cáo về việc login thông qua ssh 
- Các báo cáo số liệu thổng quan: Số user đã login, số account, group ...

+ Cú pháp lệnh: 
```sh
root@congto:~# aureport
```
+ Kết quả:
```sh
Summary Report
======================
Range of time in logs: 12/08/2014 22:31:30.416 - 12/08/2014 22:32:20.436
Selected time for report: 12/08/2014 22:31:30 - 12/08/2014 22:32:20.436
Number of changes in configuration: 2
Number of changes to accounts, groups, or roles: 0
Number of logins: 0
Number of failed logins: 0
Number of authentications: 0
Number of failed authentications: 0
Number of users: 1
Number of terminals: 0
Number of host names: 0
Number of executables: 0
Number of files: 0
Number of AVC's: 0
Number of MAC events: 0
Number of failed syscalls: 0
Number of anomaly events: 0
Number of responses to anomaly events: 0
Number of crypto events: 0
Number of keys: 0
Number of process IDs: 2
Number of events: 4

```

##### 1.2.2. Tổng hợp tất cả các thông tin về việc login ssh 
- Bao gồm cả thành công và thất bại

+ Cú pháp lệnh:
```sh
root@congto:~# aureport -au -i
```

+ Kết quả: 
```sh
...
32. 12/08/2014 17:56:51 congtt 203.162.130.241 ssh /usr/sbin/sshd yes 66
33. 12/08/2014 17:57:06 root ? pts/0 /bin/su yes 76
35. 12/08/2014 17:58:10 congtt 203.162.130.241 ssh /usr/sbin/sshd yes 89
36. 12/08/2014 17:58:18 root ? pts/1 /bin/su yes 99
45. 12/08/2014 20:06:05 root 218.108.49.219 ssh /usr/sbin/sshd no 198
52. 12/08/2014 20:06:18 root 218.108.49.219 ssh /usr/sbin/sshd no 229
...
```

##### 1.2.3. Thống kê các phiên login thành công
- Cú pháp lệnh 
```sh
root@congto:~# aureport -au -i --success
```

- Kết quả
```sh
Authentication Report
============================================
# date time acct host term exe success event
============================================
1. 12/07/2014 22:03:19 root 118.70.182.1 ssh /usr/sbin/sshd yes 14
2. 12/07/2014 22:03:19 root 118.70.182.1 ssh /usr/sbin/sshd yes 17
4. 12/07/2014 22:08:44 congtt 118.70.182.1 ssh /usr/sbin/sshd yes 53
7. 12/08/2014 10:17:20 congtt 203.162.130.241 ssh /usr/sbin/sshd yes 181
8. 12/08/2014 10:17:20 congtt 203.162.130.241 ssh /usr/sbin/sshd yes 184
9. 12/08/2014 10:23:22 root ? pts/0 /bin/su yes 195
11. 12/08/2014 11:44:34 congtt 203.162.130.241 ssh /usr/sbin/sshd yes 228

```

##### 1.2.4. Thống kê các phiên login thất bại
- Cú pháp lệnh 
```sh
root@congto:~# aureport -au -i --failed
```

- Kết quả
```sh
Authentication Report
============================================
# date time acct host term exe success event
============================================
1. 12/07/2014 22:20:28 congtt 118.70.182.1 ssh /usr/sbin/sshd no 82
3. 12/08/2014 10:40:52 ? 203.162.130.241 ssh /usr/sbin/sshd no 204
4. 12/08/2014 10:40:52 (invalid user) 203.162.130.241 ssh /usr/sbin/sshd no 205
5. 12/08/2014 18:10:38 ? 203.162.130.241 ssh /usr/sbin/sshd no 114
6. 12/08/2014 18:10:38 (invalid user) 203.162.130.241 ssh /usr/sbin/sshd no 115
8. 12/08/2014 20:05:56 root 218.108.49.219 ssh /usr/sbin/sshd no 179
9. 12/08/2014 20:06:01 root 218.108.49.219 ssh /usr/sbin/sshd no 188
12. 12/08/2014 20:06:05 root 218.108.49.219 ssh /usr/sbin/sshd no 199
```

##### 1.2.5. Thống kê  tổng quan các user đăng nhập thành công
- Cú pháp lệnh 
```sh
root@congto:~# aureport -l --success --summary -i
```

- Kết quả
```sh
Success Login Summary Report
============================
total  auid
============================
15  congtt
1  root
```

### 2. Cách 2: Sử dụng các lệnh cat, tail, awk, grep ...
#### 2.1. Kiểm tra log trong Linux
- Đối với Ubuntu, file log của SSH được lưu tại /var/log/secure. Do đó sử dụng lệnh cat, more, tail, less ...ta có thể xem được các thông tin về login trước đó.
##### 2.1.1. Kiểm tra các phiên SSH thất bại
```sh
 cat /var/log/secure | grep "Invalid user" | awk '{print $8}'
```


### B.Phòng tránh như thế nào 
```sh 
Đang tìm và viết
```
### C. Tham khảo
[1]. http://www.golinuxhub.com/2014/05/how-to-track-all-successful-and-failed.html
