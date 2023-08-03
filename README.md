# UserDefaults 란 어떤 저장소인가요?

### 01. User Defaults란 무엇인가요?

&nbsp;&nbsp;&nbsp;&nbsp;<img src="http://velog.velcdn.com/images/msi753/post/9663cf87-9d67-4bec-9404-3489b75badd8/image.png" width="400" height="200"><br/>

- User defaults는 설정데이터와 같은 작은 데이터를 저장하는 로컬 저장소 입니다
- User defaults에서 데이터는 키와 밸류로 저장합니다
- 공식문서는 최대 4GB까지 저장가능하다고 나와있지만 KB데이터 이상 저장하기에는 적합하지 않습니다.
- 특히 이미지 파일같은 상대적으로 큰 데이터가 들어가는 용도로는 사용하면 안됩니다. <br/><br/>

### 02. Default data base란 무엇인가요?

&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://www.globalnerdy.com/wp-content/uploads/2018/05/ios-app-sandbox.jpg" width="400" height="200"><br/>

- **UserDefaults는** 메모리에 임시적으로 저장하는 것이 아닌 Sandbox내에 물리적인 파일로 저장됩니다.
- 이 파일을 **Default data base**라고 부릅니다. 고유한 포멧을 가지고 있기 때문에 파일을 직접 수정할 수는 없고 반드시 User Defaults 클래스를 사용해야합니다. <br/><br/>

### 03. 스마트폰 로컬 환경에서 가장 느린 작업은 어떤 작업이고 User Defaults는 이를 어떻게 보완하나요?


&nbsp;&nbsp;&nbsp;&nbsp;<img src="http://i.stack.imgur.com/Kd8Iv.png)" width="400" height="200"><br/>

- 스마트폰 로컬 환경에서 가장 느린 작업은 디스크에서 파일을 로딩하는 것입니다. 값을 read/write 할때마다 디스크로 접근한다면 성능저하가 일어게나게 됩니다.
- 따라서 이러한 성능저하를 방지하기 위해  User Default는 전체 데이터를 Cache로 저장합니다.
- 메모리에 저장된 데이터와 file(p.list)로 저장된 데이터는 자동으로 동기화되기 때문에 개발자는 어디에 cache파일로 저장되어있는지 신경쓰지 않아도 됩니다.
- 하지만 cache 특성상 메모리 공간이 크지 않기 때문에 저장된 데이터가 크면 메모리 문제가 발생합니다.
- 1000개 중 1개만 쓰더라도 1000개가 모두 저장됩니다, 이러한 특징 때문에  User default는 작은 양의 데이터 저장 용도로만 사용해야 합니다 <br/><br/>

### 04. User defaults 의 supported types 란 무엇인가요?

- Number Types(Bool, String, Data, Array, Dictionary, Date, URL) 타입은 별도의 타입 변환 없이 User defaults에 곧바로 저장할 수 있습니다.
- 나머지 타입은 NSCoding 프로토콜을 채택하고 NSKeyed 아카이브로 아카이빙한 다음 데이터 형식으로 저장합니다. <br/><br/>

### 05.  그렇다면 구체적으로 어떻게 값을 저장하나요?

```swift
let key = "sampleKey"

@IBAction func saveData(_ sender: Any) {
    UserDefaults.standard.set("Hello", forKey: key)
}
```
 <br/><br/>

- User Defaults에 값을 저장할 때는 같은 이름을 가진 클래스를 사용합니다 이때 새로운 인스턴스를 생성해서 변수로 사용할 수 있지만 보통 standard 속성에 리턴하는 싱글톤 속성을 사용합니다
- 데이터를 저장하는 방법으로는 6개(supported types 갯수)의 set for key 메서드를 사용합니다. 첫번째 파라미터에는 저장할 값을 두번째 파라미터에는 key값을 전달합니다
- 이때 key값은 오타가 날 수도 있기 때문에 보통 속성이나 전역상수로 선언해서 사용합니다
- 또한 key값의 이름을 단순히 key가 아니라 알아보기 쉽도록 적는 것이 좋습니다 <br/><br/>

### 06. 위와 같이 저장한 값을 어떻게 읽어서 출력할 수 있을까요?

```swift
@IBAction func loadData(_ sender: Any) {
	valueLabel.text =
		UserDefaults.standard.(forKey: key)?? "Not set"
}
```

- 값을 읽어오는 메서드는 위코드처럼 `set`이 아닌 읽을 값의 타입으로 시작합니다
- 그리고 공통적으로 key를 파라미터로 받습니다.
- 앞서 “Hello”를 저장했기 때문에 읽어올 때는 `String forkey` 메서드를 호출합니다. <br/><br/>

### 07. 이번에는 저장하고 빼오는 과정을 Int 타입으로 한번에 구현해주세요

```swift
let key = "sampleKey"

@IBAction func saveData(_ sender: Any) {
    UserDefaults.standard.set("12.34", forKey: key)
}

@IBAction func loadData(_ sender: Any) {
	valueLabel.text =
		UserDefaults.standard.integer(forKey: key) //integer메서드는 값이 없다면 0리턴
}
```

- 이때 더블값(12.34)를 다른형식(int)으로 읽어와도 User Defaults는 에러로 판단하지 않고 최대한 리턴합니다. <br/><br/>

### 08. 이제 UserDefaults의 값을 삭제해주세요

```swift
UserDefaults.standard.set(nil, forKey: key)

UserDegaults.standard.removeObject(forKey: key)
```

- UserDefaults의 값을 삭제하는 방법은 2가지입니다.
- `set for key`를 통한 삭제와 `removeObject for key`를 통한 삭제입니다.
- 전자는 해당하는 key에 nil을 전달하면 삭제됩니다 <br/><br/>

### 09. UserDefaults에 기본값을 부여해주세요

```swift
//AppDelegate.swift

let thresholdKey = "thresholdkey" //가상의 설정 데이터 저장 용도
let initialLaunchKey = "intitailLaunchKey" //최초 실행시점 판단

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
		...
	
		if !UserDefaults.standard.bool(forKey: initialLaunchKey) {
				UserDefault.
		}	

}
```

- UserDefaults는 대부분 간단한 설정데이터(기본값)를 저장할 때  사용됩니다.
- 기본값은 앱을 실행할 때 한번만 설정해야합니다. 그렇지 않으면 매번 앱을 끄고 키면 기본값으로 바뀌어 버리기 때문입니다.
- 따라서 앱의 생성주기를 관리하는 파일인 AppDelegate의 `didFinishLaunchingWithOptions`에서에서 코드를 작성해야합니다.
- 내일이어서 작성하도록
