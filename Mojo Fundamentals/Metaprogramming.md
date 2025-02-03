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

```
@value
struct Person:
  var name: String
  var age: Int

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
