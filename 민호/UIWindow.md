## UIWindow

- 앱의 사용자 인터페이스의 뷰를 통해 이벤트를 전달하는 객체

![스크린샷 2022-05-02 오후 7.37.33](/Users/user/Library/Application Support/typora-user-images/스크린샷 2022-05-02 오후 7.37.33.png)

### Declaration

```swift
@MainActor class UIWIndow: UIView
```

 

### Overview

- 윈도우는 view controller와 함께 이벤트를 처리하고 앱 작동에 필수적인 많은 작업들을 수행하게 됩니다.
- UIKit의 대부분은 window와 관련된 인터렉션을 처리하며, 많은 앱의 동작을 구현하는데 필요한 다른 객체와 함께 작업하게 됩니다.
- 다음과 같은 상황에서 window를 사용하게 됩니다.
  - 앱의 콘텐트를 표시할 main window를 제공합니다.
  - 필요에 따라 추가 window를 만들어 추가 내용을 표시합니다.



- 기본적으로 Xcode는 앱의 main window를 제공합니다.
- 새로운 iOS 프로젝트를 만들면 스토리보드를 사용하여 앱의 뷰를 정의합니다.
- 스토리보드는 Xcode 텝플릿이 자동으로 제공하는 window 프로퍼티가 존재합니다. 스토리보드를 사용하지 않는 경우에는 이 window를 직접 만들어야 합니다.
- 대부분의 앱은하나의 윈도우만 필요합니다. 하지만 추가 윈도우를 생성해서 외부 스크린에 콘텐츠를 보여주는 것도 가능합니다.
- 또한 UIWindow 객체는 다음과 같은 몇가지 작업을 할 때 사용할 수 있습니다. 
  1. window의 z 축을 설정하여 다른 윈도우와 비교하여 visibility의 영향을 주도록 할 수 있다.
  2. window를 보여주고 키보드 이벤트의 타켓으로 만드는 것이 가능하다.
  3. window의 root view controller를 변경하는 것이 가능하다.
  4. 어떤 window를 보여줄지 변경하는 것이 가능하다.



- Window에는 고유의 시각적인 모습이 존재하지 않습니다. 대신에 하나 또는 그 이상의 view들이 윈도우에 host 되며 이 뷰는 root view controller에 의해 관리되어집니다.
- 스토리보드에서 root view controller를 구성하여 인터페이스에 적합한 뷰를 추가하게 됩니다.
- UIWindow의 서브클래스는 거의 필요하지 않습니다. 
- window에서 구현할 수 있는 동작의 종류는 일반적으로 더 높은 레벨의 VC에서 구현할 수 있습니다.
- 서브클래스는 윈도우의 key 상태를 변경하는 경우 동작을 커스텀하기 위해서 `becomeKey()` 또는 `resignKey` 를 오버라이드 하는 경우가 많습니다.



### Understanding Keyboard Interactions

- 터치 이벤트는 발생한 window로 전달되는 반면 관련 좌표 값이 없는 이벤트들은 key window로 전달됩니다.
- 한 번에 하나만 window가 key window가 될 수 있으며, `isKeyWindow` 를 통해 확인하는 것이 가능합니다.
- 대부분의 경우 앱의 main window가 key window 이지만, UIKit은 필요에 따라 다른 window를 지정하는 것이 가능합니다.
- 어떤 window가 key인지 알아야 하는 경우에는 `didBecomeKeyNotification` 이나 `didResignKeyNotification` 들을 확인하면 됩니다.
- 시스템은 앱의 주요 key window가 변경에 따라 위의 알림을 보냅니다. 
- window가 강제로 key가 되거나 key 상태를 다시 resign 할때는 위 클래스의 적절한 메서드를 호출하면 됩니다.

