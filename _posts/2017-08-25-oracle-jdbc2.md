---
layout: post
title: Oracle과 JDBC 연동 테스트-2
categories: dev
---

아무래도 차근차근 밟아보는 게 아니라, 일단 부딪히고 보다보니.. 막히는 게 너무 많다.

이전에 JDBC와 Oracle DB와의 연동테스트는 해봤고, 이제 실제로 DB를 끌어와야 한다. 방법은 [여기링크](https://discuss.elastic.co/t/logstash-jdbc-input-oracle-settings/26996)

설정파일은 ```~/logstash-5.5.2/bin```아래에 있는 ```logstash``` 파일과 같은 경로에 두고 새로 만들어주면 된다.

로드하는 방법은 (설정파일이 simple-out.conf라고 가정한다면)
```
   ./logstash -f simple-out.conf -V warn
```

여기서 -V 옵션은 안줘도 되고 debug level에 따라 자체적으로 줘도 되는데, 현재 5.5.2 버전에서 log4j 라이브러리하고 연동이 잘 안되는 문제가 있는 듯 하다. 그래서 5.6 버전 이상으로 타라고 하는데.. 이거 하려면 또 방화벽 열어야 함.. (안해 젠장..)

상기 링크에서 DB table을 하나 밖에 못 끌어오는데, 2개 이상의 DB 테이블을 끌어오려면 아래와 같이. [참조](https://stackoverflow.com/questions/37613611/multiple-inputs-on-logstash-jdbc)

```
input {
  jdbc {
    jdbc_driver_library => "/Users/logstash/mysql-connector-java-5.1.39-bin.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    jdbc_connection_string => "jdbc:mysql://localhost:3306/database_name"
    jdbc_user => "root"
    jdbc_password => "password"
    schedule => "* * * * *"
    statement => "select * from table1"
    type => "table1"
  }
  jdbc {
    jdbc_driver_library => "/Users/logstash/mysql-connector-java-5.1.39-bin.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    jdbc_connection_string => "jdbc:mysql://localhost:3306/database_name"
    jdbc_user => "root"
    jdbc_password => "password"
    schedule => "* * * * *"
    statement => "select * from table2"
    type => "table2"
  }
  # add more jdbc inputs to suit your needs
}
output {
    elasticsearch {
        index => "testdb"
        document_type => "%{type}"   # <- use the type from each input
        hosts => "localhost:9200"
    }
}
```

참고로 나는 ```output``` 쪽은 간단하게 아래와 같이 끝냈고.

```
  stdout {codec => json_lines}  
```

테스트는 안해봤지만, ```schedule``` 항목에서 sync 주기를 맞출 수 있는 듯 하다. 형식을 보니까 crontab 하고 같은 형식일 듯.

중요한 정보는 없지만, 혹시 모르니 흐릿하게..ㅋㅋ
![fig1]({{ site.baseurl }}/images/tech/elk/2.png)

**여기서부터가 문제인데..**
현재 땡겨와야 하는 DB 테이블이 총 321개이다.. ~~ㅎㄷㄷㄷㄷ~~
생각하는 방법은 아래와 같다.

  + oracle db 전체를 backup해서 .sql 파일을 땡겨온다
  + 스크립트 하나 간단히 작성해서, conf 파일에 321개 db를 읽어오는 설정을 작성한다
  + DB에 query를 만들어서 내가 필요로 하는 정보를 가지는 view table을 하나 별도로 만들어 그걸 땡겨온다

첫 번째 방법은.. 동기화 쪽에 문제가 있을 거 같고.
두 번째 방법은.. 좀 번거롭긴 하지만 괜찮을 듯.
세 번째 방법은.. 그럼 엘라스틱서치를 왜 쓰나. 걍 RDB 쓰지..

이런 생각이 들어서 두 번째 방법을 선택할 예정. 아.. 귀찮다..

일단 몇 개 테이블이라도 들어간 거를 kibana에서 확인하고 싶은데, 사내 방화벽 문제로 아직 kibana 포트가 안열리고 있다. ~~아오 짜증나~~

그러니, 일단 퇴근하고 다음 시간에..ㅋㅋㅋ
