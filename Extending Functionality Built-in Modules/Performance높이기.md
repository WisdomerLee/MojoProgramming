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
