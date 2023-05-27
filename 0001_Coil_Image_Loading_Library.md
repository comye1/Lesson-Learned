# COIL 이미지 로딩 라이브러리
> Jetpack Compose 프로젝트에 사용하기!

## 라이브러리 추가하기 
Jetpack Compose Extension 라이브러리만 추가해주어도 된다. 
```kotlin
implementation("io.coil-kt:coil-compose:2.4.0")
```
## 사용법
### AsyncImage 컴포저블
- 이미지 요청을 비동기적으로 실행하여 결과를 그려준다.
- 기존의 ``Image`` 컴포저블과 동일한 인자들을 지원하고, 
추가적으로 ``placeholder``/``error``/``fallback``을 위한 ``painter``와 ``onLoading``/``onSuccess``/``onError`` 콜백을 받는다.

```kotlin
AsyncImage(
    model = "https://example.com/image.jpg",
    contentDescription = null,
)
```

```kotlin
AsyncImage(
    model = ImageRequest.Builder(LocalContext.current)
        .data("https://example.com/image.jpg")
        .crossfade(true)
        .build(),
    placeholder = painterResource(R.drawable.placeholder),
    contentDescription = stringResource(R.string.description),
    contentScale = ContentScale.Crop,
    modifier = Modifier.clip(CircleShape)
)
```

### SubcomposeAsyncImage 컴포저블

- ``AsyncImage`` 컴포저블의 변형
- ``AsyncImagePainter``의 상태에 따른 ``슬롯 API``를 제공하기 위해 ``subcomposition``을 사용한다. 

```kotlin
SubcomposeAsyncImage(
    model = "https://example.com/image.jpg",
    loading = {
        CircularProgressIndicator() // loading 상태에 표시할 Composable
    },
    contentDescription = stringResource(R.string.description)
)
```
- ``content`` 인자를 통해 더욱 복잡한 로직을 구현할 수  있다. (``SubcomposeAsyncImageContent``가 이미지를 그림)
```kotlin
SubcomposeAsyncImage(
    model = "https://example.com/image.jpg",
    contentDescription = stringResource(R.string.description)
) {
    val state = painter.state
    if (state is AsyncImagePainter.State.Loading || state is AsyncImagePainter.State.Error) {
        CircularProgressIndicator()
    } else {
        SubcomposeAsyncImageContent()
    }
}
```
<details>
    <summary>Subcomposition의 성능 문제</summary> 
    Subcomposition is less performant than regular composition so this composable may not be suitable for parts of your UI where high performance is critical (e.g. lists).
</details>
<details>
    <summary>주의할 점</summary> 
    If you set a custom size for the ImageRequest using ImageRequest.Builder.size (e.g. size(Size.ORIGINAL)), SubcomposeAsyncImage will not use subcomposition since it doesn't need to resolve the composable's constraints.
</details>

### AsyncImagePainter

- ``AsyncImage``와 ``SubcomposeAsyncImage``는 내부적으로 ``AsyncImagePainter``를 사용해 모델을 불러온다. 
- ``Painter``가 필요하고 ``AsyncImage``를 사용할 수 없는 상황이라면 ``rememberAsyncImagePainter``를 사용해 이미지를 불러올 수 있다.
    ```kotlin
    val painter = rememberAsyncImagePainter("https://example.com/image.jpg")
    ```

    ``rememberAsyncImagePainter`` 는 낮은 레벨의 API이기 때문에 예상치 못한 동작을 할 수 있다. 메소드에 관한 문서를 읽어보는 것을 추천한다.

<details>
    <summary>주의할 점</summary>
If you set a custom ContentScale on the Image that's rendering the AsyncImagePainter, you should also set it in rememberAsyncImagePainter. It's necessary to determine the correct dimensions to load the image at.
</details>

### Observing AsyncImagePainter.state
- 다음에 이어서...