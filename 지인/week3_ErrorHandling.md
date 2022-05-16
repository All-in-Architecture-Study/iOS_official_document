## Error Handling

- 어떤 종류의 에러가 발생되는지 확인할 수 있어야 하며 적절할 처리가 필요하다.
- 각 에러 상황별 식별자를 사용자에게 제공해주어 에러별로 대응할 수 있도록
- Swift의 에러처리는 Cocoa NSError 클래스와 상호 호환되는 에러 핸들링 패턴 사용

### Error 타입 설정

Swift에서 Error 프로토콜을 따르는 타입의 값으로 표현되며, 에러를 그룹화하고 추가적인 정보를 제공함

```swift
enum VendingMachineError: Error {
     case invalidSelection
     case insufficientFunds(coinsNeeded: Int)
     case outOfStock
}
```

예를 들어, 코인 5개가 더 필요하다는 에러 발생시킬 수 있다

```swift
throw VendingMachineError.insufficientFunds(coinsNeeded: 5)
```

### 1. 에러 발생시키는 함수 사용하기

: 에러가 발생한 함수에서 return 값으로 에러 반환함 

- `throw` : 어떤 함수, 메소드 혹은 초기자가 에러를 발생시킬 수 있다는 것을 알리기 위해 함수 선언부에 파라미터 뒤에 붙일 수 있다.

```swift
func canThrowErrors() throws -> String // throwing function
```

- 오직 throwing function만이 에러를 발생시킬 수 있으며,
만약, throwing function이 아닌 함수에서 throw가 발생한다면 반드시 그 함수내에 throw에 대해 처리 되야 한다.
    
    ```swift
    struct Item {
        var price: Int
        var count: Int
    }
    
    class VendingMachine {
        var inventory = [
            "Candy Bar": Item(price: 12, count: 7),
            "Chips": Item(price: 10, count: 4),
            "Pretzels": Item(price: 7, count: 11)
        ]
        var coinsDeposited = 0
    	
    		//이는 각 에러 타입에 맞는 throw를 발생시키는 메소드 
        func vend(itemNamed name: String) throws {
            guard let item = inventory[name] else {
                throw VendingMachineError.invalidSelection
            }
    
            guard item.count > 0 else {
                throw VendingMachineError.outOfStock
            }
    
            guard item.price <= coinsDeposited else {
                throw VendingMachineError.insufficientFunds(coinsNeeded: item.price - coinsDeposited)
            }
    
            coinsDeposited -= item.price
    
            var newItem = item
            newItem.count -= 1
            inventory[name] = newItem
    
            print("Dispensing \(name)")
        }
    }
    ```
    

- 에러가 발생하면 함수에서 에러를 발생시키고 빠르게 함수를 탈출 할 수 있도록 해야한다! (early exit)
- do-catch / try? / try! 구문 사용해서 에러 처리 해야됨

```swift
let favoriteSnacks = [
    "Alice": "Chips",
    "Bob": "Licorice",
    "Eve": "Pretzels",
]
func buyFavoriteSnack(person: String, vendingMachine: VendingMachine) throws {
     let snackName = favoriteSnacks[person] ?? "Candy Bar"
     try vendingMachine.vend(itemNamed: snackName)
		//vend()가 에러를 발생시킬 수 있기 때문에 try 키워드 붙임
}
```

### 2. do-catch 구문 사용

- do 구문 안에서 에러가 발생하면 에러의 종류를 catch 구문으로 구분해서 처리
- catch 뒤에 어떤 에러인지 적고, 어떻게 처리할지 (만약, catch 뒤에 에러타입 명시하지 않으면, 지역상수인 error로 바인딩)

```swift
var vendingMachine = VendingMachine()
vendingMachine.coinsDeposited = 8
do {
    try buyFavoriteSnack(person: "Alice", vendingMachine: vendingMachine)
    print("Success! Yum.")
} catch VendingMachineError.invalidSelection {
    print("Invalid Selection.")
} catch VendingMachineError.outOfStock {
    print("Out of Stock.")
} catch VendingMachineError.insufficientFunds(let coinsNeeded) {
    print("Insufficient funds. Please insert an additional \(coinsNeeded) coins.")
} catch {
    print("Unexpected error: \(error).")
}
// Prints "Insufficient funds. Please insert an additional 2 coins."
```

- 모든 지정 Error 타입을 catch 할 때는 `is` 키워드를 사용

```swift
func nourish(with item: String) throws {
    do {
        try vendingMachine.vend(itemNamed: item)
    } catch is VendingMachineError {    // 모든 VendingMachineError 구분을 위해 is를 사용
        print("Invalid selection, out of stock, or not enough money.")
    }
}

do {
    try nourish(with: "Beet-Flavored Chips")
} catch {
    print("Unexpected non-vending-machine-related error: \(error)")
      // 여기에서 처럼 catch를 그냥 if-else에서 else 같이 사용 가능
}
// Prints "Invalid selection, out of stock, or not enough money."
```

### 3. 옵셔널 값 반환

- `try?` 구문으로 에러를 옵셔널 값으로 변환
- 발생하는 모든 에러를 같은 방법으로 처리하고 싶을 때 `try?` 를 사용한다.

```swift
func someThrowingFunction() throws -> Int {
    // ...
}
//무슨 타입이든 상관없이 옵셔널이 된다.
//통과한다면 여기서는 optional Integer 타입이 될 것이다.
let x = try? someThrowingFunction()

let y: Int?
do {
    y = try someThrowingFunction()
} catch {
    y = nil
}
```

### 4. 에러 발생을 중지하기

- 에러가 발생되지 않을 거라고 확신하는 경우 `try!` 를 사용

```swift
let photo = try! loadImage(atPath: "./Resources/John Appleseed.jpg")
```

### defer

- 함수가 종료된 후 파일 스트림을 닫거나, 사용했던 자원을 해지하는 일을 할 수 있음.
- defer가 여러개 있는 경우 가장 마지막줄부터 실행 → buttom-up 순으로
- open() → close() 짝을 이뤄 함수 실행

```swift
func processFile(filename: String) throws {
    if exists(filename) {
        let file = open(filename)
        defer {
            close(file) // block이 끝나기 직전에 실행, 주로 자원 해제나 정지에 사용
        }
        while let line = try file.readline() {
            // Work with the file.
        }
        // close(file) is called here, at the end of the scope.
    }
}
```

### 공식문서 자료

[https://jusung.gitbook.io/the-swift-language-guide/language-guide/17-error-handling](https://jusung.gitbook.io/the-swift-language-guide/language-guide/17-error-handling)
