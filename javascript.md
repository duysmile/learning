# Javascript Learning 
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
