---


---

<h1 id="packet-library-api-reference">Packet library API Reference</h1>
<h2 id="packet-class">Packet class</h2>
<pre><code>class ryu.lib.packet.packet.Packet(data=None, protocols=None, parse_cls=&lt;class'ryu.lib.packet.ethernet.ethernet'&gt;)
</code></pre>
<p>Lớp mã hóa/giải mã gói tin<br>
Một thể hiện được dùng để hoặc mã hóa hoặc giải mã một gói tin đơn.<br>
<em>data</em> là một mảng byte để mô tải một datagram thô để giải mã. Khi giải mã, một đối tượng Packet có thể lặp lại. Giá trị được lặp là các header giao thức (ethernet,ipv4…) và payload. Header giao thức là các thể hiện của lớp con của packet_base.PacketBase. Payload là một mảng byte. Chúng được lặp lại thứ tự on-wire</p>

