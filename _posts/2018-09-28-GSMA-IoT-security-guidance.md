---
layout: post
title: GSMA IoT security guidance & assessment
categories: Tech
---

또 오랜만에 글 쓰는 것 같다. 이렇게 한번 글 쓰기가 어려운 이유는, 늘 두 가지가 복합적이다. 1) 공부를 안하고 있다 2) 공부를 해도 글로 남기기가 귀찮다.

그냥 부담갖지 말고, 가볍게 정리해놓는 목적을 잊지 말자.

회사에서 [GSMA](https://en.wikipedia.org/wiki/GSMA) (그냥 통신사들의 모임, ~~친목단체?~~)의 IoT security guideline을 준수하겠다는 내용으로 [press release](https://www.sktelecom.com/en/press/press_detail.do?idx=1279) 가 아주 작게 있었다. ~~왜 따를까~~~

그러다보니 사내에서 IoT security를 하는 사람이 나 밖에 없는지라 이걸 담당하게 되면서 문서를 좀 살펴보게 됐는데. 아주 뜬구름 같고 현실을 모르는 문서이긴 하지만 security 측면에서 살펴보면 또 그간 몰랐던 것들이 많이 나와서 정리해둘 필요가 있을 듯 하다.

**참고로.**
GSMA 에서는 현재 크게 3가지의 주요 어젠다를 제시했는데 **1)5G 2)인증 3)IoT security** 라고 한다. 5G를 제외하고는 보안 쪽 어젠다가 2가지인 것을 보면.. 그들도 이제 할 거가 많이 없다는 걸 추측할 수 있다..

## GSMA IoT security guidance 살펴보기

GSMA에서는 크게 두 가지 범주로 IoT security에 대하여 정리했다. Guideline과 Assessment. Guideline 문서는 [overview 문서](https://www.gsma.com/iot/iot-security-guidelines-overview-document/) 와 [세부 항목 문서들](https://www.gsma.com/iot/iot-security/iot-security-guidelines/)로 다시 나뉘는데, 그들이 생각하는 IoT model을 보면 아래 그림과 같다.

![그림1]({{ site.baseurl }}/images/tech/gsma/1.png)

개인적으로, 무슨 문서를 보든지간에 가장 중요한 지점 중에 하나가 **상정하고 있는 모델/범위가 무엇이냐** 라고 생각한다. 이유는, 그 모델 하에서 문제점, 대응방안 등등이 논해지기 때문에 결국 **하려는 모든 이야기들은 미리(명시적으로 혹은 묵시적으로) 정해놓은 범위 내에서 이루어진다** 는 것을 미리 염두해야 한다. 글쓴이가 하려고 하는 이야기의 playground가 어디인지는 사실 놓치기 굉장히 쉬운 지점이다.

위 그림에서도 통신사들의 연합체인 GSMA 답게, Communication Network (즉, 이동통신망이겠지..) 는 매우 중요한 지점에 위치한다. ~~실제로도 그럴까..~~

그리고 주요 가이드라인들도 아래 구성요소 + Network operator로 이루어진다.

 * **Service ecosystem** – The Service Ecosystem represents the set of services, platforms, protocols, and other technologies required to provide capabilities and collect data from Endpoints deployed in the field.
 * **Endpoint ecosystem** – The Endpoint Ecosystem consists of low complexity devices, rich devices and gateways that connect the physical world to the digital world in via several types of wired and wireless networks


 일단 기본 링크는 아래.
 https://www.gsma.com/iot/iot-security-assessment/


##그간 잘 모르던 부분들

### Risk assessment model

 질문지 중에, risk assessment를 formal 한 방법으로 하느냐 하면서 예로 OCTAVE를 드는데, 아예 처음 듣는 단어였다. 보니까 제시된 것도 2003년인데, 신기술도 아니고.

뭔지 찾아보니 OCTAVE는 **Operationally Critical Threat, Asset, and Vulnerability Evaluation** 의 준말로써, information security risk 를 평가하려는 목적을 통해 정보자산(information-asset)을  CIA(Confidentiality, Integrity, Availability) 관점에서 살펴보는 하나의 프레임워크로 보면 된다.

~~보면 카네기 멜론에서 제시한 문서던데, 역시 CMMI를 비롯하여 카네기 멜론은 이러한 software engineering 분야에 있어서는 최고인가보다.~~

핵심은 아래 그림.

![그림2]({{ site.baseurl }}/images/tech/gsma/2.png)

즉, organization view, technological view, strategy and plan development의 세 관점으로 자산을 살펴보고 각각에 대한 항목을 아래와 같은 activity로 management 한다는 뜻이다.

 ![그림3]({{ site.baseurl }}/images/tech/gsma/3.png)

참 old-fashion 인 듯 하면서도 CC나 CMMI 등의 표준과도 상통하는 부분이 딱 보인다. 특히, 보안영역은 늘 괴리가 있는 게 (컨퍼런스나 해킹대회, 전문가집단 등) **바깥에서는 늘 기술만을 강조하고, 대기업은 뒤떨어졌다 뭐 어쩐다 얘기를 많이 하는데 사실 그게 그렇게 쉬운 일이 아니다. 개인적으로 그 이유가 안정성이나 운영 측면, 관리 측면이 함께 수반되어야 하기 때문** 이라고 생각하는데 해당 문서는 그러한 지점들을 잘 짚어내고 있는 느낌이다.    

전문은 아래 링크에서.
 * https://www.itgovernance.co.uk/files/Octave.pdf


### Secure Development Lifecycle

난 application security라든가 SDL이 표준으로 명시되어 있는지 이번에 처음 알았다..ㅋㅋ (~~별 게 다 표준으로 있구나..~~)

**ISO/IEC 27034** 에서 그러한 것들을 다루고 있는데 [링크](http://www.iso27001security.com/html/27034.html) , 유료다. 볼 수가 없네. 궁금하다. 이건 언젠가 볼 수 있을 날을 기약하며, 스킵.

참고로 Microsoft는 2013년에 이미 해당 표준을 준수 혹은 그 이상을 한다고 선언함. ~~대단하다..~~ [링크](
https://cloudblogs.microsoft.com/microsoftsecure/2013/05/14/microsoft-sdl-conforms-to-isoiec-27034-12011/
)

```
This morning Scott Charney announced in his
keynote at the Security Development Conference
that the Microsoft Security Development Lifecycle
(SDL) meets or exceeds the guidance published in
ISO/IEC 27034-1. The full text from this
announcement was as follows

...

ISO/IEC 27034 provides guidance for a risk based
and continuously improving software security
management system applied across the application
lifecycle. ISO/IEC 27034-1, Annex A contains a
case study illustrating how the SDL conforms to
the components and processes of ISO/IEC 27034.
```

결국 SDL이라는 것은 MS의 전유물이 아니라, 그들도 risk management의 일환으로 자체적인 내부 기준/프로세를 구축한 것이며 **중요한 것은 표준이 아니라 그걸 어떻게 개별 기업들이 자기네들 환경에 맞게 어떻게 소화해내느냐** 가 아닐까 싶다.  

## Supply chain security

얼마 전에 국내에도 크게 이슈가 되었던 공급망 공격, Supply chain security가 이 문서에도 명시가 되어 있다.

* 참고기사: [공급망 공격이란 무엇이며, 어떻게 대처해야 하는가](https://www.boannews.com/media/view.asp?idx=66253)

그냥 한 때의 유행이거니 했는데, 이 문서를 보면서 생각이 좀 바뀌었다. 구글 스콜라에 검색해보면, 이미 학계나 일부 업계에서는 2000년도 초반부터 SCS에 대한 고민이 많았던 것 같다.

그러면서 떠오른 게. [CC(common criteria)](https://www.commoncriteriaportal.org/files/ccfiles/CCPART3V3.1R4.pdf) 공부할 때 제일 이해가 안되었던 부분이 바로 delivery(ALC_DEL) 부분이었는데, 이게 그거구나 하는 생각이 들었다.. 아하.. ~~교수님 제가 공부를 하다 말았나봅니다..~~

아래 링크는 IBM에서 작성한 SCS 관련하여 살펴봐야 할 내용들을 정리한 문서인데. 다 읽어보진 않았지만 내용이 참 좋은 듯 하다. 좋다고 하는 이유는, 그게 회사 입장에서 benefit이 된다고 하는, **단순한 문제점 제기 -> 대안제시의 일반적인 문서가 아니라 실제 장점을 제시해주고 있기 때문** 이다. 글로벌 혹은 대규모 공급망을 가지고 있는 제조사 등은 고려해보면 참 좋을 내용이다.

* 문서링크 - [Investing in Supply Chain Security:
Collateral Benefits](http://www.husdal.com/wp-content/uploads/2009/02/investing-in-supply-chain-security.pdf)

## 현실세계와 동떨어진 부분들

### TCB / Trusted anchor

우선. 개념정리부터.

난 사실 Trust anchor하고 Root of trust하고 뭔 차이인지 모르겠다. 일단 찾아보니, Globalplatform에서는 Root of trust를 아래와 같이 정리하고 있다.

* 참고링크 - [Root of Trust Definitions and Requirements](https://globalplatform.org/wp-content/uploads/2018/05/GP_RoT_Definitions_and_Requirements_v1.0.1_PublicRelease_CC.pdf)

```
A computing engine, code, and possibly data,
all co-located on the same platform; provides
security services (as discussed in Chapter3).
No ancestor entity is able to provide a trustable
attestation (in Digest or other form) for the
initial code and data state of the Root of Trust
```

그리고 wiki에서 찾은 Trust anchor는 아래와 같다.

https://en.wikipedia.org/wiki/Trust_anchor
```
In cryptographic systems with hierarchical
structure, a trust anchor is an authoritative
entity for which trust is assumed and not derived
```

결국 같은 말 아닌가..ㅋㅋㅋ

같다 치고. GSMA 에서 가장 현실세계와 동떨어진 부분은 server-side든, endpoint든 TCB를 중요시 한다는 점이다. 실제로 서버에 HSM을 두거나, 아니면 기기에 TPM을 설치하는 케이스가 얼마나 될 것인가.. ~~없지~~

[예전에 블로그에도 TPM 관련한 글을 썼지만.](https://sungkyucho.github.io/sungkyu/tech/tpm-raspberrypi/)

TPM이나 TCB라고 다 좋은 것도 아니고. 특히 비용적인 측면에서 아직 이러한 것들은 오바다.

이 외에도 요구한 것들이 Rowhammer attack([참고링크 - 위키](https://en.wikipedia.org/wiki/Row_hammer))에 대응이 되는지 , Tamper resistant 한지를 묻는 내용들은 모두 통신사에서 이행하기는 어려운 부분으로 보인다.

그 외 signed VM images나 mutual authentication을 요구하는 것도 좀 심한 것 아닌가 싶다.

## 결론

보안의 비용이 자산의 가치를 넘게 되면, 그건 바보같은 일이 아닐까 싶다. 물론 그 "자산" 이라는 영역에 reputation 등 무형의 산정하기 어려운 요소들이 있기 때문에 그게 쉽지 않다는 것은 알고 있지만.

(왠만한 대기업들은 보안/해킹과 관련된 침해사고에 대해서 굉장히 예민하기 때문에)
