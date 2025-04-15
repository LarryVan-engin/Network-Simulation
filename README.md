# Network-Simulation
 
Sơ đồ kết nối mô phỏng mạng WAN
Các bước cấu hình các thiết bị trong mạng WAN
1.	Cấu hình địa chỉ IP cho mạng LAN1 và LAN2
Sau khi connect các thiết bị với nhau, ta tiến hành cấu hình thiết bị Router:
Mở CLI, sau đó cấu hình lần lượt như hình:
 
Trong đó:
Lệnh int f 0/0 là truy cập quyền vào kết nối FastEthernet 0/0
	Địa chỉ IP là địa chỉ IP định sẵn cho kết nối mạng LAN thông qua SWITCH với segment cuối khác 0 và 255, SUBNET MASK mặc định là 255.255.255.0 vì không phân vùng subnet.
Sau đó nhấn thêm lệnh: R0 (config-if) #no shut để ping vào mạng, nếu trên đường bus hiện mũi tên màu xanh là kết nối thành công.
Làm tương tự với mạng LAN2 với địa chỉ IP tự định sẵn với segment cuối khác 0 và 255, subnet mask tương tự.
2.	Cấu hình địa chỉ IP cho các Router
Để các Router được kết nối với nhau, ta kết nối dây thông qua các cổng Serial Port. Tương tự để cấu hình các thiết bị Router, ta mở CLI
 
Trong đó:
	Lệnh int s 3/0 là truy cập quyền vào kết nối Serial với Router khác thứ tự là 3/0
	Địa chỉ IP được định vào địa chỉ của Serial Port mà ta đã get into.
 
Sau đó ta cấu hình Clock rate để đồng bộ xung với 56000 Hz

Làm tương tự với các Router còn lại trong mạng WAN với cổng Serial Port đã thiết lập (phải trùng với địa chỉ IP Network Address của bus kết nối, chỉ thay đổi Host Address sao cho khác 0 và 255)

Để xem được các giao tiếp đã thiết lập đã kết nối chưa, ta có thể dùng câu lệnh 
R1# show ip interfacce brief
 
Kết quả như hình trên cho thấy ta đã thiết lập các địa chỉ IP cho các giao tiếp FastEthernet 0/0 (172.16.10.254) và Serial 2/0 (192.168.10.1)

Sau đó, để kiểm tra các kết nối đã thành công chưa, ta có thể test bằng cách ping data đến địa chỉ IP cần kiểm tra
 
Như hình trên, ta thấy địa chỉ IP được ping tới là 172.16.10.254 được thể hiện là Success rate is 100 percent, nghĩa là đã kết nối thành công được đến địa chỉ IP đó. 
Ngược lại, với địa chỉ IP 200.200.200.1, ta nhận được kết quả Success rate is 0 percent, nghĩa là ta chưa thiết lập thành công được kết nối từ thiết bị đang sử dụng gửi đến địa chỉ IP đó. 
3.	Cấu hình địa chỉ IP static cho các End-devices
 
Đối với từng mạng LAN, ta cấu hình địa chỉ IP cho từng thiết bị đầu cuối kết nối vào mạng (PC-Laptop).
Ta truy cập vào Deskop PC -> IP configuration.
Địa chỉ IPv4 là địa chỉ có Network Address là 2 bytes đầu giống với trên địa chỉ của giao tiếp FastEthernet 0/0, byte thứ giống với Host Address, byte cuối cùng là số thứ tự định cho device (Các device có số thứ tự khác nhau, khác 0 và 254). 
Subnet Mask được thiết lập theo default class C (256 blocks)
Default Gateway là địa chỉ IP mà PC được kết nối mạng vào, ở đây ta sử dụng địa chỉ IP trên giao tiếp FastEthernet 0/0 của Router0
Sau đó làm tương tự đối với các End-devices còn lại. 
 

Sau khi thiết lập địa chỉ IPv4 cho các devices, ta có thể kiểm tra lại đường truyền bằng cách test gửi thư qua kết nối.
 
Như trên hình ta có thể thấy, đối với địa chỉ mà ta đã liên kết đúng hoặc đã thiết lập đúng thì sẽ có Status là Successful. Ngược lại nếu ta thiết lại sai hoặc đường truyền lỗi, ta sẽ thấy thông báo là Failed.

Hoặc ta có thể kiểm tra nhanh bằng ping IP destination trên CLI hoặc Commant Promt của PC.
 
Khi ta thấy báo Lost = 0 nghĩa là đã kết nối và truyền dữ liệu thành công,  nếu Lost=4 thì nghĩa là kết nối đã gặp lỗi.
4.	Cài đặt giải thuật định tuyến cho các kết nối mạng
a.	Sử dụng giải thuật OSPF
 
Để cấu hình định tuyến sử dụng giải thuật OSPF, ta thực hiện các bước code line như trên hình.
Trong đó địa chỉ IP là địa chỉ IP có byte cuối là host 0, Subnet Mask là 0.0.0.255 dùng để đọc byte cuối.

 
Làm tương tự với các router khác, sau khi cấu hình địa chỉ IP khớp với Serial connection của router trước đó, ta sẽ nhận được dòng thông báo Loading Done.
Sau khi đã kết nối các network address, ta có thể kiểm tra lại các kết nối đó bằng cách sử dụng lệnh ‘show ip route ospf ’. Lệnh này cho phép ta xem lại các kết nối ta đã cấu hình trên giải thuật định tuyến OSPF.
 
Hoặc ta có thể sử dụng lệnh show ip route
 
Lệnh này cho phép ta xem tất cả các kết nối mà ta đã cấu hình trên router đó được link với các router có IP address khác trong mạng WAN.
Để test liên kết các định tuyến, ta có thể dụng lệnh ping IP address trên CLI hoặc Commant Prompt
b.	Sử dụng giải thuật RIP
Với sơ đồ ví dụ như hình trên, tiến hành cấu hình định tuyến RIP cho các router như sau: 
Cấu hình router R1: sử dụng RIP version 2
R1(config)#router rip
R1(config-router)#version 2
R1(config-router)#network 192.168.10.0
R1(config-router)#network 10.1.1.0
Cấu hình router R2: sử dụng RIP version 2
R2(config)#router rip
R2(config-router)#version 2
R2(config-router)#network 10.1.1.0
R2(config-router)#network 192.168.20.0
Tóm lại, để cấu hình RIP cho router thì sử dụng các câu lệnh cơ bản sau:
Router (config) # router rip
Router (config-router) # version 2
Router (config-router) # network mang_can_quang_ba
Ngoài ra còn có các option sau:
- Auto-summary (gộp các subnet lại thành một network chung)
- Default-information originate (quảng bá tuyến default route của nó cho các router cùng chạy RIP bên trong)
- Redistribute static (quảng bá những static route của nó cho các router cùng chạy RIP bên trong)
- Distance (set giá trị AD)
- Passive-interface (không cho gửi thông tin RIP đến các cổng connected với host để giảm traffic vô ích)

c.	Sử dụng giải thuật EIGRP
5.	Cấu hình mật khẩu truy cập cho các mạng
a.	Cấu hình mật khẩu cho router mạng Wired LAN
 
Mô phỏng hệ thống mạng LAN doanh nghiệp	
Để cấu hình password cho hệ thống quản lý, ta sẽ cấu hình mật khẩu trên Router Headquarter nhằm quản lý quyền truy cập của các users. Bên cạnh đó ta cũng có thể cấu hình mật khẩu đa level lên các switch nhằm tăng mức độ bảo mật, tuy nhiên điều này sẽ gây khó khăn và phiền phức trong trường hợp ta chỉ quan tâm đến mô phỏng cấu hình mật khẩu đơn giản và tập trung vào các mục tiêu khác như build hoặc giải thuật định tuyến…
Đầu tiên ta sẽ truy cập vào CLI của Router, sau đó sẽ tiến hành cấu hình như sau:
 
Trong đó:
Lệnh enable password 123 là thiết lập mật khẩu truy cập vào địa chỉ quản lý router.
Lệnh enable secret 1234 là thiết lập mật khẩu bí mật để truy cập quyền chỉnh sửa thông tin trên hệ thống router. Với mật khẩu bí mật này, ta có thể dễ quản lý quyền truy cập và chỉ có thể truy cập bởi người quản trị nắm giữ mật khẩu này.
Với username là admin, đó cũng là tài khoản mà ta sẽ có thể truy cập vào router mạng này.
Để kiểm tra lại mật khẩu đã được cấu hình thành công hay chưa, ta sẽ truy cập vào một PC bất kỳ trong mạng local, sau đó truy cập Commant Prompt và thực hiện:
 
Lệnh telnet 10.100.100.100.1 là lệnh thực hiện telnet line, là một phương pháp kết nối liên lạc các thiết bị trong mạng local. Địa chỉ IP được nhắn đến là địa chỉ IP của router mà ta định là administrator.
Password truy cập vào tài khoản admin là 123 như đã cài đặt.
 
Theo như ta thấy, để có thể truy cập quyền chỉnh sửa cũng như xem được thông tin connect trên Router, ta sẽ phải nhập mật khẩu secret là 1234 
 
Sau khi đã truy cập được vào Router, ta có thể dùng lệnh show run hoặc show running-config để xem được thông tin của admin’s user account. 
b.	Cấu hình mật khẩu cho router mạng không dây (WLAN)
 
Mô phỏng mạng home WLAN
Trong thực tế, ta rất cần cấu hình mật khẩu truy cập cho các mạng WLAN (thông dụng trong thương mại là Wifi) nhằm giới hạn lượng người dùng và tăng tính bảo mật cho người dùng đã truy cập. Vì thế ta cũng sẽ mô phỏng cách cấu hình mật khẩu cho router mạng WLAN.
Đầu tiên truy cập vào Wireless Router -> GUI -> thiết lập các thông tin cơ bản như địa chỉ Internet Connection type (Ở đây ta cấu hình theo định dạng  DHCP), Hostname, Domain, IP address và DHCP server.
 
Chuyển qua mục Wireless, ta sẽ cấu hình các mục như Network mode, SSID và Channel, Radio Band. Như hình dưới đây thì ta cấu hình chuẩn kênh có băng tần thông thường là 2.4Ghz
 

Sau đó chuyển qua Title có tên là Wireless Security, ta sẽ thấy có các mục mà ta cần thiết lập như Security Mode, Phương pháp mã hóa (Encryption), Passphrase và thời gian làm mới quyền truy cập (Key renewal).
 
Tùy vào các router mà ta cài đặt cũng như là server định tuyến mà ta có thể lựa chọn các level cho password. Có nhiều chuẩn cho ta lựa chọn như WEP, WPA, WPA2,… Mỗi tiêu chuẩn có cách mã hóa (Encryption) riêng biệt và sẽ có độ phức tạp khác nhau, điều này nhằm tăng cường bảo vệ quyền truy cập tài khoản kết nối cũng như an toàn thông tin cho user, hạn chế bị hack thông tin truy cập.
 
Để thiết lập mật khẩu quản trị viên cho router, ta sẽ chuyển qua mục Administration, sau đó sẽ nhập mật khẩu cần cấu hình vào 2 mục như hình bên dưới. Ở đây ta cấu hình mật khẩu đơn giản là 12345. 
 
Để kiểm tra lại mật khẩu đã cấu hình thành công chưa, ta có thể sử dụng End-devices bất kỳ như PC, Laptop hay Tablet.
Truy cập vào Desktop -> PC Wireless. Khi đã tìm thấy tên mạng không dây đã cài đặt -> chọn mạng ta đã thiết lập -> nhập mật khẩu  12345 và kết nối.
 
