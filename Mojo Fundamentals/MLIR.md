# MLIR이 무엇인가?
MLIR(Multi-Level Intermediate Representation)은 일련의 명령어 세트가 메모리 내 값에 대해 작동하는 어셈블리 언어와 유사한 프로그램의 중간 표현입니다.

MLIR의 주요 강점은 모듈식이며 확장 가능한 특성입니다. MLIR은 점점 더 많은 "방언"으로 구성되어 있으며 각 방언은 특정 작업과 최적화를 정의합니다.
예를 들어, 'math' 방언은 사인 및 코사인과 같은 수학 연산을 제공하는 반면, 'amdgpu' 방언은 AMD 프로세서에 특정한 연산을 제공합니다.

MLIR의 핵심 기능 중 하나는 방언 간의 상호 운용성입니다. 이를 통해 MLIR은 서로 다른 컴퓨팅 환경을 지원할 수 있습니다.
새롭고 더 빠른 프로세서와 아키텍처가 개발됨에 따라 새로운 MLIR 방언을 구현하여 해당 환경에 맞는 최적의 코드를 생성할 수 있습니다.
모든 새로운 MLIR 방언은 기존 방언과 원활하게 통합될 수 있으므로 추가 방언이 도입되면 모든 MLIR이 더욱 강력해집니다.

이러한 유연성은 아래에서 살펴볼 OurBool 유형과 같은 사용자 정의 유형을 생성하여 Python과 유사한 높은 수준의 인터페이스를 제공할 수 있음을 의미합니다.
내부적으로 Mojo와 MLIR은 새로운 프로세서가 등장할 때 이러한 편리하고 높은 수준의 유형을 최적화할 것입니다.

MLIR이 혁신적인 기술인 이유에 대해 논의할 내용이 훨씬 더 많지만 Mojo로 돌아가 OurBool 유형을 정의해 보겠습니다.

## OurBool을 정의해보기
struct 키워드를 이용하여 
```mojo
struct OurBool:
  var value: __mlir_type.i1
```
boolean은 0, 1로 true, false를 표현
OurBool은 value라는 값을 갖고 있고, MLIR에서 사용하는 내정된 타입인 i1을 사용
mojo에서는 MLIR type은 __mlir_type이라는 것을 앞에 접두사처럼 갖고 있음

i1이라는 형태로 leverage MLIR 연산을 사용할 수 있고, i1형태의 많은 데이터들을 추적하는데 최적화됨

## Leveraging MLIR
OurBool을 정의하였으니, 그대로 사용을 시도하려고 하면, 에러를 맞이하게 됨
```mojo
var a = OurBool()
```
Python에서처럼 __init__() 함수가 필요함

```mojo
struct OurBool:
  var value: __mlir_type.i1

  fn __init__(inout self):
    self.value = __mlir_op.'index.bool.constant' [
      value=__mlir__attr.false,
    ]()
```

기본 i1 값을 초기화하기 위해 index.bool.constant라는 '인덱스' 방언의 MLIR 작업을 사용

MLIR의 '인덱스' 방언은 OurBool의 값을 저장하는 데 사용하는 i1과 같은 내장 MLIR 유형을 조작하기 위한 작업을 제공합니다.
index.bool.constant 작업은 true 또는 false 컴파일 타임 상수를 입력으로 사용하고 지정된 값을 사용하여 i1 유형의 런타임 출력을 생성합니다.

위에 표시된 대로 Mojo는 __mlir_op 접두사를 통해 모든 MLIR 작업과 __mlir_attr 접두사를 통해 모든 속성에 대한 직접 액세스를 제공합니다.
MLIR 속성은 컴파일 타임 상수를 나타냅니다.

MLIR과 상호 작용하기 위한 구문이 복잡해 보일 수 있지만 MLIR 속성은 대괄호 [...] 사이에 전달되고 작업은 런타임 인수 값을 사용할 수 있는 괄호 접미사(...)를 통해 실행됩니다.
대부분의 Mojo 프로그래머는 MLIR에 직접 액세스할 필요가 없습니다. 그렇게 하는 사람들에게 이 구문은 MLIR의 강력한 방언 시스템을 내부적으로 활용하는 상위 수준 유형을 정의할 수 있는 "초강력"을 제공합니다.

이제 __init__ 메소드를 정의했으므로 OurBool 유형의 인스턴스를 생성할 수 있습니다.

## Value Semantics

```mojo
var a = OurBool()
var b = a 
```
위와 같이 처리할 때 b = a를 처리할 때 또 에러가 발생함

mojo는 value semantics를 기본으로 사용하고, 이 말은 a를 b에 할당할 때 a의 복사본이 생성되어야 한다는 것
그러나 OurBool 혹은 i1값을 복사하는 방법을 가정해두지 않기 때문에, __copyinit__ 메소드를 정의해야 함

우리의 경우 OurBool은 "사소하게 복사 가능한" 멤버가 하나만 있는 단순 유형입니다.
데코레이터를 사용하여 Mojo 컴파일러에 이를 알리고 자체 __copyinit__ 상용구를 정의하는 수고를 덜 수 있습니다.
간단하게 복사 가능한 유형은 자신의 인스턴스를 반환하는 __init__ 메서드를 구현해야 하므로 초기화 프로그램도 약간 조정해야 합니다.

```mojo
@register_passable("trivial")
struct OurBool:
    var value: __mlir_type.i1
 
    fn __init__(inout self):
        self.value = __mlir_op.`index.bool.constant`[
                value=__mlir_attr.false,
            ]()
```
위와 같이 @register_passable 데코레이터를 이용하면

```mojo
var c = OurBool()
var d = c
```
위와 같이 사용 가능

## Compile-time constants

```mojo
@register_passable("trivial")
struct OurBool:
    var value: __mlir_type.i1
 
    # ...
 
    fn __init__(inout self, value: __mlir_type.i1):
        self.value = value
```
alias라는 키워드를 이용하여 compile-time constant를 정의할 수 있음

```mojo
alias OurTrue = OurBool(__mlir_attr.true)
```
i1형태의 true 값을 집어넣기
조금 다른 형태로 false 값을 넣어보기
```mojo
alias OurFalse: OurBool = __mlir_attr.false
```

__init__으로 OurBool의 값을 상수를 이용하여 초기화 하도록 바꾸어보기
```mojo
alias OurTrue = OurBool(__mlir_attr.true)
alias OurFalse: OurBool = __mlir_attr.false
 
 
@register_passable("trivial")
struct OurBool:
    var value: __mlir_type.i1
 
    # Simplified no-argument constructor:
    fn __init__(inout self):
        self = OurFalse
 
    fn __init__(inout self, value: __mlir_type.i1):
        self.value = value
```
위에서 보면 OurTrue를 OurBool 정의하기 전에 정의하였는데, Mojo compiler가 알아서 처리함...

이런 상수를 이용하여 아래와 같이 초기화 할 수 있음
```mojo
var e = OurTrue
var f = OurFalse
```

## __bool__ 넣기
boolean은 기본적으로 논리 흐름 조절에 사용하는, 범용적인 프로그래밍 방식임
그런데 아래와 같이 OurBool을 사용하려고 하면 에러가 발생함

```mojo
var a = OurTrue
if a: print("It's true!")
```

mojo에서는 OurBool이 boolean타입인지 알 수 없음.
mojo는 단순히 OurBool은 1bit 크기를 가진 데이터를 갖고 있는 것만 봄
mojo는 bool과 같은 표준 라이브러리 유형에서 사용되는 것과 같은 bool을 전달하는 interface를 제공하는데, 
표준 라이브러리의 모든 유형을 사용자가 재정의하여 사용할 수 있음...

위의 방식으로 사용하고 싶다면 OurBool 내부에 __bool__ 메소드를 구현해야 함
__bool__의 구현은 간단한 편이고, mojo의 표준 라이브러리, 내장 유형은 MLIR을 기반으로 구현되므로, i1을 사용하는 생성자를 정의...

```mojo
alias OurTrue = OurBool(__mlir_attr.true)
alias OurFalse: OurBool = __mlir_attr.false
 
 
@register_passable("trivial")
struct OurBool:
    var value: __mlir_type.i1
 
    # ...
 
    fn __init__(inout self, value: __mlir_type.i1):
        self.value = value
 
    # Our new method converts `OurBool` to `Bool`:
    fn __bool__(self) -> Bool:
        return Bool(self.value)
```

그러면 
아래와 같이 사용할 수 있게 됨
```mojo
var a = OurTrue
if a: print("It's true!")
```
