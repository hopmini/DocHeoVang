# Getting Started

### Web analytics (JavaScript)

Tài liệu này dành cho Countly Web SDK phiên bản 26.1.X.

Countly có thể hoạt động trên tất cả các trình duyệt hỗ trợ ECMAScript 5 (ES5). Dưới đây là phiên bản tối thiểu của các trình duyệt phổ biến hỗ trợ đầy đủ ES5:

| **IE** | **Edge** | **Firefox** | **Firefox (Android)** | **Opera** | **Opera (Mobile)** | **Safari** | **Safari (iOS)** | **Chrome** | **Chrome (Android)** |
| ------ | -------- | ----------- | --------------------- | --------- | ------------------ | ---------- | ---------------- | ---------- | -------------------- |
| 11\*   | 12       | 21          | 9                     | 15        | 16                 | 6          | 6                | 23         | 98                   |

_\* Cần sử dụng polyfills._

Để xem các ví dụ về tích hợp mẫu, bạn có thể tham khảo thêm trong kho lưu trữ mã nguồn của chúng tôi.

### Adding the SDK to the Project

Để theo dõi các trang web của bạn (SPA, MPA hoặc các ứng dụng di động bao gồm các view HTML5) và các tiện ích mở rộng trình duyệt (browser extensions), bạn có thể sử dụng Countly Web SDK.

SDK này được lưu trữ tự động trên máy chủ Countly của bạn dưới dạng tệp UMD:

* Minified (Bản nén): `https://yourdomain.com/sdk/web/countly.min.js`
* Non-minified (Bản đầy đủ): `https://yourdomain.com/sdk/web/countly.js`

Ngoài ra, bạn cũng có thể sử dụng các trình quản lý gói (package managers) để cài đặt SDK:

{% tabs %}
{% tab title="nmp" %}
```
npm install countly-sdk-web
```
{% endtab %}

{% tab title="yarn" %}
```
yarn add countly-sdk-web
```
{% endtab %}
{% endtabs %}

Bạn cũng có thể truy cập SDK thông qua CDN:

{% tabs %}
{% tab title="Lastest" %}
```
// latest non minified
cdn.jsdelivr.net/npm/countly-sdk-web@latest/lib/countly.js

// latest minified
cdn.jsdelivr.net/npm/countly-sdk-web@latest/lib/countly.min.js
```
{% endtab %}

{% tab title="Specific Version" %}
```
// Some code// 26.1.0 non minified
cdn.jsdelivr.net/npm/countly-sdk-web@26.1.0/lib/countly.js

// 26.1.0 minified (JSDelivr or Cloudflare)
cdn.jsdelivr.net/npm/countly-sdk-web@26.1.0/lib/countly.min.js 
or
cdnjs.cloudflare.com/ajax/libs/countly-sdk-web/26.1.0/countly.min.js
```
{% endtab %}
{% endtabs %}

Cuối cùng, như một tùy chọn thay thế, bạn có thể tải xuống tệp `countly.min.js` từ kho lưu trữ GitHub của chúng tôi và tải nó lên bất kỳ máy chủ nào mà bạn muốn lưu trữ.

### SDK Integration

#### **Minimal Setup**

SDK này là một tệp UMD duy nhất, có thể được tải trực tiếp vào tệp HTML hoặc nhập thông qua trình quản lý gói (package manager) vào dự án và đóng gói (bundle) cùng với bộ cài (build) của bạn.

Bạn có thể sử dụng Countly Web SDK theo kiểu synchronously (đồng bộ) hoặc asynchronously (bất đồng bộ). Việc sử dụng bất đồng bộ sẽ giúp trang web hoạt động mà không bị chặn quá trình tải nội dung. Điều này cũng cho phép bạn sử dụng Countly ngay cả khi đoạn mã (script) của Countly chưa tải xong bằng cách đẩy các lời gọi hàm vào hàng đợi `Countly.q`; SDK sẽ thực thi chúng sau khi được khởi tạo (initialized).

Tại đây, bạn cũng cần cung cấp application key (APP\_KEY) và server URL của ứng dụng trên máy chủ Countly. Vui lòng kiểm tra tài liệu hướng dẫn để biết cách lấy các thông tin này.

Bạn có thể kiểm tra tất cả các tùy chọn cấu hình (config options) để điều chỉnh hành vi của SDK tại trang tài liệu gốc.

Ví dụ thiết lập mẫu (npm/yarn):

{% tabs %}
{% tab title="nmp/yarn" %}
```
import Countly from "countly-sdk-web";

Countly.init({
  // server credentials
  app_key: "YOUR_APP_KEY",
  url: "http://yourdomain.com"
});

// Track sessions and page views automatically (recommended)
Countly.track_sessions();
Countly.track_pageview();
```
{% endtab %}

{% tab title="HTML(Async)" %}
```
<!--inside <head></head>-->
<script type='text/javascript'>
var Countly = Countly || {};
Countly.q = Countly.q || [];

// server credentials 
Countly.app_key = "YOUR_APP_KEY"; 
Countly.url = "https://yourdomain.com";

// Track sessions and page views automatically (recommended)
Countly.q.push(['track_sessions']);
Countly.q.push(['track_pageview']);

// Load Countly script asynchronously
(function() {
  var cly = document.createElement('script'); cly.type = 'text/javascript';
  cly.async = true;
  cly.src = 'https://cdn.jsdelivr.net/npm/countly-sdk-web@latest/lib/countly.min.js';
  cly.onload = function(){Countly.init()};
  var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(cly, s);
})();
</script>
```
{% endtab %}

{% tab title="HTML(Sync)" %}
```
<!--inside <head></head>-->
<script type='text/javascript' src='https://cdn.jsdelivr.net/npm/countly-sdk-web@latest/lib/countly.min.js'></script>
<script type='text/javascript'>

Countly.init({
  // server credentials
  app_key: "YOUR_APP_KEY",
  url: "http://yourdomain.com"
});
// Track sessions and page views automatically (recommended)
Countly.track_sessions();
Countly.track_pageview();
</script>
```
{% endtab %}
{% endtabs %}

Nếu bạn còn thắc mắc về tính chính xác của việc tích hợp Countly SDK, bạn có thể tìm hiểu thêm về các phương pháp xác minh (verification methods) trong tài liệu hướng dẫn.

### SDK Logging

Nếu tính năng ghi log (logging) được kích hoạt, SDK của chúng tôi sẽ in ra các thông báo debug về trạng thái nội bộ (các sự kiện đang được ghi nhận và gửi đi) cũng như các vấn đề gặp phải.

Thiết lập tùy chọn debug thành true khi khởi tạo Countly để kích hoạt ghi log:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.debug = true;
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.init({
    debug:true,
    app_key:"YOUR_APP_KEY",
    url: "https://try.count.ly",
});
```
{% endtab %}
{% endtabs %}

Để biết thêm thông tin về nơi tìm các bản log của SDK, bạn có thể kiểm tra tài liệu hướng dẫn tại trang chủ của nhà cung cấp.
