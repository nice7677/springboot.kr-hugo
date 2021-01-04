---
title: "카카오 TV 챗봇 만들기"
date: 2021-01-04T16:36:41+09:00 draft: true
---

카카오TV 챗봇 만들기

일 년도 더 넘게 글을 쓰지 않았다. 이제 가끔씩 다시 적어보려고 하는데 시작이 카카오티비 챗봇이다.

이미 작년에 만들었는데 카카오TV가 죽어서 사용하질 않고 있다.

시청자가 500명 정도되는 방에서 테스트까지 끝낸 챗봇인데

테스트가 끝날 때 즈음 사람들이 대부분 트위치로 이동해 사용하질 않았다.

궁금해하는 사람이 있을 수도 있으니 어떻게 만들었는지 개발기를 적어보도록 해야겠다.

우선 카카오TV는 트위치 처럼 API를 제공하지 않는다.

하지만 Web은 제공한다 ([?]팟플레이어라는 플레이어를 사용해 보는사람들이 많다. 웹은 트위치처럼 좋지 않고 기능이적어 사람들이 잘안쓴다.)

그래서 나는 이 Web을 사용해 챗봇을 제작한다.

- kakao tv : https://tv.kakao.com

카카오티비는 API를 제공하지 않기 때문에 개발자가 유저 ID를 직접 등록해놔야 작동할 수 있다.

그렇기 때문에 위에 있는 링크에 들어가 검색을 통해 방송하는 사람의 ID 값을 가져와야 한다.

방송하는 사람의 ID 값을 가져오는 이유는 이 페이지를 계속 가져와 방송의 상태를 확인다.

확인되면 방송이 켜진 걸 알 수 있고 켜지면 방송에 ID 값이 생긴다.

그럼 그 ID로 들어간 페이지에서 채팅창의 group id를 가져올 수 있다. (group id를 가져오는 이유는 채팅 소켓에 접속해 채팅창의 내용을 가져올 수 있기 때문)

그다음으로 방송이 켜지면 셀레니움을 통해 브라우저를 하나 띄우고 실행한다 (https://hub.docker.com/r/selenium/standalone-chrome 일반적인 셀레늄이 아닌 standalone
이다.)

그리고 카카오 아이디 비번을 입력시키는 동작을 실행해 그 방에 입장한다.

단, 여기서 알아야 할 것은 제공된 API로 하는 게 아닌 웹으로 하는 것이기 때문에 방송하는 사람과의 얘기를 통해 챗봇의 아이디에 AD(매니저) 권한을 줘야 한다.

AD의 권한이 아닐 경우 채팅을 보내는 데 제한 시간이 걸린다. 그리고 AD를 줘야 채금이라든지 강퇴를 시킬 수 있다.

그다음으로 서버단에서 해당방의 채팅방에 소켓을 통해 들어가야 한다.

go 언어 resty 라이브러리를 사용해서 요청 사항은 이렇다
``` golang
client := resty.New()
resp, err := client.R().
		//SetHeader("Content-Type", "application/json").
		SetHeaders(map[string]string{
			"Content-Type":   "application/x-www-form-urlencoded",
			"Origin":         "https://live-tv.kakao.com",
			"Referer":        "https://live-tv.kakao.com/kakaotv/live/chat/user/" + LiveLinkId, // 유저마다 번호가다름 유저번호
			"Sec-Fetch-Mode": "cors",
			"User-Agent":     "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
		}).
		SetFormData(map[string]string{
			"groupid": Groupid,
		}).
		Post("https://play.kakao.com/chat/service/api/room")
```
이렇게 요청을 보내고 나면 채팅에 접속할 수 있는 ip, roomid 등이 나온다

그러면 다음으로 거기에 tcp 접속을 시도하면 된다.

접속을 하고 나면 이제 채팅을 읽을 수가 있다.

그다음 내가 원하는 명령어를 등록 후 시청자 중 내가 등록해놓은 명령어를 친 경우에 그에 맞게 설정된 메시지를 셀레니움에 들어가 채팅을 칠 수 있는 함수를 만들어 보내면 된다.

여기서 더 응용해서 본인이 원하는 기능 채금, 강퇴 등을 만들 수가 있다.

그리고 방송이 꺼질 경우 셀레니움도 같이 꺼준다. 브라우저 1개당 100MB 정도의 메모리를 먹는다.

그리고 이 챗봇은 방송하는 사람이 신청을 해서 자기방에 넣는 게 아니라 개발자가 채팅창으로 밀어 넣을 수 있다. (싫어하면 채금 먹고 강퇴당한다.)

그래서 미리 사전에 얘기를 끝내고 AD를 받고 밀어 넣어야 한다.