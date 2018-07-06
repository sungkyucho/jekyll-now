---
layout: post
title: Meltdown과 Spectre를 보며 느낀 Google Project Zero
categories: Tech
---

전 세계를 떠들썩하게 만든 취약점이 하나 나왔다.

Intel CPU에서 큰 취약점 하나(Meltdown)와, AMD/ARM/Intel (Spectre)에서 발견된 취약점으로, 인텔의 영향력 - [전 세계 PC의 10대 중 7대, 클라우드 점유율 99.8%](http://thegear.co.kr/15602) - 을 생각하면 취약점의 난이도나 재현율 등을 떠나서 큰 충격일 수 밖에 없다.

다만, 한 가지 눈에 띄는 것은 이 것을 공개한 곳은 다름 아닌 ~~그 이름도 찬란하다~~ Google Project Zero 팀이었다.

* https://meltdownattack.com/
* [인텔도 들었다놨다 한 구글… CPU 결함 찾아내고 해결책도 제시](http://news.hankyung.com/article/2018010502931)
* [인텔, 해킹 취약 '쉬쉬'…도덕성 논란](http://news.mk.co.kr/newsRead.php?no=9279&year=2018)

원래 1월 9일에 취약점을 공개하기로 하였는데, 미리 소문 및 내용을 알고 있던 사람들 간의 이야기를 캐치한 더 버지에서 이를 의심하는 기사를 냈고, 구글이 1월 4일에 문제점을 밝히면서 파장은 일파만파가 되었다. [원문기사](https://www.reuters.com/article/us-cyber-intel/security-flaws-put-virtually-all-phones-computers-at-risk-idUSKBN1ES1BO)

```
Speaking on CNBC, Intel’s Krzanich said Google
researchers told Intel of the flaws “a while ago”
and that Intel had been testing fixes that device
makers who use its chips will push out next week.
Before the problems became public, Google on its
blog said Intel and others planned to disclose the
issues on Jan. 9. Google said it informed the
affected companies about the “Spectre” flaw on
June 1, 2017 and reported the “Meltdown” flaw
after the first flaw but before July 28, 2017.
```

[AMD engineer가 올린 CPU버그 관련 힌트 글 - 출처는 Matt Oh님의 FB](https://lkml.org/lkml/2017/12/27/2)

1월 9일 발표 예정이었던 패치나 공개자료 등이, 모두 구글의 앞 선 폭로로 인해 혼선을 겪었고 그로 인해 나처럼 일개미들은 정확한 정보를 얻을 수가 없어 굉장히 힘들었다. ~~위에서는 이게 뭐야!! 하고, 뭔지는 알겠는데 구체적인 영향대상은 파악이 안되고..~~

기본적으로, responsible disclosure를 하기로 했다면 취약점 범위나 영향도, 정확한 affected device 들이 그 때 공개될텐데 이건 뭐 미리 폭탄이 터진 것이니..

일단 취약점에 대해서 살펴보자.

## Meltdown – CVE-2017-5754

#### 주요 취약점 설명
 + CPU의 Optimization을 위해 적용된 기법 가운데, out-of-order execution(이하 OOE) 기법이 있으며
 + 명칭에서와 같이 순서대로 실행을 하는 것이 아니라 코드의 결과를 예측하여 미리 실행을 하는 방법임
 + Intel CPU의 TSX(일종의 exception handler) 관련 버그와 **기존의 Cache attack 기법을 혼합하여 최종적으로 OOE에 의해 미리 로드된 cache 내용을 살펴볼 수 있는 방법** 으로
 + 최종적으로 시스템 내의 모든 메모리 영역 (Kernel + User space)을 살펴볼 수 있는 취약점임

#### 취약점 시나리오

![논문에서 제시한 예시코드]({{ site.baseurl }}/images/tech/melt/1.png)

 + 상기와 같은 어셈블리 코드가 있다고 가정할 때, 원칙대로면 ```rcx``` 로 커널 메모리가 로드되면 안됨(권한)
 + 하지만, OOE에 의해서 LINE 4(커널메모리를 로드해서 al register에 담음)가 실행될 때 로드가 된 이후 권한체크가 됨
 + 따라서, **어셈블리코드를 일부 조작하여 권한체크 이전의 캐시값을 미리 빼오는 것이 핵심 시나리오** 이며
 + 이 때 권한체크로 인한 exception handling과 Cache에서 데이터를 빼오는 방법은 기존의 취약점으로 진행됨
 + 마지막으로 가상 메모리(Virtual address) 내에 커널 메모리와 사용자 메모리가 특정 주소를 기점으로 함께 적재되어 모든 데이터에 접근이 가능함

### Spectre – CVE-2017-5753, CVE-2017-5715

#### CVE-2017-5753 : Bounds check bypass
 + Branch prediction 이라고 하는 주요 CPU에서 활용하는 최적화 기법 상에서의 문제점이며
 + 모든 high-level 언어가 컴파일 후 기계어 처리가 되면 생기는 주요 OP-code 가운데 branch 계열 (```if```/```else```/```switch```/```return``` 등 분기문) 에서의 문제점 (Meltdown과 유사한 범주의 문제점임)
 + **취약한 코드가 있는 상태에서만 발생가능한 한계점이 존재** 하여, 실질적인 공격패턴으로 이어지기 어려움
 + 아래의 예시가 있으며, ```index2```를 비롯한 변수의 값들이 동적으로 정해질 때, ```arr1 -> data[untrusted_offset_from_caller]```의 값을 공격자가 미리 예측할 수 있다는 내용임

![논문에서 제시한 예시코드 -2 ]({{ site.baseurl }}/images/tech/melt/2.png)

#### CVE-2017-5715 : Branch Target injection
 + Branch prediction 으로 인해 발생하는 취약점으로써, “Bounds check bypass”와 근본적으로 같은 종류의 취약점이나
 + 결과적으로 예측된 값에 injection 을 통해 분기 지점을 망쳐놓을 수 있는(예를 들어 악성코드가 있는 곳으로 프로그램을 분기 시킬 수 있는) 취약점임

 좀 복잡하긴 하지만, 아래 김승주 교수님이 올리신 글을 보면 훨씬 간단하게 살펴볼 수 있음.

 ```
 1. 해커 A씨는 평상시 흠모하던 B씨의 음식 취향(개인정보)을
 알고 싶음.

2. 해커 A씨는 B씨의 단골 식당을 알아낸후, 이 식당의 주방장(CPU)
에게 "B씨가 지금 이곳으로 오고 있다"는 거짓 소문을 흘림
(false prediction을 유도).

3. 단골 손님이 오고 있다고 믿은 주방장(CPU)은 신속한 서비스를
위해 줄서있는 손님들 몰래 틈틈히 B씨가 항상 주문하던 음식을
미리(out-of-order execution) 만들어 놓음.

4. 그러나 B씨는 올리가 없고, 후에 이를 알게된 주방장(CPU)은
미리 만들어 놓은 음식을 버려야함에도 불구하고 아까운 나머지
냉장고(cache)에 넣어둠.

5. 해커 A씨는 단골 식당에 전화를 걸어 메뉴에 있는 음식을
차례로 주문해 봄. 이때 유독 빨리 배달되는 음식이 있다면 그것이
미리 만들어 놓은 음식일 확률이 높으며 (side-channel attack
의 일종인 cache timing attack), 바로 그 음식이 B씨의 음식
취향(개인정보)임! 물론 중간에 차가 막힌다던지하여 시간의 오차가
생길수 있으나 이러한 noise를 줄일수 있는 방법은 상당히 많이
나와 있음.
 ```

즉, 미리미리 빨리빨리 해야 하는 CPU 특성 상 **성능만 봤을 때는 올바르게 고려된** 설계 방식이 보안적으로 봤을 때에는 큰 문제를 일으킬 수 있다는 대표적인 사례가 되지 않을까 싶다.

이러한 문제로 인해, 정식패치를 하게 되면 성능이 매우 느려질 수 있다는 관측도 어렵지 않게 찾아볼 수 있다. 실제로 회사에서 해당 패치를 하지 못하는 가장 큰 이유가, 성능이 얼마나 떨어질지 도통 감을 잡을 수 없어서다.. ~~해봐야 알지..~~

### 그리고 Google Project Zero

~~사실 구글 프로젝트 제로에 대해서 좀 적어보려고 했는데 오래되서 이제 뭘 쓰려고 했는지 기억이 가물가물하다;;~~

구글 프로젝트 제로는 2014년에 정식 런칭된 구글의 취약점 분석 전담팀이다. [위키.](https://en.wikipedia.org/wiki/Project_Zero)

대한민국이 낳은 천재해커 이정훈씨도 가 있는 것으로 알고 있고. 어쨌든 취약점 분석과 관련된 지구최강 드림팀은 자명해보인다.

다만 일반적인 취약점 분석팀은 자사 서비스나 상품/제품에 대한 취약점 분석을 주로 하는데 반해, Google Project Zero는 안전한 인터넷을 만들기 위해 다른 제품/서비스를 까는(?) 담당을 한다.

(참고로 구글 자사 제품에 대한 취약점 분석은 [VRP](https://www.google.com/about/appsecurity/reward-program/) 프로그램을 이끄는 Eduardo Vela가 스위스에서 담당하고 있는 것으로 알고 있음. 출처는 이승진 사장님)

최신 현황은 아니지만, 대충 통계를 뽑아보면 아래와 같다.

![누굴 깠나]({{ site.baseurl }}/images/tech/gpz/statistics.png)  

[출처](https://www.google.com/about/appsecurity/research/). 참고로 출처에서 보듯이 정식 project zero 출시 이전 이후 데이터가 좀 섞여 있긴 하다.

꼭 누군가를 타게팅하는 것은 아니고, 정말로 대표적인, 전 세계에 널리 퍼진 제품에 대한 취약점을 찾는 것은 확실해보인다. 그만큼 퍼져 있는 제품과 플랫폼이라면, 보안도 그만큼 신경 썼을텐데 그걸 다시 뚫고 들어가는 모습은 가히 경의롭다.

하지만. BUT.
그냥 들었던 생각이 뭐냐면.

 - 구글의 정책 상, 90일의 responsible disclosure 기간을 가진다고 한다. 과연 저 제품들이 90일 안에 패치가 되고, 업데이트가 될 수 있는 애들일까.
 - 취약점을 전부 공개하는 것이 원칙일까. 외부에 공개하지 않고 내부적으로 다른 목적으로 사용하는 애들은 없을까.
 - 과연 Project Zero 팀원들이 생각하는 본인들의 명예로운 일들이 Google이라는 조직의 목표와 일치하는 걸까.

등등 궁금한 것들이 꽤 많지만. 무엇보다도 **Google이 Google Project Zero에 투자하는 이유가 뭘까?** 라는 생각이다. 진정 순수하게 안전한 인터넷을 위하여? 순다 피차이가? (참고로 순다피차이는 구글이 자신들의 이익을 위한 관점으로 전환하는데 큰 역할을 한 사람이다)

솔직히 말하자면, Project Zero를 양날의 검으로 너무 잘 활용하고 있는 것 아닐까 싶다. 즉, 자신들의 명분인 안전한 인터넷을 만들기 위한 좋은 성과(중요 취약점)도 낼 뿐만 아니라, 그 결과를 끊임없이 발표함으로 인해서 취약점이 확인된 제품/서비스에 큰 타격을 주기도 한다는 점이다.

실제로 meltdown 으로 인해 인텔은 주가에 영향을 주기도 했고 ([출처](http://www.nocutnews.co.kr/news/4902631)), 인텔칩을 바꿔야 하는지를 검토했던 회사도 있었던 것으로 알고 있다.

그리고 인텔과 구글이 사전에 responsible disclosure를 위해 협의했던 날짜보다 더 빨리 구글이 일방적으로 공개하기도 했다. ([출처](http://www.boannews.com/media/view.asp?idx=67068)) ~~이게 얼마나 vendor를 엿먹이는 짓인지 보안 쪽에서 일해본 사람은 알 것이다~~

그런 이유로, meltdown이 공개된 시점에서 intel 은 자사 홈페이지에 패치를 올려놓지도 못했었다는 거... 아무리 찾아도 없더라...

그리고 이런 기사. 구글 클라우드는 문제 없다.. [구글, 인텔CPU 보안패치 성능저하 상쇄했다.](http://www.zdnet.co.kr/news/news_view.asp?artice_id=20180115153906)

Don't be evil. 과연 Project Zero 팀에는 적용할 수 있겠지만, 그의 주인인 Google에 이 문구를 적용할 수 있을지 한번 더 궁금해진다.
