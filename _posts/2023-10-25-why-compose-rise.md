---
title: Compose가 등장한 이유
date: 2023-10-25 19:00:00 +0900
categories: [Android, Compose]
tags: [Declarative,Imperative,View VS Compose]
pin: false
image:
  path: /assets/img/thumbnail/4.jpg
  alt: Jetpack Compose
---
## View VS Compose

&nbsp; 2008년 안드로이드는 View,ViewGroup 기반 UI 작성 기능(이하 View)을 지금까지 제공하고 있습니다. 
하지만 2020년 Compose가 등장합니다. 잘 작동하는 View가 있음에도 Compose가 등장한 배경은 무엇인지 생각해 볼 필요가 있습니다.

&nbsp; 글을 시작하기 전 뒤에 나올 내용의 이해를 돕고자 명령형 프로그래밍과 선언형 프로그래밍에 대해 간단히 설명하겠습니다. 
* 명령형(Declarative)
```kotlin
fun sumOfEvenNumbers(numbers: List<Int>): Int {
    var sum = 0
    for (number in numbers) {
        if (number % 2 == 0) {
            sum += number
        }
    }
    return sum
}
```
반복문과 조건문을 사용하여 명시적으로 각 단계를 제어합니다. **How(어떻게)** 에 집중하는 프로그래밍 스타일입니다.

* 선언형(Imperative)
```kotlin
fun sumOfEvenNumbers(numbers: List<Int>): Int {
    return numbers.filter { it % 2 == 0 }.sum()
}
```
filter와 sum 같은 고차 함수를 사용하여 **What(무엇을)** 원하는지를 선언하고, 언어나 라이브러리가 "어떻게" 처리할지를 추상화합니다.

&nbsp; 명령형의 장점은 학습곡선이 낮고, 추상화보다 특정한 상황에 특정한 알고리즘 최적화가 필요한 경우 유용할 수 있습니다. 또 하드웨어와
밀접하게 관련 있는 작업을 할때 명령형 코드가 필요합니다. 선언형의 장점은 간결해서 가독성이 좋고, 코드 외부에 영향을 주지 않아 
테스트에 유리하다는 점입니다. 명령형과 선언형의 장단점에서 Compose가 등장한 이유를 추론할 수 있습니다.

&nbsp; View에서는 2가지 방식으로 UI를 작성할 수 있습니다. Runtime시에 View, ViewGroup 객체를 동작하도록 Java 혹은 Kotlin 코드로 작성하는
방식이 있고, 마크업 언어인 XML을 이용하여 UI를 작성 후 Activity, Fragment에 XML의 참조를 불러오는 방식이 있습니다. 왜 2가지 방법을 제공해 줄까요? 
View가 등장했을 시기 Kotlin은 존재하지 않았습니다. 당시 JAVA의 최신 버전은 JAVA 6이었고 JAVA 8에서야 람다와 Stream이 생겼습니다. 
즉, 당시 JAVA는 선언형보다 명령형으로 설계하는 것이 유리했고, 그래서 View는 명령형을 기반으로 작동하도록 설계되었을 겁니다. 
그런데 모든 UI를 명령형으로 작성한다면 UI 구조가 명확하지 않고, 코드의 양이 매우 길어지는 등 여러 단점이 있겠죠.
이를 개선하기 위해 JAVA가 아닌 다른 선언적인 방식으로 UI를 작성할 수 있게 해주면 되지 않을까요? 
그래서 개발자들은 XML을 통해 선언형의 장점인 비교적 적은 양의 코드로 명확하게 UI를 작성할 수 있게 하고
그에 대한 동작을 코드에서 명시적으로 조작하는 기능을 만들었다고 생각합니다.

&nbsp; 그런데 2017년 Kotlin이 안드로이드의 공식 언어로 채택됨에 따라 상황이 달라졌습니다. Kotlin은 명령형뿐 아니라 선언형 프로그래밍에도 유용한 여러 기능을 
제공해 주었습니다. UI마저 Kotlin으로 작성한다면 더 이상 XML에 대해 알 필요가 없어지고, 선언형 UI로 여러 장점을 가져올 수 있다고 개발자들은
판단했습니다. 그래서 Compose가 탄생했습니다. 이제부터 Compose와 View를 본격적으로 비교해 보겠습니다.

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MessageCard("Android")
        }
    }
}

@Composable
fun MessageCard(name: String) {
    Text(text = "Hello $name!")
}
```
View에서 Text하나를 화면에 띄우기 위해 필요한 코드와 Compose로 작성한 위의 코드를 비교하면 선언형 UI가 얼마나 간결한지 
알 수 있습니다. Compose가 View보다 유리한 부분에 대해 제 경험을 바탕으로 자세히 적어 보겠습니다.
1. Compose는 재사용성이 뛰어납니다. 같은 UI가 여러개 필요하면 Composable 함수를 여러번 호출하면 그만입니다.
2. 일반적으로 Activity, Fragment에서 XML파일의 참조를 통해 원하는 데이터, 변경사항들을 적용 합니다.
  하지만 XML과 Activity 코드를 연동시키기 위한 잡다한 코드를 많이 작성해야 합니다. 또 자주 XML파일과 로직파일을
  번갈아가며 View의 id가 정확한지, 누락된 데이터는 없는지 확인해야 합니다. 물론 NPE를 개선하기 위한 Databinding 같은 유용한 라이브러리가
  있지만 여전히 부가적인 코드를 생성합니다. 반면 Compose를 사용하면 확실히 적은 코드를 작성 하게됩니다. 관리 코드가 적어지면 
  버그 발생률도 내려가고 유지보수에도 유리합니다.
3. Custom View를 작성할 때 모든 View는 View 클래스 혹은 그 자식을 상속한다는 특성으로 인해 부가적인 코드를 많이 작성합니다.
   예를 들어 <declare-styleable>을 attr.xml 파일에 작성해주어야 하고, 또 Java, Kotlin으로 해당 Custom View 클래스를 만들어도
   생성자에 정해진 요소를 제외하고 다른 것을 작성할 수 없습니다. 그래서 callback 같은 내부 필드를 외부에서 정의하고 싶다면 setter를 일일이 작성해 주어야 합니다. 
   반면 Composable은 함수이기 때문에 상속 관계가 없고 State와 event는 함수 인자로 전달되기 때문에 간단합니다.

하지만 Compose가 불리한 부분도 있었습니다. 
1. View보다 알아야 할게 많습니다. State, Recompostion, SideEffect, CompositionLocal 등 View에는 존재하지 않던 개념들이
  많이 있고 하나라도 깊이있게 알지 못하면 사용에 어려움이 있습니다.
2. Android Studio에서 제공해주는 Layout Editor 보다 Compose의 Preview, LiveEdit 기능은 꽤나 무거웠습니다. 아직 초기라 그런지
  자잘한 에러도 많고 반응속도도 좋지 못합니다.

---
## 결론
&nbsp; 많은 기업들이 아직 View를 유지하고 있고, Compose로 이전하기엔 시기상조라는 개발자도 있습니다. 하지만 갈수록 View보다는 
Compose에 대한 안드로이드의 지원이 늘어날 것이고, 현재 Compose가 가진 단점도 많이 보완 될 것입니다. 가까운 미래에 View로 만들어진 APP보다 Compose로 개발된 APP의 비율이 훨씬 
커질것 같다는 생각도 듭니다. 그래서 당장 사용하지 않더라도 Compose에 대해 꾸준히 공부하는 것이 현명하다고 생각합니다.

> 제 글에 잘못된 부분 또는 다른 의견이 있으시면 댓글을 통해 남겨주세요. 토론은 언제나 환영입니다.   
{: .prompt-tip }
