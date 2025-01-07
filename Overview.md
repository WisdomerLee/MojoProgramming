# Mojo
Deep Learning을 보다 쉽게 구현하고, 보다 빠르게 실행하는데 특화된 프로그래밍 언어
python의 쉬운 용법과 C언어의 빠른 속도의 장점을 결합한 언어
병렬로 처리되는데 특화되어있는데, C++처럼 복잡하게 하지 않아도 병렬로 쉽게 처리할 수 있음

# Mojo 환경 설정
https://developer.modular.com/download
위의 링크를 통해 Mojo SDK를 받을 수 있음

또한 Mojo는 기본적으로 Linux 기반으로 동작하기 때문에 windows 환경에서는 WSL을 구축하고 그 곳에서 이용해야 함
ubuntu에서는 modular를 깔기 위해 아래의 커맨드를 입력하고
```
curl https://get.modular.com | sh - && modular auth mut_40dd6c2755
```
mojo 설치를 위해
```
modular install mojo
```
를 입력할 것

plyground.modular.co에서는 간단한 프로그래밍을 jupyter notebook을 이용하여 확인할 수 있음

# Window에 Mojo 설치하기
윈도우의 WSL을 이용하여 Mojo 설치하기

windows의 powershell을 켜고
```
wsl --version
```
위의 커맨드를 입력하여 wsl의 버전을 확인하기

wsl에 ubuntu 22 버전을 설치 (22.04 version)

설치가 되고 나면 ubuntu에서 사용하게 될 사용자 이름과 비밀번호를 설정해야 함 - os의 계정과 별도이므로 이 부분은 따로 기억해 둘 것

설치가 완료되고, 사용자 이름 설정이 끝나면 linux 터미널을 이용하여
```
sudo apt update
```
를 입력하여 os 패키지를 업데이트해두기

python 개발환경을 설정하기
```
sudo apt install python3.10-venv
```
위의 커맨드는 python 3.10 버전의 환경을 설치, 만약 요구하는 버전이 있다면 그 버전으로 설치할 것

modular를 설치하기 위해 
```
curl https://get.modular.com | sh - && modular auth mut_40dd6c2755
```
커맨드를 입력하고
해당 내용이 설치가 완료되면 이어서 아래의 내용을 실행할 것

```
modular install mojo
```

그렇게 설치하고 mojo를 커맨드를 입력하면 mojo를 찾을 수 없다는 에러를 발견할 수 있는데
이는 환경변수에 mojo의 기본 경로가 설정되어있지 않기 때문에 찾지 못 하는 것

mojo를 설치하고 나오는 안내 command를 이용하여 시스템에 환경변수를 추가할 것

그 설정이 완료되고나서 커맨드 창에
```
mojo
```
를 입력하면 mojo를 이용한 프로그래밍이 가능하게 됨...

mojo를 사용하기 위한 폴더를
```
mkdir 폴더이름
```
으로 만들어두고
```
cd 폴더이름
```
위에서 만든 폴더로 들어가고나서

```
code .
```
을 이용하여 visual studio code를 설치하기

visual studio code가 설치되면, mojo plugin을 설치하고
wsl 플러그인도 설치할 것

만약 실행 시에 mojo 환경이 설정되어있다고 하지 않는다면
visual studio code를 종료하고
```
mojo
```
입력하고 다시 종료한 뒤에
```
code .
```
으로 다시 설치를 진행할 것

그리고 나서 plugin들의 설치 내용을 확인하고 실행하기

# Windows WSL을 업데이트 후 Mojo를 설치하기

https://docs.modular.com
에 접속하면 install max & mojo 라는 부분이 있는데
해당 내용을 클릭하면...

각 os에 따라 설치하는 방법이 순차적으로 나와있으므로 해당 내용을 따라할 것

차이가 있다면 가상환경의 활성화와 max를 설치하는 것

# Docker를 windows에 설치하기
docker desktop을 설치할 것

WSL 버전을 업데이트할 필요가 있을 것
```
wsl --update
```
로 버전을 업데이트 할 것


# Mojo - Docker
윈도우에서 폴더 하나를 만들고
그 폴더를 visual studio code에서 열고
docker extension을 설치할 것

dockerfile.mojosdk
라는 파일을 만들고
docker에서 맞춰야 할 환경 설치를 작성할 것

그리고 해당 내용을 토대로 image를 만들기
visual studio code에서 해당 파일을 우클릭하여 image를 만들 것

image가 만들어졌으면 visual studio code에서 docker extension을 클릭하면 images를 볼 수 있음

그러면 docker를 이용하여 만든 image를 실행할 수 있음

docker 환경 내부에 프로그램을 만들어 실행할 것
이렇게 하면 image로 설정한 개발 환경이 변화하지 않으므로 개발 환경 제어가 쉬움
