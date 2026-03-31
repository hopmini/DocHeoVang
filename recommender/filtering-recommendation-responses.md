# Filtering Recommendation Responses

Bộ lọc (Filtering) cho phép bạn tinh chỉnh các kết quả gợi ý để phù hợp với nhu cầu và sở thích của người dùng. Các trường hợp sử dụng phổ biến bao gồm:

* Loại trừ các sản phẩm đã xem - Không gợi ý những sản phẩm mà người dùng đã thấy.
* Lọc theo khoảng giá - Chỉ hiển thị các sản phẩm trong ngân sách.
* Lọc theo danh mục/thương hiệu - Tập trung vào các loại sản phẩm cụ thể.
* Lọc theo kho hàng - Chỉ gợi ý các mặt hàng còn hàng.
* Lọc theo thuộc tính - Lọc theo các thuộc tính sản phẩm tùy chỉnh (màu sắc, kích cỡ, xếp hạng, v.v.).

Việc này giúp tăng độ tương quan, cải thiện trải nghiệm người dùng và thúc đẩy tỷ lệ chuyển đổi.

#### Basic Syntax

```
https://recommendation.api.useinsider.com/v2/{algorithm}?...\&filter=[{field}][{operator}][{value}]
```

Dưới đây là một ví dụ với một bộ lọc đơn lẻ trả về các sản phẩm có giá lớn hơn 100 USD:

```
?filter=[price.USD][>][100]
```

#### Multiple Filters

Bạn có thể truyền nhiều tham số bộ lọc để kết hợp các điều kiện. Mỗi điều kiện được liên kết với nhau thông qua dấu sao (\*).

Syntax

```
?filter=[field1][operator1][value1]*[field2][operator2][value2]
```

Cách hoạt động của Multiple Filters

Nhiều bộ lọc được kết hợp với logic AND - sản phẩm phải khớp với TẤT CẢ các điều kiện để được bao gồm.

* Ví dụ 1: Loại trừ sản phẩm hiện tại + lọc theo danh mục

```
?filter=[item_id][!=][PRODUCT_ID]*[category][~][Shoes]
```

Bộ lọc này trả về các sản phẩm KHÔNG PHẢI là PRODUCT\_ID VÀ có chứa "Shoes" trong danh mục.

* Ví dụ 2: Khoảng giá + còn hàng

```
?filter=[price.USD][>][50]*[price.USD][<][200]*[in_stock][=][1]
```

Bộ lọc này trả về các sản phẩm có giá từ $50-$200 VÀ còn hàng.

#### Operator Reference

| **Tên toán tử**       | **Ký hiệu** | **Tên thay thế** | **Mô tả**                                                                              | **Ví dụ**                                              |
| --------------------- | ----------- | ---------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Equal To              | `=`         | `is`             | Khớp chính xác giá trị trường                                                          | `[color][=][Blue]` hoặc `[brand][=][Niki]`             |
| Not Equal To          | `!=`        | `nis`            | Loại trừ các sản phẩm có giá trị trường khớp                                           | `[color][!=][Red]` (loại trừ sản phẩm màu đỏ)          |
| Greater Than          | `>`         | `gt`             | Giá trị trường lớn hơn giá trị được chỉ định                                           | `[price.USD][>][100]` hoặc `[rating][>][3.5]`          |
| Greater Than or Equal | `>=`        | `gte`            | Giá trị trường lớn hơn hoặc bằng giá trị được chỉ định                                 | `[price.EUR][>=][150]` hoặc `[rating][>=][4]`          |
| Less Than             | `<`         | `lt`             | Giá trị trường nhỏ hơn giá trị được chỉ định                                           | `[price.EUR][<][150]` hoặc `[stock_count][<][5]`       |
| Less Than or Equal    | `<=`        | `lte`            | Giá trị trường nhỏ hơn hoặc bằng giá trị được chỉ định                                 | `[price.EUR][<=][150]` hoặc `[created_at][<=][now-7d]` |
| Contains              | `~`         | `ctn`            | Trường chứa giá trị được chỉ định (tìm kiếm văn bản)                                   | `[category][~][Shoes]` hoặc `[name][~][Niki Air]`      |
| Does Not Contain      | `!~`        | `nctn`           | Trường không chứa giá trị được chỉ định                                                | `[category][!~][Clearance]`                            |
| Between               | `><`        | `btw`            | Giá trị trường nằm trong một khoảng. Định dạng: `[field][><][lower_bound:upper_bound]` | `[price.USD][><][50:200]`                              |
| Not Between           | `>!<`       | `nbtw`           | Giá trị trường nằm ngoài một khoảng                                                    | `[price.USD][>!<][50:200]` (loại trừ khoảng $50-$200)  |
| Exists                | `?`         | `xst`            | Kiểm tra xem một trường có tồn tại (value=1) hay không tồn tại (value=0)               | `[price.USD][?][1]` (sản phẩm có giá USD)              |

#### Date Field Values

Insider One được thiết kế để phân tích mọi giá trị bộ lọc được cung cấp cho các trường ngày tháng. Nếu một biểu thức ngày tháng đơn giản hóa không phân tích được, hệ thống sẽ sử dụng nó như một giá trị thô (raw literal). Điều này cho phép bạn sử dụng các ngày theo nghĩa đen (ví dụ: "2022-09-30 15:45:00") trực tiếp trong bộ lọc ngày của mình.

Các trường ngày tháng có sẵn để lọc là: `created_at` và `modified_at`.

#### Operator Compatibility

Các trường ngày tháng được hỗ trợ bởi mọi toán tử ngoại trừ Between (><) và Not Between (>!<). Hạn chế này là do toán tử Between sử dụng dấu hai chấm (:) để phân tách giới hạn dưới và giới hạn trên, điều này gây xung đột với dấu hai chấm có trong định dạng ngày/giờ tiêu chuẩn.

Các toán tử thích hợp nhất cho các trường ngày tháng là:

* After (>)
* Before (<)
* Is (=)
* Is Not (!=)

#### Relative Date Filtering

Các bộ lọc ngày tháng cũng hỗ trợ các giá trị đơn vị tương đối, cho phép bạn dễ dàng lọc dựa trên thời gian so với thời điểm hiện tại (now).

* Anchor: `now`
* Units: `w` (tuần), `d` (ngày), `h` (giờ)
* Modifiers: Sử dụng `+` để thêm đơn vị hoặc `-` để trừ đơn vị.

Bạn có thể xem các ví dụ dưới đây:

| **Kịch bản**                                     | **Biểu thức bộ lọc**       |
| ------------------------------------------------ | -------------------------- |
| Lấy các sản phẩm được tạo vào tuần trước         | `[created_at][>][now-1w]`  |
| Lấy các sản phẩm được tạo cách đây tối đa 2 ngày | `[created_at][<=][now-2d]` |

#### Multiple Value Filtering (In / Not-In Filter Behavior)

Khớp các trường với nhiều giá trị bằng cách phân tách chúng bằng: `||`

Hỗ trợ bởi tất cả các toán tử ngoại trừ `between` và `not between`.

Ví dụ:

* Lấy sản phẩm từ nhiều thương hiệu:

```
?filter=[brand][=][Niki||Abiba||Pamu]
```

* Loại trừ nhiều màu sắc:

```
?filter=[color][!=][Red||Green||Blue]
```

* Lấy các sản phẩm có danh mục chứa bất kỳ thuật ngữ nào sau đây:

```
?filter=[category][~][Shoes||Boots||Sneakers]
```

#### Advanced Filtering with AND/OR

Kết hợp nhiều bộ lọc bằng cách sử dụng các toán tử logic bên trong một tham số bộ lọc duy nhất.

* And (\*) = cả hai điều kiện phải đúng
* Or (|) = một trong hai điều kiện có thể đúng

Ví dụ:

* Ví dụ AND: Khoảng giá VÀ thương hiệu

```
?filter=([price.USD][>][100]*[brand][=][Niki])
```

* Ví dụ OR: Nhiều danh mục

```
?filter=([category][~][Shoes]|[category][~][Boots])
```

*   Phức tạp: (A AND B) OR C

    Lấy các sản phẩm (giá rẻ VÀ còn hàng) HOẶC có xếp hạng cao:

```
?filter=((price.USD][<][50]*[in_stock][=][1])|[rating][>=][5])
```

Bạn cũng có thể sử dụng nhiều tham số để đạt được logic AND một cách đơn giản hơn:

```
?filter=[price.USD][<][50]&filter=[in_stock][=][1]&filter=[rating][>=][5]
```

Cách này thường rõ ràng hơn so với việc sử dụng các dấu ngoặc đơn phức tạp.

#### Dynamic Filters

Sử dụng `${value}` để tham chiếu đến giá trị trường của sản phẩm nguồn trong bộ lọc của bạn. Biểu thức được tính toán tại thời điểm yêu cầu bằng cách sử dụng dữ liệu sản phẩm thực tế.

Supported Expressions

* `${value}` - Sử dụng giá trị trường của sản phẩm nguyên trạng
* `${value*1.2}` - Nhân với 1.2 (tăng 20%)
* `${value*0.8}` - Nhân với 0.8 (giảm 20%)
* `${value*0.8}:${value*1.3}` - Khoảng với các giới hạn động

Ví dụ:

* Khớp danh mục của sản phẩm một cách động:

```
?categoryList=${value}&productId=PRODUCT_ID
```

Nếu sản phẩm có danh mục là "Shoes", các gợi ý sẽ chỉ được lọc theo "Shoes".

* Hiển thị các sản phẩm trong khoảng giá tương tự (±30%):

```
?productId=PRODUCT_ID&filter=[price.EUR][><][${value}:${value*1.3}]
```

Nếu sản phẩm có giá €100, nó sẽ hiển thị các sản phẩm có giá từ €100-€130.

#### Filter Validation Errors

Khi các bộ lọc không hợp lệ, API sẽ trả về các thông báo lỗi cụ thể với mã trạng thái HTTP:

Format & Syntax Errors (400)

| **Lỗi**                                      | **Nguyên nhân**                                                       |
| -------------------------------------------- | --------------------------------------------------------------------- |
| "Filter is not in correct format."           | Thiếu dấu ngoặc vuông, dấu ngoặc đơn không khớp, cú pháp không hợp lệ |
| "Depth of nested filters is greater than 4." | Quá nhiều cấp độ lồng nhau: ((((filter))))                            |
| "Number of filters is greater than 40."      | Tổng số điều kiện bộ lọc quá nhiều                                    |

Field Errors (400 hoặc 422)

| **Lỗi**                                                          | **HTTP** | **Nguyên nhân**                                              |
| ---------------------------------------------------------------- | -------- | ------------------------------------------------------------ |
| "Entered field name is invalid."                                 | 400      | Trường không tồn tại trong dữ liệu sản phẩm                  |
| "The field '%s' in dynamic filter was not found in the product." | 422      | Thiếu trường trong sản phẩm nguồn (chỉ dành cho bộ lọc động) |
| "Field \$$$ is not filterable product attribute."                | 400      | Trường được đánh dấu là không thể lọc trong cấu hình         |

Operator Errors (400)

| **Lỗi**                                                          | **Nguyên nhân**                                                            |
| ---------------------------------------------------------------- | -------------------------------------------------------------------------- |
| "operator field is invalid."                                     | Toán tử không xác định (sử dụng: =, !=, >, <, >=, <=, \~, !\~, ><, >!<, ?) |
| "For the \$$$ field, operator field is not valid by field type." | Toán tử không tương thích với kiểu trường (ví dụ: \~ trên trường số)       |
| "Operator \$$$ is not allowed for dynamic filters."              | EXIST/EXIST\_ALIAS không được phép dùng với ${value}                       |

Value Errors (400 hoặc 422)

| **Lỗi**                                        | **HTTP** | **Nguyên nhân**                                                           |
| ---------------------------------------------- | -------- | ------------------------------------------------------------------------- |
| "For the \$$$ field, entered value is empty."  | 422      | Giá trị trống hoặc null                                                   |
| "For the \$$$ field, value length is invalid." | 400      | Vượt quá giới hạn (100 ký tự cho tìm kiếm văn bản, 340 cho các loại khác) |
| "Value for the field \$$$ is invalid."         | 400      | Định dạng khoảng không hợp lệ (sử dụng lower:upper cho toán tử ><)        |
| "For the \$$$ field, value type is invalid."   | 400      | Không khớp kiểu dữ liệu (giá trị không phải số trên trường số)            |

Field-Specific Validations (400)

| **Trường** | **Lỗi**                                    | **Nguyên nhân**        |
| ---------- | ------------------------------------------ | ---------------------- |
| in\_stock  | "Value is invalid for in\_stock filter..." | Chỉ chấp nhận 0 hoặc 1 |

Dynamic Filter Errors (400)

| **Lỗi**                                                                        | **Nguyên nhân**                                          |
| ------------------------------------------------------------------------------ | -------------------------------------------------------- |
| "Parameters productId and locale must be provided when dynamic value is used." | Sử dụng ${value} mà không cung cấp productId hoặc locale |
| "Product not found"                                                            | productId được chỉ định không tồn tại                    |

#### Limitations

| **Ràng buộc**              | **Giới hạn** | **Ghi chú**                                         |
| -------------------------- | ------------ | --------------------------------------------------- |
| Độ sâu bộ lọc              | 4 cấp độ     | Độ sâu lồng nhau tối đa cho các dấu ngoặc đơn       |
| Tổng số bộ lọc             | 40 bộ lọc    | Tổng số điều kiện bộ lọc trong một yêu cầu          |
| Độ dài giá trị N-gram      | 100 ký tự    | Để tìm kiếm văn bản trên item\_id, name, image\_url |
| Độ dài giá trị trường khác | 340 ký tự    | Để tìm kiếm văn bản trên các trường khác            |
