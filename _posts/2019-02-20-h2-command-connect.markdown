---
layout: post
title:  "h2 consol 접속"
date:   2019-02-20 16:14:00 +0900
categories: java
---

# h2 consol 접속

---------  

스프링 배치 프로젝트 중 h2를 사용해서 배치 히스토리를 기록을 하고 있는데 서버 환경에서는 외부에서 접속 할 수가 없어서 검색중에 커맨드 접속 방법이 있어서 기록

[h2.jar download](http://www.h2database.com/html/download.html)

Platform-Independent Zip을 다운받고 압출을 풀면
bin 폴더에 h2-1.x.jar 파일이 있다

- jar 파일을 java로 실행

```
java -cp h2*.jar org.h2.tools.Shell
```

- url, user, password 입력 후 접속

```
Welcome to H2 Shell 1.4.197 (2018-03-18)
Exit with Ctrl+C
[Enter]   jdbc:h2:~/test
URL       jdbc:h2:tcp://localhost:9092/mem:h2
[Enter]   org.h2.Driver
Driver
[Enter]
User      sa
[Enter]   Hide
Password
Password
Connected
Commands are case insensitive; SQL statements end with ';'
help or ?      Display this help
list           Toggle result list / stack trace mode
maxwidth       Set maximum column width (default is 100)
autocommit     Enable or disable autocommit
history        Show the last 20 statements
quit or exit   Close the connection and exit

sql>
```


## 참조 링크
[참조](http://opensource-soa.blogspot.com/2009/03/how-to-use-h2-shell.html)