# DEBUG C 스터디 과제
분석 기간 | 2023. 10. 07 - 10. 10 23:59 <br><br>
분석 수행자 | IT융합자율학부 23 김우리<br><br>
분석 환경 | Oracle VM VitualBox 22.04 가상머신<br><br>
개요 |
- 용어 정리
- 명령어 정리<br>
- QEMU 설치<br>
- 자료 알아보기<br>
- root.sh, rootfs.img, root.img 파일로 굽기<br>

<hr>

## 용어 정리
`PoC` - 새로운 프로젝트가 실제로 실현가능성이 있는지 효과, 효용, 기술적인 관점에서부터 검증을 하는 과정을 의미한다.<br><br>
`data race` - 여러 프로세스 / 스레드가 공유된 데이터를 읽고 쓰는 작업을 할 때 실행 순서에 따라서 잘못된 값을 읽거나 쓰게 되는 상황이다.<br><br>
`UAF` - (Use After Free) 이 취약점은 메모리 할당 해제 이후에도 해당 메모리를 참조하여 사용하는 경우에 발생하는 보안 취약점이다.<br> 컴퓨터는 메모리를 효율적으로 사용하기 위해서 똑같은 크기의 메모리를 재활당하는 경우에는 이전에 할당했던 메모리 영역을 할당해준다.<br><br>
`vmlinux` - 리눅스가 지원하는 목적 파일 포맷들 중 하나에서 리눅스 커널을 포함하는 정적으로 링크된 실행파일이다.<br><br>
`Reversing` - 리버스 엔지니어링. 이미 만들어진 시스템을 역으로 추적하여 처음의 문서나 설계기법 등의 자료를 얻어내는 것이다.<br><br>
`vmlinux` - 압축되지 않은 커널 이미지를 ELF 형식으로 담고 있는 정적 링크된 실행 파일, 사실상 커널 그 자체이다.<br><br>
`qemu` - 가상화 소프트웨어 가운데 하나. Fabrice Bellard(인물)가 만들었으며 x86 이외의 기종을 위해 만들어진 소프트웨어 스택 전체를 가상머신 위에서 실행할 수 있다는 특징이 있다. 동적 변환기`Portable dynamic translation`를 사용한다. <br><br>
`kernel` - 컴퓨터 운영체제의 핵심이 되는 컴퓨터 프로그램. 시스템의 모든 것을 완전히 제어한다. <br><br>
`Directory(디렉토리)` - 파일을 보관하는 곳. 리눅스의 디렉토리는 최상위에 해당되는 루트를 중심으로 하위 디렉토리에 다수의 디렉토리가 존재하는 형태의 트리구조로 갖추어지며 계층적으로 관리된다.<br><br>      


## 명령어 정리 $ 이후 --
### 디렉토리 관련 명령어
`ls` - 디렉토리 목록 확인<br><br>
`mkdir [디렉토리 명]` - 새 디렉토리 생성<br><br>
`mkdir -p [디렉토리명 / 디렉토리명 / 디렉토리명 ....]` - 여러 디렉토리 생성 <br><br>
`cd [디렉토리 명]` - 디렉토리 이동<br><br>
`cd ..` - 부모 디렉토리로 이동<br>
> 디렉토리명이 너무 길 때, 조금만 쓰고 tab 키 누르면 자동완성 가능<br>
<br>

`rm -r [디렉토리 명] ` - 해당 디렉토리 아래 있는 내용을 모두 삭제한다.<br><br>

### 파일 관련 명령어
`toush [파일명]` - 비어있는 파일 생성 <br><br>
`rm [파일명] ` - 파일 삭제 <br><br>
`cp [파일 위치, 이름] [목적지 파일 위치, 파일 이름]` - 파일 복사<br><br> 
`mv [파일 위치, 이름] [목적지 파일 위치, 파일 이름]` - 파일 이동<br><br>
`nano [파일명]` - 새 파일 작성, 존재하는 파일 수정 <br><br>
`cat [파일명] *고양이처럼 살금살금 훔쳐본다 생각하면 잘 외워짐` - 파일 내용 보기<br><br>
`locate *.확장자` - 넣은 확장자 기준으로 파일 찾기<br><br>
`whereis ls`<br>
`whereis rm`
- 실행파일 위치 찾기<br><br>



<hr>


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

<hr>

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

<hr>

## 파일 굽기
-> root.sh / rootfs.img / root.iso 
```bush
work_dir="$(readlink -f /home/user/my_project)"
```
작업 디렉토리 경로를 절대 경로로 지정한다. 
```bush
iso_file="my_combined_image.iso"
```
생성할 ISO이미지 파일 이름
```bush
temp_dir="$(mktemp -d)"
```
ISO 이미지 생성을 위한 임시 디렉토리 생성<br>
>임시 디렉토리를 생성하는 이유는 여러가지 있다.
>1. 안전한 작업 환경을 만들기 위해. 임시 디렉토리를 사용하면 파일을 합칠 때 원본파일이나 대상 파일이 손상되지 않도록 보호할 수 있다. 원본 파일을 직접 수정하는 대신 디렉토리에 복사하고 작업을 수행한 후 원본 파일을 변경하는 것이 안전하다고 볼 수 있다.<br>
>2. 원본 파일을 유지하기 위해. 임시 디렉토리를 사용하면 원본 파일을 보존하고 변경 사항을 임시로 저장할 수 있다.<br>
>3. 임시 디렉토리를 사용하면 파일 합치기 작업을 트랜잭션`데이터베이스 시스템에서 병행 제어 및 회복 작업 시 처리되는 작업의 논리적인 단위로 사용자가 시스템에 대한 서비스 요구 시 시스템이 응답하기 위한 상태 변환 과정의 작업 단위이다.` 방식으로 처리할 수 있다. 작업이 완료되면 임시 디렉토리로 이동하거나 복사하여 합칠 수 있다.<br>
>4. 충돌을 방지하기 위해. 임시 디렉토리는 일시적으로 생성되는 고유한 이름을 가지고 있으므로 다른 작업이나 스크립트와의 이름 충돌을 방지할 수 있다. <br>
```bush
cp "$work_dir/root.iso" "$temp_dir/"
```
```bush
cp "$work_dir/root.sh" "$temp_dir/"
```
```bush
cp "$work_dir/rootfs.img" "$temp_dir/"
```
세 가지 파일을 임시 디렉토리로 복사하는 과정이다. <br>
각 파일 (ex. root.sh 등 )의 경로를 지우고 그 자리에 해당하는 파일을 집어넣은 후 수정하면 잘 돌아간다.

```bush
genisoimage -input-charset utf-8 -o "$iso_file" -R -J -l -V "My_Combined_Image" "$temp_dir"
```
genisoimage을 사용해 ISO이미지를 생성하는 코드다. <br>
이후
```bush
rm -rf "$temp_dir"
```
아까 생성해놓은 임시 디렉토리파일을 삭제한 후
```bush
echo "ISO 이미지 생성이 완료되었습니다: $iso_file"
```
셸 스크립트에서 변수 '$iso_file'의 값을 포함한 메시지를 출력하는 코드.<br>
<br>
이 코드를 마지막에 사용하는 이유는, <br>
1. 정보 제공 - 사용자나 스크립트 작성자에게 중요한 정보를 제공하고, 작업이 성공적으로 완료되었음을 나타낸다.
2. 디버깅 - 스크립트를 디버그 할 때 해당 지점까지 실행되었는지 확인하는 데 도움을 줄 수 있다. 생성된 ISO파일 경로를 확인하거나 추적할 때 유용하게 적용된다.
<br>

## 디버깅 작업

qmue로 실행을 할 때 bzimage를 커널로 선택해야 한다. -> qmue를 실행할 때의 요소.<br>
`qemu-system-x86_64`로 실행을 할 때 커널 부분으로 선택해야 함<br>
> 구운 파일에 bzimage를 넣는 것이 아님.

#### 환경 설정하기
- host는 Ubuntu 18.04<br>
- guest는 Linux kernel 4.7 -> qemu 이용<br>
```bush
sudo apt-get install qemu qemu-system
```
qemu와 qemu-system 패키지를 설치하기 위한 명령어
````bush
git clone https://kernel.googlesource.com/pub/scm/linux/kernel/git/torvalds/linux
```
Linux 커널 소스 코드를 복제하는 명령어
```bush
git checkout v4.7
```
Git 저장소에서 특정 버전 또는 브랜치로 전환하는 명령어


## 오류해결 (시간관계상 미완성,,)
```bush
qemu-kvm: command not found
```
`sudo apt-get install qemu-kvm`<br>
`sudo modprobe kvm`<br>
`qemu-system-x86_64 -name my-vm -machine pc-i440fx-2.9 -m 2048 -drive file=vm-disk.img,format=qcow2 -cpu host -nographic -daemonize -monitor tcp:127.0.0.1:12345,server,nowait`<br>

```bush
qemu-system-x86_64: -drive file=host-vm-disk.img,format=qcow2: Could not open 'host-vm-disk.img': No such file or directory
```
`qemu-img create -f qcow2 host-vm-disk.img 10G`<br>
`qemu-system-x86_64 \`<br>
`    -name host-vm \`<br>
`    -cpu host \`<br>
`    -m 2048 \`<br>
`    -drive file=host-vm-disk.img,format=qcow2 \`<br>
`    -monitor tcp:127.0.0.1:12345,server,nowait`<br>
--호스트 가상머신 설정<br>
