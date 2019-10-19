# GRAPHQL

### Schema basics
Types:
- Scalar types: giống như kiểu nguyên thủy
  - Int
  - Float
  - String
  - Boolean
  - ID(serialize ra thì giống String)
  - Một vài trường hợp thì có thể tạo thêm những [customer scalar types](https://www.apollographql.com/docs/apollo-server/schema/scalars-enums/)
- Object types: giống như định nghĩa object bên JS thôi
- `Query` type: định nghĩa chính xác những GraphQL queries(đọc dữ liệu) mà client có thể yêu cầu. Nói chung thì cũng giống như object type nhưng mà luôn có tên là Query.
- `Mutation` type: có cấu trúc và mục đích khá giống `Query`. Nhưng khác vs Query là để đọc(read operations), thì Mutation dùng để ghi(write operations).
- Input types: là những kiểu object đặc biệt cho pheps pass object là đối số cho queries với mutations. Nói chung là để làm cho tham số đầu vào gọn hơn dễ quản lí dễ đọc hơn thôi.

Documentation strings: có support markdown -> để làm document cho schema
```
"Description for the type"
type MyObjectType {
  """
  Description for field
  Supports **multi-line** description for your [API](http://example.com)!
  """
  myField: String!

  otherField(
    "Description for argument"
    arg: Int
  )
}
```

Conventions cho đặt tên:
- Field names thì dùng `camelCase`
- Type names thì dùng `PascalCase`
- Enum names cũng dùng `PascalCase`
- Giá trị enum thì nên dùng `ALL_CAPS` -> tương tự vs constants

Query-driven schema design: việc thiết kế (cấu trúc) schema nên phụ thuộc vào dữ liệu mà client cần chứ không phải là dữ liệu mà server lưu trữ

Designing mutations: 
- Trong GraphQL, mỗi mutation được khuyến nghị là nên trả về data mà nó đã modified -> việc này giúp cho client lấy được thông tin về dữ liệu mới nhất mà k cần gửi thêm một query.

Cấu trúc mutation responses
- Dùng Interface để tạo khung mẫu cho response:
```
interface MutationResponse {
  code: String!
  success: Boolean!
  message: String!
}
```

--------
### Custom scalars and enums
























