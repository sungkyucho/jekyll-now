---
layout: post
title: Mobile security에 대한 NIST requirements - 800-163 Rev1
categories: Tech
---

NIST에서 좀 특이한 문서가 하나 나왔다. 사실 특이할 건 없는데, "모바일 앱에 대해서도 이런 문서가 나오나"하는 느낌에 좀 참신했다.

Draft version 이지만 800-163 Rev1, 모바일 보안과 관련된 요구사항이 그것이며, [링크](https://csrc.nist.gov/publications/detail/sp/800-163/rev-1/draft) 에서 오른쪽 버튼으로 full text를 받을 수 있다.

가장 먼저 눈에 띄는 것은 TOE(Target of Evaluation), TSF(TOE Security Functionality) 등의 용어로 사용되는 것 보면, 이러한 요구사항을 그대로 CC 인증 쪽으로 옮길 모양이라는 점이다.

이력을 살펴보니 2015년 1월에 이미 초안이 나왔던 것 같다. [초안 링크](https://www.nist.gov/publications/vetting-security-mobile-applications)


### 800-163 Rev1 요약

간단히 요약하자면.
 * NIST 에서 정의하는 모바일 앱의 보안 기능요구사항은 아래 테이블로 정리가 되고

![그림1]({{ site.baseurl }}/images/tech/msec/1.png)

 * 이러한 요구사항을 Vetting 하기 위해서 3가지 등급을 두는데
   + Standard Security (Level 1)
   + Defines in Depth (Level 2)
   + Resilience against Reverse Engineering and Threats (Level 3)

* 각각의 레벨들은 다시 아래와 같은 카테고리로 나누어 검토를 한다고 한다.
   + Architecture, Design and Threat modeling requirements
   + Data storage and Privacy Requirements
   + Cryptography Requirements
   + Authentication and Session management requirements
   + Network communications requirements
   + Platform integration requirements
   + Code quality and build-setting requirements
Resilience Requirements

즉, 레벨에 따라서 상기 카테고리를 기반으로 검토를 하되 그 정도는 레벨이 높을수록 깐깐하게 본다고 생각하면 될 듯 하다.

한 가지 좀 특이한 점이 있다면, Vetting process (우리말로 하면 아마 검증? 보안진단 프로세스 정도?) 를 요구조건으로 두었다는 점인데 FIPS 문서 중에 이렇게 프로세스를 요구사항으로 두는 케이스가 있었던가? (사실 이런 종류 문서를 많이 읽어본 편은 아니라 ㅋㅋㅋ)

![그림2]({{ site.baseurl }}/images/tech/msec/2.png)

너무나 당연한 절차처럼 보이지만, 뭔가 보안검사 프로세스를 담당하는 사람들은 꼭 한번 읽어보면 좋을 듯 하다. 특히, **policy 부분을 security testing 영역과 분리하여 별도로 뒷단에서 검사한다는 것은 빼먹기 쉬운 부분** 이기 때문이다. (물론 이러한 policy를 명문화 할 정도로 구체적인 방침을 가지고 있다는 전제 하에..ㅋ)

눈에 띄는 것은 20 페이지에 나와 있는 아래 문구.
```
Security analysis is primarily a human-driven process.
```
보안 분석은 지식집약적 분야가 아니라 노동집약적인 ~~사람 수를 늘려라!!~~ 분야라는 것이다.. (라고 이해하면 안된다..**사람이 제일 중요하다는 뜻이다..**)

사실 본 문서가 눈에 띄는 특이점은 없지만, 2가지 인상적인 부분 때문에 블로그에 정리하는데, **첫 번째는 위에 프로세스를 중시한다는 점** 이고 두 번째는 **mobile security에서의 threat model과 Android/iOS에서의 취약점 유형을 categorization 했다** 는 점이다.이건 좀 써먹을만 할 듯.

![그림3]({{ site.baseurl }}/images/tech/msec/3.png)
![그림4]({{ site.baseurl }}/images/tech/msec/4.png)

### 시사점

#### PROS

특별히 이번에 시사점은 없지만, 개인적으로 mobile security 쪽을 잠깐 했던 사람으로써 이렇게 구체적인 요구사항이 나온다는 것이 시간이 좀 걸리긴 했지만 상당히 반가운 일이긴 하다.

아마도 대부분의 회사들이 모바일 앱 진단과 관련된 업무가 어느 정도 있을텐데 (외주를 주더라도) 이걸 참고하면 훨씬 더 좋은 퀄리티가 나올 수 있지 않을까 싶다.

실제로 위에 언급했듯이 Threat model과 vulnerabilities classification 들은 보완할 점이 있다 하더라도 꽤 잘 정리된 것 같다. 이 부분은 모바일 앱 점검, assessment process를 운영하고 있거나 운영할 예정의 담당자라면 잘 살펴보아야 할 대목이라고 생각한다.

#### CONS
물론 늘 그렇듯이. 모든 표준문서와 요구사항을 정리한 formal한 문서에는 아래와 같은 뉘앙스의 글이 언제나 담겨있다. ~~보안엔 끝이 없어~ 난 이것만 적었지만, 이게 전부는 아니야. 내 책임 아니야~~

```
As with any software assurance process,
there is no guarantee that even the most thorough
vetting process will uncover all potential
vulnerabilities or malicious behavior. Organizations should be made aware that although
app security assessments should generally improve
the security posture of the organization, the
degree to which they do so may not be easily or
immediately ascertained. Organizations should
also be made aware of what the vetting process
does and does not provide in terms of security.
```

특히. 최근처럼 Rooting조차 어렵고, rooting을 한다 하더라도 SELinux와 SEAndroid때문에 shell 조차도 할 수 있는 게 별로 없는 상태에서 **Mobile app security에 대한 needs가 더 강해질 것 같지는 않다.**

때문에 다소 늦은 감이 있긴 하지만.해당 문서가 나왔으니 아마도 DoD나 각국 정부에 MDM 등의 앱을 판매할 때 요런 요구사항을 기반으로 개발이 되어야 할 것 같다는..(정식 PP가 나온다면 더더욱).. 그래서 보안하는 사람들은 더 빡쎄질 거 같다는..결론. 끝~~~
