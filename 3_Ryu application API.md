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
<p><code>_class_ ryu.controller.ofp_event.``EventOFPMsgBase(_msg_)</code></p>
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

