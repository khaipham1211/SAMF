---


---

<h1 id="howto-write-modules"><a href="https://github.com/openwrt/luci/wiki/ModulesHowTo#howto-write-modules"></a>HowTo: Write Modules</h1>
<p><strong>Lưu ý:</strong> Nếu bạn dự định tích hợp module vào LuCI, bạn nên đọc <a href="https://github.com/openwrt/luci/wiki/Modules">https://github.com/openwrt/luci/wiki/Modules</a> trước.<br>
Bài hướng dẫn này mô tả cách viết module của bạn dành cho LuCI WebUI. Cho bài hướng dẫn này chúng tôi tham khảo thư mục cài đặt của bạn như <strong>lucidir</strong> (/usr/lib/lua/luci nếu bạn đang làm việc với version được cài đặt) và giả sử LuCi có thể động tới qua webserver thông <strong>/cgi-bin/luci</strong></p>
<h2 id="show-me-the-way-the-dispatching-process"><a href="https://github.com/openwrt/luci/wiki/ModulesHowTo#show-me-the-way-the-dispatching-process"></a>Show me the way (The dispatching process)</h2>
<p>Để viết một module bạn cần phải hiểu căn bản về quy trình xử lý trong LuCi. LuCi sử dụng cây quy trình sẽ được xây dựng bởi thực thi index-Function của mỗi available controller. CGI-environment variable <strong>PATH_INFO</strong> sẽ được dùng như đường dẫn trong cây quy trình . e.g /cgi-bin/luci/foo/bar/baz sẽ được xử lý đến foo.bar.baz<br>
Để đăng ký một function trong cây quy trình, bạn có thể dùng entry-function của <em>luci.dispatcher</em>. entry có 4 tham số (2 tùy chọn):</p>
<pre><code>entry(path, target, title=nil, order=nil)
</code></pre>
<ul>
<li><strong>path</strong> là một bản mô tả vị trí trong cây quy trình: ví dụ một đường dẫn của {“foo”,“bar”,“baz”} có thể chèn node của bạn trong foo.bar.baz.</li>
<li><strong>target</strong> mô tả action sẽ được lấy khi một user request một node. Thường định nghĩa sẵn một trong 3 cái quan trọng nhất (call, template, cbi) mô tả cuối trang này</li>
<li><strong>title</strong> định nghĩa title sẽ được hiển thị cho người dùng trong menu</li>
<li><strong>order</strong> là một số với các node trong cùng cấp độ sẽ được sắp xếp trong menu<br>
Bạn có thể gán nhiều thuộc tính hơn bằng việc thao tác bản node được trả về bằng entry-function. Vài thuộc tính ví dụ:</li>
<li><strong>i18n</strong> định nghĩa dịch file có nên được tự động tải khi trang get request</li>
<li><strong>dependent</strong> bảo vệ plugins được gọi ra của context của chúng nếu một node cha bị lỡ</li>
<li><strong>leaf</strong> ngưng hẳn parsing request ở node này và không tiếp tục cây quy trình</li>
<li><strong>sysauth</strong> yêu cầu người dùng xác thực với tài khoản người dùng được hệ thống cấp</li>
</ul>
<h2 id="its-all-about-names-naming-and-the-module-file"><a href="https://github.com/openwrt/luci/wiki/ModulesHowTo#its-all-about-names-naming-and-the-module-file"></a>It’s all about names (Naming and the module file)</h2>
<p>Bây giờ bạn đã biết về quy trình, chúng ta có thể bắt đầu viết module. Nhưng trước hết bạn phải chọn phân loại và  tên digital child mới.<br>
Giả sử bạn muốn tạo một ứng dụng mới “myapp” với một module “mymodule”.<br>
Bạn phải tạo một thư mục con <em><strong><code>lucidir/controller/myapp</code></strong></em> với một file <strong><code>mymodule.lua</code></strong> với nội dung sau:</p>
<pre><code>module("luci.controller.myapp.mymodule", package.seeall)

function index()

end
</code></pre>
<p>Dòng đầu tiên yêu cầu cho Lua để trực tiếp xác thực module và tạo mục tiêu của nó. index-Function sẽ được dùng để đăng ký actions trong cây quy trình</p>
<h2 id="teaching-your-new-child-actions"><a href="https://github.com/openwrt/luci/wiki/ModulesHowTo#teaching-your-new-child-actions"></a>Teaching your new child (Actions)</h2>
<p>Nó đây, có tên và không có actions.<br>
Giả sử bạn muốn dùng lại module myapp.mymodule đã làm ở bước trên</p>
<h3 id="actions"><a href="https://github.com/openwrt/luci/wiki/ModulesHowTo#actions"></a>Actions</h3>
<p>Mở lại <em><strong>lucidir/controller/myapp/mymodule.lua</strong></em> và chỉ thểm một function vào nó nên nội dung của nó như ví dụ sau:</p>
<pre><code>module("luci.controller.myapp.mymodule", package.seeall)

function index()
    entry({"click", "here", "now"}, call("action_tryme"), "Click here", 10).dependent=false
end
 
function action_tryme()
    luci.http.prepare_content("text/plain")
    luci.http.write("Haha, rebooting now...")
    luci.sys.reboot()
end
</code></pre>
<p>Và giờ gõ <strong>/cgi-bin/luci/click/here/now</strong> (<a href="http://192.168.1.1/luci/click/here/now">http://192.168.1.1/luci/click/here/now</a> trên OpenWRT) trên trình duyệt.</p>
<p>Bạn thấy action function đơn giản phải được thêm vào dispatching entry<br>
Như bạn có thể biết: đặc điểm CGI yêu cầu bạn gởi một Content-Type header trước rồi bạn có thể gửi nội dung. Bạn sẽ tìm vài lối tắt (shortcut) cũng như chuyển hướng function trong module <strong>luci.http</strong></p>
<h3 id="views"><a href="https://github.com/openwrt/luci/wiki/ModulesHowTo#views"></a>Views</h3>
<p>Nếu bạn chỉ muốn show cho user text hoặc vài hình ảnh, nó có thể để dùng một HTML-template. Những template này có thể cũng include vài Lua code nhưng hãy cẩn thận khi viết toàn một ứng dụng văn phòng bằng chỉ sử dụng template có thể được gọi là “bẩn” bởi các nhà phát triển khác<br>
Giờ hãy tạo một template nhỏ <em><strong>lucidir</strong></em><strong>/view/myapp-mymodule/helloworld.htm</strong> với nội dung:</p>
<pre><code>&lt;%+header%&gt;
&lt;h1&gt;&lt;%:Hello World%&gt;&lt;/h1&gt; 
&lt;%+footer%&gt;
</code></pre>
<p>và thêm vào dòng sau đến index-Function của module file.</p>
<pre><code>entry({"my", "new", "template"}, template("myapp-mymodule/helloworld"), "Hello world", 20).dependent=false
</code></pre>
<p>Giờ gõ <strong>/cgi-bin/luci/my/new/template</strong> (<strong><a href="http://192.168.1.1/luci/my/new/template">http://192.168.1.1/luci/my/new/template</a></strong>) trên trình duyệt<br>
Bạn có thể nhận thấy những thẻ ưa thích này, đây là các template đánh dấu được dùng bở LuCI template processor. Nó luôn tốt để include header, footer ở đầu và cuối một template như những người tạo ra thiết kế mặc định và menu</p>
<h3 id="cbi-models"><a href="https://github.com/openwrt/luci/wiki/ModulesHowTo#cbi-models"></a>CBI models</h3>
<p>CBI là một trong những tính năng tuyệt với nhất của LuCI. Nó tạo ra một giao diện người dùng dược trên Formular và lưu nội dung của nó vào một file cấu hình UCI/ Bạn chỉ cần mô tả cấu trúc của file cấu hình trong CBI model file và Luci thực hiện phần còn lại. Điều này bao gồm tạo, phân tích cú pháp và xác thực biểu mẫu XHTML và đọc và ghi UCI file<br>
Hãy nghiêm túc cho đoạn này và tạo ra một ví dụ thực tế <em><strong>lucidir</strong></em><strong>/model/cbi/myapp-mymodule/netifaces.lua</strong> với nội dung sau:</p>
<pre><code>m = Map("network", "Network") -- We want to edit the uci config file /etc/config/network

s = m:section(TypedSection, "interface", "Interfaces") -- Especially the "interface"-sections
s.addremove = true -- Allow the user to create and remove the interfaces
function s:filter(value)
   return value ~= "loopback" and value -- Don't touch loopback
end 
s:depends("proto", "static") -- Only show those with "static"
s:depends("proto", "dhcp") -- or "dhcp" as protocol and leave PPPoE and PPTP alone

p = s:option(ListValue, "proto", "Protocol") -- Creates an element list (select box)
p:value("static", "static") -- Key and value pairs
p:value("dhcp", "DHCP")
p.default = "static"

s:option(Value, "ifname", "interface", "the physical interface to be used") -- This will give a simple textbox

s:option(Value, "ipaddr", translate("IP Address")) -- Ja, das ist eine i18n-Funktion ;-)

s:option(Value, "netmask", "Netmask"):depends("proto", "static") -- You may remember this "depends" function from above

mtu = s:option(Value, "mtu", "MTU")
mtu.optional = true -- This one is very optional

dns = s:option(Value, "dns", "DNS-Server")
dns:depends("proto", "static")
dns.optional = true
function dns:validate(value) -- Now, that's nifty, eh?
    return value:match("[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+") -- Returns nil if it doesn't match otherwise returns match
end

gw = s:option(Value, "gateway", "Gateway")
gw:depends("proto", "static")
gw.rmempty = true -- Remove entry if it is empty

return m -- Returns the map
</code></pre>
<p>Và dĩ nhiên đừng quên thêm vài thứ như cái này vào module index-Function</p>
<pre><code>entry({"admin", "network", "interfaces"}, cbi("myapp-mymodule/netifaces"), "Network interfaces", 30).dependent=false
</code></pre>
<p>Có rất nhiều tính năng, xem tại <a href="https://github.com/openwrt/luci/wiki/CBI">https://github.com/openwrt/luci/wiki/CBI</a></p>

