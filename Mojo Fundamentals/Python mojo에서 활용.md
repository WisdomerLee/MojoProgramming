# Mojo에서 python 활용하기

mojo는 python의 subset이기 때문에 python의 모든 프로그램들과 호환이 가능

python 프로그래머는 mojo를 즉시 사용할 수 있어야 하나
그러나 Mojo는 아직 초기 개발 단계에 있으며 많은 Python 기능이 아직 구현되지 않았습니다.
현재 Python에서 작성할 수 있는 모든 것을 Mojo에서 작성할 수는 없으며 Mojo에는 아직 자체 패키지 생태계가 없습니다.

이러한 격차를 해소하기 위해 Mojo를 사용하면 Python 모듈을 가져오고,
Python 함수를 호출하고, Mojo 코드에서 직접 Python 개체와 상호 작용할 수 있습니다.
Mojo는 표준 Python 인터프리터(CPython)를 사용하여 Python 코드를 실행하므로 기존 Python 코드를 수정할 필요가 없습니다.

## Python module 불러오기
Python.import_module()을 이용하여 python module을 불러올 수 있음
```mojo
from python import Python

fn use_array() raises:
  var np = Python.import_module("numpy")
  var array = np.array([1,2,3])
  print(array)

use_array()
```

위의 방식은 numpy를 집어넣고 호출하는 방식이며,
다른 python의 설치된 모듈도 똑같은 방식으로 호출할 수 있음

그런데 활용에서 주의해야 할 사항이 있는데, 현재는 개별, python의 클래스, 함수를 불러올 수는 없고, 모듈 전체를 가져와서 모듈에서 접근해야 함...
Mojo는 아직 최상위 코드를 지원하지 않으므로 import_module() 호출은 다른 메서드 내에 있어야 합니다.
이는 모듈을 여러 번 가져오거나 모듈에 대한 참조를 전달해야 할 수도 있음을 의미합니다.
이는 Python과 동일한 방식으로 작동합니다. 모듈을 여러 번 가져오더라도 초기화 논리가 다시 실행되지 않으므로 성능 저하가 없습니다.

import_module() 함수는 예외를 발생시킬 수 있습니다(예: 모듈이 설치되지 않은 경우).
함수 내에서 이를 사용하는 경우 try/제외 절을 사용하여 오류를 처리하거나 함수 시그니처에 raises 키워드를 추가해야 합니다.
예외를 발생시킬 수 있는 Python 함수를 호출할 때도 이 문제가 발생합니다.
(예외 발생은 Mojo 표준 라이브러리보다 Python 코드에서 훨씬 더 일반적이므로 성능상의 이유로 사용이 제한됩니다.)

참고: Mojo는 런타임에 Python 인터프리터와 Python 모듈을 로드하므로 Mojo 프로그램을 실행할 때마다 호환되는 Python 인터프리터에 액세스하고 가져온 Python 모듈을 찾을 수 있어야 합니다.
자세한 내용은 Python 환경 섹션을 참조하세요.

## 사용자가 임의로 만든 python module 불러오기

```python
# mypython.py
import numpy as np
 
def gen_random_values(size, base):
    # Generate a size x size array of random numbers between base and base+1
    random_array = np.random.rand(size, size)
    return random_array + base
```
위와 같은 mypython.py이라는 파일에 위와 같은 내용을 적용한 모듈을 하나 만들고
그 모듈을 mojo에서 불러오기로 해보기

```mojo
# main.mojo
from python import Python
 
fn main() raises:
    Python.add_to_path("path/to/module")
    var mypython = Python.import_module("mypython")
 
    var values = mypython.gen_random_values(2, 3)
    print(values)
```
add_to_path라는 함수는 절대경로, 상대경로 모두 다 동작함. 

## Python에서 mojo함수를 호출하기

python에서 mojo를 호출하는 것은 제한사항이 존재함
UI 이벤트 같은 경우. main event loop를 이용하여 존재하는
mojo callback을 python에 전달할 수 없으므로... 위와 같은 경우엔 활용할 수 없음

```python
# myapp.py
import tkinter as tk
 
class App:
    def __init__(self):
        self._root = tk.Tk()
        self.clicked = False
 
    def click(self):
        self.clicked = True
 
    def create_button(self, button_text: str):
        button = tk.Button(
            master=self._root,
            text=button_text,
            command=self.click
        )
        button.place(relx=0.5, rely=0.5, anchor=tk.CENTER)
 
    def create(self, res: str):
        self._root.geometry(res)
        self.create_button("Hello Mojo!")
 
    def update(self):
        self._root.update()
```
위의 파이썬 모듈을...

```mojo
from python import Python
 
fn button_clicked():
    print("Hi from a Mojo🔥 function!")
 
fn main():
    Python.add_to_path(".")
    var app = Python.import_module("myapp").App()
    app.create("800x600")
 
    while True:
        app.update()
        if app.clicked:
            button_clicked()
            app.clicked = False
```
위와 같이 호출할 수도 있음
python 모듈의 Tkinter 의 mainloop() 함수를 호출하는 것 대신, update()메소드를 loop에서 호출하여 clicked 특성을 확인하는 형태로 변형됨을 확인할 수 있음

## Python 개발 환경
Mojo는 python의 개발 환경에 의존하는 부분이 있음

```python
import os
import subprocess
 
FIND_LIBPYTHON = """
import os
import sys
from pathlib import Path
from sysconfig import get_config_var
ext = "dll" if os.name == "nt" else "dylib" if sys.platform == "darwin" else "so"
binary = f"libpython{get_config_var('py_version_short')}.{ext}"
for folder in [Path(get_config_var(p)) for p in ["LIBPL", "LIBDIR"]]:
    libpython_path = folder / binary
    if libpython_path.is_file():
        print(libpython_path.resolve())
        exit(0)
exit(1)
"""
FIND_PYTHON_VER = "import sysconfig; print(sysconfig.get_python_version())"
 
exe_names = ["python3", "python"] + [f"python3.{i}" for i in range(8, 13)]
seen = []
executables = []
 
print("Mojo will attempt to use the first Python executable from the top:\n")
print("vers | compat | path")
for path in os.environ["PATH"].split(":"):
    for exe in exe_names:
        full_path = os.path.join(path, exe)
        if os.path.exists(full_path):
            pyver = subprocess.check_output([full_path, "-c", FIND_PYTHON_VER], text=True).strip()
            res = subprocess.run([full_path, "-c", FIND_LIBPYTHON], text=True, capture_output=True)
            libpython = res.stdout.strip()
            if res.returncode != 0:
                print(f"{pyver:<7} no      {full_path}")
            elif libpython not in seen:
                print(f"{pyver:<7} yes     {full_path}")
                seen.append(libpython)
                executables.append(full_path)
 
if not executables:
    print("No compatible Python environments found")
else:
    print("\nCreate and activate a virtual environment to use a different Python version:")
    print(f"    {executables[-1]} -m venv .venv")
    print("    source .venv/bin/activate")
```
파이썬의 개발환경은 위의 코드를 실행하여 확인할 수 있음
