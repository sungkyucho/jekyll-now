---
layout: post
title: Android factory reset - 복구 가능성 관련
categories: Tech
---

사실 이번에 작성하는 내용은, 꽤 예전에 확인했던 내용인데 백업 겸 정리. (내용 자체도 out of date 이지만, 꽤 의미있다)

두 곳의 회사를 다니면서 보안팀에 있으면 이런저런 임원진들의 문의사항을 해결해줘야 할 때가 있다. 이전 회사에서도 잊을만~ 하면 한번 있었고 이번에도 그렇고. 이번 건도 지금 회사에서 궁금해하던 내용을 찾다보니 좋은 논문 하나가 있어서 정리한 글이다.

논문 링크는 [여기](https://www.cl.cam.ac.uk/~rja14/Papers/fr_most15.pdf).
캠브릿지 대학교 컴퓨팅 랩에서 쓴 논문이며 내가 이 논문을 좋아하는 이유는 사실 이런 종류의 논문들은 굉장히 학술적이고 이론적이며 "그럼 실제는 어떤데?" 라는 질문에 대한 답변보다는 뭔가 fancy한 면을 보여주려는 게 많은데 이번 논문은 상당히 practical 하다는 점이다.

내용은 **android factory reset 이후에 데이터 복구가 과연 가능하느냐** 에 대한 research 자료이다.

## 1. 개요
 + 논문제목: Security analysis of Android factory reset
 + 주요내용: Android 폰에서 Factory reset 이후의 데이터 복원 가능성에 대한 연구결과
 + 2015년에 발표되었지만, 연구는 2014년 이전에 된 거 같네요. Froyo ~ Jelly bean에 대해서만 연구결과가 있고 이후 버전은 논외

## 2. 내용

 + Flash & file system
    + 정말 초기 스마트폰은 raw flash에 직접 접근할 수 있는 yaff2 파일시스템을 사용했지만, 최근은 eMMC(Embedded MultiMediaCard) 기반의 ext4 등을 쓰고 있음
    + eMMC를 사용하는 이유는, OS가 raw flash에 직접 접근하는 것을 막기 위한 것이고 (그냥 막 건드리면 문제가 많으니까)
    + eMMC를 대신 데이터에 접근하기 위해서는 block device들을 노출(?) 시키는데, 이 곳이 **관심있는 데이터 복원 영역**
    + 또한 flash는 특성상 유한한(finite) Read/Write를 지원하기 때문에, 삭제를 최소화 하려는 기본 정책을 가지고 있음 (meta data만 삭제하는 등)

  + Linux Kernel Deletion APIs
    + 이러한 eMMC 특성 때문에(직접 raw flash에 접근하지 않는..) eMMC에서 command 등을 통해서 raw flash를 직접 건드려야 우리가 원하는 수준의 삭제가 가능함
    + ```BLKDISCARD``` (불완전한 삭제, unlink와 유사)와 ```BLKSECDISCARD``` (제대로된 삭제, secure erase) 두 가지 옵션이 제공되고 있음 ( **지금은 아니지만** 삼성전자 초기 모델들도 성능상의 이유로 secure erase가 disable 된 것을 확인한 적도 있음)

  + Data Partition
    + 안드로이드에서 총 3가지의 data partition을 제공
       + Data partition: ```/data``` 경로를 가지며, 일반적인 앱들이 private 하게 사용하는 영역. 제일 중요한 영역.
       + Primary SD card: Internal SD카드라고도 불리는데, 기본 저장공간. “내 파일”에서 처음 접근되는 영역.
       + Secondary SD card: External SD카드라고도 불리며, 우리가 흔히 아는 착탈식 SD 카드.

## 3. 결과
  + 테스트 대상
      + 삼성전자, HTC, LG, 모토롤라, 구글(넥서스)
      + 26개의 단말을 테스트용으로 사용하고, v2.2(Froyo) ~ v4.3(Jelly Bean)까지 테스트 대상으로 삼음

  + 테스트 결과
      + Partition별 데이터 복원 가능성(Insecure deletion)

      ![fig1]({{ site.baseurl }}/images/tech/aosp_fr/1.png)

      + 상기 표에서와 같이, data partition은 **복원가능성이 최신폰으로 올수록 상당히 낮아짐** 을 알 수 있음
      + 약간 장난질이 있는데, external SD카드가 늘 100%인 이유는 **Factory reset 시, External SD카드는 공장초기화 대상이 아니기 때문** 이라고 함. 그래서 연구팀에서는 100%로 했다고함. ~~장난치다 걸리면 손 모가지인데~~

   + OS 별 복원 가능성

      ![fig2]({{ site.baseurl }}/images/tech/aosp_fr/2.png)    

      + ```BLKDISCARD```는 복원가능성 높음, ```BLKSECDISCARD```는 복원가능성 낮음. 이라고 단순화 해서 살펴봐도 상관없음(이러한 이유로 첫 번째 그림에서와 같은 결과가 나온 것이겠지..)

## 4. 기타 특이사항

  + (이게 제일 심각한 문제 같은데..) **안드로이드는 제조사 파편화가 심하기 때문에, 구현 내용이 제조사별로 다르다고 함.** 특히 HTC는 어디서는 BLKSECDISCARD를 쓰고, 어디서는 안쓰고 뒤죽박죽이라고 콕 찝어서 얘기함.
  + **업그레이드를 한다고 해서, 무조건 저렇게 되는 것은 아니라고 함.** 예를 들어, GB -> ICS 라고 해서 ```BLKSECDISCARD```를 쓰는 건 아니며, **초기 출시 펌웨어 버전이 제일 중요**
  + 넥서스4의 경우에는 다 지우는 것처럼 보이는데, 일부 데이터(16KB 영역이지만)는 건드리지 않는다고 함. 즉, 구현사항에 따라 차이가 반드시 발생한다는 점이 굉장히 큰 시사점이라고 생각. **삭제하지 않는 부분이 google credential 관련한 부분..**

## 5. 대책
  + 3rd party 앱을 통한 데이터 삭제
    + Factory reset 이후에 이러한 app을 설치 후 실행하는 것이 좋다고 함. (뭔 차이인지를 모르겠어서 자세히 읽어봐도 이유 설명은 없음..)
    + Google Credential (구글 계정)은 해당 앱들의 삭제 범위에 없다고 함. ~~허허~~
    + 특히 백신앱 등에서 제공하는 삭제 기능은 별로 추천하지 않는다고 함.
  + 가장 좋은 방법
    + Bit by bit 모든 파티션을 덮어쓰기 하는 것이 가장 좋은데, 문제는 이것을 하려면 root 권한이 필요한데 일반적인 사용자에게는 주어지지 않는 권한이어서 현실가능성은 없음
  + **현실적인 대안책**
    + 단말에서 Full Disk Encryption(FDE) 기능을 제공한다면, 이것을 활용하는 것이 좋음.
    + FDE 기능은 ICS(4.0) 이후에 기본적으로 제공이 되는데(제조사별 편차 가능), Internal SD 및 External SD 모두 FDE 실행한 후, 공장초기화를 실행하는 것이 좋다고 함.
    + 이 때, 암호화 키는 사용자 PIN이나 Password로부터 파생되는데 복잡도가 높은 Pin/Password를 사용하는 것이 좋다는 ~~당연한~~ 얘기를 함.
    + 별개로 이 부분은 유명한 보안학자인 브루스 슈나이어도 언급한 바 있으며 참고는 옆 링크. [링크](https://www.schneier.com/blog/archives/2006/09/media_sanitizat.html)
    + **FDE 이후의 Factory reset은, 훨씬 더 깊이있는 연구를 한다면 복원이 가능할 수도 있겠지만 일단 일반적인 범위 내에서는 복원이 불가** 했다고 하는 것이 최종 결론.

## 6. 마무리
  사실, 사내에 Mobile WPM 으로 광 파는 사람이 있었는데, 그걸 정면으로 반박할 수 있는 근거자료로 잘 활용했다. 사내에서 모바일 보안과 관련된 관심이 갑자기 증폭된 사건이 있었는데 ~~뭐게?~~ 이때 그 사람이 "내가 만든 mobile WPM을 쓰자" 라고 임원들에게 광팔고 다니다가 조용히 사라졌다는 것은 후문.

  사실 모바일 데이터에 대한 복원문제는 일반적으로 관심을 가질만한 사항은 아니지만 이상하게 가끔씩 이슈가 떠오를 때가 있는데 그 때 이 자료를 활용하면 좋더라. (참고로 요 내용을 기반으로 지금 있는 회사 및 그룹 전체에 관련 가이드가 나갔다는...)

  오늘의 교훈
   + 복원이 두려울 나쁜 짓을 하지말자
   + 갤럭시 S7 이후(default FDE) 및 아이폰을 쓰자
   + 믿을만한 제조사 제품을 쓰자

끝.
