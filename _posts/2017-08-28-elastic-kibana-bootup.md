---
layout: post
title: Elasticsearch - Kibana boot up
categories: Dev
---

logstash로 데이터는 이제 어떻게 넣을 것인지만 결정하면 되고, 들어간 데이터를 잘 인덱싱하고 viewing하는 것은 Elasticsearch와 Kibana의 역할이다.

처음에 내가 있는 zone에서 Elasticsearch를 설치한 서버로 접속이 잘 안되길래 방화벽 문제인 줄 알았는데, 살펴보니 별도로 configuration을 모두 해줘야 한다.



+ Elasticsearch
  + config/elasticsearch.yml
    + ```network.host``` 부분을 주석 해제하고, 서버 IP를 적는다. (아마 클러스터링 구조로 가게 되면 이게 마스터 노드를 적는 부분이려나.. 테스트를 못하니 답답하다;;)
    + 나머지는 다 냅두고 맨 마지막에 ```bootstrap.seccomp: false```를 넣는다 (이건 나중에 설명)

  + 그리고 실행을 하면!!!! 아래와 같은 에러가 발생한다.

  ![fig3]({{ site.baseurl }}/images/tech/elk/3.png)

    + Elasticsearch가 생각보다 리소스를 많이 차지한다는 것을 알 수 있으며, 결국 해당 프로세스가 관리하는 파일디스크립터의 개수라든가, 메모리 용량, 쓰레드 개수를 확 올려야 한다는 의미이다.

    + 이건 아래와 같이 ```ulimit``` 또는 ```/etc/security/limit.conf```에서 확인할 수 있다

    ![fig4]({{ site.baseurl }}/images/tech/elk/4.png)

    + 참고로 **이걸 권고수치로 올린 다음에는, 세션을 끊고 다시 접속해야 함.** [참고링크](https://github.com/elastic/elasticsearch/issues/17430)

    + 이렇게 하면, 딱 하나의 에러가 bootstrapping 과정에서 남게 되는데 그게 seccomp 이다. seccomp와 관련된 내용은 [여기 링크](https://ko.wikipedia.org/wiki/Seccomp)를 통해 확인할 수 있다. 즉, 정해진 system call 이외의 인터럽트가 발생하면 이를 허용하지 않겠다는 기능인데 만약에
      + 이 기능이 리눅스 커널에서 지원을 해주도록 커널을 업데이트 하거나
      + 아니면 **at your own risk** 스스로 책임지고 이걸 끄라는 가이드가 나온다. ~~그럼 꺼야지 ㅋㅋㅋ~~

 + Kibana 설정
   + 이건 상대적으로 간단한데, ```server.host``` 값을 설정해주고, ```server.name```을 설정해주면 끝.


  그리고 접속해보면.

  ![fig5]({{ site.baseurl }}/images/tech/elk/5.png)

  화면은 뜬다. 근데 인덱싱이 하나도 안되어 있다고.. 아마 이건 logstash에서 값을 밀어넣어줄 때 indexing 값을 안 줘서 그런 듯.

  이제 실제 데이터를 잘 집어넣어보면 될 듯. **이제 겨우 시작할 수 있을 듯 하다..ㅋㅋㅋ**
