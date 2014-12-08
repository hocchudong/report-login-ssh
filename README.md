Kiểm tra tình trạng SSH
================
###SSH thành công hay thất bại ???
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
34. 12/08/2014 17:58:10 congtt 203.162.130.241 ssh /usr/sbin/sshd yes 86
35. 12/08/2014 17:58:10 congtt 203.162.130.241 ssh /usr/sbin/sshd yes 89
36. 12/08/2014 17:58:18 root ? pts/1 /bin/su yes 99
45. 12/08/2014 20:06:05 root 218.108.49.219 ssh /usr/sbin/sshd no 198
50. 12/08/2014 20:06:13 root 218.108.49.219 ssh /usr/sbin/sshd no 219
51. 12/08/2014 20:06:18 root 218.108.49.219 ssh /usr/sbin/sshd no 228
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
3. 12/07/2014 22:08:44 congtt 118.70.182.1 ssh /usr/sbin/sshd yes 50
4. 12/07/2014 22:08:44 congtt 118.70.182.1 ssh /usr/sbin/sshd yes 53
5. 12/07/2014 22:20:33 congtt 118.70.182.1 ssh /usr/sbin/sshd yes 84
6. 12/07/2014 22:20:33 congtt 118.70.182.1 ssh /usr/sbin/sshd yes 87
7. 12/08/2014 10:17:20 congtt 203.162.130.241 ssh /usr/sbin/sshd yes 181
8. 12/08/2014 10:17:20 congtt 203.162.130.241 ssh /usr/sbin/sshd yes 184
9. 12/08/2014 10:23:22 root ? pts/0 /bin/su yes 195
10. 12/08/2014 11:44:34 congtt 203.162.130.241 ssh /usr/sbin/sshd yes 225
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
2. 12/07/2014 22:20:28 congtt 118.70.182.1 ssh /usr/sbin/sshd no 83
3. 12/08/2014 10:40:52 ? 203.162.130.241 ssh /usr/sbin/sshd no 204
4. 12/08/2014 10:40:52 (invalid user) 203.162.130.241 ssh /usr/sbin/sshd no 205
5. 12/08/2014 18:10:38 ? 203.162.130.241 ssh /usr/sbin/sshd no 114
6. 12/08/2014 18:10:38 (invalid user) 203.162.130.241 ssh /usr/sbin/sshd no 115
7. 12/08/2014 20:05:56 root 218.108.49.219 ssh /usr/sbin/sshd no 178
8. 12/08/2014 20:05:56 root 218.108.49.219 ssh /usr/sbin/sshd no 179
9. 12/08/2014 20:06:01 root 218.108.49.219 ssh /usr/sbin/sshd no 188
10. 12/08/2014 20:06:01 root 218.108.49.219 ssh /usr/sbin/sshd no 189
11. 12/08/2014 20:06:05 root 218.108.49.219 ssh /usr/sbin/sshd no 198
12. 12/08/2014 20:06:05 root 218.108.49.219 ssh /usr/sbin/sshd no 199
```
