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

