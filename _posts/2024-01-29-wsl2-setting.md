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

    ```json
    {
        "guid": "{2c4de342-38b7-51cf-b940-2309a097f518}",
        "hidden": false,
        "name": "Ubuntu-cdrive",
        "source": "Windows.Terminal.Wsl",
        "startingDirectory": "//mnt/c/Projects"
    },
    {
        "guid": "{51855cb2-8cce-5362-8f54-464b92b32386}",
        "hidden": true,
        "name": "Ubuntu-wsl",
        "source": "CanonicalGroupLimited.Ubuntu_79rhkp1fndgsc",
        "startingDirectory": "//wsl$/Ubuntu/home/heyyoungsoul"
    },
    ```

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

## 3. 윈도우 터미널 커스터마이징하기 위한 셋업(zsh, oh my zsh, powerlevel10k, terminal splash)

![terminal-custom-color-scheme.png]({{site.url}}/images/2024-01-29-wsl2-setting/terminal-custom-color-scheme.png)

### 3-1. bash셀을 zsh셀로 바꾸기

- [zsh](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH) 설치
  ```
  sudo apt install zsh
  ```

### 3-2. oh my zsh 설치하기

- [oh my zsh](https://github.com/ohmyzsh/ohmyzsh) 설치
  ```
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
  ```

### 3-3. winndow terminal 디폴트 color scheme 적용해보기

- Json.setting 파일 열기
- [window terminal color scheme](https://learn.microsoft.com/en-us/windows/terminal/customize-settings/color-schemes)에서 맘에 드는 color scheme 찾기
- 적용하기

```json
"colorScheme": "Campbell Powershell"
```

### 3-4. terminal splash를 이용해 더 예쁜 color scheme 적용하기

- [terminal splash](https://terminalsplash.com/)에서 맘에 드는 테마 찾기
- code 복사해서 schemes의 []안에 넣고 이름을 지정할 것

```json
"colorScheme": "Campbell Powershell"
```

- 일괄적용하기 위해 default에 적용할 것
  -> 그런데 바라던 컬러가 안 나옴. 이건 윈도우 자체 한계 때문임. 따라서 terminal splash에 보이는 컬러를 그대로 적용하려면 powerlevel10k를 깔아야 함.

### 3-5. terminal splash 제대로 적용하기 위해 powerlevel10k 설치하기

- [Powerlevel10k](https://github.com/romkatv/powerlevel10k)란, 우리가 설치한 터미널인 zsh 셀을 위한 테마임.
- oh my zsh를 통해 설치
  ```
  sudo git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
  ```
- Powerlevel10k를 activate하려면 ~/.zshrc 파일에서 zsh의 theme으로 설정해줘야함
  - .zshrc(z shell run command) 파일은 oh my zsh 쉘에 대한 모든 configuration이 들어있는 파일임.
  - .zshrc파일은 우분투의 최상위 경로인 root경로(~)에 있음. vscode로 열기.
    ```
    code ~/.zshrc
    ```
    ```
    ZSH_THEME="powerlevel10k/powerlevel10k"
    ```
  - 윈도우 터미널 껐다가 켜면 powerlevel10k configuration이 뜨면서 does it look like a diamond?라고 물음.
  - 만약 이 문구가 다이아몬드로 안 보이고 네모로 보이면 폰트 문제임.
    - [Powerlevel10k](https://github.com/romkatv/powerlevel10k) 깃허브에서 "window terminal" 검색하면 폰트 어떻게 바꾸라고 나옴.
      - 거기 있는 `MesloLGS NF` 폰트들 다운받아서 열고 설치
      - 윈도우 터미널의 Setting.js에 default에 `"fontFace": "MesloLGS NF"`
      - Vscode의 setting에서도 terminal integrated font family에 `MesloLGS NF` 넣어주기
  - 원하는 대로 설치 진행
    - 예시: y,y,y , rainbow, unicode, 2, 1, 1, 1, 1, 1, 2, concise, no, Instant Prompt(3)
  - wt에서 잘 되는 지 확인
  - vscode에서 잘 되는 지 확인
    - view-terminal
    - setting
      - Terminal Integrated Shell: Windows 에서 edit in setting.json 클릭
      - Terminal › Integrated › Default Profile: Windows 여기서 powershell을 Ubuntu-18.04 (WSL) 부분으로 바꿔서 체크.
      - 만약 드롭다운 선택이 아니라 Edit in setting.json일 경우, "terminal.integrated.defaultProfile.windows": "C:\\Windows\\System32\\wsl.exe"
      - 혹은 터미널실행하면 터미널의 오른쪽 위 부분에 여러가지 버튼들이 있는데 그 중에 PowerShell이라고 쓰여있는 버튼 오른쪽에 +버튼, 그옆에 아랫방향 화살표가 있는데, 그 화살표 눌러서 '기본 프로필 선택Select Default Profile' 누른 다음 'Ubuntu (WSL)' 누르면 WSL이 기본값으로 설정 됨.
      - 이때 혹시 완전 똑같지 않으면, 폴더에 파일 없어서 그런 거니까 파일 많은 곳에서 vscode 열어서 확인할 것.
- 만약, Powerlevel10k의 Configure 설정 다시 하고 싶으면,  
  `p10k configure`

### 3-6. VSCode의 starting directory도 바꿔주기

- Setting 들어가서 terminal.integrated.cwd 검색
  - ${fileDirname}
- 또는 다음과 같이 edit in setting.json
  - "terminal.integrated.cwd": "${fileDirname}"

### 3-7. WSL의 ls 명령어의 color 초기값 바꾸기

- 윈도우 터미널(즉, z shell)의 configuration 파일을 수정해야함.
  ```
  code ~/.zshrc
  ```
- 열린 파일 맨끝에 다음을 추가

  ```
  LS_COLORS="ow=01;36;40" && export LS_COLORS
  ```

## 4. 윈도우 터미널 커스터마이징하기

- 이제 color scheme을 [terminal splash](https://terminalsplash.com/) 에서 아무거나 맘에 드는 거로 적용하면 다 됨 (e.g., VS Code Windows Terminal)

  - (주의) 헷갈리지 말것:

    - wt(wsl, window powershell 등 포함한 터미널)의 컬러테마은 wt의 +탭 옆 삼각형 눌러서 setting.json가서 바꾸는 것
    - vscode의 컬러 테마는 Material Theme 익스텐션으로 바꾼 거임
    - 단, vscode 안의 터미널은 wsl로 설정했기에 그 스타일로 그리고 리눅스로 보이는 거임

  - (To be continued)

## Reference

- 노마드 코더, [개발자를 위한 윈도우 셋업 WSL2, Window Terminal, Ubuntu](https://nomadcoders.co/windows-setup-for-developers)
- hfkais, [Windows Terminal 에서 시작 경로 지정하기](https://hfkais.blogspot.com/2020/11/windows-terminal-start-directory-setting.html)
