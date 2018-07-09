---
layout: post
title: 5G Security에 대한 아주 간단한 검토
categories: Tech
---

5G 5G 5G..
자세하게는 모르겠고 간단하게 살펴보자..

속도, 안정성, 보안. 이라는 모 회사의 캐치프레이즈를 보듯이 5G는 통신사 입장에서는 새로운 붐업을 일으킬 좋은 기회이다. 왜냐하면, 다른 쪽에서는 건드리기 어려운 아주 좋은 새로운 Biz 용어이기 때문이다. (5G가 단순 기술용어 같지는 않다..)

### 5G ???
LTE가 4G인 것은 대부분 알고 있고, 와이브로 ~~완전 망한~~ 가 3.5G였던 것도 뭐 기사에도 많이 나왔으니 잘 아는 내용일 듯. 하나의 Generation을 규정짓는 것이 대부분 속도였다는 점이 부각되지만 사실 그 속도를 높이기 위해 Infra 측면에서는 무수한 변화가 필요하다.

우선 간략하게 내가 이해한 것은 5G의 특성은 **on-demand framework (or Infra?)** 가 가장 큰 차별점으로 보인다. 구글에 _5G Security_ 를 검색하면 가장 위에 나오는 Huawei의 [whitepaper](https://www.huawei.com/minisite/5g/img/5G_Security_Whitepaper_en.pdf) 를 보면 대략적인 느낌이 온다.

즉, 하나의 망을 모두가 다 똑같이 쓰는 게 아니라 서비스와 파트너 특성별로 네트워크를 slicing 하여 이를 목적에 맞게 망을 나눠서 쓸 수 있게 하겠다는 것이 기존 Generation 대비, 5G의 특성으로 보인다는 점이다.

내 해석이 잘못되었을 수도 있지만. 나는 이걸 기존 IT망이 IDC에서 클라우드로 이동했던 것처럼 NW망도 하나의 클라우드화가 되는 것이라고 바라본다. 

![그림1]({{ site.baseurl }}/images/tech/5g/1.png)

[출처 - ETSI 5G security working group - Huawei](
https://docbox.etsi.org/Workshop/2017/201706_SECURITYWEEK/06_5GSECURITY/S02/HUAWEI_WONG.pdf)

따라서, 이러한 flexibility를 지원하기 위해서 가상화니 SDN이니 NFV니 이런 용어들이 활용되고 있다고 이해하면 좋을 듯 하다. 왜냐하면 적재적소에 맞게 망을 뗐다 붙였다 하려면, 기존의 물리장비로는 도저히 불가능할테니..

### 5G Security

그렇다면.. 여기서 필요한 Security perspective는 어디에 초점을 두어야 할까?

3GPP의 ESTI에서 표준화 방안을 구성 중이며, 거기서 공개한 사이트는 아래와 같다.  [ETSI Security Working Group](https://www.etsi.org/etsi-security-week-2017/5g-security)
(여기서 중간 Presentations를 누르면 아래 [FTP 서버](https://docbox.etsi.org/Workshop/2017/201706_SECURITYWEEK/06_5GSECURITY)로 이동)

좀 오래된 자료지만, 그래도 몇 개 본 자료 중에는 5G Security 진입지점? 3GPP의 구성? 관련하여 제일 이해가 잘되는 자료는 아래 내용이다.

![그림2]({{ site.baseurl }}/images/tech/5g/2.png)
![그림3]({{ site.baseurl }}/images/tech/5g/3.png)

[출처 - ETSI 5G security working group - Ericsson Research](https://www.sics.se/sites/default/files/pub/sics.se/SecurityDay16/karl_norrman.pdf)


조금 더 구체적인 내용은 academic version 이지만, 아래 내용이 좋다.

![그림4]({{ site.baseurl }}/images/tech/5g/4.png)
![그림5]({{ site.baseurl }}/images/tech/5g/5.png)

[출처 - 5G Security: Analysis of Threats and Solutions, 2017 IEEE Conference on Standards for Communications and Networking](https://www.researchgate.net/publication/318223878_5G_Security_Analysis_of_Threats_and_Solutions)

기타 [IEEE](http://icc2018.ieee-icc.org/workshop/1st-ieee-workshop-5g-wireless-security-5g-security)에서는 아래와 같은 주제로 5G Security에 대한 폭넓은 논의를 진행하고 있다고 한다니 정말 엄청난 width/depth의 영역이 아닐 수 없다.

```5G architecture with security and privacy considerations
Security for new service delivery models
Verticals and business (non-technical) 5G security requirements and solutions
Big data analytics tools and techniques in 5G Security
Advances in lightweight cryptography and IoT/CPS security
Wireless virtualization and slicing security
Authentication, authorization, and accounting (AAA) for 5G security
Diameter security in 5G
Quantum Safe Cryptography for 5G
Secure Integration of IoT/CPS and Cloud Computing
Secure Device-to-Device communications in 5G
Secure integration of IoT /CPS and other networks
Intrusion Detection/Prevention Techniques and System Integrity
Secure data storage, communications and computing
Energy efficient security in IoT and CPS
Heterogeneous system modeling for 5G security
Secure sensing and computing techniques in 5G
Big data analytics for 5G security
Secure, privacy-aware and trustworthy IoT/CPS communications
Trust models and trust handling/propagation for 5G security
Physical layer security for 5G
5G security standardization
```

### 시사점 및 느낀 바..

간략하게 살펴본 바로, 5G Security는 현재 주로 논의되는 내용이 USIM/IMSI  등을 기반으로 네트워크 망과 관련된 보안에 특화되어 있고 이를 운용하기 위한 암호학 관련 논의가 다수 차지 하는 것으로 보인다.

Generation이 바뀌었으면 무엇이 바뀌었는지를 알고 보안도 이에 대한 새로운 패러다임으로 같이 들어가야 하는데, 아직까지 현재 수준에서 논의 중인 5G Security는 사실 기존의 이통망을 잘 이해하고 있지 않은 이상 상당히 접근하기 어려운 영역으로 보인다.

재미있는 것은 이번에 논의 중인 5G security에서 다루는 항목들 중 주요 항목들은 기존 LTE 에서 발견된 취약점을 기반으로 하는 케이스가 꽤 보인다는 점이다.

[최근에 있었던 aLTEr 취약점](https://thehackernews.com/2018/06/4g-lte-network-hacking.html?m=1) 관련해서도 5G에 대한 아래와 같은 우려를 보이고 있고,

```
The above attacks are not restricted to only 4G.
Forthcoming 5G networks may also be vulnerable to
these attacks, as the team said that although 5G
supports authenticated encryption, the feature is
not mandatory, which likely means most carriers do
not intend to implement it, potentially making 5G
vulnerable as well.
```

즉, 5G에서 암호화나 인증 등의 요소가 mandatory가 아니기 때문에 문제가 될 것이라는 우려와 함께, [IMSI catcher](https://en.wikipedia.org/wiki/IMSI-catcher) 와 관련된 privacy 침해 우려를 줄이기 위해 이런저런 논의가 진행되고 있다는 것이 위 working group 내에서 보인다.

다만, 늘 그렇듯이.

**보안 표준이 있다고 해서 보안이 지켜지는 것은 아니다.**
Software engineering 에서 얘기하는 validation과 verification의 개념처럼 과연 주어진 requirements(여기는 security requirements & specification이겠지)대로 구현이 제대로 되었는지를 verify 할 수 있는지. 그리고 그것을 통해 고객관점에서의 전반적인 보안을 달성하고 있는지 validate 할 수 있는지.

그것이 통신사가 갖추어야 할 5G Security를 추구할 때 지녀야 할 올바른 미덕이 아닐까 싶다.
