# Object - Oriented Programming

객체(object)의 개념을 앱과 소프트웨어 단계에서 디자인할 수 있도록 설계된 것을 뜻함

mojo에서도 class를 구현할 수 있음
## class와 상속(inheritance)
```mojo
class Animal {
  var name: String

  int(name: String) {
    self.name = name
  }
  func speak(){
    println("Hello, I am \(name)")
  }
}

class Dog: Animal {
  func bark() {
    println("Woof! Woof!")
  }
}

let dog = Dog(name: "Buddy")
dog.speak()
dog.bark()
```

class는 클래스를 정의하기 위해 앞에 선언한 키워드,
init은 해당 객체를 초기화할 때 사용되는 함수
Dog 클래스는 Animal 클래스를 상속받음

## Polymorphism, Encapsulation
polymorphism은 함수가 여러 형태로 정의될 수 있는 것
encapsulation은 객체 내부의 상태를 숨기는 것을 뜻 함

```mojo
class Cat: Animal {
  override func speak() {
    println("Meow, I am \(name)")
  }
}

let cat = Cat(name: "Whiskers")
cat.speak()
```
위의 예시는 polymorphism의 예시...

```mojo
class BankAccount {
  private var balance: Float = 0.0

  func deposit(amount: Float) {
    balance += amount
  }

  func getBalance() -> Float {
    return balance
  }
}

let account = BankAccount()
account.deposit(100.0)
println(account.getBalance())
```

private - class 내부에서만 접근 가능

# Functional Programming
함수 프로그래밍은 함수로 구성되고, 활용되는 프로그램 방식

higher-order function, immutability, recursion등의 기법을 사용

## Higher-order function
```mojo
func applyTwice(f: (Int) -> Int, x: Int) -> Int {
  return f(f(x))
}

func increment(x: Int) -> Int {
  return x + 1
}

println(applyTwice(f:increment, x:5))
```
함수 자체도 파라미터 형태로 전달하여, 함수를 두 번 적용하게 하는 예시

## Immutability

```mojo
let numbers = [1, 2, 3]
let newNumbers = numbers.map { $0 * 2 }
println(newNumbers) // Outputs: [2, 4, 6]
```
함수가 실행되어도 실제 데이터는 영향이 없는 것
numbers는 값이 그대로 유지됨
map은 collection으로 정의되는 데이터들에 각각의 요소들마다 함수를 적용하는 것

## Recursion
내부에서 함수 자체를 소환하여 처리하는 것
```mojo
func factorial(n: Int) -> Int {
    if n == 0 {
        return 1
    } else {
        return n * factorial(n: n - 1)
    }
}
 
println(factorial(n: 5)) // Outputs: 120
```
위에서 보이는 것처럼 함수는 내부에서 함수 자신을 호출하고 있음을 확인할 수 있음

# 병렬처리
## Threads

```mojo
import threading
 
func printNumbers() {
    for i in 1...5 {
        println(i)
    }
}
 
let thread = threading.Thread(target: printNumbers)
thread.start()
thread.join()
```
mojo에서는 thread를 통한 병렬처리 방식을 지원함...
여러 동작이 하나의 동작의 결과와 무관하게 동시에 처리되어야 할 경우 처리시간이 줄어들게 되어 성능 향상에 도움이 됨
threading.Thread > thread를 만들어 thread에서 처리할 함수를 같이 전달


## Async Programming
동작이 실행되더라도 프로그램의 실행이 막히지 않고 실행되도록 처리함

```mojo
import asyncio
 
func fetchData() async -> String {
    await asyncio.sleep(2) // Simulates a delay
    return "Data fetched"
}
 
async func main() {
    let result = await fetchData()
    println(result) // Outputs: Data fetched
}
 
asyncio.run(main())
```
async/await 두 키워드가 한 쌍으로 이루어져 비동기를 가능하게 함
asyncio : 비동기 코드 모듈
대체로 파일 저장, 불러오기 기능에 비동기 기능을 많이 활용함
