# modular Install

mojo 설치방법
https://developer.modular.com/login
으로 계정에 접속한 뒤에

## Windows
윈도우에서 설치하는 방법
WSL이 필요 
기본적으로 linux 기반으로 동작하는 언어이기 때문에 windows에서는 linux를 실행할 수 있게 하는 WSL이라는 시스템의 도움이 필요

Ubuntu 22.04 버전을 기반으로 설치할 것

### Visual Studio code를 설치
1.python extension pack을 설치 > 프로그램 재실행하기
2.WSL extension 설치
mojo sdk는 기본적으로 ubuntu, linux 기반으로 동작하기 때문에 windows에서는 반드시 wsl이 필요

### WSL에 Ubuntu 22.04.2 LTS 버전을 설치
ubuntu를 실행하려고 하면 에러가 나는데
Windows Subsystem for Linux가 활성화되지 않았다고 메시지가 뜰 것

윈도우 기능 켜기/끄기(Turn Windows features on or off) 를 들어가서
Windows Subsystem for Linux를 활성화 > 재부팅

ubuntu를 다시 실행하면
ubuntu에서 사용할 사용자 이름과 비밀번호를 지정하게 될 것

mojo를 설치하기전에 modular라는 것을 먼저 설치할 것

modular의 하나로 mojo가 있음

# mojo 사용방식
터미널에 mojo를 입력하면 python처럼 터미널에서 mojo를 줄 단위로 실행할 수 있음

## mojo 확장
python extension, jupyter notebook extension 
visual studio code에서 mojo extension, python extension, jupyternotebook extension
설치할 것

## WSL 실행시 error
WSL disto is running with wsl1이라는 에러가 발생하며 실행이 되지 않을 경우
visual studio의 설정으로 들어가서
remote로 검색하기
notebook experimental remote save, remote tunnels access prevent sleep
등의 내역 모두 활성화

settings.json 편집을 누르고
"remote.WSL.fileWatcher.polling":true,
"remote.WSL.debug":true,
"remote.WSL.fileWatcher.pollingInterval":10000,
의 내용을 추가하고 저장하기

## mojo의 기본 파일
~.mojo의 확장명을 갖고 있음
fn 은 함수를 선언할 때의 키워드

# mojo를 ubuntu, mac에 설치
