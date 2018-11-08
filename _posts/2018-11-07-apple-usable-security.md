---
layout: post
title: 아이폰 보안기능을 보며 느낀 점
categories: Tech
---

간만에 아이폰으로 넘어왔다. 갤럭시 시리즈를 주욱 쓰다가 회사 옮기면서 아이폰 6S+로 바꿨다가 다시 갤럭시 7, 노트 8으로 갔다가 돌아왔으니 근 3년 만인가.

애플 제품이 주는 특유의 그 sexy 함은 여전하지만, ~~뒷면을 불빛 아래 두면;; 어우~~ 일주일 간 사용하는 중에 감동적인 부분이 있어서 이렇게 내친 김에 정리한다.

![그림1]({{ site.baseurl }}/images/tech/ios/11.png)
![그림3]({{ site.baseurl }}/images/tech/ios/22.png)

위 그림에서와 같이
 * 첫 번째 사진에서는 자물쇠가 있고, 푸시 내용이 안 보이고
 * 두 번째 사진에서는 자물쇠가 풀렸고, 푸시 내용이 보인다

아이폰 X 이후로 적용된 얼굴인식 기능이 유일한 생체인증 기능으로 통합되었고, 자물쇠는 바로 그 인증을 통과했는지 안했는지 여부이다. 즉, 정당한 사용자를 인식할 때에는 푸시 내용이 보이고 그렇지 않을 때에는 푸시 내용을 가리며 **참고로 한번 얼굴인식이 성공했다 하더라도 잠시 화면을 껐다가 다시 켜면 보였던 푸시 내용이 안보인다.**

그에 반해 같은 기능을 안드로이드는 아래와 같이 적용하고 있다.
~~이 메뉴가 어디 있는지 아는 사람도 별로 없을꺼다..~~

![그림3]({{ site.baseurl }}/images/tech/ios/33.png)

대부분의 사람은 그게 뭐 어떠냐라고 이야기하겠지만, 나는 대단히 감동적이었다. Security & Privacy를 저렇게 사용자 관점에서 서비스 레벨로 올려놓은 케이스가 얼마나 될 것인가? **모든 것을 사용자의 책임으로 넘기는 요즘 보안 기능의 추세** 로 볼 때 분명히 애플은 보안에 대해서 어떻게 접근해야 하는지 알고 접근하는 듯한 느낌을 받았기 때문이다.

너무 극단적인 예일 수는 있지만, 거의 모든 보안솔루션+보안기능들은 "사용자/관리자의 책임"으로 마무리를 지으려고 한다. 브로셔나 소개 페이지에서는 모든 게 완벽하게 방어하고 막는 것처럼 보이지만, 실제로 최종 결정이나 판단은 모두 사용자 혹은 관리자에게 떠넘기고 있다.

Mark dowd의 The are of security assessment 에서 분류하는 취약점 분류 상, operational vulnerability는 모두 저 지점에서 발생한다. 그리고 그러한 취약점은 특성 상 매우 간단하면서도 impact는 매우 큰 취약점들이다.

나는 이런 게 극도로 싫다. **보안전문가들이 해줘야 하는 일은 현재 가진 (보안)문제점에 대해서 전체를 바라보면서 최종적으로 판단을 하고, 결정을 해주는 일** 이어야 한다고 믿는다. "모든 침해위협을 다 탐지한다!"는 게 무슨 소용인가? 사람이 하루에 백만개씩 오탐을 걸러내야 한다면.. 그렇다면 거기서 무엇을 어떻게 판단하고 결정해야만 하는 것인가? 이것이 보안 담당자들이 해줘야 하는 일이라고 생각한다.

그리고 이러한 설계 레벨에서의 선택과 결정이 UX를 포함한 서비스(Not 시스템)의 보안에 한 축이 되어야 한다고 생각한다.

## iMessage

### iMessage의 보안

imessage는 문자앱인데. 처음에는 일반적인 간단한 기능이라고 생각했다. 하지만, 결국 얘도 아이클라우드와 밀접하게 연관이 되어 있고 두 번째로 막강한 보안기능을 가지고 있다.

[관련 뉴스 - 미국 마약단속국 애플 아이메시지 해킹 불가능](http://www.dt.co.kr/contents.html?article_no=2013041102019960718001)

일반적인 TLS와는 달리 종단간 암호화를 지원하는데, **일반적으로 [텔레그램](https://core.telegram.org/api/end-to-end)이나 카카오톡에서 제공하는 방법은 주로 DH(Diffie Hellman) 기반의 프로토콜을 응용하는 반면에, iMessage는 전화번호 등의 apple ID를 기반으로 PKI와 동일한 수준의 인프라를 구축하여 하드웨어 기반의 root of trust 에서 키를 관리** 하도록 되어 있다. ~~하드웨어를 가지고 있다는 건 이런 게 강점이다;;~~

관련한 내용은 내가 존경하는 [박세준 대표의 블로그](https://www.bpak.org/blog/2014/10/%ea%b7%b8%ea%b2%83%ec%9d%b4-%ec%95%8c%ea%b3%a0%ec%8b%b6%eb%8b%a4-e2e-pfs/) 를 보면 더욱 자세히 알 수 있다.

![그림1]({{ site.baseurl }}/images/tech/ios/1.png)


> The user’s outgoing message is individually encrypted for each of the receiver’s devices. The public RSA encryption keys of the receiving devices are retrieved from IDS. For each receiving device, the sending device generates a random 88-bit value and uses it as an HMAC-SHA256 key to construct a 40-bit value derived from the sender and receiver public key and the plaintext. The concatenation of the 88-bit and 40-bit values makes a 128-bit key, which encrypts the message with it using AES in CTR mode. The 40-bit value is used by the receiver side to verify the integrity of the decrypted plaintext.
This per-message AES key is encrypted using RSA-OAEP to the public key of the receiving device. The combination of the encrypted message text and the encrypted message key is then hashed with SHA-1, and the hash is signed with ECDSA using the sending device’s private signing key. The resulting messages, one for each receiving device, consist of the encrypted message text, the encrypted message key, and the sender’s digital signature. They are then dispatched to the APNs for delivery. Metadata, such as the timestamp and APNs routing information, isn’t encrypted. Communication with APNs is encrypted using a forward-secret TLS channel.


말이 긴데 요약하자면, 결국 암호화를 위한 RSA키와 서명용(무결성 및 부인방지용) ECDSA를 활용하여 iMessage를 구현했고 이를 통해 End-to-end security를 보장한다는 점이다. 메타데이터와 관련한 통신은 forward-secrecy를 보장하는 TLS(아마 DH계열이겠지)를 사용한다는 내용이다.

결국 강력한 하드웨어 에코시스템을 기반으로 속도와 성능의 고민없이 정답같은, 교과서같은 보안기능을 구현했다는 것으로 나는 이해하고 있다. (물론 다른 것들이 나쁘다는 것은 아니다. 이런 방식도 취약점을 가질 수 있다. [애플 아이메시지 암호화 제로데이 취약점.](https://www.hackerslab.org/news/imessage-vul/))

[출처 - iOS security Sep. 2018](https://www.apple.com/business/site/docs/iOS_Security_Guide.pdf)

### iMessage의 편의성

내가 지금 맥북, 아이패드(WIFI모델), 아이폰을 사용하는데. 놀라운 건 아이클라우드를 기반으로 USIM이 없는 아이패드/맥북에서도 SMS/MMS를 보낼 수 있다는 것이다. ~~졸라 편하다;;~~

[설정방법](https://support.apple.com/ko-kr/HT202549)

부수적으로 뭐 애니모티콘, Digital touch 등의 소소한 재미를 주는 기능들도 애플의 환경에서만 느낄 수 있는 재미인 듯 하다.


## Airdrop

### Airdrop의 보안성

역시 이것도 공개키 등을 기반으로 하는 구현이 잘 되어 있다. 아래 설명에 잘 드러난다.

>
When a user enables AirDrop, a 2048-bit RSA identity is stored on the device. Additionally, an AirDrop identity hash is created based on the email addresses and phone numbers associated with the user’s Apple ID.
When a user chooses AirDrop as the method for sharing an item, the device emits an AirDrop signal over Bluetooth Low Energy. Other devices that are awake, in close proximity, and have AirDrop turned on detect the signal and respond with a shortened version of their owner’s identity hash.  


결국 이것도 통제된 범위의 강력한 하드웨어를 가지고 있기 때문에 가능한 기능으로써, 기기 안에 암호키를 활용하여 identity를 확보하고, BLE를 통해 probing을 한다는 것으로 요약할 수 있지 싶다.

### Airdrop의 편의성

애플 제품에 한한다고 하지만. 이렇게 편하고 빠르고 쉬운 file sharing 기능을 본 적이 없다.

![그림4]({{ site.baseurl }}/images/tech/ios/44.gif)

~~이보다 쉽고 편할 수가 있을까..ㅋ~~


### MFi (Made for iOS)

IoT가 주목받으면서 모두가 개방성을 외치고 시장선점을 위해 이런저런 플랫폼들이 개발되고 있을 때 apple은 MFi 를 제시했다. 이게 하나의 프로그램인데.. 사실 굉장히 인상적인 부분은 애플의 색깔이 너무 잘 드러난다는 점.

정확한 프로세스와 요구사항을 파악하기는 어려운데, 이유는 돈 내야지만 NDA를 맺고 그 과정이 공개되는 것으로 보이기 때문이다.

다만 BLE등 일부 기능을 제외하고 라이트닝 케이블부터 iphone, ipad 등과 연동하기 위한 하나의 프로세스와 애플의 요구사항을 정리하는 프레임워크로써 하드웨어부터 요구사항이 굉장히 높고 복잡함을 짐작할 수 있다.

>
Apple requires all manufacturers of HomeKit products to be certified through the Made for iPhone/iPod/iPad (MFi) program. To become certified, manufacturers must install an authentication chip in the accessory and pass extensive tests con-ducted by Apple. To pass these tests, manufacturers must send prototypes to Apple’s headquarters where the products can be tested by Apple personnel [38].

[출처 - Comparison of IoT frameworks for the smart
home](http://lup.lub.lu.se/luur/download?func=downloadFile&recordOId=8884481&fileOId=8884870)

인증칩을 별도로 구성하는 것은 기본이고 하드웨어에 대한 명세까지 요구하는 것으로 알고 있다.  ~~깡패지 뭐..~~

사실 IoT 사업에 대해서 어느 정도 알고 있는 사람들이라면, 저게 얼마나 많은 단가상승과 개발복잡도를 가져오는지 대충 짐작할 수 있을 듯 하다.

### iCloud

### iCloud의 보안성

iCloud는 거의 애플 단말의 핵심 백엔드 플랫폼으로 동작한다고 여겨진다. 처음 아이폰 6를 쓰던 중에 맥북을 하나 받았는데, 설정과정에서 Key chain 을 바바박 나눠가지면서 예전 단말의 암호키 값 등을 그대로 복원해내는 과정은 아직도 기억에 남아있다.

 ![그림135]({{ site.baseurl }}/images/tech/ios/135.png)

iOS security whitepaper에서 얘기하는 iCloud security 관련된 사항은 아래와 같다.

>
Each file is broken into chunks and encrypted by iCloud using AES-128 and a key derived from each chunk’s contents that utilizes SHA-256. The keys and the file’s metadata are stored by Apple in the user’s iCloud account.
...
Cloud Drive adds account-based keys to protect documents stored in iCloud. As with existing iCloud services, it chunks and encrypts file contents and stores the encrypted chunks using third-party services. However, the file content keys are wrapped by record keys stored with the iCloud Drive metadata. These record keys are in turn protected by the user’s iCloud Drive Service Key, which is then stored with the user’s iCloud account. Users get access to their iCloud  documents’ metadata by having authenticated with iCloud, but must also possess the iCloud Drive Service Key to expose protected parts of iCloud Drive storage

이것도 복잡해보이지만. 일단 기본적으로 파일값에 의존적인 키로 일단 암호화를 하고, 사용자의 계정에 의존적인 키로 다시 덮어서 암호화를 한다는 뜻이다. ~~이렇게까지 해도 성능이 나오나;;~~


위에서 이야기한 키체인 뿐 아니라, 애플의 보안기능과 관련된 대부분의 핵심 컴포넌트는 iCloud에서 이루어지는 것으로 보인다. **즉, 단말은 안전하게 자신의 식별값을 가지고 있고, 그 믿음을 바탕으로 iCloud가 모든 서비스를 적절하게 제공하는** 모양새라고 보면 된다.

### iCloud 편의성

iCloud 기반의 다양한 서비스가 있지만, 내가 눈여겨 본 것은 핸드폰 변경 시 해야하는 복원작업이다. 어플리케이션 백업의 수준이 안드로이드와는 비교하기 어렵다. 이유는, 안드로이드는 새로운 폰으로 데이터를 복원할 때 어플리케이션들의 데이터를 복원해내지 못하고 앱을 그냥 새로 설치해준다. 그래서 들어가보면 일일이 로그인해야 하고.. 원래 쓰던 데이터는 다 사라져 있고..

이러한 문제는 제조사 입장에서 사용자의 충성도와 연속성, 구매요소를 크게 해치는 요소로 작용하기 때문에 삼성전자에서는 갤럭시(외 몇 가지 다른 제조사 폰)를 중심으로 하여 [Smart Switch](https://www.samsung.com/sec/support/smartswitch/) 를 만들었다. 다른 안드로이드 단말 기기 대비 훨씬 더 높은 복원률을 보여주지만, 사진 및 미디어 기기에 강점이 있긴 해도 아이폰처럼 어플리케이션 데이터까지 살려주는 것은 불가능하다.

### 그 외

참고로 이 글은 안드로이드와 비교하기 위한 글은 아니다. 그냥 말 그대로 나에게 인상적이었던 내용들을 정리한 것일 뿐. 사실 iOS security 문서를 보면 **복잡하지 않은 암호 기법들을 참 적재적소에 쉽게 쓴다** 는 느낌을 받을 수 있다.

안드로이드와 아이폰의 보안성을 비교하는 것은 매우 해묵은 논쟁이지만 쉽게 결론이 나지 않는 부분이기도 하다. 다만, 어느 관점으로 보느냐에 따라서 달리 볼 수도 있을 것 같은데.

사견으로.
보안이 관리의 영역이라는 점에서, 수많은 제조사/layer(SOC부터 3rd party application 까지 구글은 전부 제각각이며 이러한 문제는 예전부터 제기되었다. [PatchDroid: scalable third-party security patches for Android devices](https://dl.acm.org/citation.cfm?id=2523679))

개방성을 표방하던 안드로이드였기 때문에 불가피했다고 본다. 실제로 구글은 안드로이드를 처음 만들 때, 레퍼런스 폰의 제조사, 칩셋벤더 등을 매번 바꾸려고 했었다. 그런데 지금은 자체적으로 픽셀폰을 만드는 것 뿐 아니라, 안드로이드의 다양한 기능들이 구글의 영향력 아래로 들어가고 있는 예제는 쉽게 찾아볼 수 있을 듯 하다.

어쨌든. 그러한 영향력을 점점 키워가면서 구글은 안드로이드를 점점 안전하게 만들어가고 있고, 삼성전자 같은 대형 제조사는 KNOX 등의 보안 플랫폼을 활용하여 이를 극대화하고 있지만. 역시 안드로이드 vs 아이폰의 보안성을 비교하자면 두 가지 이유로 아이폰에 점수를 주고 싶다.

 1) 관리영역의 명확성
 2) 편이성
 3) 개인정보 관리

특히 2번의 경우는 "이것저것 설정에 사용자의 개입이 많아야 하는 안드로이드 대비" "그냥 알아서 잘 지켜줄테니 너는 그냥 편하게 써" 라는 아이폰의 모양새에서 점수를 주고 싶고.

3번은 독일의 Douglas schumidt 교수가 "구글이 얼마나 데이터를 막 가져가나" 하고 연구한 결과인데, 여기서 구글은 나한테 점수 많이 깎였다. [출처 - Google data collection research](https://digitalcontentnext.org/blog/2018/08/21/google-data-collection-research/)

요약하자면 안드로이드 폰 기준으로 24시간 중에 위치정보를 340회 구글서버로 전송하고, 애플 기기 대비 10배의 통신을 자주하며 이는 특히 구글 플랫폼(안드로이드와 크롬)에서 매우 빈번하게 이루어진다는 점이다. 같은 앱인데도 iOS 기기에 설치된 구글 서비스는 횟수가 확 줄어든다고 한다.

~~기분나빠...~~

### 결론

우선.
이 부분은 짚고 넘어가고 싶다. **애플이 그렇다고 좋은 회사냐?** 라는 질문이 있다면, 거기에는 동의하지 못한다. ~~잘생긴 깡패같은 느낌?? 비열한 거리의 조인성처럼? ㅋㅋㅋ~~

사실 데이터를 가져가는 이유도, 구글이 잘못해서라기 보다는 애초에 구글의 BM이 그거였다고 생각한다. 애플은 그걸 가져갈 필요성을 못느낄 만큼 기기 판매가 잘 되고 있는 것이고.

즉, 그 회사도 기업이고 이윤 추구를 위해 오히려 자기네만의 벽을 구축하느라 여러 사람들을 힘들게 하고 있을 것이라는 짐작은 매우 쉽게 할 수 있지 싶다. MFi에서 언급했던 라이트닝 케이블도 독자적인 환경을 구성하는데 일조하긴 하지만, 반대로 라이트닝 케이블을 구성하기 위한 라이선스 비용을 포기하지 못하여 범용 USB로 넘어가지 못한다는 평도 있는데, 매우 합리적인 추측이라고 생각한다.

하지만, 어쨌든 기술적인 부분에만 집중하여 볼 때, iOS 제품들은 모든 기능과 서비스에 보안을 고려할 수 있을까 싶을 정도로 병적으로 보안이라는 요소가 포함되어 있다.

#### 어떻게 보안기술을 적재적소에 잘 배치했을까?

잘 살펴보면, 이렇게 보안을 가능하게 하는 것은 그들이 잘 통제된 환경을 갖추었기 때문이라고 본다. IT 보안이든, 취약점 분석을 하는 사람이든 모든 보안하는 사람들이 공통적으로 느꼈을 법한 내용은. 보안은 관리영역 위에 있다는 점이다. 10개의 서로 다른 모델의 기기 중에 제일 보안에 취약한 기기가 있다면 걔가 늘 문제가 된다. 그걸 어떻게 버릴 수도 없고, 그렇다고 대응을 하자니 대응도 쉽지 않고, 늘 잘 되는 애들은 잘 되는데 문제가 생기는 애들이 꼭 있다.

학자들은 "Secure the weakest link"라고 멋들어진 표현을 쓰지만. 조금 더 구체적으로 들어가보면, IT 보안이라고 한다면 **자산이나 현황 관리가 제대로 안된 것들에 보안문제** 가 생긴다. **"이런 서버가 있었어?" "이게 왜 여기 있어?" 하는 애들.** 모바일 보안이라고 한다면 "이런 애들이 출시했었어?" 하는 애들. "이런 기능이 있었어?" 하는 애들.

애플은 그걸 철저하게 틀어막고 있다. 또 조금 더 멋진 표현을 쓰자면 결국 attack surface를 eco-system 상에서 정확하게 구분해내고 있다. OS/Platform은 말할 것도 없고 3rd party app, 악세사리 등 주변기기들, 클라우드 등 연동 포인트들, 파일공유 등 편의기능들까지도.


#### Security by business

즉, 어느 한 기능이나 서비스에 대한 risk assessment가 아니라 eco-system과 business 전반에 걸친 security / risk assessment를 하고 있다는 느낌이다.

잘 통제/관리된 환경에서는 업데이트 대상 골라내기도 한결 수월하고, 보안기능을 집어넣기도 수월하고, 패치를 만드는 것도 한결 수월해진다. 다시 말하지만, 보안은 관리의 영역 위에 있기 때문에. 사실 관리의 삼성이 아니라 관리의 애플이 맞는 얘기가 아닐까 싶다.

물론 불편한 점도 있다. 특히 공인인증서는 일반적인 안드로이드에서는 한번만 설치하면 모든 앱들이 같이 쓸 수 있지만 아이폰은 앱별로 따로따로 공인인증서를 받아야 한다. 물론 불편하다. 하지만, 애플이 그걸 모를까? 서로 다른 사용자가 원하는 세상의 모든 편리함만을 제공하려고 하다가 스텝이 꼬이는 경우는 IT업계에서 다반사로 볼 수 있다. 그들이 자신들의 철학대로 risk taking을 하는 모습이라고 생각한다.

결론적으로 애플은 business 측면에서부터 보안을 고려하되, 그렇게 얻어진 통합적인 관리요소를 기반으로 통일성 있고, 사용자에게 편리한 보안을 제공하고 있다고 생각한다. 일반적인 대기업에서, 이렇게 하나의 방향성을 가지고 다양한 기능들이 제공되고 있다는 것은 정말로 놀라운 일이 아닐 수 없다.

**Security by Design** 이라는 표현을 통해 기술의 내재화 등을 강조하고 있지만, 사실 보안기술이나 정책이 관여할 수 있는 부분은 매우 국한적이다. **보안을 잘 활용할 수 있는 부분은 단지 설계/구현 등의 기술적인 요소모다 더 큰 스케일의 business일 수도 있다.** 정리하자면, 사업과 관련된 모든 부분 - 파트너, 기술, 정책, 인적 관리, 마케팅, 사용성 등 - 에 보안의 관점이 포함되어야 진정한 security 의 모습이 이루어질 수 있지 않을까 싶다.

### 보안은 아니지만 에어팟

사람들이 에어팟과 기어x 를 대차게 비교한다. ~~그럴만 하지..~~

난 에어팟이 대단하다고 느낀 게, 블루투스 이어폰을 서비스 관점에서 바라본다는 생각은 못했다. 버스를 많이 타는 지라, 블루투스 이어폰을 선호하는 편인데 그동안 느꼈던 불편함을 에어팟은 해소해주고 있다.

"Battery is low" 얘기를 들어야만 알 수 있었던 배터리 상황이나, 페어링의 그 불편함들, 집에 가야지만 충전이 가능한 상황 등등.

기어X도 보청기 기능을 포함하여 서비스를 구성하는데에는 잘 구현된 것 같으나 문제는 정작...

![그림1]({{ site.baseurl }}/images/tech/ios/555.png)

[출처: 0db ](https://www.0db.co.kr/xe/REVIEW_0DB/35619)

블루투스 이어폰을 사용하다 보면 알게되는 미묘한 딜레이타임이 있는데, 애플은 그걸 잡은 걸로 보인다. 단순 비교치로 보면 삼성 기어X가 3배의 딜레이타임이 있다.

**본질에 충실하면서도 서비스에 필요한 모든 것을 고려하여 단순한 기능을 서비스 차원으로 격상시키는 것**. 이게 얼마나 어려운 일인지는 왠만한 사람들은 다 알 것이다. 특히 IT업계에 있다면..
