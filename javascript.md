# Javascript Learning
- Data types: number (max  2^53 - 1), BigInt (vd: 10n, kết thúc là `n` là kiểu BigInt), string, symbol, object
  - typeof null = object nhưng kiểu dữ liệu của null là null, kiểu dữ liệu của undefined là undefined.
  - typeof array = object
- Object: kiểm tra property của object dùng toán tử `in` 
```javascript
var user = {
  name: 'Duy',
};
console.log('name' in user); // true
console.log('age' in user); // false
```
- Function:
  - IIFE (Immediately invoked function expression): hàm thực thi ngay lập tức
  ```javascript
  (function() {
    console.log('Hi');
  })();
  ```
  - Pure function:
    - Với cùng một đầu vào thì kết quả đầu ra luôn giống nhau
    - Không có side-effect
    - Lợi ích: dễ test, dễ debug, dễ đoán kết quả chương trình
- Hoisting
- let, const, var:
  - declaration
  - hoisting
  - scope: block scope for let, const
  - redeclaration
- context:
  - bind
  - apply
  - call
- arrow function
  - arrow function sẽ không có context, context của nó sẽ là context của function gần nhất, hoặc class.
- template string \`...\`
- arguments: array-like object
- default parameters
- enhanced object literal:
  - eg: const name = {
        name,
        run() {
          console.log('yeah');
        }
    }
- class:
  - inheritance
  - override
  - super
  - static
- rest: ...
  - function(a, ...rest) {} -> rest: array, arguments: array-like object
- spread: ...
  - shallow cloning: chỉ clone một lớp của object
  - E.g: 
    const a = \[1, 2, 3, 4, 5\];
    const b = \[0, ...a, 6\];
- value types and reference types.
- closure
- higher order function: là hàm nhận vào 1 hàm làm tham số hoặc là trả về một hàm.
- destructuring
  - có thể dùng default, vd: const \[a, b = 20\] = \[1\];

### javascript sử dụng cơ chế call by sharing thay vì call by value hay call by reference
Call by sharing (cũng được gọi là call by object hoặc call by object-sharing). Call by sharing ngụ ý là những giá trị trong ngôn ngữ dựa trên objects thay vì những kiểu nguyên thủy, nên là tất cả giá trị đều được đóng hộp (boxed). Có thể nói là được truyền thông qua bản sao của tham chiếu (kiểu nguyên thủy sẽ được bao đóng (boxed) trước khi truyền đi và được mở (unboxed) tại function được gọi).

Về mặt ngữ nghĩa thì call by sharing khác với call by reference: "Cụ thể là nó ko được gọi bằng giá trị bởi vì sự thay đổi của tham số thực hiện bởi routine (công việc) được gọi sẽ được hiển thị với caller. Và nó không phải là call by reference bởi vì việc truy cập ko được trao cho biến của caller, mà chỉ cho một số object nhất định. Do đó, ví dụ, nếu một biến được truyền, nó không thể mô tả việc assignment(gán) trên một biến trong scope của callee. Tuy nhiên, vì function có thể truy cập tới cùng một object với caller (ko có bản sao nào được tạo), thay đổi trên những object đó, nếu object là có thể thay đổi, bên trong function có thể hiển thị với caller, có thể khác với việc call by value. Việc thay đổi object bên trong function được hiện hữu với caller bởi vì object không phải là được copied hay cloned - nó được shared.

Ví dụ, trong Python, list là mutable, do đó
```python
def f(list):
    list.append(1)

m = []
f(m)
print(m)
```
Kết quả là [1] bởi vì method append thay đổi object được tạo.

Việc assignments(gán) bên trong function không được thông báo với caller, bởi vì trong những ngôn ngữ này, truyền biến (passing the variable) chỉ có nghĩa là truy cập (access to) tới chính object được tham chiếu đến bởi biến, ko truy cập tới biến ban đầu (của caller). Bởi vì biến tạm (rebound) chỉ tồn tại trong scope của function, còn phần (counterpart) trong caller vẫn giữ ràng buộc ban đầu(original binding).

So sánh sự thay đổi ở trên với đoạn code dưới đây, khi gán đối số với một object mới:

```python
def f(list):
    list = [1]

m = []
f(m)
print(m)
```

Kết quả là [], bởi vì câu lệnh list = [1] gán lại một list mới cho biến list chứ ko phải cho địa chỉ mà nó tham chiếu đến.

Còn với những đối tượng ko thay đổi (immutable) ko có khác biệt nào thực sự giữa call by sharing và call by value cả, ngoại trừ nếu object identity có trong ngôn ngữ. Cách sử dụng của call by sharing với những mutable object là một sự thay thế cho tham số input/output: tham số (parameter) ko được gán(đối số không được ghi đè và object identity ko thay đổi), nhưng object(đối số) là có thể thay đổi (mutated).
