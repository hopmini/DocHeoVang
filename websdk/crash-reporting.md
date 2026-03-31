# Crash Reporting

### Crash Reporting

Để tự động ghi lại và báo cáo các lỗi JavaScript chưa được xử lý (unhandled) trên trang web của bạn (Bạn có thể thêm thông tin bổ sung vào báo cáo lỗi bằng cách cung cấp một đối tượng với các cặp key/value):

{% tabs %}
{% tab title="Asynchronous " %}
```
Countly.q.push(['track_errors', {
  "your_extra_info_key": your_info
}])
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.track_errors({
  "your_extra_info_key": your_info
})
```
{% endtab %}
{% endtabs %}

Bạn cũng có thể báo cáo các ngoại lệ đã được xử lý (handled) cho máy chủ. Bạn có thể tùy chọn cung cấp thông tin bổ sung (một đối tượng) cùng với báo cáo (hoặc sử dụng những thông tin được cung cấp với phương thức `track_errors` làm mặc định).

{% tabs %}
{% tab title="Asynchronous " %}
```
try{
  //do something here
}
catch(ex){
  //report error to Countly
  Countly.q.push(['log_error', ex, extra_info]);
}
```
{% endtab %}

{% tab title="Synchronous" %}
```
try{
  //do something here
}
catch(ex){
  //report error to Countly
  Countly.log_error(ex, extra_info);
}
```
{% endtab %}
{% endtabs %}

Bạn có thể để lại các dấu vết (breadcrumbs) xuyên suốt mã nguồn cho các hành động khác nhau của người dùng. Những dấu vết này sau đó sẽ được kết hợp thành một bản ghi (log) duy nhất và cũng được báo cáo cho máy chủ.

{% tabs %}
{% tab title="Asynchronous " %}
```
Countly.q.push(['add_log', "user clicked button a"]);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.add_log("user clicked button a");
```
{% endtab %}
{% endtabs %}

### Crash Filtering

Bạn có thể bỏ qua hoặc sửa đổi các báo cáo sự cố (crash reports) mà SDK tạo ra bằng tính năng lọc sự cố (crash filtering). Bạn có thể cung cấp một hàm gọi lại (callback function) mà SDK có thể gọi khi một báo cáo sự cố đã được chuẩn bị xong. Sau đó, bạn có thể sửa đổi hoặc bỏ qua sự cố tùy theo thông tin hiện có.

{% tabs %}
{% tab title="Asynchronous " %}
```
Countly.crash_filter_callback = function (crashObject) {
      console.log("Crash object:", crashObject);
      // sửa đổi, bỏ qua hoặc trả về trực tiếp
      return crashObject;
};
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.init({
      app_key: "YOUR_APP_KEY",
      url: "https://yourdomain.com",
      crash_filter_callback: function (crashObject) {
        console.log("Crash object:", crashObject);
        // modify, omit or return directly
        return crashObject;
      }
});
```
{% endtab %}
{% endtabs %}

`crashObject` sẽ bao gồm một số thông tin về các chỉ số (metric) và dữ liệu (data) này. Tham số quan trọng nhất là `_error`, tham số mà bạn có thể kiểm tra các giá trị chuỗi (string) đang tìm kiếm. Nếu bạn muốn ngăn việc gửi một sự cố, chỉ cần trả về `null` từ hàm callback. Nếu bạn muốn gửi báo cáo như hiện tại, bạn chỉ cần trả về trực tiếp nó. Và cuối cùng, bạn có thể xóa/che giấu thông tin nhạy cảm và trả về đối tượng sự cố đã được sửa đổi trong hàm callback. SDK sẽ gửi đối tượng được trả về cho máy chủ.

### Symbolication

Giải mã lỗi (Crash symbolication) khả dụng cho người dùng phiên bản Enterprise Edition.

Nếu các tệp js bạn cung cấp đã được nén (minified), chuyển đổi (transpiled) hoặc được xử lý từ các tệp nguồn của bạn, bạn có thể sử dụng tính năng symbolication. Với symbolication, các stacktraces của bạn sẽ chỉ chính xác đến các dòng trên tệp nguồn, điều này sẽ giúp các nhà phát triển gỡ lỗi các sự cố và lỗi. Đây là một ví dụ:

Obfuscated (Đã mã hóa) De-obfuscated (Đã giải mã)

{% tabs %}
{% tab title="Obfuscated" %}
```
Error: Error at depth 3
  at cause_error (http://127.0.0.1:5501/examples/symbolication/dist/main.js:5657:13)
  at multiply (http://127.0.0.1:5501/examples/symbolication/dist/main.js:5651:3)
  at saveRecords (http://127.0.0.1:5501/examples/symbolication/dist/main.js:5648:3)
  at initializeCoreFunctions (http://127.0.0.1:5501/examples/symbolication/dist/main.js:5645:3)
  at save (http://127.0.0.1:5501/examples/symbolication/dist/main.js:5642:3)
  at removeHashes (http://127.0.0.1:5501/examples/symbolication/dist/main.js:5639:3)
  at calculateParams (http://127.0.0.1:5501/examples/symbolication/dist/main.js:5636:3)
  at addRecords (http://127.0.0.1:5501/examples/symbolication/dist/main.js:5633:3)
  at HTMLButtonElement.unhandled_error (http://127.0.0.1:5501/examples/symbolication/dist/main.js:5675:5)
```
{% endtab %}

{% tab title="De-obfuscated" %}
```
Error: Error at depth 3
  at src/index.js:66:12
  at cause_error (src/index.js:59:2)
  at multiply (src/index.js:56:2)
  at saveRecords (src/index.js:53:2)
  at initializeCoreFunctions (src/index.js:50:2)
  at save (src/index.js:47:2)
  at removeHashes (src/index.js:44:2)
  at calculateParams (src/index.js:41:2)
  at addRecords (src/index.js:87:6)
```
{% endtab %}
{% endtabs %}

Khi sử dụng bất kỳ công cụ xây dựng (build tool) nào, bạn sẽ cần chọn một tùy chọn tạo sơ đồ nguồn (source map) dưới dạng một tệp riêng biệt chứ không phải nội tuyến (inline) với tệp js cuối cùng (ví dụ: tùy chọn `devtool` cho webpack). Nếu bạn thiết lập chính xác, các bản build của bạn sẽ tạo ra một tệp sơ đồ nguồn kết thúc bằng `.map`, đây là tệp sơ đồ nguồn mà bạn sẽ tải lên máy chủ Countly của mình.

Để bắt đầu Giải mã (Symbolicating) các lỗi của bạn, bạn chỉ cần tải tệp sơ đồ nguồn lên thực thể Countly của mình thông qua menu bên: Crashes > Manage Symbols. Trong chế độ xem này, các tệp sơ đồ nguồn bạn đã tải lên sẽ được liệt kê. Để tải lên một tệp, bạn nhấp vào "Add Debug Symbol File", điền loại biểu tượng (symbol type) và các trường phiên bản ứng dụng (app version) tương ứng và tải lên tệp `.map` của bạn. Khi tệp sơ đồ nguồn của bạn đã được tải lên, bạn có thể Giải mã các báo cáo sự cố cho phiên bản ứng dụng đó trên Countly. Để biết thêm thông tin, hãy xem tài liệu Crash Symbolication.
