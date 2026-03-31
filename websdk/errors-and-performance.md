# Errors & Performance

## Crash Reporting

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

{% hint style="warning" %}
Giải mã lỗi (Crash symbolication) khả dụng cho người dùng phiên bản Enterprise Edition.
{% endhint %}

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

## Application Performance Monitoring

Nếu bạn muốn ghi lại một số chỉ số hiệu năng liên quan đến trang web của mình, có 2 cách để báo cáo các dấu vết hiệu năng (performance traces) này với Countly. Một cách là tự tạo và báo cáo chúng một cách thủ công. Cách còn lại là sử dụng một plugin sẽ vận dụng BoomerangJS để thu thập dữ liệu hiệu năng của trang web và báo cáo dưới dạng một dấu vết hiệu năng.

Bạn có thể tham khảo các ví dụ triển khai APM với BoomerangJS từ các liên kết sau:

* [Async Apm Example](https://github.com/Countly/countly-sdk-web/blob/master/examples/example_apm_async.html)
* [Sync Apm Example](https://github.com/Countly/countly-sdk-web/blob/master/examples/example_apm.html)

### Custom Traces

Để báo cáo dấu vết thủ công, bạn cần xây dựng đối tượng dấu vết và gọi một phương thức như sau:

{% tabs %}
{% tab title="Asynchronous" %}
```
//report custom trace
Countly.q.push(["report_trace",{
  type: "device", //device or network
  name: "test call", //use name to identify trace and group them by
  stz: 1234567890123, //start timestamp in milliseconds
  etz: 1234567890123, //end timestamp in milliseconds
  apm_metrics: {
      duration: 1000 //duration of trace
  }
}]);
```
{% endtab %}

{% tab title="Synchronous" %}
```
//report custom trace
Countly.report_trace({
  type: "device", //device or network
  name: "test call", //use name to identify trace and group them by
  stz: 1234567890123, //start timestamp in milliseconds
  etz: 1234567890123, //end timestamp in milliseconds
  apm_metrics: {
      duration: 1000 //duration of trace
  }
});
```
{% endtab %}
{% endtabs %}

Cho dù bạn đang sử dụng Countly theo cách đồng bộ hay bất đồng bộ, bạn luôn phải cung cấp khóa `duration` và giá trị của nó trong `apm_metrics`, nếu không các dấu vết tùy chỉnh sẽ không được ghi lại.

### Automatic Device Traces

Việc báo cáo dấu vết tự động có hai cách triển khai khác nhau tùy thuộc vào việc bạn đang sử dụng Countly theo cách đồng bộ hay bất đồng bộ. Thông thường, chúng ta muốn script của Countly được tải trước và các script liên quan đến BoomerangJS ngay sau đó.

**Asynchronous Implementation**

Để sử dụng các dấu vết thiết bị tự động trong triển khai Countly bất đồng bộ (async), bạn sẽ cần đặt cờ `loadAPMScriptsAsync` thành `true` trong đối tượng Countly. Điều này đảm bảo thứ tự tải script chính xác được thiết lập. Bạn có thể cung cấp hai cờ bổ sung cho đối tượng Countly: cờ thứ nhất là đường dẫn nguồn script BoomerangJS dưới dạng `customSourceBoomerang` và cờ thứ hai là đường dẫn nguồn script countly\_boomerang dưới dạng `customSourceCountlyBoomerang`. Nếu không được cung cấp, SDK sẽ sử dụng các script CDN mới nhất làm nguồn:

```
Countly.app_key = "YOUR_APP_KEY";
Countly.url = "YOUR_SERVER_URL";
Countly.loadAPMScriptsAsync = true;
// Countly.customSourceBoomerang = "../somewhere/boomerang.min.js";
// Countly.customSourceCountlyBoomerang = "../somewhere/countly_boomerang.js";
// ...
```

Ngoài ra, trong script khởi tạo Countly, bạn cần gọi một phương thức để bắt đầu báo cáo các dấu vết 'loading' và 'network' tự động:

```
// enables APM
Countly.q.push(["track_performance"]);
```

Phương thức này chấp nhận một đối tượng cấu hình BoomerangJS dưới dạng tham số thứ hai tùy chọn. Nếu bạn đã quen thuộc với nó, bạn có thể tự sửa đổi tùy theo nhu cầu của mình. Theo mặc định, SDK sẽ sử dụng cấu hình này:

```
{
    //page load timing
  RT:{},
  //required for automated networking traces
  instrument_xhr: true,
  captureXhrRequestResponse: true,
  AutoXHR: {
    alwaysSendXhr: true,
    monitorFetch: true,
    captureXhrRequestResponse: true
  },
  //required for screen freeze traces
  Continuity: {
    enabled: true,
    monitorLongTasks: true,
    monitorPageBusy: true,
    monitorFrameRate: true,
    monitorInteractions: true,
    afterOnload: true
  }
}
```

**Synchronous Implementation**

Để báo cáo các dấu vết tự động, bạn sẽ cần bao gồm thêm 2 tệp bổ sung trong dự án của mình ngay sau khi khai báo script Countly với các đường dẫn chính xác theo cấu trúc dự án của bạn:

```
// Option 1: You can provide local paths
<script type='text/javascript' src="../plugin/boomerang/boomerang.min.js"></script>
<script type='text/javascript' src='../plugin/boomerang/countly_boomerang.js'></script>

// Option 2: Or you can use CDN for path
<script type='text/javascript' src="https://cdn.jsdelivr.net/npm/countly-sdk-web@latest/plugin/boomerang/boomerang.min.js"></script> 
<script type='text/javascript' src="https://cdn.jsdelivr.net/npm/countly-sdk-web@latest/plugin/boomerang/countly_boomerang.js"></script>
```

Sau đó, bạn sẽ gọi một phương thức để bắt đầu báo cáo các dấu vết 'loading' và 'network' tự động. Bạn có thể tùy chọn cung cấp một đối tượng cấu hình BoomerangJS tại đây như đã đề cập ở phần triển khai bất đồng bộ. Cách sử dụng mặc định bên trong script khởi tạo Countly của bạn sẽ như sau:

```
//automatically report traces
Countly.track_performance();
```
