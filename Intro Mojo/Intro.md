# Mojo
python의 superset으로 동작하는 규격화된 프로그래밍 언어

python의 동작보다 3.5만배 처리속도가 빠른 편

빠른 성능, AI에 최적화된 기능, web application 등으로 활용 가능
CPU, GPU, TPU 등 어느 것에도 호환

AI/Machine Learning, Computer data process

기본적으로 python을 기반으로 파생된 언어이므로, 기본 구조는 간단한 편

# Mojo의 기본 특성

## Type이 지정된 함수
python은 동적으로 타입이 변환될 수 있는 변수들을 사용하는데
mojo의 경우 다른 프로그래밍 언어처럼 미리 지정된 형태의 변수를 지정하여 사용함


## Memory-optimized structures
Structures = Objects/Classes
사용되는 열쇠말 - struct
데이터 저장, 불러오기에 최적화된 구조체를 사용함
메모리 누수, 파편화 등을 방지할 수 있음

## 병렬, 평행처리 진행

키워드 2가지로 여러 일을 동시에 처리
async, await

loop unrolling, vectorization, task scheduling 기능 있음

## 함수 꾸미기 키워드
annotate에 해당되는 키워드가 있어서, 함수의 기능, 성능을 올려줄 수 있음
jit, op, graph등등

# Mojo의 동작 방식
MLIR - multi-level intermediate representation
높은 단계, 낮은 단계에 해당되는 프로그램의 요소를 모두 갖고 있음
python, C, cuda 등의 동작 원리를 모두 포함함

## Syntax comparison

```
# dynamic 방식
def add_dynamic(x, y):
  return x + y

# static 방식
# Tensor는 AI가 다루는 데이터 타입으로 vector의 확장형태
# 아래의 함수는 MLIR 오퍼레이터를 사용하기 때문에 연산을 보다 빠르게 처
fn add_static(x: Tensor<f32>, y: Tensor<f32>)Tensor<32>:
  return x + y
```

```
# 같은 차원을 갖는 서로 다른 두 tensor를 생성
a = Tensor.rand((2,3))
b = Tensor.rand((2,3))

# dynamic function을 부르고 결과를 확인할 것
c = add_dynamic(a, b)
print(c)

# static function을 부르고 결과를 확인할 것
d = add_dynamic(a,b)
print(d)
```

위의 두 과정은 같은 결과를 내는데, 결과를 얻는 과정은 다르게 처리

dynamic으로 처리되는 부분은 변수를 동적으로 처리하면서 과부하가 걸림

# Mojo의 AI 지원 특성
## Progressive Type
Integer, String, Boolean
## Parallization
vector를 처리할 때 MLIR을 도입하여, cpu의 thread, gpu의 cuda, 등의 방식으로 빠르게 처리

## Ownership, Borrow 확인
rust와 비슷하게 ownership, borrow를 확인하여 memory 문제(누수 등)를 방지

## 사용자 지정 메모리 관리
사용자가 메모리를 관리할 수도 있음
사용방법이 매우 복잡하지 않다는 특징도 가짐

## Zero-Cost Abstractions
struct에 특정 타입의 변수에 값을 지정하기!
를 처리하면 메모리에 걸리는 과부하를 막을 수 있음

## Integrated Auto-tuning
autotune이라는 함수가 있어서, 
최적의 값을 찾아줌...
이 함수, 혹은 특징은 프로그래밍 프로세스를 간단히 만들고, 코드의 실행 속도를 빠르게 해줌

# Mojo는 Python의 superset으로 등장한 프로그램 언어
python의 특징을 계승하기 때문에 기본적인 프로그래밍 로직이 간단한 편
정확히는 python++의 특성을 직접적으로 계승

## Mojo는 소스코드를 LLVM(컴파일러 디자인 프레임워크)에서 MLIR로 실행
CPU 연산을 직접적으로 바로 사용함
system programming, metaprogramming을 지원

## MLIR
재사용가능하고, 확장 가능한 컴파일러 기본 구조

그외의 내부 구조들은 mojo의 홈페이지에서 확인할 것
