# User Activity Tracking

## Events

### **Adding an Event**

Events là một cách để theo dõi bất kỳ hành động tùy chỉnh nào hoặc các dữ liệu khác mà bạn muốn theo dõi từ trang web của mình. Bạn cũng có thể thiết lập các phân đoạn (segments) để có thể xem phân tích chi tiết của hành động bằng cách cung cấp các giá trị phân đoạn.

{% hint style="warning" %}
Tất cả dữ liệu được truyền đến thực thể (instance) Countly thông qua SDK hoặc API phải ở định dạng UTF-8.
{% endhint %}

Một event bao gồm một đối tượng JavaScript với các key:

* key - tên của sự kiện (bắt buộc)
* count - số lượng sự kiện (tùy chọn) (mặc định là 1)
* sum - tổng số tiền báo cáo cùng với sự kiện (tùy chọn)
* dur - thời lượng tính bằng giây (tùy chọn)
* segmentation - một đối tượng với các cặp key/value (tùy chọn)

Dưới đây là ví dụ về việc thêm một event với tất cả các thuộc tính có thể có:

{% tabs %}
{% tab title="Asynchronous" %}
{% code lineNumbers="true" %}
```
Countly.q.push(['add_event',{
  "key": "Survey_success",
  "count": 1,
  "sum": 0,
  "dur": 25,
  "segmentation": {
    "Answer_1": "I would love to",
    "Answer_2": 12
  }
}]);
```
{% endcode %}
{% endtab %}

{% tab title="Synchronous" %}
{% code lineNumbers="true" %}
```
Countly.add_event({
  "key": "Survey_success",
  "count": 1,
  "sum": 0,
  "dur": 25,
  "segmentation": {
    "Answer_1": "I would love to",
    "Answer_2": 12
  }
});
```
{% endcode %}
{% endtab %}
{% endtabs %}

### **Timed Events**

Timed events là các phương thức tiện ích của SDK để tính toán thời lượng của một hoạt động một cách dễ dàng. Có ba phương thức khả dụng để sử dụng nhằm tính toán thuộc tính thời lượng của một sự kiện: start\_event, cancel\_event, và end\_event.

{% hint style="info" %}
Tuy nhiên, điều quan trọng cần lưu ý là các phương thức này hoạt động trên lớp bộ nhớ (memory layer) và không nên được sử dụng để tính toán thời lượng trong các tình huống xảy ra việc khởi động lại hoặc làm mới trình duyệt.
{% endhint %}

Phương thức start\_event bắt đầu một bộ đếm thời gian nội bộ trong SDK cho một tên sự kiện nhất định. Bộ đếm thời gian này hoạt động bằng cách lấy dấu thời gian (timestamp) hiện tại và lưu trữ nó vào bộ nhớ.

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['start_event', 'timedEvent']);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.start_event("timedEvent")
```
{% endtab %}
{% endtabs %}

Phương thức cancel\_event sẽ xóa dấu thời gian liên kết với một tên sự kiện nhất định nếu start\_event đã được gọi trước đó cho sự kiện đó.

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['cancel_event', 'timedEvent']);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.cancel_event("timedEvent")
```
{% endtab %}
{% endtabs %}

Phương thức end\_event dừng bộ đếm thời gian và tạo một sự kiện cho tên đã cho với thời lượng được tính toán, sau đó thêm nó vào hàng đợi sự kiện. Bạn cũng có thể truyền một đối tượng sự kiện vào phương thức này, trong trường hợp đó, nó sẽ sử dụng giá trị key làm tên sự kiện.

{% tabs %}
{% tab title="Asynchronous" %}
{% code lineNumbers="true" %}
```
//end event
Countly.q.push(['end_event', 'timedEvent']);

//or end event with additional data
Countly.q.push(['end_event',{
  "key": "timedEvent",
  "count": 1,
  "sum": 1.5,
  "segmentation": {
  "key1": "value1",
  "key2": "value2"
  }
}]);
```
{% endcode %}
{% endtab %}

{% tab title="Synchronous" %}
{% code lineNumbers="true" %}
```
//end event
Countly.end_event("timedEvent")

//or end event with additional data
Countly.end_event({
  "key": "timedEvent",
  "count": 1,
  "sum": 1.5,
  "segmentation": {
  "key1": "value1",
  "key2": "value2"
  }
});
```
{% endcode %}
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Nếu end\_event không được gọi, sẽ không có sự kiện nào được gửi đến máy chủ.
{% endhint %}

## Sessions

Sessions là các khối thời gian liên tục cho thấy người dùng đang tương tác với trang web của bạn. Mỗi tương tác được ghi lại sau khi một phiên bắt đầu sẽ được ghi nhận dưới phiên đó cho đến khi một phiên mới bắt đầu.

### **Automatic Session Tracking**

Để để SDK tự xử lý logic theo dõi phiên, bạn chỉ cần gọi:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['track_sessions']);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.track_sessions();
```
{% endtab %}
{% endtabs %}

### **Manual Sessions**

Nếu bạn cần một logic phiên tùy chỉnh, bạn có thể sử dụng các phương thức phiên thủ công để:

* Bắt đầu một phiên lúc khởi đầu
* Đánh dấu sự kết thúc của một phiên

Chỉ sử dụng các phương thức dưới đây nếu bạn không có kế hoạch sử dụng tính năng theo dõi phiên tự động và hãy đặt cài đặt `use_session_cookie` thành false trong quá trình khởi tạo (init) để có quyền kiểm soát phiên chi tiết hơn.

SDK sẽ tự động báo cáo thời lượng phiên đã trôi qua với khoảng thời gian 60 giây (điều này có thể được sửa đổi trong quá trình khởi tạo với tùy chọn cấu hình `session_update`).

Beginning a Session Để bắt đầu một phiên:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['begin_session']);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.begin_session();
```
{% endtab %}
{% endtabs %}

Ending a session Để kết thúc một phiên:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['end_session']);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.end_session(sec)
```
{% endtab %}
{% endtabs %}
