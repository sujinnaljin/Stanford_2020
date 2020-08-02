# Stanford_2020
스탠포드 👨🏻‍🦳 강의 정리 레포



## 1강

  스위프트 UI로 플젝 처음 생성하면 이런 코드가 나옴

```swift
struct ContentView: View {
    var body: some View {
        Text("Hello, World!")
    }
}
```

- 여기서 `ContentView` struct 이름 변경하면 에러남. 왜? scenedelegate에서 쓰고 있으니까 ㅇㅇ

- `body` property를 다른 이름으로 변경해도 에러 남. 왜? SwiftUI에서 View는 반드시 body 변수가 있어야하며, 최상위 View 역할 함 ㅇㅇ

  ```swift 
  public protocol View {
  
      /// The type of view representing the body of this view.
      ///
      /// When you create a custom view, Swift infers this type from your
      /// implementation of the required `body` property.
      associatedtype Body : View
  
      /// Declares the content and behavior of this view.
      var body: Self.Body { get }
  }
  ```

- `body: some View` 처럼 **some** 을 붙여주는 이유는 반환하는 애가 View에 해당한다는걸 강제하는거임. (근데 Text 가 View 타입인지는 어디서 확인할 수 있는데? 🤔) 그냥 `body: View`  하면 안되냐?🤔 하고 바꿔봤는데 *Protocol 'View' can only be used as a generic constraint because it has Self or associated type requirements* 이딴 에러 남. "some 붙이는 이유는 간단히 특정한 조건을 만족하는 제네릭 타입의 View라고 넘어가겠습니다" 라고 참고 블로그에서 하는데.. ㅇㅣ해는 잘 안감 ㅎ

- `body`는 computed property라 불릴때마다 안에 코드 `return` 하게 됨. 저기선 `return Text("Hello, world")` 하는거 

- HStack 안에서 ForEach 만나면 list 처럼 동작하는거 알고 layout 따로 잡음





**참고**

[SwiftUI 튜토리얼 1편 — 기본구조](https://medium.com/harrythegreat/swiftui-%ED%8A%9C%ED%86%A0%EB%A6%AC%EC%96%BC-1%ED%8E%B8-%EA%B8%B0%EB%B3%B8%EA%B5%AC%EC%A1%B0-11e7b589e6de)
## 2강

- MVVM

  - Swift의 View는 struct이고 기본적으로 read only임. 그래서 정확히 코드대로 보일 것을 추측할 수 있음
  - View는 statelsess 이기 때문에 model의 상태에 따라 변경되어야함. model 의 변경에 따라 반응(react)하기 때문에 reactive programming
  - 모델은 UI independent 한 데이터나 로직 담음
  - VM은 뷰를 모델에 바인딩 하거나, 데이터를 다른 데이터 형식으로 변환하는 등  View 를 위한 작업들 함. 그리고  model의 변경사항을 알고 view가 바로 업데이트할 수 있게 도와주는 등 View와 Model 을 이어주는 역할. 여기서 주의할 점은 **ViewModel은 뷰와 직접 대화(talk)**하지 않음. 뷰에게 "모델 변경됐어!"하고 알려주지 않는단 말임 ㅇㅇ 그냥 "무언가 변경됐어!"하고 **변경사항을 게시(publish)** 할 뿐. 그럼 뷰가 구독(subscribe) 함.
  - 그럼 **모델을 바꾸려면** 어캄? **VM** 에 intent user 의 intent(의도) 를 처리하기 위한 responsibility를 하나 더 추가함 (**Process Intent**)

- class & struct

  - class

    - reference 타입이라 heap 에 살고 있음.  

    - argument로 class를 넘기면 pointer가 전달되기 때문에 여러군데에서 해당하는 class를 참조하고 있을 수 있음. 

    - 그래서 몇군데서 참조하고 있는지 알 수 있게 자동으로 참조 카운트(automatically ref counted) 를 세고, 얘를 가르키는 애가 더이상 존재하지 않으면 힙에서 사라짐. 

    - 객체 지향 프로그래밍 지원

    - 언제나 변경 가능함(mutable). pointer 갖고 있기 때매 해당 heap 에 가서 변경 ㄱㄱ하면 되는 거

      ```swift
      class myClass {
          var mutableProperty: Int = 0
      }
      
      let classInstance = myClass()
      classInstance.mutableProperty = 1 // 인스턴스는 let이지만, 안의 property는 변경 가능. 
      
      classInstance = myClass() // error : 다만 instance는 자체는 let이기 때문에 변경 불가
      ```

    - MVVM 에서 ViewModel 은 언제나 class. 왜냐면 다른 뷰에서도 공유되어야하니까 ㅇㅇ. UIKit도 class 베이스. 

  - struct

    - Value 타입

    - argument로 넘기면 해당 값이 복사된 복사본이 넘어감. 실제 bitwise  copy 가 바로 일어나진 않고 않고 해당 값에 write 하려고 할때  ㄱㄱ 튼 핵심은 공유하지 않는다는거

    - array, dic, int, bool 등 대부분은 struct

    - 함수형 프로그래밍 지원

    - 변경 가능성(mutability)을 명시적으로 let이냐 var이냐를 통해 언급해줘야함

      ```swift 
      struct myStruct {
          var mutableProperty: Int = 0
      }
      
      let structInstance = myStruct()
      structInstance.mutableProperty = 1 // error : 인스턴스가 let이어서 안의 property도 변경 불가. 왜냐면 값 타입이라 property 변경되면 인스턴스 자체도 바뀜
      ```

    - 이 강의에서 볼 대부분의 것들은 struct (다만 View 는 protocol)
## 3강

### Reactive Demo - Make View always reflect its model

- ViewBuilder 안에는 변수 선언 할 수 없다 

  `ZStack { // 이 안에 변수 선언 불가 }`

- struct에서 자기 자신을 변경하는 함수 앞에는 `mutating` 키워드를 붙여야한다. 다만 init도 자기 자신을 변경하는 것인데 mutating을 안붙이는 이유는 모든 init은 기본적으로 mutating이기 때문이다. 

- **뷰모델은 모델이 변경되는 시점을 뷰에 알려줌으로써 바로 업데이트** 될 수 있도록한다. 이를 위해서 ViewModel에서 `ObservableObject` 를 따르고 모델이 변경되는 지점에서  `objectWillChange.send()` 함수를 호출한다. (`ObservableObject` 를 따르면 `objectWillChange`를 쓸 수 있다)

  ```swift
  class EmojiMemoryGame: ObservableObject {
    private var model: MemoryGame<String>
    
    //model이 변경되는 함수
    func choose(card: MemoryGame<String>.Card) {
  		objectWillChange.send()
  		model.choose(card: card)
  	}
  }
  ```

  근데 이게 귀찮으니까 아예 모델을 `@Published`로 만들어 버린다. (`@Published`는 프로퍼티에 약간의 기능을 추가시켜주는 property wrapper 중 하나이다.) `@Publisehd` property wrapper는 해당 프로퍼티가 변경될때마다 `objectWillChange.send()` 함수를 호출하는 것과 같은 기능을 한다. 그럼 이제 **모델이 변경될때마다 뷰 모델은 퍼블리싱** 된다

  ```swift
  // 1. ObservableObject를 conform 함으로써 이 class의 instance를 view에서 사용할 수 있고, 변경 사항에 따라 view가 reload 될 수 있다.
  class EmojiMemoryGame: ObservableObject {
    // 2. @Published property wrapper는 변경 사항에 따라 view가 reload될 수 있도록 한다 (SwiftUI에 view reload를 트리거)
    @Published private var model: MemoryGame<String>
  }
  ```

  위처럼 `model`에 대해서가 아니더라도 뷰 변경에 영향이 있는 변수들에 대해선 `@Published` 붙이거나 함수 안에서 `send()` 를 통해 변경 사항을 알려주게 된다.

  이제 View에서 ViewModel 이 변경될 때마다(`objectWillChange.send()`를 보낼 때마다) 다시 그려지게 하면 된다. 이를 위해 View에서 변경 사항에 따라 UI도 같이 변경되어야하는 변수 앞에 `@ObservedObject` property wrapper를 붙여준다. 이때 type은 `ObservableObject` 프로토콜을 conform 해야 한다.

  `@ObservedObject` wrapper를 붙여줌으로써 이 `ObservableObject` 가 뿜어내는(send) 것들에 관심이 있다는걸 표시하는 듯하다. 그리고 변경 되면(`objectWillChange.send()`  통해 알아채겠지) SwiftUI에 view reload를 트리거.

  ```swift
  struct EmojiMemoryGameView: View {
    @ObservedObject var viewModel: EmojiMemoryGame // EmojiMemoryGame: ObservableObject
  }
  ```

  참고로 모든 SwiftUI는 모든 viewModel에 대해서 다시 그리는게 아니라 변경된 것만 알고 그것만 다시 그린다.

### 프로토콜

- protocol extension에서는 stored property는 안되고 computed property만 됨
- `Self`는 원래 자기 자신의 동적 클래스(dynamic class)를 가리키는 키워드. protocol 안에서 `Self`는 해당 protocol을 구현하고 있는 실제 타입. 

### 레이아웃

- View가 그려지는 3단계 과정 
  1. Container View (ZStack, HStack 등)는 자신 안에 그려질 View들에게 공간 할당
  2. View들은 자신의 size 결정
  3. Container View는 자신 안에 있는 View들의 위치 결정
- HStack 이나 VStack 등은 가장 유연성이 적은 (least flexible) 뷰들 (Image, Text 등)에 대해 공간을 먼저 할당하고 그렇게 다 할당하고 나면 거기에 맞게 자기 자신의 사이즈 맞춤

**참고**

[How to use @ObservedObject to manage state from external objects](https://www.hackingwithswift.com/quick-start/swiftui/how-to-use-observedobject-to-manage-state-from-external-objects)

[SwiftUI ) @State, @ObservedObject, @EnvironmentObject](https://zeddios.tistory.com/964)

[[Swift 5.1] Self 키워드의 기능 추가 (SE-0068)](https://seorenn.tistory.com/27)

[What's .self, .Type and .Protocol? Understanding Swift Metatypes](https://swiftrocks.com/whats-type-and-self-swift-metatypes)



