---
title: Android Context에 대한 이해
date: 2023-10-03 15:00:00 +0900
categories: [Android, 기초]
tags: [context]
pin: true
image:
  path: /assets/img/thumbnail/2.jpg
  alt: Image from Unsplash
---
---

## 공식 문서의 Context에 대한 정의 

> 애플리케이션 환경에 관련된 전역 정보를 나타내는 인터페이스이다. 안드로이드 시스템에 의해 구현되는 추상 클래스이고 intent, activity, broadcasting, 리소스 등에 접근 가능하게 해 준다.

## 왜 굳이 Context가 필요한가? 

&nbsp; '왜 Context가 필요한가?'에 대해 고민하던 중 한 블로그에서 좋은 글을 발견했습니다. 그 내용을 보고 Context라는 개념에 대해 더 와닿게 되었습니다.

&nbsp; 안드로이드 개발팀에서 Context라는 객체를 설계한 배경에 대해 알기 위해, 안드로이드 개발의 특성을 넘어 모바일의 특성을 생각해 보아야 합니다. 우리는 휴대폰으로 책을 읽다가 카톡을 하고 또 은행 업무를 처리합니다. 그리고 다시 책을 읽습니다. 중요한 것은 책 애플리케이션이 다시 시작되지 않고 자연스럽게 유지되는 것이죠. 즉, 멀티태스킹이 한정된 메모리에서 이루어져야 한다는 것입니다. 아시다시피, 안드로이드는 리눅스 OS 위에서 작동합니다. 만약 프로세스와 앱이 1대1로 강하게 결합하여 있다면 여러 앱을 띄울 때 프로세스도 그만큼 만들어져야 하고 프로세스가 많아지면 뒷순위로 밀린 프로세스 혹은 앱이 메모리에서 제거되며 기존의 사용자가 이용하던 앱 정보도 날아가 버리게 됩니다. 그래서 안드로이드 앱과 프로세스는 독립적으로 설계되었습니다.  그 결과 Process에서 앱의 정보를 얻어올 수 없게 되고, 앱 정보를 저장하는 역할을 맡는 주체가 필요해졌습니다. 그 역할은 Android System Service의 _ActivityManagerService_ 가 담당하게 되었습니다. _ActivityManagerService_ 와 데이터를 주고받을 때, A 앱은 A 앱에 해당하는 데이터만 가져와야 합니다. 그래서 중간 매개체이자 앱의 신분증 역할을 하는 Context가 필요하게 된 것이죠. 문득 IOS에서는 어떻게 프로세스와 앱의 관계를 설계했는지 궁금해집니다.

## Context의 사용

&nbsp; 무분별하게 Context를 코드 내부에서 호출한다면 메모리 누수가 발생할 위험이 있습니다. 예를 들어,  Activity의 생명주기에서만 유지되어야 하는 작업(ex.dialog, Toast 등)에 ApplicationContext를 호출하는 경우 Activity가 Destory되더라도 작업은 계속 Application을 참조하고 있기에 메모리 누수가 발생합니다. 비슷하게 SharedPreference, Room등 싱글톤 객체를 호출하기 위해 context가 필요한 경우가 있는데, 이때는 ApplicationContext를 사용합니다.

### Context 호출

```kotlin
//1.현재 활성화된 activity의 ActivityContext를 리턴
View.getContext()

//2.ApplicationContext를 리턴
Activity.getApplicationContext()

//3.자신의 Context가 아닌 다른 Context를 access 할 때 사용.
ContextWrapper.getBaseContext()

//4.현재 활성화된 activity의 ActivityContext를 리턴
this
```

## 참고

[https://developer.android.com/reference/android/content/Context](https://developer.android.com/reference/android/content/Context)

[https://blog.naver.com/PostView.nhn?isHttpsRedirect=true&amp;blogId=huewu&amp;logNo=110085457720](https://blog.naver.com/PostView.nhn?isHttpsRedirect=true&blogId=huewu&logNo=110085457720)
