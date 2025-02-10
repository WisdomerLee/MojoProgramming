# mojo와 python의 interoperability

mojo와 python의 다리 역할을 해주는 wrapper library들이 있음

language extensions
interperters등을 활용하는 방식 등등의 여러 방법이 있음


## python 모듈, library 활용하기

```mojo
from python import Python
let np = Python.import_module('numpy')
# 위에서 python의 모듈을 넣고 나서는 아래에는 python처럼 코드를 작성하면 됨
a = np.array([10,20,30])

print(a)

```


## python으로 만든 일반 file/library 넣기
아래의 내용을 가진 program.py 파일을 넣는다면?

```python
import numpy as np

def code(a,b):
  z = np.random.rand(a,b)
  return z
```
와 같은 파일이 있다고 가정하고,
해당 파일을 mojo에서 불러오게 한다면 어떻게 해야 할까?

```mojo
from python import Python

Python.add_to_path('{~.py가 들어있는 폴더 경로}')
let mypython = Python.import_module("{.py의 파일 이름}")
let c = mypython.code(4,5)
print(c)
```

## python에서 mojo 타입 쓰기

```python
%%python
def python_type_function(a_int, b_float, c_bool, d_string, e_list, f_tuple):
  print(type(a_int))
  print(type(b_float))
  print(type(c_bool))
  print(type(d_string))
  print(type(e_list))
  print(type(f_tuple))

```
