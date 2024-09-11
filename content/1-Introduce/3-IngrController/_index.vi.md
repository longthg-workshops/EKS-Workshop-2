---
title: "Bộ điều khiển Ingress"
date: "2024-04-03"
weight: 3
chapter: false
pre: "<b> 1.3 </b>"
---

### Ingress
#### Tổng quan về Ingress
Ingress hiển thị các tuyến HTTP và HTTPS từ bên ngoài cụm đến các dịch vụ trong cụm. Định tuyến lưu lượng được kiểm soát bởi các quy tắc được xác định trên tài nguyên Ingress.

Sau đây là một ví dụ đơn giản về việc Ingress gửi tất cả lưu lượng của mình đến một Dịch vụ:
![ingress](../../../images/1/3/ingress.svg)

Ingress có thể được định cấu hình để cung cấp cho Dịch vụ các URL có thể truy cập bên ngoài, cân bằng tải lưu lượng, chấm dứt SSL / TLS và cung cấp dịch vụ lưu trữ ảo dựa trên tên. Bộ điều khiển Ingress chịu trách nhiệm thực hiện Ingress, thường là với bộ cân bằng tải, mặc dù nó cũng có thể định cấu hình bộ định tuyến biên hoặc giao diện người dùng bổ sung của bạn để giúp xử lý lưu lượng.

Ingress không hiển thị các cổng hoặc giao thức tùy ý. Việc hiển thị các dịch vụ khác ngoài HTTP và HTTPS ra internet thường sử dụng dịch vụ thuộc loại Service.Type=NodePort hoặc Service.Type=LoadBalancer

#### Điều kiện tiên quyết
Bạn phải có **bộ điều khiển Ingress** để thỏa mãn điều kiện của Ingress. Chỉ tạo tài nguyên Ingress không có tác dụng.

Bạn có thể cần triển khai bộ điều khiển Ingress như ingress-nginx. Bạn có thể chọn từ một số bộ điều khiển Ingress.

Lý tưởng nhất là tất cả các bộ điều khiển Ingress phải phù hợp với thông số kỹ thuật tham chiếu. Trên thực tế, các bộ điều khiển Ingress khác nhau hoạt động hơi khác nhau

### Ingress Controller
Để **Ingress** hoạt động trong cụm Kubernetes của bạn, phải có **bộ điều khiển ingress** đang chạy. Bạn cần chọn ít nhất một bộ điều khiển ingress và đảm bảo rằng nó được thiết lập trong cụm của bạn. Trang này liệt kê các bộ điều khiển ingress phổ biến mà bạn có thể triển khai.

Để tài nguyên Ingress hoạt động, cụm phải có bộ điều khiển ingress đang chạy.

Không giống như các loại bộ điều khiển khác chạy như một phần của tệp nhị phân kube-controller-manager, bộ điều khiển Ingress không được khởi động tự động với cụm. Sử dụng trang này để chọn triển khai bộ điều khiển ingress phù hợp nhất với cụm của bạn.

Kubernetes là một dự án hỗ trợ và duy trì bộ điều khiển ingress AWS, GCE và nginx.