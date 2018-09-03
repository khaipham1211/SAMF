---


---

<h1 id="ryu-application-api">Ryu application API</h1>
<h2 id="ryu-application-programming-model">Ryu application programming model</h2>
<h3 id="threads-events-and-event-queues">Threads, events, and event queues</h3>
<p>Ứng dụng Ryu là những thực thể đơn luồng triển khai nhiều chức năng trong Ryu. Những sự kiện và những thông điệp giữa chúng</p>
<p>Ứng dụng Ryu gửi những sự kiện đồng bộ lẫn nhau. Bên cạnh đó những nguồn sự kiện nội bộ Ryu cũng có thể không phải là ứng dụng Ryu. Một ví dụ về nguồn sự kiện như là bộ điều khiển OpenFlow. Trong khi một sự kiện có thể chứa các đối tượng python tùy ý thì nó không được khuyến khích để chứa các đối tượng phức tạp (như là các đối tượng không thể pick) giữa các ứng dung Ryu</p>
<p>Mỗi ứng dụng Ryu có một hàng đợi cho các sự kiện. Hàng đợi là FIFO và  giữ nguyên thứ tự các sự kiện Mỗi ứng dụng Ryu có một luồng cho xử lý sự kiện. Luồng hút hàng đợi nhận bằng cách bỏ đợi một sự kiện và gọi xử lý sự kiện tương ứng cho kiểu sự kiện. Vì bộ xử lí sự kiện được gọi trong phiên của luồng xử lý sự kiện nên phải thận trọng khi khóa. Trong khi một bộ xử lí sự kiện bị khóa, không có sự kiện nào được xử lý nữa cho ứng dụng Ryu.</p>
<p>Một vài sự kiện được dùng để triển khai đồng bộ ứng dụng bên trong gọi giữa những ứng dụng Ryu. Khi những request sử dụng cùng một bộ máy như các sự kiện bình thường, các câu trả lời của chúng được đặt trên hàng đợi dành riêng cho giao dịch để tránh khóa chết.</p>
<p>Khi những luồn và hàng được đang được triển khai với eventlet/greenlet  thì việc sử dụng trực tiếp chúng trong một ứng dụng Ryu được khuyến khích một cách mạnh mẽ</p>
<h3 id="contexts">Contexts</h3>
<p>Context là những đối tượng python bình thường chia sẽ giữa các ứng dụng Rya. Việc sử dụng context không được khuyến khích cho code mới.</p>
<h2 id="create-a-ryu-application">Create a Ryu application</h2>
<p>Một ứng dụng Ryu là một module python định nghĩa lớp con của ryu.base.app_manager.RyuApp. Nếu 2 hoặc nhiều hơn những lớp được định nghĩa trong một module, thì cái đầu tiên sẽ được chọn bởi app_manager. Ryu application là đơn độc: chỉ một thể hiện của ứng dụng Ryu được hỗ trợ</p>
<h2 id="observe-events">Observe events</h2>
<p>Một ứng dụng Ryu có thể đăng ký chính nó để lắng nghe những sự kiện cụ thể dùng ryu.controller.handler.set_ev_cls decorator.</p>
<h2 id="generate-events">Generate events</h2>
<p>Một ứng dụng Ryu có thể sinh ra những sự kiện bằng việc gọi ryu.base.app_manager hợp lsy. Phương thức RyuApp giống như send_event hoặc send_event_to_observers.</p>
<h2 id="event-classes">Event classes</h2>
<p>Một lớp sự kiện mô tả một sự kiện Ryu được sinh ra trong hệ thống. Theo quy ước, lớp sự kiện được đặt tên có tiền tố “Event”. Những sự kiện được sinh ra hoặc bổ phần lõi của Ryu hoặc Ứng dụng Ryu.  Một ứng dụng Ryu có thể đăng ký interest của nó cho  một kiểu cụ thể của sự kiện bằng việc cung cấp một phương thức xử lý sử dụng ryu.controller.handler.set_ev_cls decorator</p>
<h3 id="openflow-event-classes">OpenFlow event classes</h3>
<p>ryu.controller.ofp_event module xuất những lớp sự kiện mô tả việc tiếp nhận của thông điệp OpenFlow từ những switch đã kết nối. Theo quy ước, chúng được đặt tên như là ryu.controller.ofp_event.EventOFPxxxx với xxxx là tên của thông điệp OpenFlow tương ứng. Ví dụ, EventOFPPacketIn cho thông điệp packet-in. Bộ phận điều khiển OpenFlow của Ryu tự động giải mã thông điệp OpenFlow được nhận từ switch và gửi những sự kiện đến ứng dụng Ryu sử dụng ryu.controller.handler.set_ev_cls. Những lớp sự kiện OpenFlow là những lớp con của những lớp đó.</p>
<p><code>class ryu.controller.ofp_event.EventOFPMsgBase(_msg_)</code></p>
<p>The base class of OpenFlow event class.<br>
Lớp sự kiện OpenFlow có ít nhất các thuộc tính sau:</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>msg</td>
<td>đối tượng mô tả thông điệp OpenFlow</td>
</tr>
<tr>
<td>msg.datapath</td>
<td>một ryu.controller.Datapath instance mô tả một OpenFlow switch từ nơi chúng ta nhận được thông điệp OpenFlow</td>
</tr>
<tr>
<td>timestamp</td>
<td>Tem thời gian khi thể hiện Datapath sinh ra trong sự kiện này</td>
</tr>
</tbody>
</table><p>Đối tượng msg có nhiều hơn những thành viên thêm bổ sung giá trị của chúng được giải ra từ thông điệp OpenFlow gốc</p>
<p>Xem <a href="https://ryu.readthedocs.io/en/latest/ofproto_ref.html#ofproto-ref">OpenFlow protocol API Reference</a> để có thêm thông tin về thông điệp OpenFlow</p>
<h2 id="ryu.base.app_manager.ryuapp">ryu.base.app_manager.RyuApp</h2>
<p>Xem <a href="https://ryu.readthedocs.io/en/latest/api_ref.html#api-ref">Ryu API Reference</a>.</p>
<h2 id="ryu.controller.handler.set_ev_cls">ryu.controller.handler.set_ev_cls</h2>
<p><code>ryu.controller.handler.set_ev_cls(ev_cls,dispatchers=None)</code></p>
<p>Một decorator cho ứng dụng Ryu để khai báo bộ xử lý sự kiện</p>
<p>Phương thức Decorated sẽ trở thành một bộ xử lí sự kiện. ev_cls là một lớp sự kiện có các thể hiện RyuApp muốn nhận. Tham số điều phiếu xác định một trong các giai đoạn đàm phán sau đây (hoặc danh sách của chúng) để tạo sự kiện cho bộ xử lý. Lưu ý rằng, trong trường hợp một sự kiện thay đổi giai đoạn, giai đoạn trước giai đoạn thay đổi được dùng để kiểm tra interest.</p>

<table>
<thead>
<tr>
<th>Negotiation phase</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>ryu.controller.handler.HANDSHAKE_DISPATCHER</td>
<td>Gửi và đợi thông điệp Hello</td>
</tr>
<tr>
<td>ryu.controller.handler.CONFIG_DISPATCHER</td>
<td>Phiên bản đàm phán và gửi thông điệp feature-request</td>
</tr>
<tr>
<td>ryu.controller.handler.MAIN_DISPATCHER</td>
<td>Thông điệp Switch-feature nhận được và gửi thông điệp set-config</td>
</tr>
<tr>
<td>ryu.controller.handler.DEAD_DISPATCHER</td>
<td>Ngắt kết nối từ đối tác. Hoặc ngắt kết nối vì lỗi không thể khắc phục</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.controller.datapath">ryu.controller.controller.Datapath</h2>
<p><code>class ryu.controller.controller.Datapath(socket,address)</code></p>
<p>Class để mô tả một switch OpenFlow kết nối đến controller.<br>
Một thể hiện có những thuộc tính sau</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>id</td>
<td>64-bit OpenFlow Datapath ID. Only available for ryu.controller.handler.MAIN_DISPATCHER phase.</td>
</tr>
<tr>
<td>ofproto</td>
<td>A module which exports OpenFlow definitions, mainly constants appeared in the specification, for the negotiated OpenFlow version. For example, ryu.ofproto.ofproto_v1_0 for OpenFlow 1.0.</td>
</tr>
<tr>
<td>ofproto_parser</td>
<td>A module which exports OpenFlow wire message encoder and decoder for the negotiated OpenFlow version. For example, ryu.ofproto.ofproto_v1_0_parser for OpenFlow 1.0.</td>
</tr>
<tr>
<td>ofproto_parser.OFPxxxx(datapath,…)</td>
<td>A callable to prepare an OpenFlow message for the given switch. It can be sent with Datapath.send_msg later. xxxx is a name of the message. For example OFPFlowMod for flow-mod message. Arguemnts depend on the message.</td>
</tr>
<tr>
<td>set_xid(self, msg)</td>
<td>Generate an OpenFlow XID and put it in msg.xid.</td>
</tr>
<tr>
<td>send_msg(self, msg)</td>
<td>Queue an OpenFlow message to send to the corresponding switch. If msg.xid is None, set_xid is automatically called on the message before queueing.</td>
</tr>
<tr>
<td>send_packet_out</td>
<td>deprecated</td>
</tr>
<tr>
<td>send_flow_mod</td>
<td>deprecated</td>
</tr>
<tr>
<td>send_flow_del</td>
<td>deprecated</td>
</tr>
<tr>
<td>send_delete_all_flows</td>
<td>deprecated</td>
</tr>
<tr>
<td>send_barrier</td>
<td>Queue an OpenFlow barrier message to send to the switch.</td>
</tr>
<tr>
<td>send_nxt_set_flow_format</td>
<td>deprecated</td>
</tr>
<tr>
<td>is_reserved_port</td>
<td>deprecated</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.event.eventbase">ryu.controller.event.EventBase</h2>
<p><code>class ryu.controller.event.EventBase</code><br>
Nền của lớp sự kiện<br>
Một ứng dụng Ryu có thể định nghĩa kiểu sự kiện của nó bằng việc tạo lớp con</p>
<h2 id="ryu.controller.event.eventrequestbase">ryu.controller.event.EventRequestBase</h2>
<p><code>class ryu.controller.event.EventRequestBase</code><br>
Lớp nền cho request đồng bộ cho RyuApp.send_request</p>
<h2 id="ryu.controller.event.eventreplybase">ryu.controller.event.EventReplyBase</h2>
<p><code>class ryu.controller.event.EventReplyBase(dst)</code><br>
Lớp nền cho request đồng bộ phản hồi cho RyuApp.send_reply</p>
<h2 id="ryu.controller.ofp_event.eventofpstatchange">ryu.controller.ofp_event.EventOFPStatChange</h2>
<p><code>class ryu.controller.ofp_event.EventOFPStateChange(dp)</code><br>
Một lớp sự kiện để giai đoạn đàm phán thay đổi thông báo<br>
Một thể hiện của lớp này được gửi đến observer sau khi thay đổi giai đoạn đàm phán.<br>
Một thể hiện có ít nhất những thể hiện sau.</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>datapath</td>
<td>Thể hiện ryu.controller.controller.Datapath của switch</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.ofp_event.eventofpportstatechange">ryu.controller.ofp_event.EventOFPPortStateChange</h2>
<p><code>class ryu.controller.ofp_event.EventOFPPortStateChange(dp,reason,port_no)</code><br>
Một lớp sự kiện để thông báo trạng thái cổng thay đổi của thể hiện Datapath</p>
<p>Sự kiện này thể hiện giống như EventOFPPortStatus nhưng Ryu sẽ gửi sự kiện sau khi cập nhật từ điển cổng của thể hiện Datapath. Một thể hiện có ít nhất những thuộc tính sau.</p>

<table>
<thead>
<tr>
<th>Atribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>datapath</td>
<td>Thể hiện ryu.controller.controller.Datapath của switch</td>
</tr>
<tr>
<td>reason</td>
<td>một thuộc tính của OFPPR_*</td>
</tr>
<tr>
<td>port_no</td>
<td>Cổng có trạng thái bị thay đổi</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.dpset.eventdp">ryu.controller.dpset.EventDP</h2>
<p><code>class ryu.controller.dpset.EventDp(dp,enter_leave)</code><br>
Một lớp sự kiện để thông báo kết nối/ngắt của switch<br>
Cho OpenFlow switch có thể nhận được cùng thông báo bằng thăm dò ryu.controller.ofp_event.EventOFPStateChange. Một thể hiện của ít nhất các thuộc tính sau.</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>dp</td>
<td>Thể hiện ryu.controller.controller.Datapath của switch</td>
</tr>
<tr>
<td>enter</td>
<td>Đúng khi switch kết nối đến controller. False khi bị ngắt</td>
</tr>
<tr>
<td>ports</td>
<td>danh sách các thể hiện cổng</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.dpset.eventportadd">ryu.controller.dpset.EventPortAdd</h2>
<p><code>class ryu.controller.dpset.EventPortAdd(dp,port)</code><br>
Lớp sự kiện cho thông báo trạng thái cổng switch “ADD”<br>
Sự kiện này được sinh ra khi một cổng được thêm vào switch. Đối với OpenFlow switch, mỗi cái có thể nhận được cùng thông báo bởi observing ryu.controller.of_event.EventOFPPortStatus. Một thể hiện có ít nhất những thuộc tính sau</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>dp</td>
<td>một thể hiện ryu.controller.controller.Datapath của switch</td>
</tr>
<tr>
<td>port</td>
<td>số hiệu cổng</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.dpset.eventportdelete">ryu.controller.dpset.EventPortDelete</h2>
<p><code>class ryu.controller.dpset.EventportDelete(dp,port)</code><br>
Một lớp sự kiện dành cho thông báo trạng thái “DELETE” port<br>
Sự kiện này được sinh ra khi một cổng bị loại bỏ khỏi switch. Đối với OpenFlow switch, mỗi cái có thể nhận được ùng một thông báo bởi observing ryu.controller.ofp_event.EventOFPPortStatus. Một thể hiện có ít nhất những thuộc tính sau.</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>dp</td>
<td>Thể hiện ryu.controller.controller.Datapath của switch</td>
</tr>
<tr>
<td>port</td>
<td>Số hiệu cổng</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.dpset.eventportmodify">ryu.controller.dpset.EventPortModify</h2>
<p><code>class ryu.controller.dpset.EventPortModify(dp,new_port)</code></p>
<p>Một lớp sự kiện dành cho thông báo trạng thái “MODIFY” port<br>
Sự kiện này được sinh ra khi một cổng bị loại bỏ khỏi switch. Đối với OpenFlow switch, mỗi cái có thể nhận được ùng một thông báo bởi observing ryu.controller.ofp_event.EventOFPPortStatus. Một thể hiện có ít nhất những thuộc tính sau.</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>dp</td>
<td>Thể hiện ryu.controller.controller.Datapath của switch</td>
</tr>
<tr>
<td>port</td>
<td>Số hiệu cổng</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.network.eventnetworkport">ryu.controller.network.EventNetworkPort</h2>
<p>` class ryu.controller.network.EventNetworkPort(network_id, dpid, port_no, add_del)<br>
Một lớp sự kiện để các thông báo của cổng đến và xuất phát</p>
<p>Sự kiện này được sinh ra khi một cổng được giới thiệu đến hoặc loại bỏ bởi mạng bởi REST API. Một thể hiện có ít nhất những thuộc tính sau</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>network_id</td>
<td>Network ID</td>
</tr>
<tr>
<td>dpid</td>
<td>OpenFlow Datapath ID của switch đến cổng thuộc về nó</td>
</tr>
<tr>
<td>port_no</td>
<td>Số hiệu cổng theo  OpenFlow của cổng</td>
</tr>
<tr>
<td>add_del</td>
<td>True = add. False = remove</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.network.eventnetworkdel">ryu.controller.network.EventNetworkDel</h2>
<p>` class ryu.controller.network.EventNetworkDel(network_id)<br>
Lớp sự kiện để xóa network</p>
<p>Sự kiện này được sinh ra khi một mạng bị xóa với REST API. Một thể hiện có ít nhất những thuộc tính sau</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>network_id</td>
<td>Network ID</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.network.eventmacaddress">ryu.controller.network.EventMacAddress</h2>
<p><code>class ryu.controller.network.EventMacAddress(dpid, port_no, network_id, mac_address, add_del)</code></p>
<p>Lớp sự kiện để đăng ký địa chỉ vật lý điểm cuối</p>
<p>Sự kiện này được sinh ra khi một địa chỉ vật lý điểm cuối được cập nhật bởi REST API. Một thể hiện có ít nhất những thuộc tính sau</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>network_id</td>
<td>Network ID</td>
</tr>
<tr>
<td>dpid</td>
<td>OpenFlow datapath ID của switch đến cổng thuộc về nó</td>
</tr>
<tr>
<td>port_no</td>
<td>Số hiệu cổng theo OpenFlow của cổng</td>
</tr>
<tr>
<td>mac_address</td>
<td>Địa chỉ vật lý cũ của cổng nếu add_del = False. Ngược lại là địa chỉ MAC mới</td>
</tr>
<tr>
<td>add_del</td>
<td>False nếu kết quả là cổng có thể loại bỏ. Ngược lại là True</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.tunnels.eventtunnelkeyadd">ryu.controller.tunnels.EventTunnelKeyAdd</h2>
<p><code>class ryu.controller.tunnels.EventTunnelKeyAdd(network_id,tunnel_key)</code></p>
<p>Một lớp sự kiện để đăng ký tunnel key.</p>
<p>Sự kiện này được sinh ra khi một tunnel key được đăng ký hoặc cập nhật bở REST API. Một thể hiện có ít nhất những thuộc tính sau</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>network_id</td>
<td>Network ID</td>
</tr>
<tr>
<td>tunnel_key</td>
<td>Tunnel key</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.tunnels.eventtunnelkeydel">ryu.controller.tunnels.EventTunnelKeyDel</h2>
<p><code>class ryu.controller.tunnels.EventTunnelKeyDel(network_id, tunnel_key)</code></p>
<p>Một lớp sự kiện cho đăng ký tunnel key</p>
<p>Sự kiện này được sinh ra khi một tunnel key bị loại bỏ bởi REST API. Thể hiện có ít nhất những thuộc tính sau</p>

<table>
<thead>
<tr>
<th>Attribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>Network_id</td>
<td>Network ID</td>
</tr>
<tr>
<td>tunnel_key</td>
<td>Tunnel key</td>
</tr>
</tbody>
</table><h2 id="ryu.controller.tunnels.eventtunnelport">ryu.controller.tunnels.EventTunnelPort</h2>
<p>` class ryu.controller.tunnels.EventTunnelPorrt(dpid, port_no, remote_dpid, add_del)<br>
Lớp sự kiện để đăng ký tunnel port<br>
Sự kiện được sinh ra khi một tunnel port được thêm vào hoặc loại bổ bởi REST API.<br>
Một thể hiện có ít nhất những thuộc tính sau</p>

<table>
<thead>
<tr>
<th>Atttribute</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>dpid</td>
<td>OpenFlow datapath ID</td>
</tr>
<tr>
<td>port_no</td>
<td>Cổng OpenFlow</td>
</tr>
<tr>
<td>remote_dpid</td>
<td>Cổng OpenFlow của tunnel peer</td>
</tr>
<tr>
<td>add_del</td>
<td>True nếu add tunnel. False nếu remove</td>
</tr>
</tbody>
</table>
