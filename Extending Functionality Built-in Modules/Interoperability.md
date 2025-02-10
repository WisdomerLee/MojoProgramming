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

primitive types - built in type
사용자 지정 data type 

value를 넘겨주는 방식을

mojo에서 dict() 함수로 쓰기

```mojo
from python import Python
from python.object import PythonObject

var dictionary = Python.dict()
dictionary["Car"] = "BMW"
dictionary["Bike"] = "Harley Davidson"

print(dictionary.keys())
print(dictionary.values())
```

# Pandas의 데이터 분석을 들여오기

```mojo
from python import Python
let pd = Python.import_module("pandas")

df = pd.read_csv("/home/~.csv")

print(df)
print(df.info())
```
물론 위의 코드를 쓸 수 있으려면 python 환경에서 pip install pandas를 이용해 해당 패키지가 깔려있어야 함

# Matplotlib의 그래프 그리기 기능을 불러오기

```mojo
from python import Python

let plt = Python.import_module("matplotlib.pyplot")
x = ["A", "B", "C", "D"]
y = [10, 20, 30, 40]

plt.bar(x,y)
plt.show()
```
pyplot으로 그래프를 그리려면 최소한 x, y의 두 개에 해당되는 데이터 묶음들이 필요함
