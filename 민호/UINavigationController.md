# UINavigationController

- 계층적인 stack-based 체계를 정의하는 컨테이너 뷰 컨트롤러



### Declaration 

```swift
@MainActor class UINavigationController : UIViewController
```





### Overview

- navigation controller는 컨테이너 뷰 컨트롤러로써 하나 또는 그 이상의 child view controller를 네비게이션 인터페이스에서 관리합니다.
- 이 타입의 인터페이스는 오직 하나의 child view controller를 한번에 보여줍니다.
- 뷰컨의 어떤 아이템을 선택하면 새로운 뷰컨을 애니메이션과 함께 푸시합니다.
- 네비게이션 바의 백 버튼을 탭한다면 top view controller는 제거됩니다.
- 네비게이션 인터페이스를 이용하여 계층 구조적인 데이터 관리

d