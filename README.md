Kiểm tra tình trạng SSH
================
###SSH thành công hay thất bại ???
- Xuất phát từ việc hệ thống bị scan bởi các tools và boot-net, cụ thể là scan port 22 (port mặc định của giao thức SSH).
Do đó mình tổng hợp các lệnh, tổ hợp lệnh để kiểm tra xem hệ thống của bạn đã bị những IP nào dò quét, để từ đó xem xét phương 
pháp phòng tránh là gì !

### Cách 1: Sử dụng lệnh `aureport`
- Thông tin về lệnh
```sh
whatis aureport
aureport (8)         - a tool that produces summary reports of audit daemon logs
```
- Centos:  Lệnh này trong CentOS 6.6 có sẵn
- Ubuntu: Trong Ubuntu cần cài đặt gói sau `sudo apt-get -y install auditd`

