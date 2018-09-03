---


---

<h1 id="packet-library">Packet library</h1>
<h2 id="introduction">Introduction<a href="https://ryu.readthedocs.io/en/latest/library_packet.html#introduction" title="Permalink to this headline"></a></h2>
<p>Ryu packet library giúp phân tích và xây dựng nhiều giao thức packet. dpkt là thư viện phổ biến cho cùng mục đích. Tuy nhiên nó không được thiết kế để xử lý những giao thức được xen kẽ; vlan, mpls, gre, v…v. Nên chúng tôi triển khai packet library.</p>
<h2 id="network-addresses">Network Addresses</h2>
<p>Trừ khi được chỉ định khác, địa chỉ MAC/IPv4/IPv6 được chỉ định bằng chuỗi người có thể đọc được được cho thư viện. VD: ‘08:60:6e:7f:74:e7’, ‘192.0.2.1’, ‘fe80::a60:6eff:fe7f:74e7’</p>
<h2 id="phân-tích-packet">Phân tích Packet</h2>
<p>Đầu tiên, hãy xem bằng cách nào chúng tôi có thể dùng thư viện để phân tích gói tin nhận được trong bộ xử lí cho thông điệp OFPPacketIn</p>
<p>from ryu.lib.packet import packet</p>
<pre><code>@handler.set_ev_cls(ofp_event.EventOFPPacketIn, handler.MAIN_DISPATCHER)
def packet_in_handler(self, ev):
    pkt = packet.Packet(array.array('B', ev.msg.data))
    for p in pkt.protocols:
        print p
</code></pre>
<p>Bạn có thể tạo một thể hiện lớp Packet với dữ liệu thô nhận được. Sau đó thư viện packet phân tích dữ liệu và tạo ra thể hiện lớp giao thức bao gồm dữ liệu. Lớp packet ‘protocols’ có những thể hiện lớp protocol.<br>
Nếu một gói tin TCP được nhận, một vài thứ sau được in ra:</p>
<pre><code>&lt;ryu.lib.packet.ethernet.ethernet object at 0x107a5d790&gt;
&lt;ryu.lib.packet.vlan.vlan object at 0x107a5d7d0&gt;
&lt;ryu.lib.packet.ipv4.ipv4 object at 0x107a5d810&gt;
&lt;ryu.lib.packet.tcp.tcp object at 0x107a5d850&gt;
</code></pre>
<p>Nếu vlan không được dùng, bạn sẽ thấy vài thứ như sau:</p>
<pre><code> &lt;ryu.lib.packet.ethernet.ethernet object at 0x107a5d790&gt;
&lt;ryu.lib.packet.ipv4.ipv4 object at 0x107a5d810&gt;
&lt;ryu.lib.packet.tcp.tcp object at 0x107a5d850&gt; 
</code></pre>
<p>Bạn có thể truy cập đến thể hiện lớp giao thức chỉ định bằng việc sử dụng vòng lặp lớp packet. Hãy thử kiểm tra VLAN id nếu VLAN được dùng:<br>
from ryu.lib.packet import packet</p>
<pre><code>@handler.set_ev_cls(ofp_event.EventOFPPacketIn, handler.MAIN_DISPATCHER)
def packet_in_handler(self, ev):
    pkt = packet.Packet(array.array('B', ev.msg.data))
    for p in pkt:
        print p.protocol_name, p
        if p.protocol_name == 'vlan':
            print 'vid = ', p.vid
</code></pre>
<p>Bạn sẽ thấy vài thứ như sau:</p>
<pre><code>ethernet &lt;ryu.lib.packet.ethernet.ethernet object at 0x107a5d790&gt;
vlan &lt;ryu.lib.packet.vlan.vlan object at 0x107a5d7d0&gt;
vid = 10
ipv4 &lt;ryu.lib.packet.ipv4.ipv4 object at 0x107a5d810&gt;
tcp &lt;ryu.lib.packet.tcp.tcp object at 0x107a5d850&gt;
</code></pre>
<h2 id="xây-dựng-packet">Xây dựng Packet</h2>
<p>Bạn cần phải tạp ra thể hiện lớp giao thức mà bạn muốn để gửi và thêm chúng vào một thể hiện lớp packet thông qua phương thức add_protocol và sau đó gọi các phương thức tuần tự. Bạn có dữ liệu thô để gửi. Ví dụ sau đang xây dựng một gói tin arp.</p>
<pre><code>from ryu.ofproto import ether
from ryu.lib.packet import ethernet, arp, packet

e = ethernet.ethernet(dst='ff:ff:ff:ff:ff:ff',
                      src='08:60:6e:7f:74:e7',
                      ethertype=ether.ETH_TYPE_ARP)
a = arp.arp(hwtype=1, proto=0x0800, hlen=6, plen=4, opcode=2,
            src_mac='08:60:6e:7f:74:e7', src_ip='192.0.2.1',
            dst_mac='00:00:00:00:00:00', dst_ip='192.0.2.2')
p = packet.Packet()
p.add_protocol(e)
p.add_protocol(a)
p.serialize()
print repr(p.data)  # the on-wire packet
</code></pre>

