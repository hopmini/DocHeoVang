# Best Practices for Recommendation API

### Category List Format

Khi lọc theo danh mục, hãy truyền `categoryList` dưới dạng một mảng JSON đã được mã hóa URL (URL-encoded):

```
categoryList=["shoes","boots"]
```

Trong cURL, hãy mã hóa các dấu ngoặc một cách chính xác:

```
?categoryList=%5B%22shoes%22%2C%22boots%22%5D
```

### Hyperpersonalization (hp)

Kích hoạt tính năng siêu cá nhân hóa (hyperpersonalization) bằng cách thêm `hp=1` hoặc `hp=true`:

```
?userId=user123&hp=1
```

Tính năng này sử dụng các điểm tương đồng/sở thích (affinities) của khách hàng để cá nhân hóa kết quả sâu hơn.

### Mixed Strategy

Kết hợp nhiều thuật toán gợi ý trong một yêu cầu duy nhất để nhận được các kết quả đa dạng.

```
GET /mixed?partnerName=X&locale=en_US&userId=user1&currency=USD&strategy=[...]
```

Định dạng mảng chiến lược (Strategy array format):

```
[
  {"recommendationType": "ub", "size": 4},
  {"recommendationType": "vtv", "size": 3, "productId": "prod1"},
  {"recommendationType": "mvop", "size": 3, "filters": ["\[category\]\[=\]\[shoes\]"]}
]
```

Các loại (types) có sẵn: `ub`, `ue`, `vtv`, `btb`, `cp`, `sp`, `mvoc`, `mvop`, `mpoc`, `mpop`, `mpol`, `naoc`, `naop`, `tpoc`, `tpop`, `mvpoc`, `mvpop`, `mm`, `hdop`, `hdoc`, `rvp`, `lpt`. Mỗi chiến lược chỉ định loại thuật toán của nó, số lượng sản phẩm cần trả về và các bộ lọc riêng (tùy chọn).

### Manual Merchandising in Mixed

Sử dụng `mm` để bao gồm các sản phẩm cụ thể mà bạn muốn quảng bá. Trường `productId` là bắt buộc.

```
{"recommendationType": "mm", "size": 3, "productId": "featured1,featured2,featured3"}
```

Ví dụ đầy đủ:

```
GET /mixed?partnerName=X&locale=en_US&currency=TRY&strategy=[{"recommendationType":"ub","size":5},{"recommendationType":"mm","size":3,"productId":"promo1,promo2,promo3"},{"recommendationType":"mvop","size":2}]
```

* Truyền các ID sản phẩm dưới dạng các giá trị phân tách bằng dấu phẩy.
* Sản phẩm được trả về theo thứ tự bạn chỉ định.
* Chỉ các sản phẩm còn hàng mới được trả về.

### Multiple Item IDs in Item-Based Algorithms

Lấy gợi ý dựa trên nhiều sản phẩm cùng lúc bằng cách truyền các ID phân cách bằng dấu phẩy:

```
GET /vtv?partnerName=X&locale=en_US&productId=item1,item2,item3&size=10&currency=USD
```

Các endpoint hỗ trợ là `/vtv`, `/btb`, `/cp`, `/sp`. Điều này hữu ích khi bạn muốn đưa ra gợi ý dựa trên nhiều mặt hàng trong giỏ hàng hoặc danh sách yêu thích (wishlist).

### Group Products

Bao gồm các biến thể sản phẩm (ví dụ: các kích thước/màu sắc khác nhau) trong phản hồi bằng cách sử dụng `getGroupProducts`.

```
GET /ub?partnerName=X&locale=en_US&userId=user1&getGroupProducts=true&groupProductsFields=price,in_stock,product_attributes.color
```

Tham số (Parameters)

* `getGroupProducts=true` — Kích hoạt các sản phẩm biến thể trong phản hồi.
* `groupProductsFields` — Các trường bổ sung cần bao gồm (phân tách bằng dấu phẩy).

Cấu trúc phản hồi (Response structure)

```
{
  "item_id": "shoe-blue-m",
  "group_products": [
    {"item_id": "shoe-blue-s", "price": {"USD": 99}, "in_stock": 1},
    {"item_id": "shoe-blue-l", "price": {"USD": 99}, "in_stock": 1}
  ]
}
```

Các biến thể được nhóm theo `groupcode`. Bạn có thể yêu cầu các trường lồng nhau, chẳng hạn như `product_attributes.color`.

### Filter Chaining with asteriks (\*)

Để kết hợp nhiều bộ lọc với logic AND, hãy sử dụng dấu `*`. Dưới đây là một ví dụ về việc áp dụng nhiều bộ lọc:

```
GET ...&filter=[category][=][shoes]*[brand][=][nike]*[price][<][100]
```
