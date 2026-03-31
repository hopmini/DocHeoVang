# Get Recommendations

## Recommendation API (SR-API)

Recommendation API (SR-API) là một dịch vụ RESTful dùng để truy xuất các gợi ý sản phẩm được cá nhân hóa thông qua lập trình. Nó đóng vai trò là giao diện kết nối với công cụ gợi ý của Insider One, sử dụng các mô hình học máy (machine learning), phân tích hành vi người dùng theo thời gian thực và dữ liệu về mối tương quan sản phẩm để trả về các tập hợp mục (item sets) có cấu trúc.

### Endpoint (Điểm cuối)

Recommendation API tuân theo cấu trúc RESTful tiêu chuẩn:

GET `https://recommendation.api.useinsider.com/v2/{algorithm-name}`

Truy cập bộ sưu tập Postman của chúng tôi để kiểm tra yêu cầu này.

`{algorithm-name}` là định danh duy nhất cho logic gợi ý sẽ được thực thi.

Ví dụ:

* `/v2/most-popular` - Các sản phẩm được xem nhiều nhất
* `/v2/user-based` - Gợi ý cá nhân hóa cho người dùng
* `/v2/purchased-together` - Các mục thường được mua cùng nhau

Tham khảo phần Algorithm Descriptions (Mô tả Thuật toán) để xem danh sách đầy đủ.

Trước khi thực hiện yêu cầu đầu tiên, bạn sẽ cần:

API Token - Được sử dụng để xác thực các yêu cầu của bạn đối với Smart Recommender API.

Tất cả các yêu cầu đều yêu cầu API token của bạn trong tiêu đề (header) yêu cầu.

Custom

`X-Auth-Token: YOUR_API_TOKEN`

Partner ID/Name - Định danh cửa hàng của bạn

Locale - Mã ngôn ngữ và khu vực được sử dụng cho bối cảnh gợi ý (ví dụ: en\_US, fr\_FR)

Currency - Mã tiền tệ (ví dụ: USD, EUR)

### Algorithm Abbreviations (Các từ viết tắt thuật toán)

Mỗi thuật toán có một từ viết tắt ngắn được sử dụng trong nội dung phản hồi (response payloads):

* mvop = Most Popular Items (Các mục phổ biến nhất)
* ub = User Based (Dựa trên người dùng)
* btb = Purchased Together (Mua cùng nhau)
* chef = Chef (Bộ chọn chiến lược tự động)

Tham khảo phần Algorithm Descriptions để xem danh sách đầy đủ.

### Query Parameters (Tham số truy vấn)

Tất cả các điểm cuối đều hỗ trợ các tham số chung:

| **Tham số**    | **Mục đích**                        | **Kiểu dữ liệu** | **Bắt buộc** |
| -------------- | ----------------------------------- | ---------------- | ------------ |
| `partnerName`  | Định danh cửa hàng của bạn          | String           | Có           |
| `locale`       | Ngôn ngữ/khu vực (ví dụ: en\_US)    | String           | Có           |
| `currency`     | Mã tiền tệ (ví dụ: USD)             | String           | Có           |
| `userId`       | Định danh người dùng để cá nhân hóa | String           | Không        |
| `categoryList` | Lọc theo danh mục sản phẩm          | Array            | Không        |
| `filter`       | Lọc nâng cao (xem Filtering Guide)  | String           | Không        |
| `size`         | Số lượng sản phẩm (0-100)           | Integer          | Không        |
| `details`      | Bao gồm đầy đủ thông tin sản phẩm   | Boolean          | Không        |

### Sample Request (Yêu cầu mẫu)

Curl

```
curl -X GET "https://recommendation.api.useinsider.com/v2/most-popular?partnerName=yourpartnername&locale=tr_TR&size=1&details=true" \
                    -H "X-Auth-Token: YOUR_API_TOKEN"
```

JavaScript

```
const apiToken = 'YOUR_API_TOKEN';
                    const endpoint = 'https://recommendation.api.useinsider.com/v2/most-popular';
                    const params = {
                    partnerName: 'yourPartnerName',
                    locale: 'tr_TR',
                    size: 1,
                    details: true
                    };
                    const queryString = new URLSearchParams(params).toString();
                    fetch(`${endpoint}?${queryString}`, {
                    method: 'GET',
                    headers: {
                    'X-Auth-Token': apiToken
                    }
                    })
                    .then(response => response.json())
```

Python

```
import requests
                    api_token = 'YOUR_API_TOKEN'
                    endpoint = 'https://recommendation.api.useinsider.com/v2/most-popular'

                    params = {
                    'partnerName': 'yourPartnerName',
                    'locale': 'tr_TR',
                    'size': 1,
                    'details': True
                    }

                    headers = {
                    'X-Auth-Token': api_token
                    }

                    response = requests.get(endpoint, params=params, headers=headers)
                    data = response.json()
                    print(data)
```

### Sample Response (Phản hồi mẫu)

Các phản hồi tuân theo cấu trúc JSON nhất quán:

```
{
        "success": true,
        "total": 10,
        "types": {
        "mvop": 10
        },
        "data": ["productId1", "productId2", "..."]
}
```

Với `details=true`, mảng `data` chứa đầy đủ các đối tượng sản phẩm bao gồm giá cả, hình ảnh, danh mục và các thuộc tính.

#### 200 / Success-OK

Một phản hồi thành công sẽ trông như thế này:

```
{
        "success": true,
        "total": 1,
        "types": {
        "mvop": 1
        },
        "data": ["SKU-DK-0011"]
}
```

| **Trường** | **Ý nghĩa**                                                    |
| ---------- | -------------------------------------------------------------- |
| `success`  | Giá trị Boolean cho biết yêu cầu có thành công hay không       |
| `total`    | Số lượng sản phẩm được trả về                                  |
| `types`    | (Các) thuật toán được sử dụng (mvop = Most Popular of Partner) |
| `data`     | Mảng các ID sản phẩm                                           |

### Getting Product Details (Lấy thông tin chi tiết sản phẩm)

Theo mặc định, API chỉ trả về các ID sản phẩm. Để lấy đầy đủ thông tin sản phẩm (hình ảnh, giá cả, mô tả), hãy thêm `details=true`.

Curl

```
curl -X GET "https://recommendation.api.useinsider.com/v2/most-popular?partnerName=yourpartnername&locale=tr_TR&size=1&details=true" \
                    -H "X-Auth-Token: YOUR_API_TOKEN"
```

JavaScript

```
const params = {
                    partnerName: 'yourpartnername',
                    locale: 'tr_TR',
                    size: 1,
                    details: true
                    };
```

Python

```
params = {
                    'partnerName': 'yourpartnername',
                    'locale': 'tr_TR',
                    'size': 1,
                    'details': True
                    }
```

Phản hồi với `details=true` bao gồm:

* `image_url` - Hình ảnh sản phẩm
* `name` - Tên sản phẩm
* `price` - Giá sản phẩm (theo đơn vị tiền tệ)
* `category` - Các danh mục sản phẩm
* `discount` - Thông tin giảm giá
* `in_stock` - Trạng thái kho hàng

Dưới đây là một ví dụ đầy đủ với `details=true`:

```
{
        "success": true,
        "total": 1,
        "types": {
        "mvop": 1
        },
        "data": [
        {
        "item_id": "SKU-HA-0011",
        "name": "Hummingbird Decorative Cushion",
        "locale": "en_US",
        "image_url": "https://cdn.demo-shop.com/images/home-accessories/hummingbird-cushion-45x45.jpg",
        "url": "https://www.demo-shop.com/en/home-accessories/hummingbird-decorative-cushion?ins_sr=eyJwcm9kdWN0SWQiOiJTS1UtSEEtMDAxMSJ9",
        "in_stock": 1,
        "price": {
        "USD": 24.99
        },
        "original_price": {
        "USD": 29.99
        },
        "discount": {
        "USD": 5.0
        },
        "category": [
        "Home Accessories",
        "Decorative Cushions"
        ],
        "description": "Soft cotton decorative cushion with hummingbird pattern. Ideal for living rooms and bedrooms.",
        "brand": "Demo Home",
        "color": "Multicolor",
        "size": "45x45 cm",
        "tags": [
        "cushion",
        "home-decor",
        "living-room"
        ],
        "material_type": "Cotton",
        "washable": "Yes",
        "room": "Living Room"
        }
        ]
}
```

### API Rate Limits (Giới hạn tần suất API)

Giới hạn tần suất cho các lệnh gọi API trực tiếp được thiết lập là 1000 cuộc gọi mỗi phút, áp dụng cho tất cả các điểm cuối của Recommendation API. Việc vượt quá giới hạn này sẽ khiến các yêu cầu bổ sung trả về mã trạng thái 429.

