---


---

<h1 id="howto-setting-up-a-development-environment">HowTo: Setting up a Development Environment</h1>
<p>Cách được khuyến khích để phát triển LuCI là sửa đổi nó trên hệ thống OpenWrt thực trong việc kết hợp với trình soạn thảo hoặc IDE hỗ trợ điều khiển từ xa sửa tập tin thông qua giao thức SCP hoặc SFTP.<br>
Có nhiều cách để chạy OpenWrt, nó có thể được cài đặt trên phần cứng thực hoặc triển khai trong máy ảo. Việc sử dụng một OpenWrt x86 image trong Qemu ( hoặc Qemu-KVM / Virt-Manager) là một sự lựa chọn tốt để phát triển LuCI. Tham khảo <a href="https://wiki.openwrt.org/doc/howto/qemu">https://wiki.openwrt.org/doc/howto/qemu</a></p>
<h2 id="yêu-cầu-phần-mềm"><a href="https://github.com/openwrt/luci/wiki/DevelopmentEnvironmentHowTo#required-software"></a>Yêu cầu phần mềm</h2>
<p>Đầu tiên, hệ thống OpenWrt được up và đang chạy và xác thực SSH được cài đặt, chắc chắn rằng LuCI được cài đặt và có thể truy cập trên trình duyệt. Nếu chưa cài, cài nó với</p>
<pre><code>opkg update; opkg install luci.
Sau đó chắc chắn rằng web server được cho phép và khởi động với `/etc/init.d/uhttpd enable; /etc/init.d/uhttpd start`.
</code></pre>
<p>Tiến hành với việc cài đặt <code>openssh-sftp-server</code> để OpenWrts root filesystem available qua SFTP. Trỏ IDE hoặc editor đến địa chỉ IP của OpenWrt system, cung cấp thông tin xác thực root và chắc chắn rằng bạn có thể truy cập thành phần filesystem.</p>
<h2 id="filesystem-paths"><a href="https://github.com/openwrt/luci/wiki/DevelopmentEnvironmentHowTo#filesystem-paths"></a>Filesystem paths</h2>
<p>Đa số LuCI code đặt trong <code>/usr/lib/lua/luci</code> đồng thời tài nguyên web (Js, CSS,…) được đặt trong thư mục <code>/www</code> trên hệ thống.</p>
<h2 id="modifying-code"><a href="https://github.com/openwrt/luci/wiki/DevelopmentEnvironmentHowTo#modifying-code"></a>Modifying code</h2>
<p>Tất cả file có thể được sửa đổi trực tiếp trên hệ thống. Lưu ý rằng the LuCI framework caches các biến thể biên dịch sẵn  của thư viện Lua của nó trong thư mục <code>/tmp</code>. Khuyến khích dùng <code>uci set luci.ccache.enable=0; uci commit luci</code> để vô hiệu hóa code caching cho mục đích phát triển</p>
<h2 id="integrating-changes"><a href="https://github.com/openwrt/luci/wiki/DevelopmentEnvironmentHowTo#integrating-changes"></a>Integrating changes</h2>
<p>Sau khi thực hiện, sao chép file từ hệ thống đích vào local git clone và commit chúng. Files từ <code>/usr/lib/lua</code> thuộc về thư mục<code>luasrc/</code> của kho lưu trữ tương ứng, file từ <code>/www</code> thuộc về <code>htdocs/</code> và mọi thứ khác thuộc về thư mục <code>root/</code> . Hãy xem các module có sẵn để có ý tưởng về cấu trúc chung</p>

