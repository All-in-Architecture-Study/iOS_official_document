## 접근제어 (Access Control)

- Access control restricts access to parts of your code from code in other source files and modules.
- 코드를 작성하는 한 파일에서 다른 파일에 있는 코드에 대한 접근을 명시적으로 작성해서 이를 관리함

### Module과 Source file

- `module` 과 `source file` 에 따라 다른 접근함
- Module
    - 코드를 배포하는 단일 단위로 하나의 프레임워크나 앱이 이 단위로 배포
    - 다른 모듈에서 Swift의 `import` 키워드를 사용해 import 될 수 있음
    - 프로젝트의 하위에 있는 targets도 각각 모두 하나의 module 임
    - ex) UIKit, Foundation
- Source File
    - 각각의 module 안에 있는 파일들
    - 소스파일에 여러 특정 타입을 선언해 사용할 수 있음
    - ex) example.swift 같은 파일들

### 접근레벨 (Access Levels)
- Open
    - 가장 개방적인 접근 제어자
    - 선언한 모듈이 아닌 다른모듈에서도 사용이 가능함.
    - 다른 모듈에서 오버라이드와 서브클래싱이 O
- Public
    - 선언한 모듈이 아닌 다른모듈에서도 사용이 가능함.
    - 다른 모듈에서 오버라이드와 서브클래싱 X
- Internal
    - 기본 접근레벨이며 default 접근 제어자임
    - 작성된 모듈 전체에서만 사용 가능
- File-private
    - 특정 entity를 선언한 소스파일 안에서만 사용 가능함.
    - 서로 다른 클래스가 같은 파일안에 있고 fileprivate로 선언되어 있으면 둘은 서로 접근 가능
- Private
    - 가장 제한적인 접근제어자
    - 특정 엔티티가 선언된 괄호 { } 안에서만 사용 가능
    - fileprivate과 달리 같은 파일 안에 있어도 서로 다른 객체가 private 로 선언되어 있다면 둘은 서로 접근 X
 
![image](https://user-images.githubusercontent.com/37897873/167602793-8c5a2dd1-f209-4a41-b2bd-c1998115bdd2.png)

## 접근레벨 가이드 원칙

- 더 낮은 레벨을 갖고 있는 다른 엔티티를 특정 엔티티에 선언해서 사용 X
- public 변수는 internal, file-private, private 타입에서 정의될 수 없음.
- func (함수)는 그 함수의 parameter type이나 return type 보다 더 높은 접근 레벨을 갖을 수 없음. 
왜냐? 함수에는 접근 가능하지만,
    1) 파라미터에 접근 불가하거나
    2) 반환 타입보다 접근 레벨이 낮아 함수의 관련 코드를 이용할 수 없을 수 있음

```swift
private struct Car {
 //🚨 error
 public var engin: String 
}
```

### Default Access Level

- 아무 Access Level이 명시되지 않으면 `internal` 타입

### Access Levels for Single-Target Apps

- 단일 타겟 앱에서는 특별히 접근 레벨을 명시할 필요가 없다 (?)
- 필요에 따라 file-private, `private` 등을 사용해 앱 내에서 구현 세부사항을 숨길 수 있음

### Access Levels for Frameworks

- 프레임 워크를 개발한다면, `public` or `open`으로 지정해서 다른 모듈에서 볼 수 있고, 접근 가능하도록 해야함
- 감추고 싶은 부분은 `internal` 로 선언

### Access Levels for Unit Test Targets

- 기본적으로  open 이나 public 으로 지정된 엔티티만 다른 모듈에서 접근 가능하지만,
- 유닛 테스트를 하는 경우 모듈을 Import 할 때, import 앞에 `@testable` 이라는 Attribute를 붙여주면 모듈을 테스트 가능한 모듈로 컴파일해 사용

```swift
@testable import { targetName }
```

### Setter를 Getter보다 제한적으로 설정

- Swift에서는 Setter를 더 제한적으로 설정할 수 있음 (반대는 불가)
- getter, setter를 모두 따로 작성하지 않아도 되는 큰 장점!
- getter는 public인데, setter는 fileprivate임

```swift
public struct Car {
	fileprivate var privateEngine: String
	
	public var engine: String {
		get {
			return self.privateEngine
		}
		set {
			self.privateEngine = engine
		}
  }
}

//위의 코드를 이렇게 한줄로 사용 가능 !
public struct Car {
	fileprivate(set) var engine: String
}

```

- 예를 들어, private(set)은 외부에서 settable 하지 않기 때문에, 프로토콜에서는 gettable만 들어가야 함!

```swift
protocol HumanType {
	var boy: String { get }
}
struct Human: HumanType {
	private(set) var boy: String
}

```

### Custom Type

- 커스텀 틀래스에 특정 접근 레벨을 지정 가능!
- 그 레벨을 지정하면 클래스 내부 프로퍼티나 리턴타입의 접근레벨은 클래스의 레벨 접근 권한만 사용
    - ex) file-private 타입 클래스라면, 내부 접근레벨은 file-private 권한임.
    - 즉, 특정 타입의 접근레벨의 지정은 그 타입의 멤버(프로퍼티, 메소드 초기자 와 서브스크립트)에 기본 접근레벨에 영향을 미침
- ⭐️ 이 때 public 타입만 기본적으로 public(X) 대신 internal 멤버를 가짐. (실수로 API 노출 방지 위함)

```swift
public class SomePublicClass {                  // explicitly public class
    public var somePublicProperty = 0            // explicitly public class member
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

class SomeInternalClass {                       // implicitly internal class
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

fileprivate class SomeFilePrivateClass {        // explicitly file-private class
    func someFilePrivateMethod() {}              // implicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}

private class SomePrivateClass {                // explicitly private class
    func somePrivateMethod() {}                  // implicitly private class member
}
```

### Tuple Types

- 튜플은 자체적으로 접근권한 명시하지 않고, 사용하는 클래스, 구조체, 함수 등에 따라 자동으로 최소 접근 레벨을 부여받음.
- ex) 하나는 internal, 다른 하나는 private 권한을 갖는 2가지 타입 구성 튜플이라면, 
더 낮은 private 접근레벨 가짐!

### Function Types

- 함수 타입의 접근레벨은 파라미터 타입과 리턴타입의 접근레벨 중 최소의 접근레벨로 계산돼 사용
- 이 때 SomeInternalClass 는 Internal Type이고, SomePrivateClass 는 Private 타입일 때, 
둘 중 Private가 더 낮은 레벨이므로, 함수도 private 변수를 붙여줘야 함.

```swift
private func someFunction() -> (SomeInternalClass, SomePrivateClass) {
    // private 명시하지 않으면 컴파일 에러 발생 
}
```

### Enumeration Types

- 각 case는 enum 접근 레벨을 따르고 개별 다른 접근레벨 지정 할 수 없음
- enum이 public 이기 때문에 모두 public 접근 레벨을 가짐.

```swift
public enum CompassPoint {
    case north
    case south
    case east
    case west
}
```

---

- 참고자료
    - [https://jusung.gitbook.io/the-swift-language-guide/language-guide/25-access-control#access-levels-for-unit-test-targets](https://jusung.gitbook.io/the-swift-language-guide/language-guide/25-access-control#access-levels-for-unit-test-targets)
    - [https://hcn1519.github.io/articles/2018-01/Swift_AccessControl](https://hcn1519.github.io/articles/2018-01/Swift_AccessControl)
