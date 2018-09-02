---


---

<p><strong>THÀNH PHẦN CỦA RYU</strong></p>
<p><strong>Có thể thực thi</strong></p>
<p><strong>bin/ryu-manager</strong></p>
<p>Công cụ thực thi chính</p>
<p><strong>Thành phần cơ sở</strong></p>
<p><strong>ryu.base.app_manager</strong></p>
<p>Quản trị trung tâm của ứng dụng Ryu</p>
<ul>
<li>Tải ứng dụng Ryu</li>
<li>Cung cấp <em>phiên</em> cho ứng dụng Ryu</li>
<li>Vạch đường những thông điệp giữa các ứng dụng Ryu</li>
</ul>
<p><strong>Bộ điều khiển OpenFlow</strong></p>
<p><strong>ryu.controller.controller</strong></p>
<p>Thành phần chính của bộ điều khiển OpenFlow</p>
<ul>
<li>Xử lí những kết nối từ switches</li>
<li>Sinh ra và vạch đường những sự kiện đến những thực thể ứng dụng Ryu chính xác</li>
</ul>
<p><strong>ryu.controller.dpset</strong></p>
<p>Quản trị switches</p>
<p>Lặp kế hoạch để được thay thế bởi ryu/toplogy</p>
<p><strong>ryu.controller.ofp_event</strong></p>
<p>Định nghĩa sự kiện OpenFlow</p>
<p><strong>ryu.controller.ofp_handler</strong></p>
<p>Xử lí OpenFlow cơ bản bao gồm đàm phán</p>
<p><strong>OpenFlow wire protocol encoder and decoder</strong></p>
<p><strong>ryu.ofproto.ofproto_v1_0</strong></p>
<p>OpenFlow 1.0 definitions</p>
<p><strong>ryu.ofproto.ofproto_v1_0_parser</strong></p>
<p>Decoder/Encoder implementations of OpenFlow 1.0.</p>
<p><strong>ryu.ofproto.ofproto_v1_2</strong></p>
<p>OpenFlow 1.2 definitions.</p>
<p><strong>ryu.ofproto.ofproto_v1_2_parser</strong></p>
<p>Decoder/Encoder implementations of OpenFlow 1.2.</p>
<p><strong>ryu.ofproto.ofproto_v1_3</strong></p>
<p>OpenFlow 1.3 definitions.</p>
<p><strong>ryu.ofproto.ofproto_v1_3_parser</strong></p>
<p>This module implements OpenFlow 1.3.x.</p>
<p>Module này cũng triển khai vài tiện ích mở roojgn được trình bày trong “OpenFlow Extendsions for 1.3.X Pack” :</p>
<ul>
<li>
<pre><code> EXT-230 Bundle Extension (without bundle properties)
</code></pre>
</li>
<li>
<pre><code>    EXT-236 Bad flow entry priority error Extension
</code></pre>
</li>
<li>
<pre><code>    EXT-237 Set async config error Extension
</code></pre>
</li>
<li>
<pre><code>    EXT-256 PBB UCA header field Extension
</code></pre>
</li>
<li>
<pre><code>    EXT-260 Duplicate instruction error Extension
</code></pre>
</li>
<li>
<pre><code>    EXT-264 Multipart timeout errors Extension
</code></pre>
</li>
</ul>
<p>The following extensions are partially implemented</p>
<ul>
<li>EXT-187 Flow entry notifications Extension (ONFMP_FLOW_MONITOR only)</li>
</ul>
<p>The following extensions are not implemented yet.</p>
<ul>
<li>
<p>EXT-191 Role Status Extension</p>
</li>
<li>
<p>EXT-192-e Flow entry eviction Extension</p>
</li>
<li>
<p>EXT-192-v Vacancy events Extension</p>
</li>
<li>
<p>EXT-232 Table synchronisation Extension (Error codes only)</p>
</li>
</ul>
<p><strong>ryu.ofproto.ofproto_v1_4</strong></p>
<p>OpenFlow 1.4 definitions.</p>
<p><strong>ryu.ofproto.ofproto_v1_4_parser</strong></p>
<p>Decoder/Encoder implementations of OpenFlow 1.4.</p>
<p><strong>ryu.ofproto.ofproto_v1_5</strong></p>
<p>OpenFlow 1.5 definitions.</p>
<p><strong>ryu.ofproto.ofproto_v1_5_parser</strong></p>
<p>Decoder/Encoder implementations of OpenFlow 1.5.</p>
<p><strong>Ứng dụng Ryu</strong></p>
<p><strong>ryu.app.cbench</strong></p>
<p>A dumb OpenFlow 1.0 responder for benchmarking the controller framework. Intended to be used with oflops cbench.</p>
<p><strong>ryu.app.simple_switch</strong></p>
<p>Một OpenFlow 1.0 L2 switch</p>
<p><strong>ryu.topology</strong></p>
<p>Khám phá switch và link. Có thể thay thế ryu/controller/dpset.</p>
<p><strong>Thư viện</strong></p>
<p><strong>ryu.lib.packet</strong></p>
<p>Gói thư viện Ryu. Mã hóa và giải mã những triển khai của những giao thức thông dung như TCP/IP</p>
<p><strong>ryu.lib.ovs</strong></p>
<p>Thư viện tương tác ovsdb</p>
<p><strong>ryu.lib.of_config</strong></p>
<p>Triển khai OF-Config</p>
<p><strong>ryu.lib.netconf</strong></p>
<p>Định nghĩa NETCONF dùng bở ryu/lib/of_config</p>
<p><strong>ryu.lib.xflow</strong></p>
<p>Một triển khai của sFlow và NetFlow</p>
<p><strong>Thư viện bên thứ 3</strong></p>
<p>**ryu.contrib.ovs</p>
<p>Ràng buộc Open vSwitch python. Dùng bởi ryu.lib.ovs</p>
<p><strong>ryu.contrib.oslo.config</strong><br>
Thư viện cấu hình Oslo. dùng cho ryu-manager command-line cài đặt và cấu hình files.</p>
<p><strong>ryu.contrib.ncclient</strong></p>
<p>Thư viện python cho NETCONF client. Dùng bởi ryu.lib.of_config</p>

