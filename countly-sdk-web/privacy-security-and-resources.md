# Privacy, Security & Resources

## User Consent

Phần này hướng dẫn cách thiết lập quản lý sự chấp thuận (consent management) tuân thủ GDPR với Countly Web SDK.

Nếu tính năng quản lý sự chấp thuận được bật, các tính năng của SDK sẽ yêu cầu phải có sự đồng ý trước khi bắt đầu hoạt động. Bằng cách này, bạn sẽ có toàn quyền kiểm soát việc theo dõi người dùng trong mọi phần trên trang web của mình.

Cần lưu ý rằng SDK không tự động lưu trữ trạng thái chấp thuận (no persistency). Lập trình viên hoàn toàn chịu trách nhiệm về việc lưu trữ/giữ trạng thái chấp thuận của người dùng trước khi cung cấp trạng thái đó cho SDK.

### Feature Names

SDK cung cấp các tính năng khác nhau yêu cầu sự chấp thuận. Bạn có thể kiểm tra tất cả các tính năng được hỗ trợ bởi SDK hiện tại thông qua thuộc tính **Countly.features**. Dưới đây là danh sách tất cả các thuộc tính kèm theo giải thích:

* sessions: theo dõi thời điểm, tần suất và thời gian người dùng sử dụng trang web.
* events: cho phép gửi các sự kiện (events) của bạn đến máy chủ.
* views: cho phép theo dõi các lượt xem/trang mà người dùng đã truy cập.
* scrolls: cho phép theo dõi hành vi cuộn trang của người dùng trên heatmap.
* clicks: cho phép theo dõi các lượt nhấp chuột và nhấp vào liên kết của người dùng trên heatmap.
* forms: cho phép theo dõi các lần gửi biểu mẫu (form submissions) của người dùng.
* crashes: cho phép theo dõi các lỗi JavaScript.
* attribution: cho phép theo dõi phân bổ trực tiếp (direct attribution tracking).
* users: cho phép thu thập/cung cấp thông tin người dùng, bao gồm cả các thuộc tính tùy chỉnh.
* star-rating: cho phép theo dõi đánh giá và phản hồi của người dùng thông qua các widget xếp hạng.
* feedback: cho phép sử dụng và báo cáo các widget khảo sát (survey), NPS và xếp hạng.
* apm: cho phép theo dõi hiệu năng của ứng dụng bằng cách ghi lại các dấu vết (traces).
* location: cho phép ghi lại vị trí của người dùng (quốc gia, thành phố).
* remote-config: cho phép người dùng tải cấu hình từ xa (remote config) từ máy chủ.

### Setup During Init

Để bật chế độ quản lý sự chấp thuận (và vô hiệu hóa việc theo dõi cho đến khi sự chấp thuận được cấp cho một tính năng cụ thể), bạn chỉ cần truyền giá trị `true` cho cờ cấu hình `require_consent` trong quá trình khởi tạo SDK.

{% tabs %}
{% tab title="Asynchronous" %}
```
// in your Countly init script
Countly.require_consent = true;
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.init({
  app_key: "YOUR_APP_KEY",
  url: "https://your.server.ly",
  require_consent: true // this will enable consent management
});
```
{% endtab %}
{% endtabs %}

### Changing Consent

Khi khách truy cập đến trang web của bạn, bạn có thể kiểm tra xem mình đã có sự chấp thuận từ khách này hay chưa. Nếu chưa, bạn có thể hiển thị một thông báo (popup) giải thích những gì sẽ được theo dõi và cho phép họ chấp thuận. Khi người dùng chọn các tùy chọn chấp thuận, bạn nên lưu trữ nó một cách bền vững. Mỗi khi Countly tải, hãy thông báo cho Countly biết người dùng đã cấp quyền cho những tính năng nào bằng cách gọi phương thức **`Countly.add_consent`** và truyền vào một hoặc nhiều tính năng (dưới dạng một mảng).

Bạn cũng có thể cho phép người dùng thay đổi ý định về các cài đặt riêng biệt. Khi có thay đổi, bạn có thể gọi phương thức **Countly.add\_consent** hoặc **Countly.remove\_consent** để cho phép Countly theo dõi các tính năng cụ thể hoặc vô hiệu hóa việc theo dõi chúng.

Dưới đây là một ví dụ tổng quát:

{% tabs %}
{% tab title="Asynchronous" %}
```
// to add consent {string|array}
Countly.q.push(['add_consent', feature]);

// to remove consent {string|array}
Countly.q.push(['remove_consent', feature]);

```
{% endtab %}

{% tab title="Synchronous" %}
```
// to add consent {string|array}
Countly.add_consent(feature)

// to remove consent {string|array}
Countly.remove_consent(feature)
```
{% endtab %}
{% endtabs %}

### Feature Groups

Tùy thuộc vào trang web và trường hợp sử dụng của bạn, bạn cũng có thể muốn kết hợp một số tính năng thành một nhóm bằng phương thức `group_features`.

{% tabs %}
{% tab title="Grouping features" %}
```
Countly.group_features({
  activity:["sessions","events","views"],
  interaction:["scrolls","clicks","forms"]
});
//After this call Countly.add_consent("activity") to allow "sessions","events","views"
//or call Countly.add_consent("interaction") to allow "scrolls","clicks","forms"
//or call Countly.add_consent("crashes") to allow some separate feature
```
{% endtab %}

{% tab title="One group for everything" %}
```
Countly.group_features({
  all:["sessions","events","views","scrolls","clicks","forms","crashes","attribution","users"]
});
//After this call Countly.add_consent("all") to allow all features
```
{% endtab %}
{% endtabs %}

## Security and Privacy

### Parameter Tamper Protection

Bạn có thể thiết lập một mã `salt` tùy chọn để tính toán checksum cho dữ liệu yêu cầu gửi đi cùng với mỗi yêu cầu, thông qua trường `&checksum`. Bạn sẽ cần thiết lập mã `salt` chính xác như vậy trên máy chủ Countly (Management > Applications > Salt for checksum). Khi mã `salt` trên máy chủ Countly được thiết lập, tất cả các yêu cầu sẽ được kiểm tra tính hợp lệ của trường `&checksum` trước khi được xử lý.

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.app_key = "YOUR_APP_KEY";
Countly.url = "https://yourdomain.com";
Countly.salt = "your_salt";
// init after      

```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.init({
  app_key: "YOUR_APP_KEY",
  url: "http://yourdomain.com",
  salt: "your_salt"
});
```
{% endtab %}
{% endtabs %}

## FAQ

### Can I integrate Countly Web SDK to my TypeScript Project

TypeScript là một siêu tập ngôn ngữ (superset) của JavaScript với cú pháp chặt chẽ. nó giúp bạn phát hiện lỗi sớm bằng cách thêm kiểu dữ liệu tĩnh (static typing) vào ngôn ngữ. TypeScript được biên dịch xuống JavaScript cơ bản nên nó có thể được sử dụng ở bất cứ đâu JavaScript có thể chạy, cho dù là Node.js hay trình duyệt.

Countly Web SDK được viết bằng JavaScript cơ bản nên nó tương thích với các dự án TypeScript của bạn bằng cách bật tùy chọn `allowJs` trong tệp `tsconfig.json` của dự án. Tuy nhiên, vì chúng tôi sử dụng các tính năng JavaScript chạy trên trình duyệt, dự án của bạn cũng phải có khả năng chạy được trên trình duyệt.

### Ignoring your own bots

Hành vi mặc định của Countly Web SDK là bỏ qua các bot đang thu thập thông tin trang web của bạn để cung cấp dữ liệu phân tích người dùng chính xác hơn. Tuy nhiên, Countly không thể phát hiện và chặn tất cả các bot trên internet vì chúng tôi sử dụng chuỗi `userAgent` để nhận diện, và các bot spam có thể ẩn mình bằng cách cung cấp `UserAgent` thông thường. Tuy nhiên, điều này không áp dụng cho các bot do chính bạn tạo ra.

Để các bot của riêng bạn cũng bị Countly Web SDK bỏ qua, bạn phải bao gồm cụm từ 'CountlySiteBot' trong chuỗi `userAgent` của bot đó. Điều này cho phép SDK nhận diện bot của bạn là một trong những bot cần bỏ qua và SDK sẽ ngừng ghi lại dữ liệu cho bot đó.

### Why aren’t I able to see AngularJS errors on the Countly dashboard?

Theo mặc định, AngularJS thường tự xử lý (nuốt) các lỗi. Bạn sẽ cần mở rộng `$exceptionHandler` của Angular để gọi hàm `Countly.log_error()`. Để biết thêm thông tin, vui lòng tham khảo bài viết tại blog này.

### Incognito mode and ad blockers

Chế độ ẩn danh (Incognito mode) ngăn trình duyệt lưu trữ cookie và các dữ liệu trang web khác. Cookie là các tệp nhỏ mà trang web sử dụng để theo dõi hoạt động của người dùng, ghi nhớ sở thích và cung cấp trải nghiệm cá nhân hóa. Trong chế độ ẩn danh, cookie không được lưu lại. Điều này có nghĩa là bất kỳ thông tin nào SDK của chúng tôi lưu trong `local storage` của trình duyệt sẽ bị mất vào lần tiếp theo người dùng mở trình duyệt ẩn danh. Những thông tin này bao gồm các thứ như device ID và hàng đợi sự kiện/yêu cầu (event/request queues). Mỗi khi một người truy cập vào trang web tích hợp Countly ở chế độ ẩn danh, họ sẽ được coi là một người dùng mới. Vấn đề này có thể được giảm thiểu bằng cách có một trang xác thực (đăng nhập) trên trang web của bạn.

Các trình chặn quảng cáo (Ad blockers) sử dụng nhiều kỹ thuật khác nhau để xác định và chặn các yêu cầu không mong muốn, chẳng hạn như phân tích các mẫu URL, các miền theo dõi đã biết hoặc các đoạn mã JavaScript cụ thể. Chúng cũng có thể phát hiện và chặn các yêu cầu gửi đến các dịch vụ theo dõi hoặc phân tích đã biết, khiến chúng có thể chặn các yêu cầu gửi đến máy chủ Countly từ SDK của chúng tôi. Điều này có thể dẫn đến việc dữ liệu bị thiếu hoặc không đầy đủ. Đây là một khả năng có thể xảy ra, nhưng không phải tất cả các trình chặn quảng cáo đều chặn yêu cầu của chúng tôi, tùy thuộc vào cài đặt bộ lọc của chúng.

Để giảm thiểu vấn đề này ở một mức độ nhất định, bạn có thể kích hoạt tính năng bắt buộc sử dụng phương thức POST cho tất cả các yêu cầu trong SDK. Việc chuyển sang yêu cầu POST có thể khiến các trình chặn quảng cáo khó phát hiện và chặn yêu cầu hơn một chút, vì các tham số được gửi trong thân yêu cầu (request body) thay vì trên URL. Tuy nhiên, nó không làm cho yêu cầu trở nên vô hình hoặc miễn nhiễm với việc bị chặn. Các trình chặn quảng cáo vẫn có thể phân tích lưu lượng mạng và kiểm tra các tiêu đề (headers) cũng như tải trọng (payload) của yêu cầu; vì vậy, nếu máy chủ của bạn do Countly lưu trữ và tên miền nằm trong bộ lọc của trình chặn quảng cáo, yêu cầu đó vẫn sẽ bị chặn.
