---
title: "golang go-pg 사용하기"
date: 2019-03-23T14:22:38+08:00
---

# golang에서 postgresql orm go-pg 사용하기

처음 글에서는 go-pg를 사용한 connection과 close에 대해 적어본다 예제 샘플을 따라하면 잘 될것이다.

나는 postgresql을 사용하기 위함이 아니라 cockroach db를 go에서 사용하기 위해 go-pg를 선택했다.

go-pg에 대한 링크를 걸어 둡니다.

[Github](https://github.com/go-pg/pg) / [Document](https://godoc.org/github.com/go-pg/pg)

위에 링크를 첨부해 두었다 우선 go-pg에 접속하기 위해서는 다음과 같은 세팅을 잡는다.

  - database.go
  
  ```golang
func Connect() *pg.DB {
	db := pg.Connect(&pg.Options{
		User:      "user",
		Database:  "database",
		Addr:      "addr:port"
	})
	if db == nil {
		log.Println("Failed to connect to db")
	} else {
		log.Println("Connection to db success")
	}
	return db
}

func Close(db *pg.DB) {
	closeErr := db.Close()
	if closeErr != nil {
		log.Println("Error while closing the connection, Reason: %v", closeErr)
	}
	log.Println("Connection closed success")
}
  ```
이 설정으로 db에 connect와 close를 할 수 있다.

다음 편에서는 go-pg를 사용한 select에 대해 알아보도록 하겠습니다.