- Nếu muốn query theo object nằm trong array, từ 2 field trở lên, ví dụ query một object nằm trong một array field có type = 2, và name = 'abc' thì dùng `$elemMatch`
db.collection.find({
  arrayField: {
    $elemMatch: {
      type: 2,
      name: 'abc',
    }
  }
})

- $where operator: dùng với một javascript expression
Ví dụ:
db.collection.find({
  $where: "this.name === 'abc'"
})

- $inc: tăng một filed lên n đơn vị
- $push: đẩy vào một array
- $pull: lấy ra từ array
- $addToSet: thêm vào và k duplicate trong array
