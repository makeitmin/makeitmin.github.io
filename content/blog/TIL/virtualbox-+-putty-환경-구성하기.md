---
title: VirtualBox + PuTTY 환경 구성하기
date: 2021-08-06 09:08:76
category: TIL
thumbnail: { thumbnailSrc }
draft: false
---

회사에서 사용하기로 한 개발환경은 VirtualBox에 가상 환경을 올리고, PuTTY를 통해 접속하는 형태이다. 가상 환경은 이미 만들어두신 OVA 형식의 이미지가 있어서, 복사를 받아서 올리기만 하면 됐다. 각 환경 구성 방법과 오류가 발생하여 해결한 기록을 남겨두려고 한다.

## VirtualBox 환경 구성하기

1. **VirtualBox 설치 파일과 확장팩 다운로드하기**  
[VirtualBox 공식 홈페이지](https://www.virtualbox.org/)의 [다운로드](https://www.virtualbox.org/wiki/Downloads) 카테고리에 접속하면 설치 파일을 받을 수 있다. **VirtualBox [버전] platform pakages** 의 하단에 있는 링크 중에 선택하면 된다. 현재 VirtualBox를 설치하려는 환경은 Windows 이므로 Windows hosts 링크를 선택했다. 다운로드가 완료되면 `VirtualBox-[버전]-145957-Win.exe` 실행 파일이 로컬에 저장된다.  
바로 아래에 **VirtualBox [버전] Oracle VM VirtualBox Extension Pack**의 하단에 있는 All platforms 링크를 클릭해서 확장팩도 저장한다.  
2. **VirtualBox 설치 진행하기**  
설치 파일을 실행하면 설치를 진행할 수 있고, 진행하면서 설정은 따로 바꾸지 않고 기본값으로 지정했다. 확장팩은 VirtualBox 설치가 완료된 후 실행하면 자동으로 반영된다.
3. **VirtualBox 실행하기**  
설치된 VirtualBox를 실행하면 Oracle VM VirtualBox 관리자 창이 나타난다. 상단의 메뉴 중에서 가상 시스템 가져오기를 선택하여 보유한 가상 환경 이미지(.ova)의 경로를 입력할 수 있다. 경로를 입력하고 가져오기 버튼을 클릭하면 환경이 생성된다.

## PuTTY 환경 구성하기

1. **PuTTY 설치 파일 다운로드하기**  
[PuTTY 공식 홈페이지](https://www.putty.org/)에 가면 [다운로드](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 페이지로 이동할 수 있는 링크가 있다. 해당 페이지로 이동해서 **Package files** 하단에 있는 링크 중에 자신의 OS와 비트에 맞는 MSI 파일을 다운로드 받는다.
2. **PuTTY 설치 진행하기**  
설치 파일을 실행하여 기본값으로 설치를 진행한다.
3. **PuTTY로 가상 환경 접속하기**  
PuTTY를 실행하면 접속을 위해 알맞은 Host Name(또는 IP 주소)와 Port를 지정한다. IP 주소는 가상 환경마다 다르므로 확인이 필요하다.  
VirtualBox의 **설정>네트워크>고급**에서 포트포워딩 규칙 버튼을 클릭하면, 생성되는 창의 호스트 IP 컬럼에서 확인할 수 있다. Port는 사용 중이 아니라면 보통 22번으로 지정한다.  
Session에 이름을 입력하여 저장하면 접속할 때마다 IP 주소와 Port를 입력하지 않아도 되고, Open 버튼을 클릭하여 가상 환경에 접속할 수 있다.

## VirtualBox 종료하기

VirtualBox를 창을 닫아 강제 종료하면 문제가 발생할 수 있다. 꼭 VirtualBox로 실행 중인 가상 환경 안에서 시스템을 정상적으로 종료해야 한다.

## VirtualBox 오류 기록

### 문제상황

VirtualBox를 실행하였을 때 오류 메세지가 뜨면서 프로그램 실행 불가

### 오류 메세지

명령행에서 시작할 머신을 지정해야 합니다.  
사용 방법: VirtualboxVM --startvm <name|UUID>  
이름이 name이거나 uuid가 uuid인 virtualbox 가상 머신을 시작합니다.

### 해결방법

**첫 번째 시도: 설치된 가상 머신 중에 같은 이름을 가진 가상 머신이 있는지 확인**

가상 머신이 중복되어 시작할 머신을 지정해달라는 오류인 것으로 짐작하고 다음을 시도하였다.

1. 관리자 권한으로 CMD 실행
2. 가상 머신이 존재하는 경로로 이동

    ```bash
    cd C:\Users\[사용자 이름]\VirtualBox VMs
    ```

3. 같은 이름의 폴더가 존재한다면 1개만 남기고 삭제
4. 다시 실행

해당 경로에 들어갔지만, 가상 머신이 중복되어 발생하는 오류는 아닌 것으로 확인하였다.

**두 번째 시도: 가상머신 UUID 확인**

오류 메세지에 UUID가 있어 무엇인지 찾아보니 가상 머신이 고유하게 가진 id(Universally Unique Identifier)인 것을 알게 되었다. 따라서 UUID를 알아내고, 오류 메세지에 있는 명령어에 직접적으로 지정하여 실행해주면 오류가 해결될 것으로 짐작하고 다음을 시도하였다.

1. 관리자 권한으로 CMD 실행
2. VirtualBox 커맨드를 사용하기 위해 VirtualBox 설치 경로로 이동

    ```bash
    cd C:\Program Files\Oracle\VirtualBox
    ```

3. 가상 머신 리스트 확인

    ```bash
    # 명령어 실행
    VBoxManage.exe list vms

    # 성공 시 "가상 머신 이름" {UUID} 형식으로 리스트 출력
    "example_vm" {4ecvfdh574q-erw42-4...}
    ```

4. UUID 복사 및 명령어 실행

    ```bash
    VirtualboxVM --startvm 4ecvfdh574q-erw42-4...
    ```

해당 방법으로 가상 머신 실행에 성공하였고, 이후로도 오류가 발생하지 않았다.

## 참고자료

- VirtualBox 공식 홈페이지 ([문서](https://www.virtualbox.org/))
- PuTTY 공식 홈페이지 ([문서](https://www.putty.org/))
- 가상 머신 중복 확인 ([블로그](https://ffoorreeuunn.tistory.com/43))
- UUID의 정의 ([문서](https://en.wikipedia.org/wiki/Universally_unique_identifier))
- UUID 리스트 조회하기 ([블로그](https://lng1982.tistory.com/293))