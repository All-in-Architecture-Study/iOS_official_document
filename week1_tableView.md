## Table Views - Filling a Table with Data

아래 정리한 내용은 블로그에도 사진과 함께 정리 잘 되어 있습니다. 👉 [Blog Link](https://julia1281.tistory.com/69)

----
### **Overview**

Table views는 인터페이스의 데이터 기반 요소입니다.

[UITableViewDataSource](https://developer.apple.com/documentation/uikit/uitableviewdatasource) 프로토콜을 채택하는 객체를 사용해서 데이터의 각 부분을 화면에 렌더링하는데 필요한 View와 함께 App의 Data를 제공합니다.  
(그쵸 테이블 뷰를 사용하기 위해선 필수로 해당 프로토콜을 채택해야죠?!)

Table views는 화면에 view를 준비하고, 데이터 소스 객체와 함께 작동하여 해당 데이터를 최신 상태로 유지합니다.

Table views는 데이터를 행과 섹션으로 구성합니다.

행(Rows)은 개별 데이터 항목을 표시하고 섹션(Section)은 관련 행을 그룹화 합니다.

섹션(Section)은 필수는 아니지만, 이미 계층화된 데이터를 구성하는 좋은 방법입니다.

예를 들어, 연락처 앱에서 각 연락처의 이름을 한 행에 표시하고 그 사람의 첫 글자를 기준으로 섹션을 그룹화합니다.

(섹션을 통해 그룹화할 수 있다는 것이 포인트인 것 같네요!)

### **Provide the Numbers of Rows And Sections**

화면에 표시되기 전, table views는 행과 섹션의 총 개수를 지정하도록 요구합니다.

데이터 소스 객체를 다음 2가지 방법으로 제공합니다.

(UITableViewDataSource 프로토콜 채택 후 필수로 작성해야 하는 메소드들이죠?)

```
func numberOfSections(in tableView: UITableView) -> Int // Optional 
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int
```

이러한 메소드를 구현할 때 가능한 빨리 행과 섹션을 반환합니다. 이렇게 하려면, 행과 섹션 정보를 쉽게 검색할 수 있는,방식으로 데이터를 구조화해야 할 수 있습니다.

예를 들어, 배열(Array)을 사용하여 테이블의 데이터를 관리하는 것을 고려해보세요.

배열을  Table view 자체의 자연스러운 구성과 일치하기 때문에 섹션(Section)과 행(Rows)을 구성하는데 좋은 도구 입니다.

아래 예제 코드는, 다중 섹션 테이블의 행과 섹션수를 반환하는 데이터 소스 메서드의 구현을 보여줍니다.

해당 Table에서 각 행은 문자열을 표시하므로 구현은 각 섹션에 대한 문자열 배열을 저장합니다.

섹션을 관리하기 위해서, 구현(implementation)은 배열의 배열(hierachical Data)을 사용합니다.

  
1\. 섹션 수를 관리하기 위해 data source는 배열의 항목 수를 반환합니다.

2\. 특정 섹션의 행의 개수를 가져오기 위해서 data source는 해당 자식 배열의 항목수를 반환합니다.

(데이터를 보이기 위해 이중 배열(hierachical Data)을 사용하였네요.  
numberOfSections 에서는 배열의 섹션 개수를 반환할 수 있게하고, numberOfRowsInSection 에서는 해당 섹션의 데이터들의 개수를 반환하여 값을 반환하며 테이블 뷰의 값을 채웁니다.)

```
var hierarchicalData = [[String]]() 

//1
override func numberOfSections(in tableView: UITableView) -> Int {
   return hierarchicalData.count
}
//2 
override func tableView(_ tableView: UITableView, 
                        numberOfRowsInSection section: Int) -> Int {
   return hierarchicalData[section].count
}
```

### **Define the Appearance of Rows**

셀을 사용해서 storyboard에서 테이블 행의 모양을 정의합니다.  
[UITableViewCell](https://developer.apple.com/documentation/uikit/uitableviewcell) 은 테이블 행에 대한 템플릿 역할을 하는 객체입니다. 

셀은 views 이고, 콘텐츠를 표시하는데 필요한 모든 하위 view를 포함할 수 있습니다.

콘텐츠 영역에 labels, image views 등 여러 views들을 추가하고 constraints를 사용해서 해당 views를 정렬할 수 있습니다.

(셀 하위 뷰에 보여질 label이나 이미지를 추가하여 셀의 값을 구성할 수 있겠죠? 이 때, constraints도 구성하여야 레이아웃이 잡힙니다.)

앱의 interface에 tableView를 추가하려면, 구성할 하나의 prototype cell을 포함합니다.

prototype cell 더 추가하려면 tableView를 선택하고 prototype cell 속성을 업데이트하세요.

각 셀에서 모양을 정의하는 스타일이 있습니다. UIKit에서 제공하는 표준 스타일 중 하나를 선택하거나 사용자 정의 스타일을 정의할 수 있습니다.

스토리보드 파일에서 각 프로토타입 셀에 대해 다음 작업을 수행합니다.

-   셀 스타일을 custom or standard 타입 중 하나로 설정합니다.
-   셀 Identifier 속성에 비어있지 않은 문자열을 할당해야 합니다.  
    (⭐️ 셀의 Identifier를 무조건 지정해야 합니다.)
-   custom한 셀은 셀의 view와 constraints를 추가합니다.
-   custom한 셀의 클래스를 지정하세요.  
    (custom cell의 TableViewCell을 상속하는 swift 파일을 하단의 이미지와 같이 ⭐️ 클래스 지정해주어야 연결됩니다.)

custom cell을 만들 때, UITableViewCell의 views들을 관리하기 위해 하위 클래스를 정의해야 합니다.

하위 클래스에서 앱 데이터에 표시되는

1) custom view에 outlets을 추가하고

2) 실제 스토리보드에 outlets들을 연결합니다.

셀을 구성하는 더 많은 정보는 [Configuring the Cells for Your Table](https://developer.apple.com/documentation/uikit/views_and_controls/table_views/configuring_the_cells_for_your_table) 여기를 참고하세요. (이것도 추후에 정리해보겠습니당!)

### Create and Configure Cells for Each Row

table view가 화면에 그려지기 전, 데이터 원본 객체에 테이블의 보이는 부분 or 근처에 있는 행에 대한 셀을 제공하도록 요청합니다.

데이터 소스 객체의 메서드는 [tableView(\_:cellForRowAt:](https://developer.apple.com/documentation/uikit/uitableviewdatasource/1614861-tableview) 빠르게 응답해야합니다. 

(이 메소드도 tableview를 구성하기 위한 필수 메소드 입니다.)

1\. 테이블 뷰의 메소드 [dequeueReusableCell(withIdentifier:for:)](https://developer.apple.com/documentation/uikit/uitableview/1614878-dequeuereusablecell) 를 호출해 샐 객체를 검색합니다.

2\. 앱의 custom data로 셀의 view를 구성합니다.

3\. 테이블 뷰에 셀을 반환합니다.

#### Configure Standard cell

standard cell은 기본으로 text가 제공되어 그대로 사용할 수 있지만, custom cell을 사용할 경우 따로 access 할 수 있는 contents를 추가하여 디자인해야 합니다.

아래 예시는 standard cell을 이용하여 기본으로 제공된 label인 text를 사용하여 데이터를 구성합니다.

```
override func tableView(_ tableView: UITableView,
                        cellForRowAt indexPath: IndexPath) -> UITableViewCell {
   // cell의 적절한 타입을 지정해야 합니다.
   let cell = tableView.dequeueReusableCell(withIdentifier: "basicStyleCell", for: indexPath)
        
   // 셀의 section의 row를 통해 데이터에 접근하여 셀의 데이터를 구성합니다.
   cell.textLabel!.text = "Row \(indexPath.row)"
   return cell
}
```

#### Configure Custom cell 

이 부분은 공식 문서의 내용은 아니지만, 자주 custom 하여 셀을 구성하므로 제가 사용했던 코드의 예시를 정리해봤습니다.

1\. custom한 cell을 구현하기 위해 UITableViewCell을 상속받는 swift 파일을 생성하여

2\. 스토리보드에 필요한 image와 lable을 구성하여

3\. 우측 Custom Class에 해당 셀의 .swift 파일을 지정하고

4\. 스토리보드에 구성한 image와 lable을 끌어와 @IBOutlet을 연결하여 해당 값들에 접근할 수 있도록 연결합니다.

5\. 이제 해당 UIController에서 cellForRowAt 메소드에 standard cell 구현했던 것과 동일하게 셀의 타입을 지정해줘야 하는데요.  
이 때 custom하게 따로 지정해줘야 했기 때문에 기본 cell 타입이였던 셀의 타입을 따로 지정했던 셀 타입으로 타입변환 시켜줍니다!  
그리고, 셀의 값을 변경해주면 동적으로 변경되겠죠?!

```
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
		//custom하여 생성한 셀의 타입을 변환시킵니다.
        let offerCell = tableView.dequeueReusableCell(withIdentifier: "ReceivedOfferTableViewCell", for: indexPath) as! ReceivedOfferTableViewCell
        let companyData = offerCompanyList[indexPath.row]
        offerCell.companyIcon.image = companyData.companyIcon
        offerCell.companyName.text = companyData.companyName
        offerCell.offeredDate.text = companyData.offerDate
 }
```

