﻿

## **ỨNG DỤNG ĐẦU TIÊN**

 **Whetting Your Appetite**
 Nếu bạn muốn quản lí thiết bị mạng (switch, router,...) bằng cách của mình, bạn cần viết ứng dụng RYU của mình. Ứng dụng của bạn nói cho Ryu bạn muốn quản lí thiết bị như thế nào. Sau đó Ryu cấu hình thiết bị qua giao thức OpenFlow...
 Việc viết ứng dụng Ryu rất dễ. Nó chỉ là Python scripts.
 **BẮT ĐẦU VIẾT**
 Chúng tôi trình bày ứng dụng Ryu làm cho OpenFlow switch câm ở tầng 2
 Mở trình soạn thảo tạo một file mới với nội dung như sau:

     from ryu.base import app_manager
    
    class L2Switch(app_manager.RyuApp):
        def __init__(self, *args, **kwargs):
            super(L2Switch, self).__init__(*args, **kwargs)

Ứng dụng Ryu chỉ là một Python scripts nên bạn có thể lưu lại với bất kỳ tên, phần mở rộng gì và mọi nơi bạn muốn. Giờ hãy đặt tên file là 'l2.py' ở thư mục home.
Ứng dụng này chưa có gì hữu dụng cả. Tuy nhiên nó là một ứng dụng Ryu hoàn chỉnh. Thực tế thì bạn có thể chạy ứng dụng này:

    % ryu-manager ~/l2.py
    loading app /Users/fujita/l2.py
    instantiating app /Users/fujita/l2.py

Tất cả những gì bạn có thể làm là khai báo cần một subclass mới của RyuApp để chạy chương trình Python như một ứng dụng Ryu
Tiếp theo hãy thêm chức năng gửi và nhận gói đến tất cả các cổng.

    from ryu.base import app_manager
    from ryu.controller import ofp_event
    from ryu.controller.handler import MAIN_DISPATCHER
    from ryu.controller.handler import set_ev_cls
    from ryu.ofproto import ofproto_v1_0
    
    class L2Switch(app_manager.RyuApp):
        OFP_VERSIONS = [ofproto_v1_0.OFP_VERSION]
    
        def __init__(self, *args, **kwargs):
            super(L2Switch, self).__init__(*args, **kwargs)
    
        @set_ev_cls(ofp_event.EventOFPPacketIn, MAIN_DISPATCHER)
        def packet_in_handler(self, ev):
            msg = ev.msg
            dp = msg.datapath
            ofp = dp.ofproto
            ofp_parser = dp.ofproto_parser
    
            actions = [ofp_parser.OFPActionOutput(ofp.OFPP_FLOOD)]
            out = ofp_parser.OFPPacketOut(
                datapath=dp, buffer_id=msg.buffer_id, in_port=msg.in_port,
                actions=actions)
            dp.send_msg(out)

Một phương thức mới 'packet_in_handle' được thêm vào lớp L2Switch. Nó được gọi khi Ryu nhận một thông điệp OpenFlow packet_in. Trick 'set_ev_cls' decorator. Decorator này bảo Ryu khi hàm decorated được gọi

Tham số đầu tiên của decorator cho biết một sự kiện làm cho hàm được gọi. Để dễ hiểu hơn, mỗi khi Ryu nhận được một thông điệp packet_in, chức năng này sẽ được gọi

Tham số thứ hai cho biết trạng thái của switch. Có lẽ, bạn muốn mặc kệ thông điệp packet_in trước cuộc đàm phán giữa Ryu và switch hoàn thành. Việc dùng 'MAIN_DISPATCHER' như tham số thứ hau có nghĩa là hàm này được gọi chỉ sau khi cuộc đàm phán hoàn tất.

Tiếp theo cùng nhìn về nữa đầu của hàm 'packet_in_handler'.

 - ev.msg là một đối tượng đại diện cho một cấu trúc dữ liệu packet_in
 - msg.dp là một đối tượng đại diện cho một datapath (switch)
 - dp.ofproto và dp.ofproto_parser là những đối tượng đại diện cho giao thức OpenFlow mà Ryu và switch đã đàm phán

Nữa sau:

 - Lớp OFPActionOutput được dùng với thông điệp packet_out để chỉ định
   một cổng của switch bạn muốn để gửi packet out of. Ứng dụng này cần 1   switch để gửi ra tất cả các cổng nên hằng số OFPP_FLOOD được dùng
 - Lớp OFPPacketOut được dùng để xây dựng thông điệp packet_out
 - Nếu bạn gọi phương thức send_msg của lớp Datapath với một lớp đối tượng thông điệp OpenFlow, Ryu xây dựng và gởi dữ liệu định dạng on-wire
đến switch.

Đến đây, bạn đã hoàn tất cài đặt chương trình Ryu đầu tiên. Bạn đã sẵn sàng chạy chương trình Ryu này một cách hữu ích.


