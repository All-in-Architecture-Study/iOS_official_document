## UIImage

- 이미지 데이터를 앱에서 관리하는 객체



### Declaration

```swift
class UIImage: NSObject
```



### Overview

- 이미지 객체를 사용하여 모든 종류의 이미지 데이터를 나타내며 **UIImage** 클래스는 기본 플랫폼에서 지원되는 모든 이미지 형식의 데이터를 관리하는 것이 가능합니다.

- 이미지 객체는 변경할 수 없으므로 디스크의 이미지 파일이나 프로그래밍 방식으로 생성된 이미지 데이터와 같은 기존 이미지 테이터를 이용하여 객체를 만들게 됩니다.
- 이미지 객체는 아마 단일 이미지 또는 애니메이션을 이용한 이미지 시퀀스를 포함할 수 있습니다.



- 우리는 이미지 객체를 몇가지의 다른 방법으로 사용할 수 있습니다.
  - **UIImageView** 객체를 이용하여 이미지를 할당하고 인터페이스에 보여줍니다.
  - button, slider, 또는 segmented control 같은 시스템 컨트롤에 커스터마이즈 하는데 이미지를 사용합니다.
  - View 도는 다른 그래픽 context를 이용하여 이미지를 직접적으로 그립니다.
  - API로 이미지 데이터를 전달합니다.
- 이미지 객체는 모든 플랫폼의 기본 이미지 형식을 지원하지만 대부분의 이미지에 PNG 또는 JPEG 파일을 사용하는 것이 좋습니다.
- 이미지 객체는 위의 두 형식을 읽고 표시하도록 최적화되어 있으며 이러한 형식은 다른 이미지 형식보다 더 나은 성능을 제공합니다.
  - 왜나하면 PNG 포맷은 lossless 하기 떄문에 앱 인터페이스에 사용하는 이미지에 특히 권장됩니다.



### Creating Image Objects

- 이미지 객체를 생성하기 위하여 이 클래스 메서드를 이용할때는 만드시 존재하는 이미지 데이터가 파일 또는 데이터 구조ㅔ 있어야 합니다.
- 빈 이미지를 만들고 그 안에 콘텐츠를 그릴 수 없습니다.
- 이미지 객체를 만들 수 있는 많은 옵션이 있으며 특정 상황에 맞게 사용합니다.

​	

 1. **Init(named: in: compatibleWith:)**

    	- 앱의 기본 번들에 있는 이미지 asset 또는 이미지 파일에서 이미지를 만듭니다.

    	- 이러한 방법은 이미지 데이터를 자동으로 캐시하므로 자주 사용하는 이미지에 특히 권장됩니다.

2. **imageWithContentsOfFile**, **init(contentsOfFile:)** 
   - 초기 데이터가 번들에 있지 않는 이미지 객체를 만들 때 사용합니다.
   - 이러한 방법은 매번 디스크에서 이미지 데이터를 로드하므로 동일한 이미지를 반본적으로 로드할 때는 사용하지 않는 것이 좋습니다.
3. **animatedImage(with: duration: )**, **animatedImageNamed(_: duration: )**
   - 여러 sequential 이미지로 구성된 단일 이미지 객체를 만드는 메서드입니다.
   - UIImageView 객체에 결과 이미지를 전달하여 인터페이스에서 애니메이션을 생성합니다.



- UIImage 클래스의 다른 방법을 사용하명 Core Graphics 이미지 또는 사용자가 직접 만든 이미지 데이터와 같은 특정 유형의 데이터를 이용하여 애니메이션을 만들 수 있습니다.
- 또한, UIKit은 사용자가 직접 그린 콘텐츠에서 이미지를 만들 수 있는 **UIGraphicsGetImageFromCurrentImageContext() ** 함수를 제공합니다.  
  - 이 함수는 bitmap 기반 그래픽 context와 함께 사용하여 drawing command를 캡처하는 것이 가능합니다.



- 이미지 asset은 앱과 함께 제공되는 이미지를 관리하는 가장 쉬운 방법입니다. 
- 새로운 Xcode 프로젝트를 생성하면 여러 이미지를 추가할 수 있는 asset 라이브러리가 포함되어 있습니다.
- 이미지 세트는 플랫폼, 특정 환경 및 scale factor 별로 다른 이미지를 제공하는 것이 가능합니다.

- 디스크에서 이미지를 로드할 뿐만 아니라 **UIImagePickerController** 객체를 사용하여 사용 가능한 카메라 또는 사진 라이브러리에서 이미지를 제공하도록 사용자에게 요청하는 것도 가능합니다.
- image picker는 이미지를 선택하기 위한 사용자 인터페이스를 표시합니다.
- 이때 이미지에 접근하기 위해서는 사용자 권한이 필요합니다.



### Defining a Strechable Image

- 확장 가능한 이미지는 기본 이미지 데이터를 보기좋게 만족스러운 방식으로 나타낼 수 있는 영역을 정의하는 이미지입니다.

- 일반적으로 사용 가능한 공간을 채우기 위해 증가하거나 축소할 수 있는 background를 만드는 데 사용됩니다.

- 확장 가능한 이미지는 이미 존재하고 있는 이미지에 inset을 추가하게 됩니다.

  - resizableImage(withCapInsets:), resizableImage(withCapInsets: resizingMode:)
  - inset은 이미지를 두 개 이상의 부분으로 세분화 합니다.
  - 0이 아닌 값을 지정하면 다음 이미지와 같이 9개의 부분으로 분할된 iamge가 생성됩니다

  ![스크린샷 2022-05-10 오후 5.47.25](/Users/user/Desktop/스크린샷 2022-05-10 오후 5.47.25.png)





### Comparing Images

- **isEqual(_:)** 메서드는 두 이미지 객체가 동일한 이미지 데이터를 포함하는지 여부를 확인할 수 있는 유일한 신뢰할 수 있는 방법입니다.
- 다음 코드는 이미지를 비교하는 올바른 방법과 잘못된 방법입니다.

```swift
// Load the same image twice.
let image1 = UIImage(named: "MyImage")
let image2 = UIImage(named: "MyImage") 

// The image objects may be different, but the contents are still equal.
if image1 != nil && image1!.isEqual(image2) {
    // Correct. This technique compares the image data correctly.
} 
if image1 == image2 {
    // Incorrect! Direct object comparisons may not work.
}
```



### Accessing the Image Data

- 이미지 객체는 기본 이미지 데이터에 직접 접근할 수 없습니다.
- 그러나 앱에서 사용하기 위해 다른 방법을 사용하여 이미지 데이터를 검색하는 것이 가능합니다.
- 특히 cgImage 또는 ciImage 프로퍼티를 사용하여 각각 Core Graphics 및 Core Image와 호환되는 이미지 버전을 검색하는 것이 가능합니다.
- 또는 pngData(), jpegData(compressionQuality:) 메서드를 사용하여 PNG 또는 JPEG 형식의 영상 데이터를 포함하는 NSData 객체를 생성할 수 있습니다.