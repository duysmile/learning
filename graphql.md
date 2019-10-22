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

Tự xây dựng directive cho mình theo hướng dẫn ở  [đây](https://www.apollographql.com/docs/apollo-server/schema/creating-directives/)

### Implementing directives

Implementing schema directives
- Vì tài liệu GraphQL ko thảo luận bất cứ chiến lược cụ thể nào cho directives, điều này làm cho mỗi GraphQL Server framework phải tự expose API để implement những directives mới.

- Nếu bạn đang dùng Apollo Server, và package `graphql-tools`, có thể cung cấp những công cụ mạnh mẽ để implementing directive syntax: class `SchemaDirectiveVisitor`.

- Để implement một schema directive dùng `SchemaDirectiveVisitor`, đơn giản chỉ cần tạo một subclass của `SchemaDirectiveVisitor` và overrides một trong nhiều phương thức visitor sau:
  - `visitSchema(schema: GraphQLSchema)`
  - `visitScalar(scalar: GraphQLScalarType)`
  - `visitObject(object: GraphQLObjectType)`
  - `visitFieldDefinition(field: GraphQLField<any, any>)`
  - `visitArgumentDefinition(argument: GraphQLArgument)`
  - `visitInterface(iface: GraphQLInterfaceType)`
  - `visitUnion(union: GraphQLUnionType)`
  - `visitEnum(type: GraphQLEnumType)`
  - `visitEnumValue(value: GraphQLEnumType)`
  - `visitInputObject(object: GraphQLInputObjectType)`
  - `visitInputFieldDefinition(field: GraphQLInputField)`

- Bằng việc override phương thức như `visitObject`, một subclass của SchemaDirectiveVisitor thể hiện sự quan tâm(expresses interest) đến các loại schema nhất định như `GraphQLObjectType`(loại tham số đầu tiên của `visitObject`).

- Tên của những method này tương ứng với những vị trí mà một directive có thể được sử dụng trong schema. Ví dụ, vị trí `INPUT_FIELD_DEFINITION` được xử lí bởi visitInputFieldDefinition.

- Đây là một ví dụ về việc implement `@deprecated` directive:
```javascript
const { SchemaDirectiveVisitor } = require('apollo-server');

class DeprecatedDirective extends SchemaDirectiveVisitor {
  public visitFieldDefinition(field: GraphQLField<any, any>) {
    field.isDeprecated = true;
    field.deprecationReason = this.args.reason;
  }
  
  public visitEnumValue(value: GraphQLEnumValue) {
    value.isDeprecated = true;
    value.deprecationReason = this.args.reason;
  }
}
```

Để apply phần implementation trên vào schema với `@deprecated` directive, chỉ cần đơn giản là pass `DeprecatedDirective` class  vào constructor của Apollo Server thông qua option `schemaDirectives`:
```javascript
const { ApolloServer, gql } = require('apollo-server');

const typeDefs = gql `
  type ExampleType {
    newField: String
    oldField: String @deprecated(reason: "Use \'newField\'.")
  }
`;

const server = new ApolloServer({
  typeDefs,
  resolvers,
  schemaDirectives: {
    deprecated: DeprecatedDirective
  }
});

server.listen().then(({ url }) => {
  console.log(`Server ready at ${url}`);
});
```

Thay vào đó, nếu bạn muốn modify một schema object đã tồn tại, bạn có thể dùng interface `SchemaDirectiveVisitor.visitSchemaDirectives` trực tiếp.
```javascript
SchemaDirectiveVisitor.visitShemaDirectives(schema, {
  deprecated: DeprecatedDirective
});
```
 Lưu ý là một subclass của `SchemaDirectiveVisitor` có thể được khởi tạo nhiều lần để visit nhiều chỗ khác nhau của `@deprecated` directive. Đó là lý do tại sao bạn cung cấp 1 class chớ k phải một thực thể của class đó.
 
 Nếu vì một số lí do mà bạn có một schema dùng tên khác cho `@deprecated` directive, nhưng bạn muốn dùng chung implementation thì hoàn toàn có thể đc. Cùng một class `DepreactedDirective` có thể pass vào một tên directive khác, đơn giản chỉ cần đổi cái key trong object `schemaDirectives` được pass vô chô constructor của Apollo Server.

[Xem thêm ví dụ](https://www.apollographql.com/docs/apollo-server/schema/creating-directives/#uppercasing-strings)

### Fetching data with resolvers
Resolver map
- Để respond cho queries, một schema cần có những resolve functions cho tất cả các fields. Những function này được gọi là "resolvers map". Và chúng có liên hệ với những schema fields và types.
```javascript
const { gql } = require('apollo-server');
const { find, filter } = require('lodash');

const schema = gql `
  type Book {
    title: String
    author: Author
  }

  type Author {
    books: [Book]
  }

  type Query {
    author: Author
  }
`;

const resolvers = {
  Query: {
    author(parent, args, context, info) {
      return find(authors, { id: args.id });
    },
  },
  Author: {
    books(author) {
      return filter(books, { author: author.name });
    },
  },
};
```

- Với resolver map trên, thì query `{ author { books } }` sẽ gọi resolver `Query.author` trước là trả keetsquar tới `Author.books`. Kết quả trả về sẽ bao gồm giá trị của` Author.books` được lồng dưới `data.author.books`.

- Lưu ý là bạn không cần phải đặt tất cả các resolver trong một object. Dựa thêm phần [modularizing resolvers](https://www.apollographql.com/docs/apollo-server/data/data/#modularizing-resolvers) để học cách combine nhiều resolvers map thành một.

Resolver type signature
- Ngoài giá trị của parent resolvers, thì resolvers còn nhận thêm những tham số khác. Signature đẩy đủ của resolver function gồm 4 tham số theo thứ tự như sau: `(parent, args, context, info)` và có thể trả về một object hoặc một Promise. Một promise được resolve thì những children resolver sẽ tiếp tục thực thi. Điều này rất hữu ích khi fetching data từ backend.

- Tham số resolver dựa theo naming conventions được mô tả chi tiết sau:
1. `parent`: là object bao gồm kết quả được trả về từ resolver ở parent filed, hoặc trong trường hợp ở top-level Query field, `rootValue` được truyền vào từ config của server. Tham số này cho phép lồng nhau.

2. `args`: một object với những tham số được truyền vào field trong query. Ví dụ, nếu field được gọi với `query{ key(arg: "you meant") }`, thì `args` object sẽ là `{ args: "you meant" }`.

3. context: đây là một object chia sẻ với tất cả các resolvers trong một query cụ thể, và được dùng để chứa state của mỗi request, bao gồm thông tin authentication, dataloader instances, và bất cứ thứ gì khác cần được đưa vào khi resolving query. Đọc thêm [bài viết này](https://www.apollographql.com/docs/apollo-server/data/data/#context-argument) để hiểu rõ hơn về khi nào và cách sử dụng context.

4. `info`: tham số này chứa thông tin về trạng thái thực thi (execution state) của query, bao gồm field name, path tới field từ root, và nhiều thứ nữa. Nó chỉ là document trong GraphQL, nhưng được mở rộng với thêm nhiều tính năng bởi những modules khác, như [apollo-cache-control](https://github.com/apollographql/apollo-server/tree/master/packages/apollo-cache-control).

Resolver results
- Resolvers trong GraphQL có thể trả về nhiều loại kết quả khác nhau: 
1. `null` hoặc `undefinded` - nó chỉ ra là object không được tìm thấy. Nếu schema của bạn cho phép field đó có thể null, thì kết quả sẽ có thể là null, Nếu field đó là `non-null`, thì kết quả sẽ 'bubble up' tới trường nullable gần nhất và kết quả đó sẽ được set thành null. Điều này đảm bảo là API consumer sẽ không bao giờ nhận một giá trị `null` khi mà nó mong đợi một kết quả.

2. Một mảng - chỉ đúng nếu schema chỉ định là kết quả là một list. Và sub-selection của query sẽ chạy một cho mỗi item trong mảng.

3. Một promise - resolver thường thực thi bất đồng bộ như là fetching data từ một database hay một backend API, do đó nó sẽ trả về promises. Có thể kết hợp với array, do đó resolver có thể trả về một promise mà resolve thành một array, hoặc một array promises, và cả hai điều được xử lí hết.

4. Một giá trị scalar hoặc object - một resolver cũng có thể trả về những loại giá trị này, mà không có ý nghĩa đặc biệt nào mà chỉ được truyền vào bất kỳ nested resolvers nào, như được mô tả trong phần tiếp theo.


Parent argument
- Tham số đầu tiên cho mỗi resolver, `parent`, có thể gây confuse một chút, nhưng sẽ dễ thôi nếu mà bạn xem ví dụ GraphQL query dưới đây:
```graphql
query {
  getAuthor(id: 5){
    name
    posts {
      title
      author {
        name # this will be the same as the name above
      }
    }
  }
}
```

- Mỗi GraphQL query là một cây functions được gọi ở server. Do đó `parent` chưa kết quả của parent resolver, trong trường hợp này thì là:
1. `parent` trong `Query.getAuthor` sẽ là bất kì config server nào được truyền vào cho `rootValue`.

2. `parent` trong `Author.name` và `Author.posts` sẽ là kết quả từ `getAuthor`, giống như một Author object từ backend.

3. `parent` trong `Post.title` và `Post.author` sẽ có một item từ mảng kết quả `posts`.

4. `parent` trong `Author.name` là kết quả từ `Post.author` ở trên.

- Mỗi resolver function được gọi tương ứng với phần lồng nhau trong query. Để hiểu hơn về sự chuyển tiếp từ query tới resolvers từ những góc nhìn khác thì có thể tham khảo [blog này](https://blog.apollographql.com/graphql-explained-5844742f195e#.fq5jjdw7t).

Context argument
- Context là cách bạn truy cập vào các shared connections và fetchers trong resolvers để lấy data.

- `Context` là đối số thứ 3 được truyền vào mỗi resolver. Điều này rất hữu dụng để truyền vào thứ mà resolver cần, như [authentication scope](https://blog.apollographql.com/authorization-in-graphql-452b1c402a9), database connections, và những custom fetch functions. Thêm nữa, nếu bạn sử dụng [dataloaders để bactch requests](https://www.apollographql.com/docs/apollo-server/data/data-sources/#what-about-dataloader) trên các resolvers, bạn có thể attach chúng vào `context` luôn.

- Như một best practice, `context` nên giống nhau cho tất cả resolvers, bất kể là query hay mutation, và resolvers ***không được thay đổi nó (context)***. Điều này đảm bảo tính nhất quán giữa các resolvers, và giúp tăng tốc độ development.

- Để cung cấp một `context` cho resolvers, thêm một `context` object trong contructor của Apollo Server. Constructor này được gọi vs mỗi request, do đó bạn có thể set `context` dựa trên chi tiết mỗi request(như HTTP headers).
```javascript
const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: ({ req }) => ({
    authScope: getScope(req.headers.authorization)
  })
}));

// resolver
(parent, _, context) => {
  if(context.authScope !== ADMIN) throw AuthenticationError('not admin');
  ...
}
```
- Context cũng có thể được tạo bất đồng bộ, để khởi tạo các database connections và những operations khác.
```javascript
context: async () => ({
  db: await client.connect(),
})

// resolver
(parent, _, context) => {
  return context.db.query('SELECT * FROM table_name');
}
```

Default resolver
- Resolvers ko cần thiết cho tất cả các type, vì Apollo Server cung cấp một default có thể thực hiện 2 actions phụ thuộc vào nội dung của `parent`:
1. Trả về thuộc tính của `parent` với các trường liên quan.
2. Goị 1 function ở `parent` với các tên trường liên quan và cung cấp những tham số resolver như là arguments.

- Trong schema dưới đây, trường `title` của `Book` không cần một resolver nếu kết quả của `books` resolver cung cấp một list các object đã chứa một trường `title`.
```graphql
type Book {
  title: String
}

type Author {
  books: [Book]
}
```

Modularizing resolvers (tách module)
- Chúng ta có thể thực hiện cùng một module với các resolvers bằng cách truyền qua nhiều resolver object và liên kết chúng lại với hàm `merge` của Lodash hoặc là những hàm tương đương:
```javascript
// comment.js
const resolvers = {
  Comment: { ... }
}

export resolvers;
```

```javascript
// post.js
const { merge } = require('lodash');

const Comment = require('./comment');
const resolvers = merge({
  Post: { ... }
}, Comment.resolvers);

export resolvers;
```

```javascript
// schema.js
const { merge } = require('lodash');
const Post = require('./post.js');

// Merge all of the resolver objects together
const resolvers = merge({
  Query: { ... }
}, Post.resolvers);

const server = new ApolloServer({
  typeDefs,
  resolvers,
});

server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`)
});
```

Sending queries
- Một khi đã hoàn thành resolver map, đây là lúc bắt đầu test lại những queries trong GraphQL Playground.

Naming operations
- Khi send queries hay mutations trong ví dụ trên, chúng ta có thể dùng `query { ... }` hoặc `mutation { ... }`. Mọi thứ đều rất ổn nhưng chúng ta nên đặt tên để để nhận biết và debug hoặc là tổng hợp những operations tương tự nhau để  đo lường hiệu suất ứng dụng, ví dụ khi dùng [Apollo Graph Manager](https://engine.apollographql.com/) để monitor 1 API.

-Operations có thể được đặt tên bằng cách đặt một định danh sau `query` hoặc `mutation`, chúng ta có thể tạo `HomeBookListing` như sau:
```javascript
query HomeBookListing {
  getBooks {
    title
  }
}
```

Queries with variables
- Trong ví dụ trên chúng ta dùng các giá trị cố định, với GraphQL chúng ta có thể cung cấp các biến trong queries để có thể thay đổi các giá trị khi queries đơn giản và hiệu quả, dễ quản lí hơn. Ví dụ như thế này:

```graphql
mutation HomeQuickAddBook($title: String, $author: String = "Anonymous") {
  addBook(title: $title, author: $author) {
    title
  }
}
```

Ở GraphQL clients, như [Apollo Client](https://www.apollographql.com/docs/react/), quan tâm đến việc gửi biến tới server tách biệt với operation:
```graphql
{
  "query": "...",
  "variables": { "title": "Green Eggs and Ham", "author": "Dr. Seuss" }
}
```
- Ch

