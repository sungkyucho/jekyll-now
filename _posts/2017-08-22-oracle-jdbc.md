---
layout: post
title: Oracle과 JDBC 연동 테스트
---

요즘 ELK(Elasticsearch + Logstash + Kibana)로 뭐 좀 할 수 있는 거 없나 끄적이는 중..

아무래도 회사에서 직접 데이터를 가져올 수 있는 그 quality & quantity 모두 좀 쉽지가 않고(한낱 일개미 따위가..) 그 중에서도 또 실제로 big data나 machine learning쪽에 실습해볼만한 데이터도 흔치 않다.

그래도 일단은 한번쯤 직접 손으로 만져봐야 하는 것이니.. 아무 데이터나 서버에 있는 걸로 ELK 실습 중, 회사에서는 대부분 oracle DBMS를 쓰는데 이를 logstash와 연동하기 위해서 우선 JDBC로 oracle과 접속되는지 확인이 필요함.

mysql은 open source라서 그런가, 레퍼런스가 많은 거 같은데 의외로 oracle은 간단하게 테스트하는 방법이 없어서 기록해 둠ㅋㅋ

[굉장히 잘 정리된 사이트](https://support.symantec.com/en_US/article.TECH219803.html) 가 있고, 이대로 따라하면 됨.

+ JDK 1.8은 우선 깔고(요즘 ELK는 1.8 기준임)
+ JDBC를 [여기서 - oracle 홈페이지](http://www.oracle.com/technetwork/apps-tech/jdbc-112010-090769.html) 에서 다운받음
+ 아래처럼 test code를 받고
 ```.shell
 $ curl https://symwisedownload.symantec.com//resources/sites/SYMWISE/content/live/SOLUTIONS/219000/TECH219803/en_US/JDBCInfo.txt?__gda__=1503520521_2f84cfcdb0a24872390c20ae0f27c0db > JDBCInfo.java
 ```
+ 컴파일 하여
 ```.shell
 $ javac JDBCInfo.java
 ```
+ config에 맞게 돌려보면 끝
```.shell
$ java JDBCInfo "jdbc:oracle:thin:<ID>/<PWD>@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<IP>)(PORT=<PORT#>))(CONNECT_DATA=(SERVER=<TYPE>)(SERVICE_NAME=<SERVICE NAME)))"
```
![fig1]({{ site.baseurl }}/images/tech/elk/1.png)


다음에는 Logstash 설정을 통해 oracle에 있는 data를 migration할 예정.. 당분간은 쉽게쉽게, but 기록해두기
