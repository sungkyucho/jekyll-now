---
layout: post
title: KRACK(Key reinstallation Attack)와 시사점
categories: Tech
---

얼마 전에 보안 관련 뉴스를 도배한 취약점이 하나 나왔다. KRACK이라고 명명된 취약점인데 [위키 항목](https://en.wikipedia.org/wiki/KRACK)도 생기고, 국내외 보안 관련 뉴스를 거의 독식했다.

KRACK은 Key re-installation attack이라고 해서, 802.11에서 제공하는 보안기능을 우회할 수 있는 것인데 사실 말만 들으면 전 세계에 있는 WIFI관련 기능을 탑재한 모든 기기(핸드폰, 노트북, M2M 및 IoT기기 등)에 영향을 미칠 수 있다. 사실 저렇게 크게 뉴스가 난 것도 이러한 영향도 때문일 것이다.

근데.. 늘 그렇듯이.. 취약점이 발견되었다고 하는 뉴스에 대해서는 좀 비판적으로 살펴봐야 한다. 이걸 사실 예전에 Blackhat에 참관해본 이후로 이러한 시각이 생겼는데.. 해커들의 잔치라고 하는 블랙햇에서 조차도 기업 홍보용 발표가 굉장히 눈에 띄었기 때문이다.

스타급 해커들의 발표 맨 앞과 뒤에는 늘 회사 홍보 마크가 붙었고, 그 어느 장소를 가도 스폰서의 홍보, private session 에서조차도 기업홍보가 눈에 띄었고, 그렇게 큰 영향력을 가진 것처럼 온갖 뉴스지면을 장식하던 [Android stagefright](https://en.wikipedia.org/wiki/Stagefright_(bug)) 취약점도 이러한 나의 비판적인 시각에 일조했던 거 같다.

이번에는 사실 기업은 아니고 뭔가 벨기에 쪽 학교에 다니는 학생으로 보이긴 했지만, 저렇게 뻥 터뜨리는 것은 늘 좀 비판적으로 살펴볼 필요가 있다는 마음에 논문을 찾아 읽어보기 시작했다.

## 1. 관련 링크
+ https://www.krackattacks.com/
+ https://papers.mathyvanhoef.com/ccs2017.pdf
+ 나머지는 그냥 뉴스 검색 ㄱㄱ

## 2. 문제점 및 현황
+ 핵심은 다양한 조건을 가진 상태로 Key re-installation attack(이하 KRACK)이 가능하다는 것이며, 논문 상으로 취약점이 굉장히 다양하게 기술되어 있는 듯 하지만 주된 핵심은 "암호화된 메시지를 받느냐 안받느냐"등의 조건(condition)을 기반으로 KRACK이 가능한지를 검토한 결과를 발표한 것이다.
+ 논문 내부에서도 MiTM 특성 상 실제로 수행되기 어려운 공격이라는 자평을 내보이는데, 이유는 아래와 같음.
  + Windows와 iOS에서는 KRACK 취약점이 발생하기 위한 조건이 성립되지 않음 -> Retransmission msg #3를 받아들이지 않기 때문
  + MiTM이 잘 동작하려면 MAC addr.이 동일한 rogue AP를 전제해야 하는데 이게 현실적으로는 굉장히 큰 장애요소
  + 구현 특성이 조금 있음, 즉 각 vendor별로 spec.에 따른 구현방식이 차이가 조금 있음
+ 외부에서의 평도 **기술적으로는 훌륭하나, 난이도가 높고 재현가능성은 낮다** 는 평이 보이며, 실 환경에서의 공격가능성은 낮은 것으로 보인다는 게 일단 overview 결과이다.

## 3. Background & 취약점 내용

그럼 조금 더 자세히 논문을 읽어보자.

먼저 개인적으로 원 논문을 굳이 매번 찾아서 읽는 이유인데..

+ 우선 취약점 내용 자체보다도 주변적인 background가 정말 풍부하게 있기 때문이다.
+ 두 번째로 **뉴스나 블로그 보고 그 취약점 안다고 하면 솔직히 개인적으로 쪽팔리기 때문** 이다. 보안업계에서 입보안, 입보안러라는 비아냥 섞인 용어가 있다. 입으로만 떠드는.. ~~나도 움찔;;;~~ 최소한 내가 입보안이긴 하지만 ~~인정한다..~~ 최소한 뭔가 알고 떠들고 싶다.
+ 마지막으로 논문의 related work 부분에서 제시하는 다양한 지식들은 그 어떤 블로그나 ppt 자료보다도 훌륭한 지식으로 활용될 수 있다.

### 1. 802.11i (무선랜 보안표준)

+ TKIP (Deprecated, 보안이슈로 인해 현재 폐기된 상태)을 사용하며 RC4-128bit와 48bit 의 nonce를 사용한다.
+ CCMP, AES-CCM 모드로 동작하며 WPA에서 사용하는 기본적인 암호 스킴인데
+ 2012년에 상기 Protocol 둘 다 마음에 안들어서 AES-GCM(GCM은 G-Hash기반으로 메시지 인증이 가능한 mode of operation)이 spec.에 추가되었다.
+ 기타 이 부분은 워낙 검색만 해봐도 잘 나오기 때문에 패쓰.

### 2. 키 계층

+ WPA의 경우 PMK(Pre-shared Master Key), ANonce(AP의 Nonce), SNonce(Supplicant 즉 client의 nonce), MACaddr 을 기반으로 PTK가 만들어지고
+ PTK(Pairwise Transition Key)는 3가지 키로 다시 나뉘게 되는데..
+ KCK(Key Confirmation Key), KEK(Key Encryption Key), TK(Temporal Key)로 나뉘게 됨
+ 조금 복잡해보이지만, 아래 그림에서 양 쪽 첫 번째 두 번째 블록을 보면 PTK가 각 비트별로 어떻게 각 키들로 구성되는지, MSK가 어떻게 PMK로 나뉘는지 알 수 있음. ~~즉, 그냥 길게 만들고 나서 잘라낸(concatenation)거..~~

![fig1]({{ site.baseurl }}/images/tech/krack/1.png)

### 3. 4-way handshake

+ 저러한 키들을 나눠갖기 위한 negotiation 과정이 바로 4-way handshake이며, 해당 그림은 아래와 같다.

![fig2]({{ site.baseurl }}/images/tech/krack/2.png)

+ 취약점은 이 과정에서 발생하며, **1번 3번 메시지가 누락이 되면 (각각) 2번 4번 메시지를 다시 재전송** 해야 하는데(위에서 Windows와 애플은 이걸 안한다는 뜻.. ~~오호, 이 취약점을 알고 계셨던건가요~~), 이 때 **제일 중요한 nonce가 reset이 된다는 것이 KRACK의 핵심 취약점!!**
+ **Nonce가 reset 된다는 의미는, nonce가 replay attack을 방지하기 위한 목적이라는 점** 을 상기하면 된다. 즉, 예전에 썼던 걸 다시 그대로 쓰려고 하면 nonce(의 freshness)가 막아줘야 하는데 이게 reset이 되버리면 예전에 썼던 것들이 그대로 먹힌다는 점이다.


## 4. 영향도 및 1차 평가

### 취약점으로 인한 영향
+ 가장 중요한 것은 802.11에서 nonce를 재사용하는 것에 대한 영향도이며 즉, 이걸 재사용했을 때 replay attack은 기본적으로 가능하고 복호화까지 가능하다고 기술되어 있다. **(이 부분은 하기 별도 기술)**
+ 문제는, TKIP, CCMP, GCMP 모두 stream cipher를 사용한다고 되어 있으며(AES로 Stream cipher를 쓴다는 게 잘 이해가 안되지만, 일단 packet이나 frame을 각각 nonce+Key로 암호화한다고 이해하자) 이 때 stream cipher의 key freshness는 nonce에 의존하게 됨(per-packet encryption)
+ 즉, nonce가 reset 되어 버리면 stream cipher의 안전도에 영향을 미치며, 복호화(또는 해독)이 가능하다고 되어 있는데 여기서 CCMP는 많이 어렵고(이론상 가능하다 정도로 치부), GCMP에서 영향이 가장 크다고 함
+ Decryption과 관련된 부분은, 결론적으로 실제 복호화를 하기 위해서는 꽤 많은 컴퓨팅 파워를 써야 하는 것 아닌가 싶은데.. **가장 중요한 복호화 관련된 설명이 굉장히 부실하다**

```
By forcing nonce reuse in this manner, the
data-confidentiality protocol can be attacked,
e.g., packets can be replayed, decrypted, and/or
forged. The same technique is used to attack the
group key, PeerKey, and fast BSS transition
handshake.
```

+ 다만, 복호화가 설명대로 된다면.. TCP/HTTP injection 등 상상할 수 있는 모든 공격이 가능하기 때문에 impact는 크다고 볼 수 있기 때문에 KRACK에 대해서 많은 관심이 집중되고 있는 것이다.

### 취약점에 대한 중간 평가
+ KRACK은 reasonable한 공격으로 보이지만 실제로 공격하기에도 어렵고, 공격을 해서 얻는 것도 많지 않을 것으로 보임
+ 마지막으로 WPA/WPA2의 경우 수학적으로 증명된 안전성을 가지지만 (provable secure) 현실 세계에서 깨졌다는 점에서 많은 사람들이 충격을 받았으며 이 점이 본 논문의 가장 가치있는 그리고 기여한 부분으로 저는 평가됨 (실제 공격의 난이도나 재현가능성은 낮음에도..)
+ 실제로 이번 논문도 여러 차례에 걸쳐서 수학적으로 증명된 시스템의 특성을 건드리지 않았음에도 취약점을 밝혀냈다고 공치사를 함. (사실 논문은 당연히 그래야 하지만..ㅋ)

```
In spite of its history and security proofs
though, we show that the 4-way handshake is
vulnerable to key reinstallation attacks.
```

```
Interestingly, our attacks do not violate the
security properties proven in formal analysis of
the 4-way and group key handshake.
```

```
Second, it is not because a protocol has been
formally proven secure, that implementations of
it are also secure.
```

~~고마해라. 니 잘 했다 그래~~

## 5. Decryption에 대해 조금 더 살펴보기

일단 지금까지 읽었을 때, WPA/WPA2를 깼다는 것은 일반적으로 어떤어떤 취약점을 통해 해독 또는 복호화를 할 수 있다는 것으로 받아들이는 것이 상식적일 듯 하다.

근데 아무리 봐도, 이러한 내용에 대한 상세 기술이 없다. 즉, 4-way handshake를 통해 PMK와 함께 실제 데이터를 암호화 하는 TK가 생성될 때 replay attack을 방지하기 위한 nonce가 reset된다는 취약점은 이해가 되지만 그것이 실제로 decryption과 연결되는 지점을 도대체가 찾을 수가 없었다.

### Android zero-encryption vulnerability
이게 아마 복호화와 가장 밀접한 취약점일 것 같으나, 결론적으로 해당 취약점은 KRACK은 아니다.
+ 6.3절에서의 zero-encryption은 android 6.0과 wear 2.0 및 wpa_supplicant 2.4/2.5에 있는 key-reinstallation attack과는 별개의 버그로 분류한다
+ 이게 802.11의 스펙 상 TK를 메모리에서 날려버리라고 되어 있다보니 꼬여서 생긴 버그고, 이게 잘 수정이 안되어서 발생하는 것으로 기술되어 있으며
+ 따라서 역시 핵심은 **key-reinstallation attack 그 자체이기 때문에 zero encryption issue는 KRACK과는 분리하여 생각해야** 한다.

### 2.4절 및 6.1절에서의 설명
+ 논문 구조를 잘 살펴보면, 2.4에서 설명하는 TKIP/CCMP/GCMP에서의 키 생성 과정과 역할을 설명한 글과 6.1에서 둘 다 키 생성과정에서 nonce 의 역할을 설명하고(2.4) 그 영향을 설명(6.1)하고 있는 쌍을 이루고 있다.  
+ 예를 들어, TKIP은 PTK -> TK -> MIC key1(64bit) + MIC key2(64bit)로 나누어지는데 여기서 MIC key가 이런저런 이유로 복원이 된다고 한다
+ 근데 decryption은 아니고 integrity관련 키이기 때문에 forge가 가능하다고 6.1에 추가설명이 되어 있으며
+ CCMP는 IV, nonce, TK 등이 핵심 key material들인데 이 중에 msg#3 때문에 nonce가 reset 되고 TK가 그대로 사용될 수 있다
+ 마지막으로 GCMP는 CCMP문제 + MIC 복원까지 가지고 있어 재앙이다는 식으로 6.1절에 설명되어 있는데...
+ CCMP/GCMP의 IV 나 TKIP의 per-packet key를 알아야 최종적으로 decryption이 가능할텐데 그런 얘기는 눈을 씻고 찾아봐도 없으며, 따라서 실제 직접적인 decryption이 가능한 것보다는 "가능성이 높아진다" 정도로만 이해하는 것이 맞다.. **즉, 실제 알려진 취약점의 영향도보다는 훨씬 낮은 수준의 위협으로 보는 것이 바람직하다..**

## 추가사항
+ 일반적인 회사의 무선랜 구성 상, 802.1x의 보안채널을 두는데 KRACK이 그 단계 이후에 진행이 되기 때문에 802.1x 도 우회할 수 있을 것이라는 결론과 [외부 링크 글](https://security.stackexchange.com/questions/171451/is-wpa2-enterprise-affected-by-the-krack-attack)이 있다
+ 따라서 회사 보안담당자들도 좀 잘 알아볼 필요가 있는(다만, 실제 공격보다 외부 감사 등에 대비한ㅋㅋㅋ) 이슈인 것은 맞다.
+ 참고로, 해당 건에 대해서 고려대학교 김승주 교수님도 아래와 같이 간단한 의견을 제시했다

```
정확히는 Known Plaintext Attack으로 봐야할듯요.
그래서 일부 비평가들은 KRACK attack이 Heartbleed만큼
sexy하지는 않다고 말하는 것이기도 하고.. ^^

암호해독 관점에서 볼때 Known Plaintext Attack(기지평문
공격)이 그렇게 황당한 것은 아닙니다. KPA란 과거에 통신했던
암호문(C1)에 대한 평문(P1)을 알고 있다는 전제하에서 대상암호문
(target ciphertext) C2를 해독하겠다는 것인데요..

과거 통신했던 암호문이라도 비밀보존기간이 지나면 공개되기 때문에
얼마든지 평문을 알아낼수 있구요 (예를 들면 대통령이 출국할 때까지
그 동선에 대한 정보가 비밀이지만 일단 출국하고 나면 9시 뉴스에
나오듯), 그게 아니라도 짧은 평문은 guessing할수 있으니까요.

```

~~굉장히 좋은 지적이긴 하지만, 그래도 practical 보다는 theoretical 한 성격에 가깝다는 얘기로 들리긴 한다~~

## 6. 결론

KRACK 자체를 폄하하려는 생각도 없고, 또 충분히 좋은 취약점이라고 생각한다. 특히 나는 아무래도 기업에서 일을 하는 입장이다보니, **이렇게 쉽게 찾을 수 있는 (당연스레 사용되고 앴는) 기기에서의 취약점은 매우 가치있는 취약점** 이라고 생각한다.

다만, 분명히 해당 취약점에 대해 공론화 하고, 기사화 될 때에는 그 영향도가 충분히 설명되어야 한다고 생각한다. **FUD - Fear, Uncertainty, Doubt -** 는 보안 그 자체에 있어 매우 좋지 않은 요소로 작용할 수 있기 때문이다.

사람들에게 공포심을 심어주는 것은 주목받을 수 있는 계기이기도 하지만, 그것에 대해서도 충분히 책임질 수 있는 기사를 써줘야 하는 것 아닐까 한다.

마지막으로 보안 담당자들도 취약점이 나왔다고 해서 임원이나 팀장에게 달려가 "이런 거 있어요. 문제가 터졌어요!"하는 자세보다는 **충분히 기술적인 검토를 진행한 이후에 ~~약간의 주관이 섞인~~ 보고를 올리는 게 맞지 않을까** 싶다.

**물론 모른척 하거나, "나만 알꺼야!하는 이기적인 자세가 더 나쁘긴 하다**

끝~~~~
