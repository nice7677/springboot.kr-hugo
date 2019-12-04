---
title: "Golang Google OAuth 사용 하기"
date: 2019-11-21T17:21:14+08:00
---

Golang에서 google photo를 사용하기 위해서 찾았던 내용을 정리 하려고 한다.

이건 내 방법이고 다른 방법이 있을 수 있음.

[Google OAuth 2.0 for Web Server Applications](https://developers.google.com/identity/protocols/OAuth2WebServer)

우선 위에 있는 사이트를 참조해 정리 한다.

위 사이트에서는 Go는 라이브러리가 지원 한다고 적혀 있다.

![이미지](/images/golang-google-oauth/1.png)

하지만 쓰지 않았다. ㅋㅋ

내가 사용한 방법은 [**resty**](https://github.com/go-resty/resty)라는 http 라이브러리를 사용했다.

resty는 spring으로 보면 resttemplate 같은? 나는 같은 용도로 사용한다.

resty설치를 위해 다과 같이 입력해 준다.

```
go get -u github.com/go-resty/resty/v2
```

설치가 끝나면 라이브러리를 사용할수 있다. 

사용법은 README에 상세하게 적혀있다.

이제 OAuth에 필요한 작업을 하기 위해 본격적으로 만들어 보자

[Google Cloud Platform](https://console.cloud.google.com/?hl=ko)에서 프로젝트 생성과 사용자 인증 정보를 생성해야 한다.

![이미지](/images/golang-google-oauth/2.png)

API 및 서비스를 들어가면 다음과 같은 메뉴가 나오는데 거기서 사용자 인증 정보를 눌러 생성하자

![이미지](/images/golang-google-oauth/3.png)

애플리케이션 이름을 적어주고 도메인과 애플리케이션 링크도 적어주자

![이미지](/images/golang-google-oauth/5.png)

![이미지](/images/golang-google-oauth/4.png)

작성이 끝나면 다음으로 메뉴에서 사용자 인증 정보를 들어가면 다음과 같이 정보가 생성되 있는데

정보를 클릭하면 상세 내용을 볼 수 있다.

![이미지](/images/golang-google-oauth/6.png)

![이미지](/images/golang-google-oauth/7.png)

이제 여기서 클라이언트 ID(client_id)와 클라이언트 보안 비밀(client_sceret)이 우리가 필요한 정보다.

이제 OAuth에 권한을 부여 할 아이디를 적용해 주는 단계가 필요하다.

`Step 1: Set authorization parameters`을 보게 되면 사용 할 수 있는 파라미터들이 정리 되있다.

`Step 2: Redirect to Google's OAuth 2.0 server`를 보면 적용 할 수 있는 방법이 있다.

인증 정보를 생성할때 만들어 놓은 리다이렉트 url과 client_id가 필요하다.

scope는 나는 구글 포토를 사용하기 위해서 다음과 같이 범위를 잡았다.

`scope=https://www.googleapis.com/auth/photoslibrary+https://www.googleapis.com/auth/photoslibrary.appendonly`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>SPRINGBOOT.KR</title>
</head>
<body>
<a href="
https://accounts.google.com/o/oauth2/v2/auth?
scope=https://www.googleapis.com/auth/photoslibrary+https://www.googleapis.com/auth/photoslibrary.appendonly&
access_type=offline&
include_granted_scopes=true&state=state_parameter_passthrough_value&
redirect_uri=http://localhost:3005/redirect&response_type=code&
client_id=Your client id
" style="font-size: 50px;">SPRINGBOOT.KR</a>
</body>
</html>
```

이렇게 잡아 놓으면 링크를 눌렀을때 권한을 부여할수 있는 페이지로 이동된다.

그리고 권한을 다 주게 되면 위에서 설정해 놓은 redirect_uri로 code 값이 들어오는데

그 부분을 캐치해 code를 따로 들어준다.

나는 ehco를 사용하고 있어서 `c.FormValue("code")` 이렇게 하면 잘 받아 졌다.

그리고 이제 저렇게 받은 code 값을 사용해 OAuth를 요청 할 수 있다.

```golang
func GetAccessToken(code string) {
	client := resty.New()
	resp, err := client.R().
		SetHeader("Content-Type", "application/x-www-form-urlencoded").
		SetFormData(map[string]string{
			"code":          YOUR_CODE,
			"client_id":     YOUR_CLIENT_ID,
			"client_secret": YOUR_CLIENT_SECRET,
			"redirect_uri":  "http://localhost/redirect",
			"grant_type":    "authorization_code",
		}).
		Post("https://www.googleapis.com/oauth2/v4/token")
	if err != nil {
		log.Println(err.Error())
	}
}
```
위의 코드에서 보면 클라이언트 아이디와 시크릿이 필요하다.

아까 받은 사용자 인증 정보의 값을 저기에 입력하면 된다.

그리고 요청을 보내면 다음과 같이 Response가 오면 성공

```json
{
  "access_token":"1/fFAGRNJru1FTz70BzhT3Zg",
  "expires_in":3600,
  "token_type":"Bearer",
  "refresh_token":"1/xEoDL4iW3cxlI7yDbSRFYNG01kVKM2C-259HOF2aQbI"
}
```

그럼 이제 저기에 있는 access token을 api를 사용할때 사용하면 된다!

expires_in에 있는 3600은 초단위로 1시간이다.

그럼 리프레시 토큰은.. 다음 기회에 작성 하겠습니다.