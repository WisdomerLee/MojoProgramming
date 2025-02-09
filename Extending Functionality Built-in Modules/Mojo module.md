# Mojo의 Module

Mojo modules
mojo packages

## mojo modules
쉽게 말해 file 단위로 생각할 것

내부에 함수에 main 함수가 없고 일반 함수들만 존재

아래의 mymodule.mojo의 파일을 살펴보기
```mojo
struct MyPair:
  var first: Int
  var second: Int

  fn __init__(inout self, first: Int, second: Int):
    self.first = first
    self.second = second

  fn dump(self):
    print(self.first, self.second)
```
Main()함수가 없고, import를 통해 다른 파일, 모듈에서 활용 가능

아래의 main.mojo를 살펴보기
```mojo
from mymodule import MyPair
import mymodule
import mymodule as my

fn main():
  let mine = MyPair(2,4) # 첫 번째 import 방식이면 
  let mine = mymodule.MyPair(2,4) # 두 번째 import 방식이면
  let mine = my.MyPair(2,4) # 세 번째 import 방식이면
  mine.dump()
```
두 파일이 모두 같은 폴더에 있다면 위와 같은 형태로 모듈의 내용을 불러올 수 있음

## mojo packages
쉽게 이야기 해 폴더 단위로 인식할 것
기본 폴더 아래에 __init__.mojo 파일과 그 외 같은 기능을 담당하는 연관된 module 파일들의 묶음

Directory structure > __init__.mojo
해당 구조는 논리적으로 연관된 기능을 하나로 묶을 수 있게 해줌

__init__.mojo 파일은
package의 초기화를 담당 혹은 marker를 담당

Init은 패키지를 정의
marker는 초기화 코드가 포함될 수 있고, 함수, 상수, 등이 들어갈 수 있음

## package, module 집어넣기
폴더 > package 이름으로 볼 것
module > ~.mojo 파일의 이름

## Compiled package에서 집어넣기

module을 compiled 된 package module에서 넣게 되면..
~.mojopkg라는 파일 형태로 전환되어 하나의 파일로 처리됨
이렇게 되면 mojopkg앞의 파일 이름(패키지 이름)에서 module 이름을 적용하여 집어넣기
pkg파일은 init이 내부에 들어있어야 함
folder 내부에도 init이 들어있어야 함



## Compiled package 만들기
compiling package는 mojo package command를 이용하여 만듦

```
mojo package {패키지로 만들 폴더이름} -o {패키지 이름}.mojopkg

```
위와 같은 명령어로 처리할 것

## package 이름 바꾸기
mojopkg의 파일 이름을 바꾸어도 package 이름이 변경되지 않으므로 주의할 것
```
mojo package {패키지로 만들 폴더이름} -o {패키지 이름}.mojopkg
```
위의 커맨드로 compile을 다시 할 것...

# __init__ 파일 특징
빈 파일일 수 있음 > 그러나 아주 중요한 역할을 수행
python과 달리 import를 통해 실행시킬 수 없음

## init.mojo의 목적
패키지 인식... index
python과 달리 init을 실행시킬 수 없음
api 접근으로 init.mojo를 사용

## import statement를 간단히

using {패키지 이름}.{모듈 이름}
패키지의 
__init__.mojo 파일에
```mojo
from .mymodule import MyPair
```
와 같은 문구가 쓰여있으면..
(파일 이름 앞에 .이 있음을 기억할 것)

main 파일에서
```mojo
from MyPackage import MyPair

```
위와 같이 module 이름을 생략하고 바로 넣는 것을 볼 수 있음

## mojo의 표준 library와 __init__.mojo 사용

```mojo
from algorithm.functional, import map
```
와 같이 특정한 기능만 넣고 싶을 때..

__init__.mojo에
```mojo
from .functional import *
from .reduction import *
```
위와 같이 선언되어 있어 가능한 것
