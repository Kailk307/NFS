# Thiết lập hệ thống chia sẻ tập tin qua NFS (Network File System)
## Mô tả:
Dự án nhằm thiết lập và cấu hình hệ thống chia sẻ tệp tin qua giao thức NFS giữa một máy chủ (NFS Server) và một hoặc nhiều máy trạm (NFS Client). Hệ thống cho phép các máy trạm truy cập vào một thư mục chia sẻ trên máy chủ để đọc và ghi dữ liệu trong môi trường mạng nội bộ, đảm bảo hiệu quả, an toàn và dễ dàng quản lý.
## Mục tiêu dự án
- Xây dựng hệ thống chia sẻ tệp tin qua NFS hoạt động ổn định trong mạng nội bộ.
- Đảm bảo quyền đọc/ghi dữ liệu từ phía máy trạm.
- Cấu hình bảo mật để chỉ các máy trạm được chỉ định mới có thể truy cập vào thư mục chia sẻ.
- Thiết lập hệ thống tự động mount để giảm thiểu thao tác thủ công trên máy trạm.
## Các bước thực hiện dự án
1. Chuẩn bị môi trường
- Sử dụng Ubuntu Server 20.04 LTS làm hệ điều hành
- Link download: https://releases.ubuntu.com/focal/
2. Cài đặt dịch vụ NFS Server
- Cài đặt dịch vụ NFS Server :
#### sudo apt update                 
#### sudo apt install nfs-kernel-server -y
- Tạo thư mục chia sẻ và thiết lập quyền cho thư mục :
#### sudo mkdir -p /var/nfs/shared
#### sudo chown nobody:nogroup /var/nfs/shared
#### sudo chmod 777 /var/nfs/shared 
- Cấu hình thư mục chia sẻ :
#### sudo nano /etc/exports ( chỉnh sửa)
#### /var/nfs/shared 192.168.1.0/24(rw,sync,no_subtree_check) 
#### sudo exportfs -ra ( áp dụng cấu hình)
- Khởi động và kiểm tra dịch vụ :
### Khởi động dịch vụ
#### sudo systemctl enable nfs-kernel-server
#### sudo systemctl start nfs-kernel-server
### Kiểm tra dịch vụ
#### sudo exportfs -v     
#### sudo systemctl status nfs-kernel-server
3. Cài đặt dịch vụ NFS Client
- Cài đặt NFS Client
#### sudo apt update    
#### sudo apt install nfs-common -y
- Tạo thư mục và Mount thư mục chia sẻ
#### sudo mkdir -p /mnt/nfs_shared
#### sudo mount 192.168.1.100:/var/nfs/shared /mnt/nfs_shared
- Kiểm tra mount
#### df -h | grep nfs
-Kiểm tra quyền đọc/ghi
#### echo "Hello from NFS Client" | sudo tee /mnt/nfs_shared/test.txt
#### cat /mnt/nfs_shared/test.txt
- Kiểm tra file trên server
#### /var/nfs/shared/test.txt
- Bảo mật NFS dựa trên IP Client :
#### /var/nfs/shared 192.168.1.101(rw,sync,no_subtree_check)
#### sudo exportfs -ra ( áp dụng)
- Cấu hình tự động mount
#### sudo nano /etc/fstab
#### 192.168.1.100:/var/nfs/shared /mnt/nfs_shared nfs defaults 0 0
#### sudo reboot
#### df -h | grep nfs
