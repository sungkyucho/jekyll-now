---
layout: post
title: Raspberry pi - TPM 연동해보기
categories: Tech

---

IoT 보안, IoT 보안 하면서 많은 이슈가 불거져 나왔고, 개인적으로 IoT 보안의 방향은 Mobile security와 비슷한 방향으로 흘러가는 것처럼 보인다. 이유는, (사견으로) IT 역사상 가장 빠른 폭발적인 성장을 보였던 모바일 시장이 가장 시간적으로 최근의 일이면서, 또 그만큼 보안과 관련된 문제도 많이 겪었고 대응했기 때문.

즉, 정답지가 있는 상태이고 따라서 보안기술 동향도 아마 mobile security를 많이 참고할 것으로 보인다. (하지만, 이런 견해에는 좀 반대다. IoT는 product security보다는 backend에서의 이상징후 등이 더욱 발전하지 않을까 하는 생각 -> 비용문제 때문에)

다만, 그럼에도 불구하고 Amazon Echo나 Google Home의 경우에 보안 SoC를 활용하여 보안기능을 넣은 정황(?)들이 많이 보인다. 그래서 나는 뭘 공부해볼까 하다가, 일단 역사가 가장 오래되고 레퍼런스를 찾기 쉬우면서도 general 한 H/W 모듈인 TPM을 간략하게 살펴봄.

## 1. TPM?
[TPM(위키 백과사전 링크)](https://en.wikipedia.org/wiki/Trusted_Platform_Module)은 Trusted Platform Module의 약자로써, 어짜피 널리 알려진 HW 기반 보안모듈이기 때문에 별다른 설명은 생략한다.

이것을 Raspberry pi 에 연결해보려고 했던 이유는 사실, IoT 보안에 있어 HW 보안모듈이 굉장히 큰 트렌드가 되고 있기 때문..

KISA 에서 발행한 [IoT 보안 공통원칙](https://www.google.co.kr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwj4_cqR6N3VAhUBybwKHf5cAqMQFgglMAA&url=https%3A%2F%2Fwww.kisa.or.kr%2Fjsp%2Fcommon%2Fdown.jsp%3Ffolder%3Duploadfile%26filename%3DloT_%25EA%25B3%25B5%25ED%2586%25B5%25EB%25B3%25B4%25EC%2595%2588%25EC%259B%2590%25EC%25B9%2599_V1(%25EC%259B%25B9%25EC%259A%25A9).pdf&usg=AFQjCNH4iodhdo26PnFRM4sfOxaVmU1mig) 이라든가, [IoT 보안기술 동향](https://www.google.co.kr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&ved=0ahUKEwiF5aj9593VAhWBULwKHXEzCscQFggoMAE&url=http%3A%2F%2Fwww.alio.go.kr%2Fdownload.dn%3FfileNo%3D2285511&usg=AFQjCNGEPZhunNx_rSMnQJdKFC4Ny9FKEQ)을 보더라도 HW 보안에 대해서 꽤 언급하고 있다. 이를 STMicro 같은 곳에서는 별도의 SoC 형태로, ICTK 같은 곳에서는 [PUF](https://en.wikipedia.org/wiki/Physical_unclonable_function ) 같은 기술로 커버하려고 하는데 대부분 장단점이 있다.

TPM은 그에 반해 역사가 꽤 오랜 보안모듈이고, 리눅스나 윈도우에서 충분히 개발가능한 수준의 레퍼런스가 있고 general purpose한 특성이 있기 때문에 별도의 SDK 가 필요한 위의 HW 보안모듈 대비 테스트 해보고 적용해보기 쉽다.

문제가 있다면 가격. IoT 보안에 있어서 가격은 굉장히 critical한 요소이며 무조건 싸고 가볍게 만들어야 하는 IoT 보안의 특성 대비 그다지 주목받기 어려운 단점들이 있다. [여기서](http://storefarm.naver.com/cubebite) 개발용 칩을 하나 구매했는데, ~~더럽게 비싸네~~ 8만 9천원인데. 이건 개발용 보드이고 실제 상용보드는 **볼륨에 따라 다르지만** $2 언더라고 생각하면 될 듯. 근데 2달러가... 실제로 상용제품을 (특히 IoT 제품을) 만드는 입장에서는 어마어마하게 큰 돈이라는 점...

![fig1]({{ site.baseurl }}/images/tech/tpm/1.png)

## 2. TPM 특징

TPM과 관련된 자료는 구글에서 쉽게 찾을 수 있으므로 이 부분은 SKIP 하고.. 내가 생각하는 TPM의 특징은 아래와 같음

 - SRK를 이용한 기기인증
 - seal data를 이용한 대칭키 보호
 - bind key를 이용한 비대칭키 보호
 - remote attestation (원격검증? ~~한글로 번역이 뭔지;;~~)

 HW보안모듈 이라는 title 과는 다르게 의외로 secure boot라든가, HW crypto lib. 처럼 개발자 입장에서 필요한 부분은 조금 미미한 편이다. 물론 레퍼런스를 더 찾아보면 되겠지만..

 그리고 linux 기준으로 [trousers](http://trousers.sourceforge.net/) 등의 별도 라이브러리 설치가 필요한데, 이게 사실 꽤 무거운데다 안정화 되어서 그런건지, 이제 쓰는 사람이 없어서 그런 것인지 유지보수가 잘 안되는 듯..

 HW와 밀접하게 동작하는 코드들은 사실 유지보수가 생명인데.. 오픈소스라 하더라도 상용제품에서 발생하는 문제들을 대응하기 위해서는 그만큼의 개발인력을 상시 확보해야한다는 오픈소스와 상용시스템 간의 괴리 때문에 비용도 꽤 많이 필요할 듯 하다.

 하지만 어쨌든. 오랜 시간을 걸쳐 windows bitLocker라든가 여기저기서 상용화가 되었던 기능이니만큼, 보안기능을 테스트해보고 활용해보기에는 또 이만한 게 없는 듯 하다.

## 3. 준비물
 - 라즈베리파이3 및 라즈비안 (Jessie lite version)
 - 리눅스 커널
   - 이게 상당히 어려운데 TPM interface 가 I2C 이므로 이 부분에 대해서 부트로더 쪽 작업이 필요하다.
   - 마침 [시큐리티플랫폼](https://www.kr.securityplatform.co.kr/blank-2)에서 [github에 올린 커널](https://github.com/SecurityPlatformCoKr/meta-sp)이 있으며, 바이너리 파일은 [여기]({{ site.baseurl }}/images/tech/tpm/kernel_for_tpm.tar.bz2)를 클릭
   -  
 - TPM board
   - v1.2 이며, CUBEBYTE에서 구매한 Infinion칩으로써, datasheet는 [여기](http://www.datasheetlib.com/datasheet/69951/slb-9635-tt1.2_infineon-technologies/download.html)
    - 개발보드이기 때문에 부착은 어렵지 않으며 라즈베리파이에 그냥 I2C에 맞게 쭉 붙여주면 됨


![fig2]({{ site.baseurl }}/images/tech/tpm/2.png)

## 4. TPM enabling

 - 결론적으로 가장 많은 삽질을 했던 곳은 7-inch 모니터로 구성을 해보려고 했는데, video driver하고 충돌이 있다고 한다.
 - 따라서, 모니터로 연결하지 말고 UART를 통해 serial port 로 디버깅 해야 함

### 1) UART Setup
```c
/media/~/boot/config.txt
enable_uart=1
```

### 2) WIFI Setup - [참조 링크](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)

```c
$ sudo cat /etc/wpa_supplicant/wpa_supplicant.conf

    country=Asia/Seoul
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1

    network={
	ssid="MY SSID here"
	psk="MY PSK here"
    }
```

### 3) TPM 관련 라이브러리/패키지 설치

#### Install Trousers
```c
    sudo apt-get update
    sudo apt-get install trousers libtspi1 libtspi-dev tpm-tools
```

#### TPM enabled kernel download
```c
    (위의 바이너리 기준)
    tar jxf kernel_for_tpm.tar.bz2
```

#### Install Image
```c
    sudo mv /boot/kernel7.img /boot/kernel7.img.raspbian
    tar c -O -C target . | sudo tar x -C /

    **여기서 /boot가 FAT을 사용하기 때문에 에러가 발생하나, 이 부분은 그냥 ignore**

    sudo depmod -a 4.4.13
```

#### Single mode 변환
 여기까지가 준비단계이고, 이제부터 실제로 TPM 을 세팅해줘야 하는데 TPM 세팅은 run level이 single mode일 때만 가능하다. 따라서 아래와 같이 설정해준다.

```c
 vi /boot/extlinux/extlinux.conf
 "default FIT" -> "default FIT.single"
```

그리고 전원케이블을 완전히 분리한 후, OFF & ON (RESTART)
그러면... 잘 부팅이 되고

![fig4]({{ site.baseurl }}/images/tech/tpm/4.png)

**된다!!!**

```
## Loading fdt from FIT Image at 01000000 ...
   Using 'conf@1' configuration
   Trying 'fdt@1' fdt subimage
     Description:  raspberry pi with infineon
     Type:         Flat Device Tree
     Compression:  uncompressed
     Data Start:   0x0141f5a8
     Data Size:    15429 Bytes = 15.1 KiB
     Architecture: ARM
     Hash algo:    sha1
     Hash value:   e2ea5b6defac95f8b23573916e26f34f95750610
   Verifying Hash Integrity ... sha1+ OK
   Booting using the fdt blob at 0x141f5a8
   Loading Kernel Image ... OK
   Using Device Tree in place at 0141f5a8, end 014261ec
Sboot measuring ... Sboot initializing SRTM
sboot: Failed to initialize TPM

Starting kernel ...

```
위 로그에서 TPM initilization이 되지 않았다고 실망할 필요는 없음. 이제 되게 함..

이제부터 tpm_takeownership이든, setactive든 다양한 command를 활용해서 TPM을 enabling하면 된다.

### 4) TPM enabling commands

순서가 틀리면..
```c
root@raspberrypi:~# tpm_setenable -ef
Tspi_TPM_SetStatus failed: 0x0000002d - layer=tpm, code=002d (45), Bad physical presence value
```
...

```c
root@raspberrypi:~# tpm_setenable -efpresence -a
Tspi_TPM_SetStatus failed: 0x00000003 - layer=tpm, code=0003 (3), Bad Parameter
Change to Physical Presence Failed
```

등의 에러로 인해 엄청난 삽질을 하게 됨..

#### A. TPM Clear : Reboot (Power off & on) - 전원케이블을 완전히 분리하여 Reboot

```c
    # service tcsd start
    # tpm_clear -f
    # halt
```

#### B. TPM Set Enable : Reboot (Power off & on) - 전원케이블을 완전히 분리하여 Reboot
```c
    # service tcsd start
    # tpm_setenable -ef
    # tpm_setactive -a
    # halt
```

#### C. TPM Take ownership : Reboot (Power off & on) - 전원케이블을 완전히 분리하여 Reboot

```c
    # service tcsd start
    # tpm_takeownership -yz
    # vi /boot/extlinux/extlinux.conf
    "default FIT.single" -> "default FIT"

    # halt
```

 - 사실 여기서 ```-yz```옵션은 ```tpm_takeownership```의 경우 TPM에 대한 access 권한을 가져가는 사람을 인증하기 위한 패스워드를 입력받는데, 그걸 그냥 skip하겠다는 뜻이다.
 - 이게 굉장히 시사하는 바가 큰데.. **결국 TPM 을 소유한 사람을 패스워드로 인증하겠다는 것** 이며 HW security라고 한들 결국 **(시스템 내부로 한정지어서) crypto의 끝판은 access control이라는 아이러니한 결론** 이 난다.
 - ```y``` 옵션을 빼면, 패스워드를 입력받게 되어 있는데 이를 어떻게 관리하느냐하는 또다른 보안관리 상의 문제가 도출된다..

## 5. TEST

```
pi@raspberrypi:~$ tpm_sealdata version
  TPM 1.2 Version Info:
  Chip Version:        1.2.133.32
  Spec Level:          2
  Errata Revision:     3
  TPM Vendor ID:       IFX
  Vendor Specific data: 85200050 0074706d 3438ffff ff
  TPM Version:         01010000
  Manufacturer Info:   49465800
```
![fig3]({{ site.baseurl }}/images/tech/tpm/3.png)

## 6. 마무리

어쩌면 당연한 것일 수도 있지만, 참 손이 많이 가고 또 기대했던 것 만큼의 보안을 제대로 제공해주지는 못한다는 느낌이 든다. 즉, TPM 은 정말 암호키를 보호하기 위한 용도. 딱 거기까지인 듯 하다.

이걸 보니까 TrustZone이나 SGX에서 왜 Trusted Execution Environment를 강조하는지 알겠다. 즉, 단순히 암호화키 보호가 아니라 어플리케이션이 실행되는 환경 자체를 보호하기 위한 더 거시적인 차원에서의 보호 메커니즘의 탄생은 당연한 수순이었던 거 같다.

그리고 그만큼 TEE는 정말 어렵다...ㅋㅋㅋ (open ported TEE - Linaro, OP-TEE, 한국발음 "옵티"도 살짝 도전해봤지만, 이건 좀 시간을 많이 투자해야 할 듯 하다)
