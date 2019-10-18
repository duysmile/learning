# GRAPHQL

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
