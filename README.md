# MVVM - VM괴 AAC-VM의 차이

[참고문서](https://wooooooak.github.io/android/2019/05/07/aac_viewmodel/)

### 오해의 소지가 많은 AAC ViewModel

안드로이드 아키텍쳐 컴포넌트(AAC)는 ViewModel이라는 추상클래스를 제공한다. 안드로이드 개발자나 또는 아키텍쳐에 관심이 많은 개발자라면 ViewModel이란 단어를 들었을 때 아마도 가장 먼저 MVVM패턴을 떠올릴 것이다. 마이크로소프트에서 처음 MVVM을 선보이면서 ViewModel이란 단어를 **뷰와 모델 사이에서 데이터를 관리하고 바인딩 해주는 요소**로 칭했기 때문이다. 실제 MVVM패턴 에서의 viewModel의 역할이 그렇다.

그럼 구글이 안드로이드 개발의 편의성을 위해 제공하는 ViewModel은 어떨까? 앱 개발의 대세가 MVVM이다 보니 많은 개발자가 이름만 보고서는 MVVM의 ViewModel 일거라고 생각한다. 그러나 일반적인 MVVM의 viewModel과 전혀 상관이없다. 1도 상관이 없다. 심지어 안드로이드 공식 문서에도 viewModel을 설명하면서 mvvm 패턴을 이야기 하지 않는다. AAC의 뷰모델이 MVVM패턴의 뷰모델이라면 당연히 언급이 있어야 하는데, 없다.

### AAC ViewModel이란?

**화면 회전같은 환경 변화에서 뷰에 사용되는 데이터를 유지시키기 위한, 라이프사이클을 알고있는 클래스**라고 한다. 우리가 알고있는 mvvm에서의 viewModel(뷰와 모델 사이에서 데이터를 관리하고 바인딩해주는)역할과는 설명이 다르다. 즉, 이름만 viewModel이지 MVVM의 viewModel과 **전혀 관계가 없는 클래스**라는 것이다.

구글이 viewModel을 잘 못 알고 만든 것일까? 절대 그렇지 않다. 한 가지 잘 못한게 있다면 이름을 헷갈리게도 viewModel로 지었다는점…? 사용자가 화면을 회전 시킬 경우를 생각해보자. 기존 view에는 로그인 된 사용자의 정보를 가지고 있다. 사용자가 화면을 회전 시키면 액티비티는 종료되었다가 다시 생성된다. 그렇기 때문에 사용자의 데이터를 또 다시 받아와야 한다. 그저 화면 회전이 되었을 뿐인데 말이다. 그러나 AAC 뷰모델은 화면을 회전 시켜도 로그인 정보를 그대로 보존해놓기 때문에 다시 유저 로그인 정보를 불러오지 않아도 된다. 이건 정말 편한 기능이다(직접 구현 해보면 얼마나 귀찮고 어려운 작업인지 알게 될것이다). **AAC의 ViewModel은 딱 이 역할만 하는 클래스**일 뿐이다. **ViewModel을 생성하고 거기에 유저 로그인 정보를 넣었을 뿐, 그렇다고 이게 MVVM 패턴이 되지는 않는다.**

그런데 생각해보니 조금 이상한 점이 있다. 분명 앱 개발의 대세는 mvvm 패턴이 되어가고 있는데 “mvvm 패턴 샘플 예제”를 검색해서 안드로이드 앱 예제를 보면 대부분 AAC ViewModel을 사용하고 있다. AAC ViewModel은 MVVM의 ViewModel과 전혀 상관이 없다고 했는데…. 인터넷에 올라온 예제들이 모두다 잘못된 예제일까? 그렇게 star를 많이 받은 안드로이드 MVVM 예제 샘플 코드들이 모두 AAC ViewModel을 MVVM의 ViewModel로 착각하고 만든 것일까? 그렇지 않다. **AAC의 ViewModel을 MVVM의 ViewModel로써 사용할 수 있다.** MVVM의 뷰모델의 역할은 위에서 보았듯이 뷰와 모델 사이에서 데이터를 관리하고 바인딩해주는 것이다. 뷰모델이 가지고 있는 데이터를 옵저버블하게 해주고, 뷰에서는 데이터 바인딩으로 그것을 구독하고 있으면 되는 것이다. AAC ViewModel이라고 그게 안될리가 없다. 오히려 화면 회전시 데이터를 유지시켜주는 기능까지 있으므로 더 좋다. AAC ViewModel에 LiveData를 사용하여 바인딩 시킬 수 있다.

안드로이드 개발을 하면서 MVVM패턴을 사용하려고 한다면, 꼭 AAC의 ViewModel을 사용하지 않아도 MVVM구현은 가능하다. 선택은 개발자의 몫이다. 그러나 놓치지 말아야 할 것은, AAC의 뷰모델은 MVVM에서 말하는 ViewModel과 다르다는 것을 아는 것이다. 이걸 모르고 AAC ViewModel을 mvvm ViewModel처럼 사용한다면 개발 도중 어려움이 생길 가능성이 크다. **한 가지 예를 들자면, AAC ViewModel은 ViewModelProviders를 사용해서 ViewModel을 만드는데, 이렇게 만들어진 뷰모델은 그 액티비티에서 딱 하나만 존재하게 된다. 액티비티 한 개 내에서만 유효한 싱글톤인 셈이다. 이런 특성은 일반적인 MVVM에서는 강제되는 것이 아니기 때문에 혼란이 올 수 있다.**

바로 위에서 “뷰모델은 그 액티비티에서 딱 하나만 존재하게 된다”라고 설명했는데 오해의 소지가 있을수 있을 것 같아서 추가적으로 언급하자면, 이 말이 뷰 한개에 뷰모델 유형이 딱 한개 존재해야 한다는 것은 아니다. 예를 들어 SignUpActivity 뷰가 있다고 했을 때, 그에 대응하는 SignUpViewModel 딱 하나만이 존재해야 한다는 것은 아니다. MVVM패턴에서는 뷰와 뷰모델은 1:n 관계이기 때문이다. 개발자는 필요에 따라서 얼마든지 UserPersonalDataViewModel, UserAccountViewModel 등등 여러가지 뷰모델로 나눠서 사용이 가능하다. 다만 UserPersonalDataViewModel을 한번 생성하면, 그 액티비티에서 UserPersonalDataViewModel을 여러번 생성해도 그것은 싱글톤이기 때문에 하나의 객체만 계속 사용된다는 것이다. 그리고 한 가지더, 구글은 하나의 뷰에 하나의 뷰모델만 두고 사용하는 것을 권장한다. SignUpActivity가 있다면, SignUpViewModel 하나만 놔두고, 그 안에 여러 Model과 LiveData를 사용하는 것을 권장하고 있다. 이것은 원래의 MVVM 원칙과 맞지 않는 내용이다. 더 좋고 더 나쁜 방식은 없다. 구글이 추천하는 방식과 원래 MVVM의 원칙 중 어떤것이 더 자신의 프로젝트에 맞는지는 개발자가 판단할 몫이다.

### 결론

AAC-VM으로 MVVM-VM처럼 서용할 수있다. 오히려 화면 회전에 대한 데이터 유지까지 있어서 더 좋다.
그러나 AAC-VM은 Activity내에 1개만 생성이 가능하다. AAC-VM은 Activity안에서의 싱글톤 개념인데, MVVM패턴에서 뷰와 뷰모델은 1:n 관계를 가지기 때문에 Activity내의 여러 Fragment를 가질시에 여러 Fragment에 ViewModel을 사용하긴 어렵다. 이러니 프로젝트에 맞추어 설계해야한다.

처음 mvvm을 공부해 나갈 때, 내게 더욱 더 혼란을 주는 몇 가지 강연들이 있었다.

##### *MVVM 패턴 이야기 하면서 구글 이야기를 한다면 자리를 박차고 나가라. - 강사룡님*

또는

##### *MVVM은 AAC의 ViewModel과 연관성이 없다. AAC ViewModel을 전제로 MVVM을 설명하려고 한다면 단언컨데 우리 회사 1차 면접도 통과하지 못할 것이다.- 정승욱님*


