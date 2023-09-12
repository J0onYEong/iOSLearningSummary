---

---
---

##  shape의 영역

shape는 path매서드를 구현해야 하는 프로토콜이다.


path매소드는 shape이 가지게될 공간의 영역(CGRect)를 전달받는다.


이걸 이용해서 화면에 비율에 맞는 shape을 만들 수 있다.


하지만 shape이 그려지는 것은 이 영역과는 아무런 관련이 없다. 즉 영역을 침범해도 괜찮다.


이것은 특정 shape을 이용하여 뷰를 잘라낼 때(clip) 아주 유용하다.


아래코드의 shape은 정해진 영역을 벗어난 그림을 그리고 있다.


```swift

struct SomeShape: Shape {

    func path(in rect: CGRect) -> Path {

        var path = Path()

        path.move(to: CGPoint(x: rect.minX, y: rect.minY))
	    path.addLine(to: CGPoint(x: rect.midX, y: rect.minY + 50))
        path.addLine(to: CGPoint(x: rect.maxX, y: rect.minY))
        path.addLine(to: CGPoint(x: rect.maxX, y: rect.maxY))
		✋✋✋✋✋
        path.addLine(to: CGPoint(x: rect.minX, y: rect.maxY + 50))
        path.addLine(to: CGPoint(x: rect.minX, y: rect.maxY))

        return path

    }

}


struct ContentView: View {

    var body: some View {

        SomeShape()
            .fill(.cyan.opacity(0.7))
            .frame(width: 200, height: 200)
            .border(.red)
    }   
}
   
```

![[스크린샷 2023-09-12 오전 1.23.16.png]]

영역을 벗어나도 랜더링에는 문제가 없는 것을 확인 할 수 있다.


자 이것을 어떻게 이용할 수 있을까?


SwiftUI는 자식의 몸뚬이가 부모보다 클 경우에 부모 뷰를 벗어난다 하더라도 랜더링을 한다.


예를 들어 아래코드는 그림과 같이 랜더링 된다.

```swift
struct ContentView: View {

    var body: some View {

        ZStack {

            Rectangle()

                .frame(width: 30, height: 200)

        }

        .frame(width: 50, height: 100)

        .border(.red)

    }   

}
```
![[스크린샷 2023-09-12 오전 1.27.16.png]]

이렇게 부모 뷰를 벗어났지만 특정 비율에 맞게 잘라낼 수 있다.


``
```swift
struct ContentView: View {

    var body: some View {

        ZStack {

            SomeShape()
                .fill(.cyan.opacity(0.5))

            Rectangle()
                .frame(width: 50, height: 200)

        }
        .frame(width: 100, height: 100)
        .border(.red)
        .clipShape(SomeShape())

    }   

}
```
![[스크린샷 2023-09-12 오전 1.34.36.png]]

의도한 대로 잘 잘려나간 것을 확인 할 수 있다.


이 학습으로 하나만 얻어가면 된다. 


**shape는 그려지는 영역이 제공받은 영역을 벗어나도 상관없다.**
