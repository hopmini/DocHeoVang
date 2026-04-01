# Engagement & Optimization

## Content Zone

Tính năng Content Zone giúp tăng cường sự tương tác của người dùng bằng cách cung cấp các loại khối nội dung khác nhau như tin nhắn trong ứng dụng (in-app messaging), quảng cáo hoặc các lời nhắc tương tác người dùng thông qua việc sử dụng Journeys. Các khối nội dung này được cung cấp một cách linh động từ trình xây dựng nội dung (content builder) trên máy chủ, đảm bảo người dùng nhận được thông tin liên quan và cập nhật nhất.

{% hint style="success" %}
Để tìm hiểu cách sử dụng Journeys & Content Builder nhằm tạo các tin nhắn In-App, bạn có thể tham khảo bài viết này.
{% endhint %}

Để kích hoạt tính năng này và bắt đầu lấy nội dung từ máy chủ theo các Journey của bạn, chỉ cần sử dụng phương thức sau:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(() => { Countly.content.enterContentZone(); });

// or
Countly.q.push(['content.enterContentZone']);

// or with filtering you can pick and choose which content to show
function filter(params) {
    var shouldContentBeShown = true;
    console.log("Content filter called with params:", params);
    // Add your content filtering logic here and determine whether to show content
    return shouldContentBeShown;
}

Countly.q.push(['content.enterContentZone', filter]);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.content.enterContentZone();

// or with filtering you can pick and choose which content to show
function filter(params) {
    var shouldContentBeShown = true;
    console.log("Content filter called with params:", params);
    // Add your content filtering logic here and determine whether to show content
    return shouldContentBeShown;
}

Countly.content.enterContentZone(filter);
```
{% endtab %}
{% endtabs %}

Lời gọi hàm này sẽ truy xuất và hiển thị bất kỳ nội dung nào có sẵn cho người dùng. Nó cũng sẽ định kỳ kiểm tra xem có nội dung mới hay không, và nếu có, SDK sẽ lấy và hiển thị nội dung đó cho người dùng.

### Fine Tuning Content

Nếu bạn cần kích hoạt một Journey và hiển thị nội dung của nó ngay sau một hành động cụ thể của người dùng, bạn có thể sử dụng phương thức dưới đây để đẩy nhanh quá trình:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(() => { Countly.content.refreshContentZone(); });

// or
Countly.q.push(['content.refreshContentZone']);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.content.refreshContentZone();
```
{% endtab %}
{% endtabs %}

Khi bạn muốn thoát khỏi Content Zone và dừng việc SDK kiểm tra nội dung mới, bạn có thể sử dụng phương thức này:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(() => { Countly.content.exitContentZone(); });

// or 
Countly.q.push(['content.exitContentZone']);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.content.exitContentZone();
```
{% endtab %}
{% endtabs %}

Nếu bạn cần thay đổi tần suất yêu cầu Content Zone từ giá trị mặc định là 30 giây, bạn có thể cung cấp một giá trị tùy chỉnh cho các khoảng thời gian (tính bằng giây) trong quá trình khởi tạo (tối thiểu 15 giây):

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.content_zone_timer_interval = 45; // seconds
     
// Initialize the SDK
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.init({
  app_key: "YOUR_APP_KEY",
  url: "https://your.server.ly",
  content_zone_timer_interval: 45, //seconds
});
```
{% endtab %}
{% endtabs %}

## Heatmaps

Tính năng Heatmaps là một plugin dành riêng cho web giúp bạn trực quan hóa các tương tác của người dùng trên trang web của mình. Web SDK hỗ trợ chức năng này bằng cách cung cấp thông tin về các lượt nhấp chuột (click) và cuộn trang (scroll) của người dùng cho máy chủ của bạn. Sau đó, từ máy chủ, bạn có thể kích hoạt lớp phủ (overlay) Heatmaps để trực quan hóa các cụm nhấp chuột và vùng cuộn trang này ngay trên trang web.

Để hiển thị lớp phủ này, SDK sẽ tải một số tập lệnh nhất định từ máy chủ của bạn. Để cho phép các tập lệnh này được tải từ một nơi khác ngoài máy chủ Countly mà SDK gửi thông tin đến (ví dụ: máy chủ nội bộ của bạn), SDK cung cấp tùy chọn danh sách trắng (whitelisting) trong quá trình khởi tạo. Để đưa các miền khác ngoài máy chủ Countly vào danh sách trắng, bạn nên cung cấp một mảng gồm các miền này dưới dạng giá trị Chuỗi (String) thông qua cờ `heatmap_whitelist` khi khởi tạo:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.app_key = "YOUR_APP_KEY";
Countly.url = "https://try.count.ly";
Countly.heatmap_whitelist = ["https://you.domain1.com", "https://you.domain2.com"];
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.init({
  app_key:"YOUR_APP_KEY",
  url: "https://try.count.ly",
  heatmap_whitelist: ["https://you.domain1.com", "https://you.domain2.com"]
});
```
{% endtab %}
{% endtabs %}

### Tracking Clicks

Phương thức này sẽ tự động theo dõi các lượt nhấp chuột trên chế độ xem (view) được báo cáo gần nhất và hiển thị chúng trên bản đồ nhiệt.

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['track_clicks']);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.track_clicks();
```
{% endtab %}
{% endtabs %}

Trong trường hợp bạn gặp sự cố khi xem heatmaps, vui lòng tham khảo Hướng dẫn khắc phục sự cố (Troubleshooting guide) này.

{% hint style="success" %}
Lưu ý về việc xem heatmaps với nội dung HTTP/HTTPS: Trình duyệt không cho phép tải nội dung iframe HTTP trên các trang web HTTPS. Vì lý do này, nếu bạn đang sử dụng HTTPS trên thực thể Countly của mình, bạn sẽ chỉ có thể xem nội dung HTTPS và không thể hiển thị nội dung trang HTTP.
{% endhint %}

Sau khi bạn tích hợp SDK và bắt đầu gửi dữ liệu nhấp chuột, tất cả các bản đồ nhiệt được tạo có thể được xem tại mục Analytics > Heatmaps.

### Tracking Scrolls

Phương thức này sẽ tự động theo dõi hành vi cuộn trang trên chế độ xem (view) được báo cáo gần nhất và hiển thị chúng trên bản đồ nhiệt.

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['track_scrolls']);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.track_scrolls();
```
{% endtab %}
{% endtabs %}

Tương tự như Click Heatmaps, dữ liệu thu thập được có thể xem tại mục Analytics > Heatmaps. Bạn có thể thay đổi loại bản đồ nhiệt trên thanh công cụ phía trên sau khi mở một chế độ xem.

Đây là bản dịch phần Remote Config cho tài liệu của bạn. Tôi giữ nguyên các tiêu đề (Headings) và tên hàm bằng tiếng Anh, dịch nội dung diễn giải sang tiếng Việt chuẩn văn phong kỹ thuật.

## Remote Config

Tính năng Remote Config cho phép bạn truy xuất dữ liệu mà bạn đã tạo trên máy chủ của mình. Tùy thuộc vào các điều kiện bạn đã thiết lập, bạn có thể lấy dữ liệu từ máy chủ cho các người dùng cụ thể thỏa mãn những điều kiện đó và xử lý dữ liệu Remote Config theo bất kỳ cách nào bạn muốn. Từ việc thay đổi màu nền của trang web đến việc hiển thị một thông điệp nhất định, các khả năng là vô hạn. Để biết thêm thông tin về Remote Config, vui lòng kiểm tra tại đây.

Trong khi truy xuất Remote Config, SDK sẽ tự động ghi danh người dùng vào thử nghiệm A/B (A/B testing). Tuy nhiên, bạn có thể chọn ghi danh (hoặc không) người dùng của mình vào thử nghiệm A/B một cách rõ ràng trong khi truy xuất các giá trị remote config hoặc sau đó. Để biết thêm thông tin về thử nghiệm A/B, vui lòng kiểm tra tại đây.

### Automatic Remote Config

Chức năng Automatic Remote Config bị tắt theo mặc định và cần được bật một cách rõ ràng. Khi tính năng này được bật, SDK sẽ cố gắng truy xuất dữ liệu dựa trên một số trình kích hoạt (triggers) cụ thể. Ví dụ: sau khi khởi tạo SDK hoặc khi thay đổi device ID.

Bạn có thể bật tính năng này bằng cách cung cấp một hàm gọi lại (callback function) cho cờ `remote_config` hoặc đặt nó thành `true` khi khởi tạo SDK.

Nếu bạn cung cấp một hàm callback, hàm này sẽ được gọi khi Remote Config được tải lần đầu và khi nó được tải lại nếu bạn thay đổi `device_id`. Hàm callback này nên có hai tham số: tham số thứ nhất dành cho lỗi (error) và tham số thứ hai dành cho đối tượng Remote Config.

Asynchronous / Synchronous

{% tabs %}
{% tab title="Asynchronous" %}
```
// in your Countly init script
Countly.app_key = "YOUR_APP_KEY";
Countly.url = "https://try.count.ly";
Countly.debug = true;
Countly.remote_config = true;

// OR

// provide a callback to be notified when configs are loaded
Countly.app_key = "YOUR_APP_KEY";
Countly.url = "https://try.count.ly";
Countly.debug = true;
Countly.remote_config = function(err, remoteConfigs){
  if (!err) {
    //we have our remoteConfigs here
    console.log(remoteConfigs);
  }
};
```
{% endtab %}

{% tab title="Synchronous" %}
```
// in your Countly init script
Countly.init({
  app_key:"YOUR_APP_KEY",
  url: "https://try.count.ly",
  debug: true,
  remote_config: true 
});

// OR

// provide a callback to be notified when configs are loaded
Countly.init({
  app_key:"YOUR_APP_KEY",
  url: "https://try.count.ly",
  debug: true,
  remote_config: function(err, remoteConfigs){
    if (!err) {
      //we have our remoteConfigs here
      console.log(remoteConfigs);
    }
  }
});
```
{% endtab %}
{% endtabs %}

### Manual Remote Config

Nếu muốn, bạn có thể truy xuất Remote Config theo cách thủ công để nhận giá trị mới nhất vào bất kỳ lúc nào sau khi khởi tạo. Để thực hiện, bạn phải sử dụng lệnh gọi `fetch_remote_config`. Phương thức này cũng được sử dụng để tải lại các giá trị nhằm cập nhật chúng theo những thay đổi mới nhất bạn đã thực hiện trên máy chủ.

Bằng cách sử dụng phương thức này, bạn có thể tải toàn bộ đối tượng, hoặc chỉ tải một số khóa (keys) cụ thể, hoặc bỏ qua một số khóa cụ thể nhằm giảm lượng dữ liệu truyền tải cần thiết (trong trường hợp giá trị của một số khóa rất lớn). Lệnh gọi này sẽ tự động lưu các khóa đã truy xuất vào bên trong SDK.

#### **Fetch All Keys**

Tại đây, bạn không cần cung cấp bất kỳ tham số nào cho lệnh gọi, nhưng việc cung cấp một hàm callback là một thực hành được khuyến khích. Hàm callback này nên có hai tham số: thứ nhất là lỗi và thứ hai là đối tượng Remote Config.

```
// load the whole configuration object with a callback
Countly.fetch_remote_config(function(err, remoteConfigs){
  if (!err) {
    console.log(remoteConfigs);
  // or do something else here if you want with remoteConfigs object
  }
});

// or whole configuration object with no params
Countly.fetch_remote_config();
```

#### **Fetch Specific Keys**

Tại đây, các khóa nên được cung cấp dưới dạng các giá trị chuỗi trong một mảng, đóng vai trò là tham số đầu tiên trong lệnh gọi `fetch_remote_config`. Bạn có thể cung cấp một hàm callback làm tham số thứ hai. Hàm callback này nên có hai tham số: thứ nhất là lỗi và thứ hai là đối tượng Remote Config.

```
// load specific keys only, as `key1` and `key2`
Countly.fetch_remote_config(["key1","key2"], function(err, remoteConfigs){
  if (!err) {
    console.log(remoteConfigs);
    // or do something else here if you want with remoteConfigs object
  }
});
```

#### **Fetch All Except Specific Keys**

Tại đây, tham số đầu tiên nên được đặt thành `null` hoặc `undefined`, và các khóa mà bạn muốn bỏ qua phải được cung cấp ở tham số thứ hai dưới dạng một mảng các chuỗi. Bạn có thể cung cấp một hàm callback làm tham số thứ ba. Hàm callback này nên có hai tham số: thứ nhất là lỗi và thứ hai là đối tượng Remote Config.

```
// load all key values except specific keys, as `key1` and `key2'
Countly.fetch_remote_config(null, ["key1","key2"], function(err, remoteConfigs){
  if (!err) {
    console.log(remoteConfigs);
    // or do something else here if you want with remoteConfigs object
  }
});
```

### Accessing Remote Config Values

Bạn có thể gọi `get_remote_config` mỗi khi muốn nhận đối tượng Remote Config của một giá trị cho một khóa (key) cụ thể hoặc tất cả các khóa từ bộ nhớ cục bộ (local storage) của bạn.

Phương thức này nên được gọi sau khi Remote Config đã được tải thành công, nếu không nó sẽ chỉ trả về một đối tượng rỗng hoặc các giá trị undefined.

```
//get whole Remote Config object
var remoteConfig = Countly.get_remote_config();

//or get value for specific key like 'test'
var test = Countly.get_remote_config("test");
```

### A/B Testing

Để thực hiện việc này, bạn phải đặt cờ `use_explicit_rc_api` thành `true` trong quá trình khởi tạo (mặc định là `false`). Điều này sẽ sử dụng API Remote Config mới và ghi danh người dùng của bạn vào thử nghiệm A/B nếu họ đủ điều kiện. Tuy nhiên, nếu bạn muốn sử dụng API mới mà không tự động ghi danh người dùng, cờ `rc_automatic_optin_for_ab` nên được đặt thành `false` trong khi khởi tạo (mặc định là `true`).

Nếu bạn muốn ghi danh người dùng vào thử nghiệm A/B mà không thông qua API Remote Config, bạn có thể sử dụng lệnh gọi `enrollUserToAb` với các khóa (một mảng các giá trị chuỗi) mà bạn muốn ghi danh người dùng vào.

```
// enrolling user for 'key1' and 'key2'
Countly.enrollUserToAb(["key1","key2"]);
```

### Consent

Nếu tính năng đồng ý (consents) được bật, để truy xuất dữ liệu Remote Config, bạn phải cung cấp quyền 'remote-config' để tính năng này có thể hoạt động.

## User Feedback

Nếu bạn muốn nhận phản hồi từ người dùng, có một vài cách để thực hiện việc đó trong Countly. Người dùng có thể để lại phản hồi thông qua Feedback Widgets (Surveys, NPS, Ratings). Với sự trợ giúp của các widget này, bạn có thể đặt nhiều câu hỏi cho khách hàng và tìm hiểu chi tiết về ý kiến cũng như sở thích của họ.

### Feedback Widgets

{% hint style="success" %}
Feedback Widgets là một plugin thuộc phiên bản Countly Enterprise.&#x20;
{% endhint %}

Có thể hiển thị 3 loại widget phản hồi: [NPS](https://support.countly.com/hc/en-us/articles/4652903481753-Feedback-Overview#h_01HAY62C2QB9K7CRDJ90DSDM0D), [Survey](https://support.countly.com/hc/en-us/articles/4652903481753-Feedback-Overview#h_01HAY62C2Q965ZDAK31TJ6QDRY) và [Rating](https://support.countly.com/hc/en-us/articles/4652903481753-Feedback-Overview#h_01HAY62C2R4S05V7WJC5DEVM0N).&#x20;

Để biết thêm thông tin chi tiết về Feedback Widgets, bạn có thể tham khảo [tại đây.](https://support.countly.com/hc/en-us/articles/4652903481753-Feedback-Overview)&#x20;

{% hint style="warning" %}
Trước khi bất kỳ widget phản hồi nào có thể hiển thị, bạn cần tạo chúng trong bảng điều khiển (dashboard) Countly của mình.
{% endhint %}

Để hiển thị các widget có sẵn cho người dùng, bạn có thể sử dụng các phương thức thông qua giao diện `feedback`. Các phương thức này sẽ nhận một tham số duy nhất:

* nameTagOrID - Giá trị chuỗi để chọn một widget theo tên, thẻ (tag) hoặc ID của nó (tùy chọn).

Nếu giá trị này không được cung cấp hoặc không tìm thấy widget nào khớp với tham số, SDK sẽ hiển thị widget đầu tiên có sẵn:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(() => { Countly.feedback.showNPS("nameTagOrID"); });
Countly.q.push(() => { Countly.feedback.showSurvey("nameTagOrID"); });
Countly.q.push(() => { Countly.feedback.showRating("nameTagOrID"); });

// or
Countly.q.push(["feedback.showNPS", "nameTagOrID"]);
Countly.q.push(["feedback.showSurvey", "nameTagOrID"]);
Countly.q.push(["feedback.showRating", "nameTagOrID"]);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.feedback.showNPS("nameTagOrID");
Countly.feedback.showSurvey("nameTagOrID");
Countly.feedback.showRating("nameTagOrID");
```
{% endtab %}
{% endtabs %}

{% hint style="success" %}
Nếu bạn cần một logic phức tạp hơn để hiển thị các widget, bạn có thể kiểm tra tài liệu mở rộng tại [đây](https://support.countly.com/hc/en-us/articles/360037441932-Web-analytics-JavaScript#h_01JF06CEVMYPPTDFTTTJ2DG7F6).
{% endhint %}

#### Manual Reporting

Việc báo cáo Feedback Widgets thủ công bao gồm 3 bước chính:

1. Lấy danh sách widget từ máy chủ bằng `get_available_feedback_widgets`.
2. Lấy dữ liệu của một widget từ danh sách đó bằng `getFeedbackWidgetData`.
3. Báo cáo kết quả của widget đó bằng `reportFeedbackWidgetManually`.

Tại bước đầu tiên, bằng cách sử dụng hàm `get_available_feedback_widgets`, bạn có thể lấy danh sách các widget khả dụng từ máy chủ dưới dạng một mảng các đối tượng (Array of widget Objects). Hàm này nhận một hàm callback làm tham số; callback này nên có hai tham số: thứ nhất là danh sách được trả về và thứ hai là lỗi. Bên trong callback, bạn nên xử lý mảng đối tượng này và chọn một đối tượng mà bạn muốn báo cáo kết quả. Mảng và các đối tượng này sẽ trông như sau:

```
{
  "result":[
      {
        "_id":"614811419f030e44be07d82f",
        "type":"rating",
        "appearance":{
          "position":"mleft",
          "bg_color":"#fff",
          "text_color":"#ddd",
          "text":"Feedback"
          },
        "tg":["/"],
        "name":"Leave us a feedback"
      },
      {
        "_id":"614811419f030e44be07d839",
        "type":"nps",
        "name":"One response for all",
        "tg":[]
      },
      {
        "_id":"614811429f030e44be07d83d",
        "type":"survey",
        "appearance":{
          "position":"bLeft",
          "show":"uSubmit",
          "color":"#0166D6",
          "logo":null,
          "submit":"Submit",
          "previous":"Previous",
          "next":"Next"
          },
        "name":"Product Feedback example",
        "tg":[]
      }
    ]
  }
```

Tại bước thứ hai, bằng cách sử dụng hàm `getFeedbackWidgetData`, bạn có thể lấy dữ liệu của widget đã chọn. Hàm này có hai tham số: thứ nhất là đối tượng widget lấy được từ bước 1 và thứ hai là một callback với hai tham số (dữ liệu truy xuất được và lỗi).

Bước thứ ba và cũng là bước cuối cùng là báo cáo kết quả cho widget bằng hàm `reportFeedbackWidgetManually`. Hàm này nhận ba tham số: đối tượng widget từ bước 1, dữ liệu widget từ bước 2 và đối tượng kết quả (result object) mà bạn muốn báo cáo. Đối tượng kết quả này sẽ có các cặp key/value khác nhau tùy thuộc vào loại widget; bạn có thể xem giải thích chuyên sâu về cách tạo đối tượng này tại đây.

Ví dụ triển khai các khái niệm trên:

```

// an example of getting the widget list, using it to get widget data and then recording data for it manually. widgetType can be 'nps', 'survey' or 'rating'
function getFeedbackWidgetListAndDoThings(widgetType) {
  // get the widget list
  Countly.get_available_feedback_widgets(
    // callback function, 1st param is the feedback widget list
    function (feedbackList, err) {
      if (err) { // error handling
        console.log(err);
        return;
      }

      // find the widget object with the given widget type. This or a similar implementation can be used while using fetchAndDisplayWidget() as well
      const countlyFeedbackWidget = feedbackList.find(widget = widget.type === widgetType);
      if (!countlyFeedbackWidget) {
        console.error(`[Countly] No ${widgetType} widget found`);
        return;
      }

      // Get data with the widget object
      Countly.getFeedbackWidgetData(CountlyFeedbackWidget,
        // callback function, 1st param is the feedback widget data
        function (feedbackData, err) {
          if (err) { // error handling
            console.error(err);
            return;
          }

          const CountlyWidgetData = feedbackData;
          // record data according to the widget type
          if (CountlyWidgetData.type === 'nps') {
            Countly.reportFeedbackWidgetManually(CountlyFeedbackWidget, CountlyWidgetData, { rating: 3, comment: "comment" });
          } else if (CountlyWidgetData.type === 'survey') {
            var widgetResponse = {};
            // form the key/value pairs according to data
            widgetResponse["answ-" + CountlyWidgetData.questions[0].id] = CountlyWidgetData.questions[0].type === "rating" ? 3 : "answer";
            Countly.reportFeedbackWidgetManually(CountlyFeedbackWidget, CountlyWidgetData, widgetResponse);
          } else if (CountlyWidgetData.type === 'rating') {
            Countly.reportFeedbackWidgetManually(CountlyFeedbackWidget, CountlyWidgetData, { rating: 3, comment: "comment", email: "email", contactMe: true });
          }
        }

      );
    })
}
```

### Consent

Nếu tính năng đồng ý (consents) được bật, để sử dụng Feedback Widgets, bạn phải cung cấp các quyền 'feedback' và 'star-rating' để tính năng này hoạt động.
