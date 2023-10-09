# DEBUG C 스터디 과제
분석 기간 | 2023. 10. 07 - 10. 10 23:59 <br>
분석 수행자 | IT융합자율학부 23 김우리<br> 
분석 환경 | Oracle VM VitualBox 22.04 가상머신<br>
개요 |<ol>
        <li>용어 정리</li><br>
        <li>QEMU 설치</li><br>
        <li>자료 열기</li><br>
        <li>해결<br>
      </ol>

<hr>

## 용어 정리
PoC - 새로운 프로젝트가 실제로 실현가능성이 있는지 효과, 효용, 기술적인 관점에서부터 검증을 하는 과정을 의미한다.<br><br>
data race - 여러 프로세스 / 스레드가 공유된 데이터를 읽고 쓰는 작업을 할 때 실행 순서에 따라서 잘못된 값을 읽거나 쓰게 되는 상황이다.<br><br>
UAF - (Use After Free) 이 취약점은 메모리 할당 해제 이후에도 해당 메모리를 참조하여 사용하는 경우에 발생하는 보안 취약점이다.<br> 컴퓨터는 메모리를 효율적으로 사용하기 위해서 똑같은 크기의 메모리를 재활당하는 경우에는 이전에 할당했던 메모리 영역을 할당해준다.<br><br>
vmlinux - 리눅스가 지원하는 목적 파일 포맷들 중 하나에서 리눅스 커널을 포함하는 정적으로 링크된 실행파일이다.<br><br>
Reversing - 리버스 엔지니어링. 이미 만들어진 시스템을 역으로 추적하여 처음의 문서나 설계기법 등의 자료를 얻어내는 것이다.<br><br>
vmlinux - 압축되지 않은 커널 이미지를 ELF 형식으로 담고 있는 정적 링크된 실행 파일, 사실상 커널 그 자체이다.<br><br>




## Linux에 QEMU설치 -> 굳이 하지 않아도 되는 부분

소프트웨어 리포지토리 업데이트
```bash
sudo apt update && upgrade 
```

QEMU 설치 프로세스 이동
```bash
sudo apt install libvirt-daemon
sudo systemctl enable libvirtd
sudo systemctl start libvirtd
```

설치
```bash
sudo apt install qemu-kvm
```

이후
```bash
sudo apt install git
git clone https://gitlab.com/qemu-project/qemu.git
cd qmue
git submodule init
git submodule update --recursive
```
 까지는 해결 완료 했으나,
 
```bush
./configure
```
configure 설치가 안되어 있다는 오류 문구 발생
```bush
Sphinx not found/usable, disabling docs.
ERROR: Cannot find Ninja
```
수정하기 위해
```bush
cd~
cd qemu
```
이후 또 다른 오류 등장
```bush
ERROR: meson setup failed
```
해결 X.


## 파일 적용

rootfs - 파일 시스템을 뜻한다. 리눅스 파일 시스템을 미리 패키지화 해놓은 바이너리다. 

![image](https://github.com/woori04/woori/assets/100141821/36780598-1ae9-49db-91e3-deec5c2d7d64)
busybox를 설치했다. 
```bush
sudo apt-get install debootstrap
```
배포판 설치하는데 사용되는 유틸리티 설치.
```bush
sudo mkdir /ubuntu-rootfs
```
rootfs를 설치할 디렉토리 생성. uduntu-rootfs 디렉토리 생성 코드
```bush
sudo debootstrap --arch=amd64 bionic /ubuntu-rootfs http://archive.ubuntu.com/ubuntu/
```
ubuntu rootfs를 /ubuntu-rootfs 디렉토리에 설치함. bionic은 원하는 ubuntu 버전에 대한 코드명으로 변경할 수 있다. <br> 위 명령은 Ubuntu 18.04 LTS (Bionic Beaver) 버전의 rootfs를 설치합니다.
```bush
sudo chroot /ubuntu-rootfs
```
rootfs 디렉토리로 chroot를 사용해 진입한다. 
```bush
passwd
```
chroot 환경에서 루트 패스워드를 설정할 수 있다. 이는 선택사항이므로 지나쳐도 무관하다.
```bush
exit
```
chroot환경을 빠져나올 수 있다. 
***번외로, rootfs를 더 이상 사용하지 않는 경우 마운트 해제하는 코드
```bush
sudo umount /ubuntu-rootfs
```

## 파일 굽기
-> root.sh / rootfs.img / root.iso 


