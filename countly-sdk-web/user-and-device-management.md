# User & Device Management

## Device ID Management

### Retrieving Current Device ID

Countly cung cấp một phương thức thuận tiện (`get_device_id`) để bạn lấy Device ID của người dùng hiện tại:

```
var id = Countly.get_device_id();
```

SDK ghi nhận loại ID. Các loại này bao gồm:

* <kbd>DEVELOPER\_SUPPLIED</kbd>
* <kbd>SDK\_GENERATED</kbd>
* <kbd>TEMPORARY\_ID</kbd>

<kbd>DEVELOPER\_SUPPLIED</kbd> nghĩa là ID này được gán bởi logic nội bộ của bạn. <kbd>SDK\_GENERATED</kbd> nghĩa là ID được tạo ngẫu nhiên bởi SDK, và <kbd>TEMPORARY\_ID</kbd> nghĩa là SDK đang ở chế độ ngoại tuyến (offline mode).

Bạn có thể lấy loại Device ID của người dùng bằng cách gọi hàm `get_device_id_type`:

```
var idType = Countly.get_device_id_type();
```

Bạn có thể sử dụng các enum `DeviceIdType` để đánh giá loại Device ID đã lấy được:

```
var idType = Countly.get_device_id_type();
if (idType === Countly.DeviceIdType.SDK_GENERATED) {
  // ...do something
}
```

### Changing Device ID

Bạn có thể thay đổi Device ID của người dùng bằng phương thức `set_id`:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['set_id', "newId"]);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.set_id("newId");
```
{% endtab %}
{% endtabs %}

Ảnh hưởng của phương thức này trên máy chủ sẽ khác nhau tùy thuộc vào loại ID hiện tại được lưu trữ trong SDK tại thời điểm bạn gọi:

1. Nếu ID hiện tại đang lưu trữ là SDK\_GENERATED, thì trên máy chủ, tất cả thông tin được ghi nhận cho Device ID đó sẽ được hợp nhất vào ID mới mà bạn cung cấp, và người dùng cũ với ID SDK\_GENERATED sẽ bị xóa.
2. Nếu ID hiện tại đang lưu trữ là DEVELOPER\_SUPPLIED hoặc TEMPORARY\_ID, thì trên máy chủ, nó cũng sẽ tạo một người dùng mới với ID mới này nếu nó chưa tồn tại.

Nếu bạn cần một logic phức tạp hơn, bạn sẽ cần sử dụng phương thức được đề cập tại đây để thay thế.

{% hint style="success" %}
LƯU Ý: Lời gọi hàm sẽ từ chối các giá trị Device ID không hợp lệ. Một giá trị hợp lệ là không null, không undefined, có kiểu dữ liệu là chuỗi (string) và không phải là chuỗi rỗng.
{% endhint %}

### Temporary Device ID (Offline mode)

Có một số trường hợp bạn muốn SDK thu thập dữ liệu nhưng không gửi về máy chủ cho đến một thời điểm nhất định. Ngoài ra, chế độ này cho phép bạn trì hoãn việc cung cấp thuộc tính `device_id` cho đến sau này.

Ví dụ: nếu bạn muốn theo dõi người dùng bằng một `device_id` tùy chỉnh, chẳng hạn như ID khách hàng nội bộ, và bạn chỉ nhận được giá trị đó ngay khi người dùng đăng nhập. Tuy nhiên, bạn cũng muốn theo dõi những gì người dùng đã làm trước khi đăng nhập.

Việc sử dụng chế độ ngoại tuyến trong ngữ cảnh này cho phép bạn bỏ qua việc hợp nhất người dùng và các chi phí vận hành máy chủ đi kèm, bao gồm cả bất kỳ dữ liệu tổng hợp nào có thể bị sai lệch.

{% hint style="warning" %}
Nếu chế độ ngoại tuyến được kích hoạt và tính năng đồng ý (consent) đã được bật, tất cả các sự đồng ý đã cung cấp trước đó sẽ bị xóa. Điều này có nghĩa là tất cả các tính năng sẽ ngừng hoạt động cho đến khi sự đồng ý mới được cung cấp lại. Do đó, sau khi vào chế độ ngoại tuyến, bạn nên thiết lập lại sự đồng ý.
{% endhint %}

Để khởi chạy SDK ở chế độ ngoại tuyến, chỉ cần cung cấp giá trị cấu hình `offline_mode` là `true`. Tại thời điểm này, bạn có thể bỏ qua việc cung cấp giá trị `device_id` nếu muốn.

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.debug = false;
Countly.app_key = "YOUR_APP_KEY";
Countly.url = "https://try.count.ly";
Countly.offline_mode = true;

Countly.init();
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.init({
  debug:false,
  app_key:"YOUR_APP_KEY",
  url: "https://try.count.ly",
  offline_mode: true
});
```
{% endtab %}
{% endtabs %}

Hoặc bạn có thể bật chế độ ngoại tuyến tại bất kỳ thời điểm nào sau đó trong SDK:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['enable_offline_mode']);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.enable_offline_mode();
```
{% endtab %}
{% endtabs %}

Nếu bạn muốn tắt chế độ ngoại tuyến và tùy chọn cung cấp `device_id` vào lúc khác, bạn có thể làm như sau:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['disable_offline_mode', device_id]);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.disable_offline_mode(device_id);
```
{% endtab %}
{% endtabs %}

### Device ID Generation

Theo mặc định, Countly tạo và gán một Device ID ngẫu nhiên (UUID) cho mỗi thiết bị truy cập trang web của bạn. ID này được lưu tại `localstorage` của trình duyệt. Giả định rằng `localstorage` không bị xóa, thiết bị này sẽ được nhận diện bằng ID đã lưu trữ này từ thời điểm đó trở đi.

Hạn chế của phương pháp này:

* Nhiều người dùng sử dụng cùng một thiết bị sẽ được coi là một người dùng duy nhất.
* Khi một người dùng truy cập trang web của bạn từ nhiều thiết bị hoặc trình duyệt ở chế độ ẩn danh, tất cả các thiết bị sẽ được coi là những người dùng riêng biệt.

Nếu những kịch bản này là vấn đề đáng quan ngại, bạn có thể giảm thiểu chúng bằng các chiến lược quản lý thiết bị khác nhau như:

* Thêm trang đăng nhập/xác thực và gán Device ID tại đó.
* Chế độ ngoại tuyến (đã đề cập ở trên) cho đến khi bạn xác định được người dùng và gán ID sau đó.

Bạn cũng có thể cung cấp một ID trong quá trình khởi tạo (`init`) để ngăn SDK tạo ID ngẫu nhiên:

```
// adding device ID here will prevent the generation of a random ID
Countly.init({
  app_key:"YOUR_APP_KEY",
  url: "https://your.server",
  device_id:"yourDeviceID",
});
```

Bạn cũng có thể cung cấp một ID trong truy vấn tìm kiếm (search query) của liên kết mở trang web của bạn. Nếu bạn sử dụng tham số `cly_device_id`, giá trị của nó sẽ được đặt làm Device ID thay vì tạo một ID ngẫu nhiên:

```
// you can assign a device ID to a user through a link that you have provided to them by adding cly_device_id to your url query
yoursite.com + ?cly_device_id=yourDeviceID
```

Nếu bạn muốn xóa Device ID đã lưu trữ trước đó khỏi bộ nhớ, bạn có thể đặt cờ `clear_stored_id` thành `true` trong cấu hình khởi tạo:

JavaScript

```
// this will erase the stored device ID from the local storage every time the Countly is initialized
Countly.init({
  app_key:"YOUR_APP_KEY",
  url: "https://try.count.ly",
  clear_stored_id: true,
});
```

{% hint style="success" %}
#### Device ID Priority

Nếu bạn đã sử dụng nhiều phương pháp để đặt Device ID cho người dùng trong lần khởi tạo đầu tiên, Countly sẽ dựa vào hệ thống thứ tự ưu tiên của Device ID để gán ID chính xác cho người dùng. Thứ tự ưu tiên như sau:

ID được đặt từ **ID > Developer set ID > Temp ID (offline mode) > SDK generated ID**
{% endhint %}

## User Location

Với SDK được tích hợp vào trang web, bạn có thể muốn theo dõi vị trí của người dùng. Theo mặc định, Countly Server sử dụng cơ sở dữ liệu GeoIP để suy ra vị trí của người dùng từ các yêu cầu gửi đến. Tuy nhiên, SDK cung cấp khả năng thiết lập vị trí tùy chỉnh cho người dùng của bạn.

### Setting Location

Có 3 tham số vị trí có thể được cung cấp cùng với SDK:

* Mã quốc gia theo tiêu chuẩn ISO hai chữ cái ("jp", "gr", v.v.)
* Tên thành phố ("Kyoto", "Athens", v.v.)
* Địa chỉ IP của người dùng

Bạn có thể cung cấp bất kỳ thông tin nào trong số này khi khởi tạo SDK:

{% tabs %}
{% tab title="Asynchronous" %}
```
// you can directly provide the city and the country code
Countly.city = "Tokyo";
Countly.country_code = "jp";
// or you can provide the ip address
Countly.ip_address = "198.168.1.1";
     
// Initialize the SDK
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.init({
  app_key: "YOUR_APP_KEY",
  url: "https://your.server.ly",
  //  you can directly provide the city and the country code
  city: "Tokyo",
  country_code: "jp",
  // or you can provide the ip address    
  ip_address: "198.168.1.1",
});
```
{% endtab %}
{% endtabs %}

## User Profiles

### User Details

Bạn có thể cung cấp cho Countly các thông tin người dùng như tên đăng nhập (username) hoặc địa chỉ email. Điều này cho phép bạn phân biệt người dùng trên tab "User Profiles", tính năng này khả dụng với phiên bản Countly Enterprise Edition.

{% hint style="warning" %}
Nếu một tham số được đặt là một chuỗi rỗng, tham số đó sẽ bị xóa trên phía máy chủ.
{% endhint %}

Danh sách các tham số bạn có thể truyền vào:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['user_details',{
  "name": "Arturs Sosins",
  "username": "ar2rsawseen",
  "email": "test@test.com",
  "organization": "Countly",
  "phone": "+37112345678",
  //Web URL to picture
  "picture": "https://pbs.twimg.com/profile_images/1442562237/012_n_400x400.jpg", 
  "gender": "M",
  "byear": 1987, //birth year
  "custom":{
    "key1":"value1",
    "key2":"value2",
    ...
  }
}]);
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.user_details({
  "name": "Arturs Sosins",
  "username": "ar2rsawseen",
  "email": "test@test.com",
  "organization": "Countly",
  "phone": "+37112345678",
  //Web URL pointing to user picture
  "picture": "https://pbs.twimg.com/profile_images/1442562237/012_n_400x400.jpg", 
  "gender": "M",
  "byear": 1987, //birth year
  "custom":{
    "key1":"value1",
    "key2":"value2",
    ...
  }
 });
```
{% endtab %}
{% endtabs %}

Để tải trực tiếp tệp ảnh đại diện của người dùng:

{% tabs %}
{% tab title="Asynchronous" %}
```
// assuming you have an input element that accepts images
var fileInput = document.getElementById("profilePicInput");
var file = fileInput.files[0];
if (!file) {
    alert("Please select an image file.");
    return;
}
Countly.q.push(["uploadUserProfilePicture", file]);
```
{% endtab %}

{% tab title="Synchronous" %}
```
// assuming you have an input element that accepts images
var fileInput = document.getElementById("profilePicInput");
var file = fileInput.files[0];
if (!file) {
    alert("Please select an image file.");
    return;
}
Countly.uploadUserProfilePicture(file);
```
{% endtab %}
{% endtabs %}

### Modifying Custom Data

Ngoài ra, bạn có thể thực hiện các thao tác khác nhau trên các giá trị dữ liệu tùy chỉnh, chẳng hạn như tăng giá trị hiện tại trên máy chủ hoặc lưu trữ một mảng các giá trị dưới cùng một thuộc tính.

Sau khi sử dụng các trình sửa đổi (modifiers), đừng quên gọi `userData.save` để gửi dữ liệu đến máy chủ.

Danh sách các phương thức khả dụng có thể tìm thấy bên dưới:

{% tabs %}
{% tab title="Asynchronous" %}
```
Countly.q.push(['userData.set', key, value]) //set custom property
Countly.q.push(['userData.unset', key]) //remove custom property
Countly.q.push(['userData.set_once', key, value]) //set custom property only if property does not exist
Countly.q.push(['userData.increment', key]) //increment value in key by one
Countly.q.push(['userData.increment_by', key, value]) //increment value in key by provided value
Countly.q.push(['userData.multiply', key, value]) //multiply value in key by provided value
Countly.q.push(['userData.max', key, value]) //save max value between current and provided
Countly.q.push(['userData.min', key, value]) //save min value between current and provided
Countly.q.push(['userData.push', key, value]) //add value to key as array element
Countly.q.push(['userData.push_unique', key, value]) //add value to key as array element, but only store unique values in array
Countly.q.push(['userData.pull', key, value]) //remove value from array under property with key as name
Countly.q.push(['userData.save']) //send userData to server
```
{% endtab %}

{% tab title="Synchronous" %}
```
Countly.userData.set(key, value) //set custom property
Countly.userData.set_once(key, value) //set custom property only if property does not exist
Countly.userData.increment(key) //increment value in key by one
Countly.userData.increment_by(key, value) //increment value in key by provided value
Countly.userData.multiply(key, value) //multiply value in key by provided value
Countly.userData.max(key, value) //save max value between current and provided
Countly.userData.min(key, value) //save min value between current and provided
Countly.userData.push(key, value) //add value to key as array element
Countly.userData.push_unique(key, value) //add value to key as array element, but only store unique values in array
Countly.userData.pull(key, value) //remove value from array under property with key as name
Countly.userData.save() //send userData to server
```
{% endtab %}
{% endtabs %}

### Orientation Tracking

Tính năng theo dõi hướng thiết bị (Orientation tracking) được bật theo mặc định và sẽ được gửi nếu có sự đồng ý (consent) "user" bắt buộc (nếu tính năng consent được bật). Countly sẽ báo cáo hướng thiết bị khi phiên bắt đầu và tại bất kỳ thời điểm nào hướng thiết bị thay đổi.

Bạn có thể tắt tính năng theo dõi hướng thiết bị bằng cách cung cấp cài đặt `enable_orientation_tracking` khi khởi tạo SDK như sau:

{% tabs %}
{% tab title="Asynchronous" %}
```
// in your Countly init script
Countly.enable_orientation_tracking = false;
```
{% endtab %}

{% tab title="Synchronous" %}
```
//to disable orientation tracking
Countly.init({
  app_key:"YOUR_APP_KEY",
  url: "https://try.count.ly",
  enable_orientation_tracking: false //this will disable orientation tracking
});
```
{% endtab %}
{% endtabs %}
