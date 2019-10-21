# GRAPHQL

## Schema basics
Types:
- Scalar types: giống như kiểu nguyên thủy
  - Int
  - Float
  - String
  - Boolean
  - ID(serialize ra thì giống String)
  - Một vài trường hợp thì có thể tạo thêm những [custom scalar types](https://www.apollographql.com/docs/apollo-server/schema/scalars-enums/)
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
## Custom scalars and enums

GraphQL gồm nhiều kiểu vô hướng (scalar): Int, Float, String, Boolean và ID. Những kiểu này thì phần lớn đã cover được hết các case, nhưng cũng cần thêm vài loại dữ liệu atomic, hoặc để thêm validation vào các types có sẵn. Để làm được việc này thì GraphQL cho phép custom scalar types. Enumerations thì tương tự như custom scalars nhưng mà có giới hạn số giá trị, được định nghĩa trước trong một list strings.

Custom scalars:
- Để định nghĩa một custom scalar thì dùng cú pháp ni:
```
scalar MyCustomScalar
```
- Sau khi khai báo như vậy thì chúng ta định nghĩa behavior của `MyCustomScalar` bằng cách pass một instance của `GraphQLScalarType` class trong resolver map. Instance này có thể được định nghĩa với 1 dependency hoặc trong source code.

Sử dụng package
- Chúng ta có thể xài cái package `graphql-type-json` để xài mấy cái custom types `json` này.
- Cài `graphql-type-json`:
> npm install graphql-type-json

- Rồi sau đó xài nó như sau:
```javascript
const { ApolloServer, gql } = require('apollo-server');
const GraphQLJSON = require('graphql-type-json');

const schemaString = gql`
  scalar JSON

  type Foo {
    aField: JSON
  }

  type Query {
    foo: Foo
  }
`;

const resolveFunctions = {
  JSON: GraphQLJSON
};

const server = new ApolloServer({ typeDefs: schemaString, resolvers: resolveFunctions });

server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`)
});
```

Custom `GraphQLScalarType` instance
- Việc định nghĩa một instance `GraphQLScalarType` cung cấp quyền kiểm soát các custom scalar và có thể thêm vào Apollo server như sau:
```javascript
const { ApolloServer, gql } = require('apollo-server');
const { GraphQLScalarType, Kind } = require('graphql');

const myCustomScalarType = new GraphQLScalarType({
  name: 'MyCustomScalar',
  description: 'Description of my scalar type',
  serialize(value) {
    let result;
    // Implement custom behavior by setting the 'result' variable
    return result;
  },
  parseValue(value) {
    let result;
    // Implement custom behavior here by setting the 'result' variable
    return result;
  },
  parseLiteral(ast) {
    switch(ast.kind) {
      case Kind.Int:
        // return a literal value, such as 1 or 'static string'
    }
  }
});

const schema = gql `
   scalar MyCustomScalar

  type Foo {
    aField: MyCustomScalar
  }

  type Query {
    foo: Foo
  }
`;

const resolverFunctions = {
  MyCustomScalar: myCustomScalarType
};

const server = new ApolloServer({ typeDefs: schemaString, resolvers: resolverFunctions });

server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`)
});
```

Một số ví dụ về [custom scalar](https://www.apollographql.com/docs/apollo-server/schema/scalars-enums/)

Date
- Schema
```graphql
const typeDefs = gql`
  scalar Date

  type MyType {
    created: Date
  }
`
```
```javascript
const { GraphQLScalarType } = require('graphql');
const { Kind } = require('graphql/language');

const resolvers = {
  Date: new GraphQLScalarType({
    name: 'Date',
    description: 'Date custom scalar type',
    parseValue(value) {
      return new Date(value); // value from the client
    },
    serialize(value) {
      return value.getTime(); // value sent to the client
    },
    parseLiteral(ast) {
      if (ast.kind === Kind.INT) {
        return parseInt(ast.value, 10); // ast value is always in string format
      }
      return null;
    },
  }),
};

const server = new ApolloServer({ typeDefs, resolvers });

server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`)
});
```

Validations
- Schema
```graphql
const typeDefs = gql`
  scalar Odd

  type MyType {
    oddValue: Odd
  }
`
```
- Resolver:
```javascript
const { ApolloServer, gql } = require('apollo-server');
const { GraphQLScalarType } = require('graphql');
const { Kind } = require('graphql/language');

function oddValue(value) {
  return value % 2 === 1 ? value : null;
}

const resolvers = {
  Odd: new GraphQLScalarType({
    name: 'Odd',
    description: 'Odd custom scalar type',
    parseValue: oddValue,
    serialize: oddValue,
    parseLiteral(ast) {
      if (ast.kind === Kind.INT) {
        return oddValue(parseInt(ast.value, 10));
      }
      return null;
    },
  }),
};

const server = new ApolloServer({ typeDefs, resolvers });

server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`)
});
```

## Enums

Enum thì tương tự như 1 scalar type, nhưng chỉ có thể là một trong vài giá trị được định nghĩa trong schema. Enums được dùng rất phổ biến khi người dùng chỉ dược chọn một vài lựa chọn cho trước. 

Ví dụ, schema:
```graphql
enum AllowedColor {
  RED
  GREEN
  BLUE
}
```

Và sau đó enum có thể được dùng ở bất cứ đâu:
```graphql
type Query {
  favoriteColor: AllowedColor # là một giá trị trả về
  avatar(borderColor: AllowedColor): String # là  một đối số
}
```

Một query sẽ như thế này:
```graphql
 query {
  avatar(borderColor: RED
 }
```

Để pass một giá trị enum như một biến, dùng 1 chuỗi JSON:
```graphql
query MyAvatar($color: AllowedColor) {
  avatar(borderColor: $color)
}
```
Và variable đây:
```
  {
    color: "[RED]"
  }
```

Interval values
- Thỉnh thoảng một backend cho phép một giá trị khác cho 1 enum trong public API. Trong một ví dụ API bao gồm `RED`, tuy nhiên trong resolvers chúng ta dùng `#f00`. Tham số của resolvers trong ApolloServer cho phép thêm những giá trị custom cho enums chỉ tồn tại nội bộ như sau:
```javascript
const resolvers = {
  AllowedColor: {
    RED: '#f00',
    GREEN: '#0f0',
    BLUE: '#00f',
  }
};
```

Những cái này không thay đổi public API, và những resolver chấp nhận những giá trị này thay vì giá trị trong schema:
```javascript
const resolvers = {
  AllowedColor: {
    RED: '#f00',
    GREEN: '#0f0',
    BLUE: '#00f'
  },
  Query: {
    favoriteColor: () => '#f00',
    avatar: (parent, args) => {
      // args.borderColor is '#f00', '#0f0', hoặc '#00f'
    }
  }
};
```

## Union và Interfaces

Union type
- `Union` chỉ ra rằng 1 field có thể trả về nhiều hơn một loại đối tượng(object type), nhưng không tự định nghĩa các fields cụ thể. Union rất hữu ích để trả về những kiểu data rời rạc từ 1 single fields.
```javascript
const { gql } = require('apollo-server');

const typeDefs = gql`
  union Result = Book | Author

  type Book {
    title: String
  }

  type Author {
    name: String
  }

  type Query {
    search: [Result]
  }
`;
```

Vì một query yêu cầu 1 union field, nó được thực hiện trên một trường union-type phải chỉ định các object type mà trường đó muốn. Cái này được giải quyết bằng cách thêm `__resolveType` field trong resolver map. `__resolveType` định nghĩa loại kết quả nằm ngoài các tùy chọn khả dụng với môi trường GraphQL.
```javascript
const resolvers = {
  Result: {
    __resolverType(obj, context, info) {
      if(obj.name) {
        return 'Author';
      }
      
      if(obj,title) {
        return 'Book';
      }
      
      return null;
    }
  },
  Query: {
    search: () => { ... }
  }
};

const server = new ApolloServer({
  typeDefs,
  resolvers
});

server.listen().then(({ url }) => {
  console.log(`Server ready at ${url}`);
});
```

Query sau thể hiện sự cần thiết của `__resolveType`, nó yêu cầu data khác nhau được vào types:
```javascript
{
  search(contains: "") {
    ... on Book {
      title
    }
    ... on Author {
      name
    }
  }
}
```

Interface type
- Interfaces là cách mạnh mẽ để xây dựng và sử dụng GraphQL schema thông qua abstract type. Abstract types ko thể dùng trực tiếp trong schema, nhưng có thể dùng để xây dựng những blocks để tạo những kiểu cụ thể.

- Xem xét ví dụ khi mà những kiểu khác nhau của book chia sẻ một common set attributes, như text books và coloring books. Một nền tảng đơn giản cho những loại sách này được thể hiện thông qua interface:
```graphql
interface Book {
  title: String
  author: Author
}
```

Chúng ta sẽ không thể thực tiếp dùng interface để query một quyển sách, nhưng chúng ta có thể dùng nó để implement những kiểu cụ thể. Tưởng tượng một màn hình trong một app cần hiển thị danh sách tất cả các cuốn sách, mà không chỉ rõ loại sách cụ thể. Chúng ta có thể tạo ra chức năng đó theo cách sau:
```graphql
type TextBook implements Book {
  title: String
  author: Author
  classes: [Class]
}

type ColoringBook implements Book {
  title: String
  author: Author
  colors: [Color]
}

type Query {
  schoolBooks: [Book]
}
```

Trong ví dụ này, chúng ta dùng Book interface như một mô hình nền tảng cho cả `TextBook` và `ColoringBook`. Sau đó, một trường `schoolBooks` được chỉ định là được trả về một list các book(như `[Book]`).

Tương tự như Union, Interface yêu cầu một trường `__resolveType` trong resolver map để xác định type interface nào nên resolve.
```javascript
const resolvers = {
  Book: {
    __resolveType(book, context, info){
      if(book.classes){
        return 'TextBook';
      }

      if(book.colors){
        return 'ColoringBook';
      }

      return null;
    },
  },
  Query: {
    schoolBooks: () => { ... }
  },
};
```

Implement danh sách book bây giờ được đơn giản hóa do chúng ta đã loại bỏ sự quan tâm về việc loại sách nào được trả lại. Query có thể trả về text books và coloring books:
```graphql
query GetBooks {
  schoolBooks {
    title
    author
  }
}
```

Điều này rất hữu ích cho những nội dung phổ biến (common content), quyền hạn user trong hệ thống(user role systems) và hơn thế nữa.

Hơn nữa, nếu cần trả về những fields chỉ cung cấp bởi TextBook hoặc ColoringBook (không phải cả hai) chúng ta có thể yêu cầu các phần nhỏ(fragments) từ những kiểu trừu tượng trong query. Những fragments này sẽ được điền vào khi thích hợp, chỉ có TextBook thì mới có `classes`, còn nếu là ColoringBook thì chỉ có `colors` ví dụ như sau:
```graphql
query GetBooks {
  schoolBooks {
    title
    ... on TextBook {
      classes: {
        name
      }
    }
    ... on ColoringBook {
      colors: {
        name
      }
    }
  }
}
```

## Using schema directives

Dùng schema directive đển chuyển đổi schema types, fields, và arguments

Một directive là một định danh với dấu `@` ở trước, tùy chọn theo sau bởi một list tên các arguments, có thể xuất hiện sau hầu hết bất cứ cú pháp trong GraphQL Query hoặc schema languages. Đây là một ví dụ:
```graphql
directive @deprecated(
  reason: String = "No longer supported"
) on FIELD_DEFINITION | ENUM_VALUE

type ExampleType {
  newField: String
  oldField: String @deprecated(reason: "Use `newField`.")
}
```

Định nghĩa directive theo cú phá `directive @deprecated ... on ...`
và sử dụng nó `@deprecated(reason: ...)`

Default Directives:
- GraphQL cung cấp một vài default directives như: `@deprecated`, `@skip`, và `@include`.
  - @deprecated (reason: String): đánh dấu là field này đã bị deprecated kèm vs message.
  - @skip (if: Boolean!): việc thực thi GraphQL bỏ qua field nếu đúng bằng cách k gọi resolver.
  - @include (if: Boolean!): gọi resolver cho trường chú thích(annotated field) nếu đúng

Using custom schema directives
- Để dùng một custom schema directive, pass class được implemented cho Apollo Server thông qua tham số `schemaDirectives`, là một object mà map directive name với directive implementations:
```javascript
const { ApolloServer, gql, SchemaDirectiveVisitor } = require('apollo-server');
const { defaultFieldResolver } = require('graphql');

// Create(or import) a custom schema directive
class UpperCaseDirective extends SchemaDirectiveVisitor {
  visitFieldDefinition(field) {
    const { resolve = defaultFieldResolver } = field;
    field.resolve = async function (...args) {
      const result = await resolve.apply(this, args);
      if (typeof result === 'string') {
        return result.toUpperCase();
      }
      
      return result;
    }
  }
}

// Construct a schema using GraphQL schema language
const typeDefs = gql `
  directive @upper on FIELD_DEFINITION
  
  type Query {
    hello: String @upper
  }
`;

// Provide resolver functions for your schema fields
const resolvers = {
  Query: {
    hello: (parent, args, context) => {
      return 'hello, world';
    }
  }
};

// Add directive to the ApolloServer constructor
const server = new ApolloServer({
  typeDefs,
  resolvers,
  schemaDirectives: {
    upper: UpperCaseDirective,
  }
});

server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`)
});
```














