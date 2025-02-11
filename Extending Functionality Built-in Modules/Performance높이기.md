# 병렬처리(Parallelization)

```mojo
from algorithm import parallelize

fn matmul_parallelized(C: Matrix, A: Matrix, B: Matrix)
  @parameter
  fn calc_row(m: Int):
    for k in range(A.cols):
      @parameter
      fn dot[nelts: Int](n: Int):
        C.store[nelts](m,n, C.load[nelts](m,n)+ A[m,k] * B.load[nelts](k,n))
      vectorize[nelts,dot](C.cols)
  parallelize[calc_row](C.rows, C.rows)
```
병렬로 실행하도록 선언된 매트릭스 곱

# Compiler의 문제
LLVM, GCC와 같이 널리 알려진 compiler 기술 방식은 CPU, GPU의 종류와 무관하게 처리할 수 있으나, 최신 chip 구조에 적합하도록 동작하는 compiler가 아님

mojo는 MLIR이라는 것을 처음으로 내제한 프로그래밍 언어
mojo는 Python보다 실행 속도가 매우 빠르고, 코드가 실행되는데 최적화되도록 동작함
또한 python의 코드를 mojo로 단순히 import시켜 실행시키더라도 14배 빠르게 동작함
그리고 약간의 코드 변형을 통해(predefined type, parallelization, data caching, auto-tuning, performance 등의 내용을 포함하면 최소 몇 천배 이상의 속도가 나올 수 있음

