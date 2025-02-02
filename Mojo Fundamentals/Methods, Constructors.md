# Methods, Constructors
method, constructors는 서로 비슷하나 일부 차이가 있음

## methods(함수)
함수가 필요한 이유는
기능을 분류하고, 재사용을 간편하게 만들어줌

## constructors(생성자)
초기화를 하고, 파라미터의 값을 지정하며, 객체를 생성함

__copyinit__, __moveinit__, __takeinit__ 의 메소드들이 있음

value semantic : 값 저장
reference semantic : 값 저장 없음
둘 다 메모리에 저장되어 있음은 똑같음


objects = values > value semantics
그러나 objects는 서로 다름

objects가 같은 값을 가져도, 그 둘은 서로 다른 공간에서 같은 값을 가진 별도의 오브젝트

## __copyinit__()

이 방법은 특정 값을 하나의 객체에서 다른 객체로 옮길 때 사용함

```mojo
struct Book:
  var title: String
  var author: String

  # constructor
  fn __init__(inout self, title: String, author: String):
    self.title = title
    self.author = author

  # Copy initialization method
  fn __copyinit__(inout self, existing: Self):
    # 제목, 지은이를 이미 존재하는 책에서 복사
    self.title = existing.title
    self.author = existing.title

# 원본 책 만들기
var originalBook = Book("The Great Gatsby", "F. Scott Fitzgerald")

# __copyinit__을 이용하여 새 객체 만들기
var copiedBook = originalBook
```

```mojo
# 책의 내용 확인하기
originalBook.dump()
copiedBook.dump()
```

## __moveinit__()
이 방식은 object의 data와 함께 해당 ownership도 함께 이동함
> 이 방식은 성능 향상을 위해 이루어지는 형태

```mojo
struct Box:
  var contents: String

  fn __init__(inout self, contents: String):
    self.contents = contents
  # move initialize method
  fn __moveinit__(inout self, existing: Self):
    # ownership을 옮기고
    self.contents = existing.contents
    # 옮기고 나선 원본에 있던 부분은 비우기
    existing.contents = ""

# Box 생성
var oldBox = Box("Books and Toys")

# content를 옮기기 > oldBox에 있던 내용이 newBox로 옮겨짐
var newBox = oldBox
```

```mojo
# 새로 만든 박스의 내용물 확인
print(newBox.contents)
# 이전 박스의 내용물 확인
print(oldBox.contents)
```

## __takeinit__()

Take operation은 옮겨갈 object에서 동작하고,
특정 logic을 처리하면서 옮겨감

```mojo
struct BoxOfChocolate:
  
  var chocolates: List[String]

  fn __init__(inout self, chocolates: List[String]):
    self.chocolates = chocolates

  fn __takeinit__(inout self, source: Self):
    # 데이터 옮기기
    self.chocolates = source.chocolates
    # 원본의 데이터 삭제
    source.chocolates = []

  fn printChocolates(self):
    print("Chocolates in the box:", self.chocolates)
```

# Method Overloading
함수 overloading

```mojo
struct Complex:
  var re: Float32
  var im: Float32

  fn __init__(inout self, x: Float32):
    """주어진 실수를 토대로 복소수 만들기"""
    self.re = x
    self.im = 0.0
  fn __init__(inout self, r: Float32, i: Float32):
    """실수, 허수부의 숫자를 토대로 복소수 만들기"""
    self.re = r
    self.im = i

```

```mojo
var c1 = Complex(3.14)

var c2 = Complex(2.0, 4.5)
```
