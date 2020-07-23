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
