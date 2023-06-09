# Ktor Client(Android)에서 토큰을 동적으로 붙이기
## 부제 Interceptor의 쓰임
> **왜 동적으로 붙이는데?**\
> "로그인하지 않고 계속하기"를 누르는 이들을 위하여..!
>

## 나의 상황
- Hilt 사용중으로, HttpClient를 앱 시작 시점에 생성하여 공용으로 사용한다.
- 로그인하지 않아도 사용이 가능하다. 즉, 요청에 토큰을 포함할수도, 안할 수도 있다.
- 최초 로그인 시점에 토큰이 내부적으로 저장되고, 이를 통해 자동 로그인이나 리프레시를 수행한다. 

## 시도 1. 호출할 때 전달한다.

ViewModel &rarr; (UseCase) &rarr; Repository &rarr; DataSource

이 사이에 토큰을 전달해주는 방법이다.

토큰을 관리하는 클래스는 결국 앱 내부에서의 입출력을 수행할 것이기에 DataSource에 위치하는 것이 적절하다.
그 다음은 **추상화**의 문제이다.
토큰의 관리 주체를 Repository, UseCase, ViewModel의 순서대로 원하는 대로 추상화할 수 있다.

이 시점에서 가징 간편한 판단 기준은 **토큰의 포함 여부를 어디서, 어떻게 결정할 것인지**가 될 듯 하다.

To Be Continued...