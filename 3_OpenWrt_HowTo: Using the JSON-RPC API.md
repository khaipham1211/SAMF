---


---

<h1 id="howto-using-the-json-rpc-api"><a href="https://github.com/openwrt/luci/wiki/JsonRpcHowTo#howto-using-the-json-rpc-api"></a>HowTo: Using the JSON-RPC API</h1>
<p>LuCI cung cấp một số thư viện của nó cho những ứng dụng bên ngoài qua một JSON-RPC API. Bài hướng dẫn này trình bày cách sử dụng nó và cung cấp thông tin về những function.</p>
<h2 id="basics"><a href="https://github.com/openwrt/luci/wiki/JsonRpcHowTo#basics"></a>Basics</h2>
<p>Để hữu hiệu hóa API bạn cần cài đặt gói sau và khởi động lại uhttpd:</p>
<pre><code>opkg install luci-mod-rpc
/etc/init.d/uhttpd restart
</code></pre>
<p>LuCI đi với một JSON De-/Encoder hữu dụng cùng nhau với một JSON-RPC-Server triển khai đặc điểm <strong>JSON-RPC 1.0</strong> và 2.0 (party).<br>
LuCI JSON-RPC server đưa ra vài API độc lập. Chính vì thế bạn phải dùng <strong>URLs khác cho mỗi exported library</strong>. Giả sử LuCI-installation của bạn có thể được chạm để qua <code>/cgi-bin/luci</code>, bất kỳ exported library có thể chạm đến thông qua <code>/cgi-bin/luci/rpc/LIBRARY</code>.</p>
<h2 id="authentication"><a href="https://github.com/openwrt/luci/wiki/JsonRpcHowTo#authentication"></a>Authentication</h2>
<p>Đa số exported library sẽ yêu cầu một chứng chỉ xác thực để được gọi cùng. Nếu bạn nhận được <code>HTTP 403 Forbidden</code> thì bạn có lẽ đang lỡ mất token chứng chỉ xác thực . Để lấy được token bạn phải gọi phương thức<code>login</code> của RPC-Library <code>auth</code>. Theo ví dụ của bạn từ trên, function đăng nhập này sẽ được cung cấp ở <code>/cgi-bin/luci/rpc/auth</code>. Function chấp nhận 2 tham số: <code>username</code> và <code>password</code> ( của một tài khoản khả dụng trên hệ thống host ) và trả về token xác thực.<br>
Ví dụ:</p>
<pre><code>curl http://&lt;hostname&gt;/cgi-bin/luci/rpc/auth --data '
{
 "id": 1,
 "method": "login",
 "params": [
 "youruser",
 "somepassword"
 ]
}'
</code></pre>
<p>Hồi đáp:</p>
<pre><code>{"id":1,"result":"65e60c5a93b2f2c05e61681bf5e94b49","error":null}
</code></pre>
<p>Nếu bạn muốn gọi bất kỳ exported library nào yêu cầu một token bạn phải nối tiếp nó như một tham số xác thực URL đến RPC-Server URL. Ví dụ, thay vì gọi <code>/cgi-bin/luci/rpc/LIBRARY</code>, bạn nên gọi <code>/cgi-bin/luci/rpc/LIBRARY?auth=TOKEN</code>.<br>
Nếu JSON-RPC client của bạn là Cookie-aware (như đa số trình duyệt) bạn sẽ nhận được token xác thực cùng với một session cookie và có lẽ là không cần phải nối nó vào RPC-Server URL.</p>
<h2 id="exported-libraries"><a href="https://github.com/openwrt/luci/wiki/JsonRpcHowTo#exported-libraries"></a>Exported Libraries</h2>
<h3 id="uci"><a href="https://github.com/openwrt/luci/wiki/JsonRpcHowTo#uci"></a>uci</h3>
<p>UCI-Library <code>/rpc/uci</code> đưa ra functionality để tương tác với Universal Configuration Interface.<br>
<strong>Exported Functions:</strong></p>
<ul>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.add">(string) add(config, type)</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.apply">(integer) apply(config)</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.changes">(object) changes([config])</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.commit">(boolean) commit(config)</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.delete">(boolean) delete(config, section[, option])</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.delete_all">(boolean) delete_all(config[, type])</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.foreach">(array) foreach(config[, type])</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.get">(mixed) get(config, section[, option])</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.get_all">(object) get_all(config[, section])</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.get">(mixed) get_state(config, section[, option])</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.revert">(boolean) revert(config)</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.section">(name) section(config, type, name, values)</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.set">(boolean) set(config, section, option, value)</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.uci.html#Cursor.tset">(boolean) tset(config, section, values)</a></li>
</ul>
<h3 id="fs"><a href="https://github.com/openwrt/luci/wiki/JsonRpcHowTo#fs"></a>fs</h3>
<p>Thư viện Filesystem <code>/rpc/fs</code> đưa ra functionality để tương tác với filesystem trên mấy host<br>
<strong>Exported Functions:</strong><br>
<a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.fs.html">Complete luci.fs library</a><br>
<strong>Note</strong>: Mọi function được export như chúng vì chúng ngoại trừ <code>readfile</code> mã hóa giá trị trả về của chúng trong <code>base64</code> và <code>writefile</code>chỉ chấp nhận giải mã dữ liệu <code>base64</code> như tham số thứ 2, Lưu ý là các function cùng sẽ chỉ được sẵn dùng khi <code>luasocket</code> packet được cài đặt trên hostsystem</p>
<h3 id="sys"><a href="https://github.com/openwrt/luci/wiki/JsonRpcHowTo#sys"></a>sys</h3>
<p>Thư viện System <code>/rpc/sys</code> đưa ra functionality để tương tác với hệ điều hành trên máy host<br>
<strong>Exported Functions:</strong></p>
<ul>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.sys.html">Complete luci.sys library</a></li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.sys.group.html">Complete luci.sys.group library</a> đặt trước tên phương thức <code>group.methodname</code>.</li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.sys.net.html">Complete luci.sys.net library</a> đặt trước tên phương thức <code>net.methodname</code>.</li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.sys.process.html">Complete luci.sys.process library</a> đặt trước tên phương thức <code>process.methodname</code>.</li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.sys.user.html">Complete luci.sys.user library</a> với tiền tố <code>user</code>.</li>
<li><a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.sys.wifi.html">Complete luci.sys.wifi library</a> với tiền tố <code>wifi</code>.</li>
</ul>
<h3 id="ipkg"><a href="https://github.com/openwrt/luci/wiki/JsonRpcHowTo#ipkg"></a>ipkg</h3>
<p>Thư viện IPKG <code>/rpc/ippkg</code> đưa ra functionality để tương tác với gói quản lý (IPKG hoặc OPKG) trên máy host.<br>
<strong>Exported Functions:</strong><br>
<a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/openwrt/luci/master/documentation/api/modules/luci.model.ipkg.html">Complete luci.model.ipkg library</a></p>

