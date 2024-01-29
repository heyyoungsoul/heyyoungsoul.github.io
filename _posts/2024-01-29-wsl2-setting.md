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
    {% include codeHeader.html %}
    ```PowerShell
      Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
    ```
  - [chocolately package 사이트](https://community.chocolatey.org/packages)에서 패키지명 검색
    - 예시: Adobe acrobat, VLC media player, Python 3.8
  - choco [패키지명]으로 설치.

## Reference

- 노마드 코더, [개발자를 위한 윈도우 셋업 WSL2, Window Terminal, Ubuntu](https://nomadcoders.co/windows-setup-for-developers)-
