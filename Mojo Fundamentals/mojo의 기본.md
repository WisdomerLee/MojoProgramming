# mojo의 기본
변수, 선언, 데이터형태

## Variables
Containers 
값을 저장하는 곳, 변수들
특정 값을 지정하여, 그 값을 호출하는데 사용

## Declarations
변수를 변형 가능, 변형 불가능으로 바꾸어 설정
let, var
let은 변형 불가, var는 변형 가능

## DataTypes
String, Integer, Float, Bool

## let vs var
let은 변형되지 않음
var는 변형 가능

Variables = data를 저장하는 역할을 함
declaration = 저장된 데이터가 불변인지, 변경 가능인지를 선언
datatypes = 저장되는 데이터의 형태를 지정

var로 선언할 때는 datatype과 그 값은 나중에도 집어넣을 수 있음
var로 선언된 것은 나중에도 값을 바꿀 수 있음

let으로 선언되는 것은 선언과 동시에 반드시 값이 지정되어야 하기 때문에 타입을 지정하지 않아도 됨

# variables의 범위
global scope, function scope, block scope

## fn, def 함수 선언
fn의 경우 타입을 강제 지정하고, 메모리 문제에서 안전한 방식

vs code에서는 def 함수의 선언 방식을 공식적으로 지원하진 않음
fn 방식으로 선언하는 것을 지원

함수 선언할 때 함수의 입력으로 지정되는 argunment들 (입력 변수)들은 무조건 타입이 지정되어야 하고, 출력되는 변수 역시 타입이 지정되어야 함
함수 내부의 변수는 타입을 지정하지 않아도 됨

또한 기본 값을 지정하게 할 수 있음 > 기존 python의 코드에서 하던 것처럼 값을 지정한 형태로 함수를 선언하면, 해당 변수는 선언하지 않으면 개발자가 선언한 기본 값으로 자동으로 지정되어 들어감

## struct 변수
method, field, 함수 overloading, meta programming을 위한 가리킴 키워드

class와 차이...
python의 class는 생성된 뒤에 수정할 수 있으나
mojo의 struct는 생성된 뒤에 수정할 수 없음 > compile된 이후 수정 불가능한 상태로 고정됨
