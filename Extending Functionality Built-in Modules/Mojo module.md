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


