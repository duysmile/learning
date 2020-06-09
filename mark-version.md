# Semantic versioning 
Keyword: `semver`

Version được đánh theo dạng `MAJOR.MINOR.PATCH`
1. MAJOR version khi các api cũ thay đổi không còn tương thích
2. MINOR version khi một số tính năng mới được thêm những vẫn đảm bảo tương thích các version cũ (backwards-compatible)
3. PATCH version dành cho những bản vá lỗi fix bug nhỏ vẫn tương thích với các version cũ

Một số nguyên tắc để install package:
- Dấu `*`:
> X.* <=>  >= X.0.0 và < (X+1).0.0
>
> X.Y.* <=>  >= X.Y.0 và < X.(Y+1).0

- Dấu `-`:
> X.Y.Z - X'.Y'.X' <=>  >= X.Y.Z và <= X'.Y'.Z'
>
> X.Y.Z - X'.Y'  <=>  >= X.Y.Z và <= X'.Y'.*

- Dấu `~`:
> ~X.Y.Z  <=>  >= X.Y.Z và < X.(Y+1).0
>
> ~X.Y  <=> X.Y.*

- Dấu `^`:
> ^X.Y.Z
>
> - X != 0  <=> >= X.Y.Z và < (X+1).0.0
>
> - 0.Y.Z (Y!=0) <=> >= 0.Y.Z < 0.(Y+1).0
>
> - 0.0.Z (Z!=0) <=>  Không đổi
