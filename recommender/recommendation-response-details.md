# Recommendation Response Details

Bảng dưới đây trình bày các mã trạng thái và loại phản hồi từ Recommendation API. Bảng liệt kê các Mã trạng thái (Status Codes), mô tả của chúng và các kịch bản mà bạn có thể nhận được các mã trạng thái này.

<table data-header-hidden><thead><tr><th width="262"></th><th></th></tr></thead><tbody><tr><td><strong>Status Code</strong></td><td><strong>Status Code Scenarios</strong></td></tr><tr><td>200 - Success</td><td>Các yêu cầu API thành công nhận phản hồi với mã trạng thái 200.</td></tr><tr><td>400 - Bad Request</td><td>Các yêu cầu API không thành công nhận phản hồi với mã trạng thái 400. Các cuộc gọi API thiếu tham số endpoint nhận mã trạng thái này.</td></tr><tr><td>403 - Forbidden</td><td>Các yêu cầu API không được phép nhận phản hồi với mã trạng thái 403. Các cuộc gọi API dẫn đến việc Xác thực Origin/CORS không thành công nhận mã trạng thái này.</td></tr><tr><td>422 - Unprocessible Content</td><td>Các cuộc gọi API thiếu nội dung bộ lọc động (dynamic filter) nhận mã trạng thái này. Tham khảo tài liệu Filtering Products để biết thêm chi tiết.</td></tr><tr><td>429 - Too Many Requests</td><td>Các yêu cầu API bị giới hạn lưu lượng (throttled) nhận phản hồi với mã trạng thái 429.</td></tr></tbody></table>

### 200 / Success-OK

Các yêu cầu Recommendation API thành công sẽ nhận được một phản hồi endpoint chứa các trường sau:

* <kbd>success</kbd>: trường xác định sự thành công của phản hồi API.
* <kbd>total</kbd>: trường hiển thị số lượng gợi ý được trả về từ phản hồi API.
* <kbd>types</kbd>: trường liệt kê các loại thuật toán gợi ý được trả về từ endpoint.
* <kbd>data</kbd>: trường liệt kê các gợi ý và chi tiết của chúng.

Dưới đây là một yêu cầu Recommendation API thành công và phản hồi ví dụ:

Sample Request

```
https://recommendationv2.api.useinsider.com/v2/most-popular?details=true&partnerName=yourPartnerName&locale=en_US&currency=USD&size=1
```

Sample Response

```
{
    "success": true,
    "total": 1,
    "types": {
        "mvop": 1
    },
    "data": [
        {
            "image_url": "http://insiderone.com/img/p/1/3/13.jpg",
            "name": "Hummingbird cushion",
            "item_id": "11",
            "url": "https://insiderone.com/home-accessories/11-hummingbird-cushion.html#ins_sr=eyJwcm9kdWN0SWQiOiIxMSJ9",
            "description": "Hummingbird cushion in category Home Accessories",
            "in_stock": 1,
            "price": {
                "USD": 0.57
            },
            "locale": "en_US",
            "product_attributes": {
                "test": "productTest",
                "testattributes": "productTest"
            },
            "category": [
                "Home Accessories"
            ],
            "discount": {
                "USD": 0.0
            },
            "original_price": {
                "USD": 0.57
            }
        }
    ]
}
```

### 400 / Bad Request

Trong các kịch bản sau, Recommendation API trả về phản hồi với mã trạng thái 400:

* Thiếu các tham số endpoint bắt buộc.
* Thiếu giá trị của các tham số endpoint bắt buộc.
* Sử dụng sai các tham số endpoint.

Các yêu cầu và phản hồi API sau đây minh họa các ví dụ về những kịch bản này.

Sample Request 1

Tham số `partnerName` bị thiếu trong yêu cầu dưới đây:

```
https://recommendationv2.api.useinsider.com/v2/most-popular?details=true&currency=USD&locale=en_US
```

Sample Response 1

```
{
    "success": false,
    "message": "Missing parameter: partnerName",
    "data": []
}
```

Sample Request 2

Giá trị `locale` bị thiếu trong yêu cầu dưới đây:

```
https://recommendationv2.api.useinsider.com/v2/most-popular?details=true&currency=USD&locale=&partnerName=yourPartnerName
```

Sample Response 2

```
{
    "success": false,
    "message": "Locale is invalid.",
    "data": []
}
```

### 403 / Forbidden

Recommendation API thực hiện xác thực nguồn gửi (sender origin validation) cho các đối tác kích hoạt tính năng Xác thực Origin/CORS. Khi tính năng này được bật, Recommendation API chỉ cung cấp nội dung gợi ý thành công cho các trình gọi từ các miền được phép. Các yêu cầu từ các miền không có trong danh sách được phép sẽ nhận được lỗi với mã trạng thái 403.

Dưới đây là phản hồi của Recommendation API cho các xác thực không thành công:

```
{
    "success": false,
    "message": "Origin validation error.",
    "data": []
}
```

### 422 / Unprocessible Content

Mã trạng thái phản hồi endpoint này thường nhận được khi trang của đối tác lưu trữ chiến dịch gợi ý không thể cung cấp các chi tiết mà endpoint của Recommendation API cần sử dụng.

Ví dụ về một kịch bản:

* Bạn tạo và kích hoạt một chiến dịch Web Smart Recommender trên các Trang Sản phẩm có sử dụng Bộ lọc động (Dynamic Filtering) trên trường màu sắc (color).
* Trong Bộ lọc động, các giá trị thuộc tính của các trường được sử dụng trong bộ lọc được lấy từ Recommendation API.
* Do đó, các web client không được thông báo liệu thuộc tính được yêu cầu có hiện diện cho sản phẩm hiện tại hay không.
* Trong trường hợp giá trị thuộc tính được sử dụng trong Bộ lọc động bị thiếu, Recommendation API không thể thực hiện Bộ lọc động.
* API sẽ trả về mã trạng thái 422 cho những trường hợp đó.

Sample Request

```
https://recommendationv2.api.useinsider.com/v2/most-popular?details=true&filter=([color][=][${value}])&currency=TRY&locale=tr_TR&partnerName=yourPartnerName&productId=11
```

Sample Response

Thuộc tính màu sắc (color) bị thiếu đối với mặt hàng có ID “11”. Khi thuộc tính này được yêu cầu với bộ lọc động, Recommendation API phản hồi bằng mã trạng thái 422 (biểu thị rằng có nội dung không thể xử lý được).

```
{
    "success": false,
    "message": "The field 'color' in dynamic filter was not found in the product.",
    "data": []
}
```

### 429 / Too Many Requests

Khi bạn vượt quá giới hạn tốc độ (rate limit), Recommendation API sẽ tạm thời hạn chế lưu lượng (throttle) các yêu cầu của bạn. Giới hạn tốc độ được tính toán bằng cách sử dụng cửa sổ trượt một phút (rolling one-minute window), vì vậy nếu bạn bị hạn chế, bạn sẽ lấy lại được quyền truy cập sau khi cửa sổ hiện tại được đặt lại (tối đa là một phút).

```
{
    "success": false,
    "message": "Rate exceeded.",
    "data": []
}
```
