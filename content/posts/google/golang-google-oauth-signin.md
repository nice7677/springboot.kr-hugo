---
title: "Golang Google Oauth Signin 인증 방법"
date: 2019-12-12T00:37:20+09:00
---

2019년 1월에 Google+ API가 종료 됬습니다.

그래서 찾아보는데... 찾았습니다.

물론 너무 짧은 시간동안 서치하고 사용한거라 원래 공유되있는 내용 일수도 있습니다.

[여기](https://developers.google.com/identity/sign-in/web)로 가면 쉽게 로그인을 사용할수 있는 방법이 있는데요.

저는 또 사용하지 않았습니다.

그래서 찾은 방법은 우선 [Golang Google OAuth 사용 하기](/posts/google/golang-google-oauth/)와 겹치는 내용이 있기 때문에 그부분은 스킵하고 바로 진행하겠습니다.
 
그부분 이해하시고 참고하여 따라 하시면 됩니다.

다만 저 포스트에 나와있는 내용중 code를 발급받을때 scope를 ``https://www.googleapis.com/auth/userinfo.email``로 잡아 주시면 됩니다.

```html
<a href="
https://accounts.google.com/o/oauth2/v2/auth?
scope=https://www.googleapis.com/auth/userinfo.email&
access_type=offline&
include_granted_scopes=true&state=state_parameter_passthrough_value&
redirect_uri=http://localhost:3005/googlecallback&response_type=code&
client_id=YOUR_CLIENT_ID
">
```

다음으로 위에 링크된 포스트에 있는 resty를 사용하여 진행 합니다.

우선 사용자의 정보를 가져오기 위해서는 위의 내용에서 인증을 한뒤 발급받은 코드를 사용해 id_token을 받아야합니다. 

발급을 받기 위해서 위에서 말한 코드를 받는 작업을 해주시면 됩니다.

![이미지](/images/golang-google-oauth/signin/1.png)

다음으로 전에 작성한 포스트에 있는 스코프와 범위가 달르기 떄문에 위에 적어논 범위로 잡고 요청을 보내고 받은 코드를 사용하면 id_token이 들어옵니다.

요청을 보내면 이렇게 나옵니다!!

![이미지](/images/golang-google-oauth/signin/2.png)

코드를 보겠습니다.

```golang
func (this *GoogleService) GetEmail(code string) error {

    // 기존 token 받는 코드
	client := resty.New()
	resp, err := client.R().
		SetHeader("Content-Type", "application/x-www-form-urlencoded").
		SetFormData(map[string]string{
			"code":          code,
			"client_id":     CLIENT_ID,
			"client_secret": CLIENT_SECRET,
			"redirect_uri":  "http://localhost:3005/googlecallback",
			"grant_type":    "authorization_code",
		}).
		Post("https://www.googleapis.com/oauth2/v4/token")
	if err != nil {
		log.Println(err.Error())
	}
	src_json := resp.Body()
	var m map[string]string
	err = json.Unmarshal(src_json, &m)
	if err != nil {
		log.Println(err.Error())
	}


    // 여기 부터는 id_token을 사용해서 google사용자 정보를 가져오는 요청
	client = resty.New()
	resp, err = client.R().
		Get("https://oauth2.googleapis.com/tokeninfo?id_token=" + m["id_token"])
	if err != nil {
		log.Println(err.Error())
	}
	src_json = resp.Body()
	var n map[string]string
	err = json.Unmarshal(src_json, &n)
	if err != nil {
		log.Println(err.Error())
	}
	log.Println(n["email"])

	return nil

}
```

위에 주석을 한 부분의 요청 주소를 보시면 ``"https://oauth2.googleapis.com/tokeninfo?id_token=" + m["id_token"]``로 되있습니다.

위에서 받은 id_token을 다음 요청 부분에서 넣어주고 GET으로 요청하시면

짜잔 

![이미지](/images/golang-google-oauth/signin/3.png)

이렇게 사용자 정보가 나오는데 여기서 필요한 부분을 슬쩍하시면 됩니다. 저는 email을 슬쩍했습니다.

댓글 남겨주세요~~