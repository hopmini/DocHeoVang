# Personalized Recommendation Features

Recommendation API cung cấp một loạt các khả năng cá nhân hóa giúp điều chỉnh các gợi ý sản phẩm phù hợp với từng người dùng cá nhân. Bằng cách tận dụng hành vi của người dùng như lịch sử duyệt web, hoạt động mua hàng và các tương tác thời gian thực, API mang lại các gợi ý sản phẩm liên quan hơn, giúp tăng mức độ tương tác và tỷ lệ chuyển đổi.

Để kích hoạt tính năng cá nhân hóa, bạn phải bao gồm Insider ID trong các yêu cầu Recommendation API của mình. Điều này cho phép hệ thống liên kết các yêu cầu gửi đến với các hồ sơ người dùng hiện có và áp dụng logic dựa trên hành vi.

Hiện tại, Recommendation API hỗ trợ các tính năng cá nhân hóa sau:

### Personalized Recommendation Algorithms

Các thuật ngữ gợi ý sau đây vốn dĩ đã cung cấp các gợi ý sản phẩm được cá nhân hóa dựa trên dữ liệu hành vi và tương tác của người dùng.

* **User-Based Recommendations:**

&#x20;Tạo các gợi ý cá nhân hóa bằng cách phân tích các tương tác trong quá khứ của người dùng và các kiểu hành vi của những người dùng tương tự.

* **Real-Time User Engagement Recommendations:**

&#x20;Cung cấp các gợi ý cá nhân hóa theo thời gian thực dựa trên hoạt động trong phiên hiện tại và các tương tác trực tiếp của người dùng.

* **Recently Viewed Products:**&#x20;

Gợi ý các sản phẩm dựa trên các lượt xem trang sản phẩm gần đây nhất của người dùng, giúp củng cố ý định duyệt web gần đây.

* **Purchased with Last Purchased:**

&#x20;Gợi ý các sản phẩm thường được mua cùng với lần mua hàng gần đây nhất của người dùng, hỗ trợ các kịch bản bán chéo (cross-sell) hiệu quả.

Các thuật toán này tận dụng cả dữ liệu lịch sử và dữ liệu thời gian thực để đảm bảo các gợi ý luôn phù hợp, kịp thời và phù hợp với sở thích của từng cá nhân.

### Personalization with the User’s Last Visited Item

Recommendation API có thể cá nhân hóa kết quả bằng cách sử dụng trang sản phẩm cuối cùng mà người dùng đã truy cập. Hành vi này áp dụng cho cả thuật toán User-Based Recommendations và Real-Time User Engagement.

Khi API không có đủ dữ liệu ở cấp độ người dùng để tạo ra một gợi ý cá nhân hóa hoàn toàn, nó sẽ tự động chuyển sang cơ chế dự phòng (fallback) là các gợi ý Viewed Together dựa trên sản phẩm được xem gần đây nhất của người dùng.

Cơ chế fallback này đảm bảo rằng các gợi ý vẫn duy trì được sự liên quan và nhận biết được ngữ cảnh, ngay cả khi dữ liệu lịch sử của người dùng bị hạn chế.

### Personalization with Users Recent Interactions

Recommendation API có thể tự động loại trừ các sản phẩm mà người dùng đã tương tác, nhờ đó họ sẽ không thấy các gợi ý cho những mặt hàng họ đã xem, đã mua hoặc đã tương tác theo cách khác.

Sử dụng các tham số sau để kích hoạt các loại trừ này:

| **API Endpoint Parameter** | **Description**                                                                        |
| -------------------------- | -------------------------------------------------------------------------------------- |
| excludeViewItem            | Loại trừ X lượt truy cập sản phẩm cuối cùng của người dùng khỏi phản hồi API           |
| excludeViewDay             | Loại trừ các lượt xem trang sản phẩm của người dùng trong X ngày qua khỏi phản hồi API |
| excludePurchaseItem        | Loại trừ X lượt mua sản phẩm cuối cùng của người dùng khỏi phản hồi API                |
| excludePurchaseDay         | Loại trừ các lượt mua sản phẩm của người dùng trong X ngày qua khỏi phản hồi API       |

Các yêu cầu Recommendation API sau đây minh họa cho các tính năng cá nhân hóa này:

Yêu cầu dưới đây hướng dẫn Recommendation API loại trừ mười sản phẩm cuối cùng mà người dùng đã xem trong các trang chi tiết sản phẩm khỏi phản hồi API:

```
https://recommendationv2.api.useinsider.com/v2/most-popular?details=true&currency=TRY&locale=tr_TR&partnerName=dataforceapi&userId=testUser&excludeViewItem=10
```

Yêu cầu dưới đây hướng dẫn Recommendation API loại trừ các sản phẩm mà người dùng đã mua trong ba ngày qua:

```
https://recommendationv2.api.useinsider.com/v2/most-popular?details=true&currency=TRY&locale=tr_TR&partnerName=dataforceapi&userId=testUser&excludePurchaseDay=3
```

#### Attribute Affinity

Điểm số thuộc tính yêu thích (Attribute affinity scores) đại diện cho sở thích của người dùng đối với các thuộc tính sản phẩm cụ thể dựa trên hành vi tương tác của họ, bao gồm xem sản phẩm, hành động thêm vào giỏ hàng và mua hàng. Các lượt mua hàng có trọng số cao hơn các lượt xem, vì chúng cho thấy ý định mạnh mẽ hơn. Điểm số yêu thích được chuẩn hóa theo tổng hoạt động của người dùng. Kết quả là, một người dùng có tỷ lệ mua hàng trên số lượt xem cao sẽ thể hiện mức độ yêu thích mạnh mẽ hơn so với một người dùng duyệt web thường xuyên nhưng hiếm khi chuyển đổi thành đơn hàng.

Recommendation API sử dụng attribute affinity để cá nhân hóa các gợi ý sản phẩm theo các mối quan tâm đã được chứng minh của từng người dùng. Dữ liệu yêu thích được làm mới hàng ngày, đảm bảo các gợi ý phản ánh hành vi người dùng gần đây và có liên quan.

Trong Recommendation API, tham số endpoint hp kiểm soát việc tính năng Attribute Affinity có được áp dụng hay không. Khi được bật, API sẽ kết hợp điểm số yêu thích thuộc tính của người dùng vào logic gợi ý, ưu tiên các sản phẩm phù hợp với sở thích mạnh mẽ nhất của người dùng.

Yêu cầu Recommendation API sau đây minh họa cách tính năng Attribute Affinity được áp dụng khi tham số hp được bật:

```
https://recommendationv2.api.useinsider.com/v2/most-popular?details=true&currency=TRY&locale
```
