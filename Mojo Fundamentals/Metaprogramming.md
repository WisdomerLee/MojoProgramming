# MetaProgramming
## Decorator

Metaprogramming은 무엇인가?

main program code가 있고
meta code는 새 data를 기존 code에 덧붙이고

변경, 생성, 등을 할 수 있게 만드는 것

### @value decorator
```mojo
@value
struct Person:
  var name: String
  var age: Int

```
위와 같이 처리하면 저 decorator에 약속된 코드들이 자동으로 붙어 실행될 수 있게 처리됨
그 대표적인 것들이 
```mojo
fn __init__(arguments):
fn __copyinit__(arguments):
fn __moveinit__(arguments):
```
위와 같은 코드들은 모든 struct마다 정의되어야 하는데, 그것을 사용자가 직접 일일이 집어넣고 하다보면 코드 중복도 심해질 뿐더러, 유지보수할 때 어려움이 증가하게 됨

```mojo
@value
struct Person:
  var name: String
  var age: Int

# 아래의 내용은 내부 코드에서 자체적으로 생산하게 됨 > 사람이 아래의 코드를 짤 이유가 전혀 없게 됨...
  fn __init__(inout self, owned name: String, age: Int):
    self.name = name^
    self.age = age

  fn __copyinit__(inout self, existing: Self):
    self.name = existing.name
    self.age = existing.age

  fn __moveinit__(inout self, owned existing: Self):
    self.name = existing.name^
    self.age = existing.age

```

```mojo
@value
struct Person:
  var name: String
  var age: Int

# 생성
var person = Person("Alice", 30)


```
@value로 __init__(), __copyinit__(), __moveinit__() 함수가 자동 생성되므로...

## Decorators
@value
@register_passable
@parameter
@always_inline

과 같은 decorator는 struct나 method에 쓸 수 있음

### @register_passable
```mojo
@register_passable
struct Pair:
  var a: UInt32
  var b: UInt32
```
위와 같이 @register_passable이 붙은 경우
type들이 CPU의 register라는 곳에 바로 들락날락 할 수 있게 처리함

struct에 적용 가능

```mojo
@value
@register_passable
struct Pair:
  var a: UInt32
  var b: UInt32

fn test():
  let x = Pair(5, 10)
  var y = x
  y.a = 10
  y.b = 20
  print(x.a, x.b)
  print(y.a, y.b)

test()
```

주의 사항: value 가 register로 할당되면 moveinit은 사용할 수 없고, 오직 copyinit만 사용 가능


### @parameter
여러 함수로 중첩된 함수일 때 사용하는 것으로, 실행중의 value 혹은 생성되는 parameter등을 확인하는 것

```mojo
fn add_print[a: Int, b: Int]():
  @parameter
  fn add[a: Int, b: Int]() -> Int:
    return a + b

```
위와 같이 처리하면 add 함수는 parametric closure 함수가 됨 

이것이 무슨 뜻인가??
실시간으로 변화하는 값들을 계속 추적하여 담고 있게 됨
그런데 이 특징은 안전하지 않은 특성을 갖고 있음...

@parameter를 갖고 있으면...
value 값으로 처리한 상태의 값으로 전환하여 들고 있게 됨

```mojo
fn add_print[a: Int, b: Int]():
  @parameter
  fn add[a: Int, b: Int]() -> Int:
    return a + b
  let x = add[a,b]()
  print(x)

add_print[5, 10]()
```
컴파일 하는 순간 그 값을 미리 계산하여 들고 있어, 실시간 실행 시에 계산을 할 필요를 줄여줌

### @always_inline

```mojo
fn add_print[a: Int, b: Int]():
  @always_inline
  fn add[a: Int, b: Int]() -> Int:
    return a + b
  let x = add[a,b]()
  print(x)

```
일반적으로 compiler는 inline 함수를 만나면 성능 향상을 위해 overhead와 연관된 함수 호출을 다른 형태의 코드로 변환

@always_inline은 컴파일러에 inline 함수처럼 성능 향상을 위해 호출하도록 강제하는 것..?
다만 이 방식은 memory를 더 많이 소모하는 특징을 갖고 있음
그리고 일부 코드가 중복되기 때문에 컴파일된 파일의 크기가 늘어나는 문제 또한 갖고 있게 됨

## Parameterization
파라미터화
python에서는 argument, parameter 를 구분하지 않고 사용함

compile time에서 결정되는 값과, 미리 불러오는 값의 차이

struct, function에서 parameterization이 동작하는 방식...

```mojo
from math import sqrt

fn rsqrt[dt: DType, width: Int](x: SIMD[dt, width]) -> SIMD[dt, width]:
  return 1 / sqrt(x)

print(rsqrt[DType.float16, 4](42))


```
위에서 보이는 것처럼 []안에 parameter들을 정의하고, 설정해두며,
parameter들은 compile되는 시점에서 결정되는 것들이기 때문에...

### Parameterized SIMD Type

