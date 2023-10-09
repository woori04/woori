# DEBUG C 스터디 과제

Linux에 QEMU를 설치하기 위한 전제 조건
```bash
sudo apt update && upgrade 
```
소프트웨어 리포지토리 업데이트
```bash
sudo apt install libvirt-daemon
sudo systemctl enable libvirtd
sudo systemctl start libvirtd
```
QEMU 설치 프로세스 이동
```bash
sudo apt install qemu-kvm
```
설치
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
configure 설치가 안되어 있다는
```bush
Sphinx not found/usable, disabling docs.
ERROR: Cannot find Ninja
```
오류문구 발생
```bush
