---
layout: single
title: "WSL2 setting"
date: 2024-01-29 21:01:00 +0900
categories:
  - WSL
tags:
  - [WSL]
toc: true
toc_sticky: true
toc_icon: "fas fa-utensils"
---

## 1. 윈도우에서 패키지 설치하기

- 패키지를 설치할 때,

  - 리눅스에서는 간편하게 콘솔로 설치 가능함.
  - 윈도우에서는 .exe 파일 다운로드, 더블클릭, 등등 설치 과정이 번거롭고, 환경변수 설정 에러가 자주 일어남.

- ✨ 윈도우에서 리눅스처럼 간편하게 콘솔로 프로그램 설치하려면? [chocolately](https://community.chocolatey.org/) 사용!

  - window powershell 관리자 실행
  - chocolately를 설치

    ```python
      Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
    ```

  - [chocolately package 사이트](https://community.chocolatey.org/packages)에서 패키지명 검색
    - 예시: Adobe acrobat, VLC media player, Python 3.8
  - choco [패키지명]으로 설치.
    ![choco-package-download]({{site.url}}/images/2024-01-29-wsl2-setting/choco-package-download.png)

## 2. WSL 설치하기

- 기본 프로그램 설치
  - Visual Studio Code (추가 작업 모두 선택)
    - 익스텐션: Python, ESLint, Prettier, Material Theme, Material Icon Theme
  - Google Chrome
  - Git
- Windows Powershell 대신, Windows Terminals (wt) 설치
  - choco를 통해 설치
- WSL 설치
  - 관리자 권한, [wsl --install](https://learn.microsoft.com/en-us/windows/wsl/install) 입력
  - Window 기능 켜기/끄기 - Linux용 Windows 하위 시스템 체크, 가상 머신 플랫폼 체크
  - 리부팅
  - UNIX 계정 만들기
  - 버전 확인 wsl -l -v (파워쉘) / wsl.exe -l -v(우분투)
- 우분투 버전 확인
  - cat /etc/os-release

## 2. WSL의 starting directory 설정하기

- 윈도우 디렉토리 설정하기

  - 터미널 탭 삼각형 눌러서 ubuntu 실행
  - 삼각형-설정-왼쪽 밑 json파일 열기 누르면, VS code로 setting.json이 열림
  - 이 때 vscode에 WSL 익스텐션 설치하기
  - 뉴 탭+ 누르면 windows terminal이 아니라 ubuntu가 default로 뜨도록 설정하기
    - defualtProfile에 우분투 guid 넣기
    - 우분투 이름 원하는 대로 설정 (삼각형 누르면 탭에 뜰 이름임)
  - 우분투 시작 디렉토리 바꾸기
    - Setting.json에서 ubuntu 안에 추가하면 됨
      - Source가 CanonicalGroupLimited인 경우 → 우분투만의 디렉토리 설정 가능
        - \\wsl$ 로 파인더에서 접근 가능함
        - `"startingDirectory": "//wsl$/Ubuntu/home/heyyoungsoul"`
      - Source가 Window.Terminal.Wsl인 경우 → 윈도우 사용자 디렉토리로 설정
        - `"startingDirectory": "//mnt/c/Users/jhyvf"`
        - `"startingDirectory": "//mnt/c/Projects"`
  - [같은 ubuntu wsl2가 버그로 두 개가 생김](https://superuser.com/questions/1737942/different-profiles-of-the-same-wsl2-linux-instance-in-windows-terminal).
    -> 윈도우 디렉토리에 접근 가능한 Wsl꺼를 쓰기로 결정. 하나는 hidden.
    -> 아래는 나의 현재 터미널 세팅의 두 개의 우분투 시작 경로:
    ![my-starting-directory]({{site.url}}/images/2024-01-29-wsl2-setting/my-starting-directory.png)

  - 시작경로가 잘 되었는지 체크
    ```
    $pwd
    $ls
    $cd ..
    $cd .\OneDrive\바탕화면
    ```
    -> 바탕화면 한글로 뜨는 경우: 우클릭-속성-위치에서 영어로 변경 가능은 하지만, 애초에 microsoft 계정이어서 바탕화면, 문서 등이 onedrive안에 있는 경우에는 컴퓨터 설정을 영어로 하지 않는 이상 안 바뀜. 심지어 윈도우 깔 때부터 영어로 해야 한다는 말도 있음. 그니까 일단 그대로 둘 것.
    ```
    $code
    $cd ..
    cd .\Document
    code hello.js
    ```
    -> 이 때 vs code에 필요한 것들이 설치됨

## 3. 윈도우 터미널 zsh로 커스터마이징하기

(To be continued)

## Reference

- 노마드 코더, [개발자를 위한 윈도우 셋업 WSL2, Window Terminal, Ubuntu](https://nomadcoders.co/windows-setup-for-developers)
- hfkais, [Windows Terminal 에서 시작 경로 지정하기](https://hfkais.blogspot.com/2020/11/windows-terminal-start-directory-setting.html)
