---
layout: single
title: "Copy to clipboard (code snippet)"
date: 2024-01-29 23:01:00 +0900
categories:
  - GitHub Blog
tags:
  - [GitHub Blog]
toc: true
toc_sticky: true
toc_icon: "fas fa-utensils"
---

## Copy to Clipboard button 넣는 법

minimal-mistakes 기반 블로그 기준입니다.

### 1. \_config.yml 수정

맨 끝에 다음 코드 추가

```yml
after_footer_scripts:
  - https://cdn.jsdelivr.net/npm/clipboard@2/dist/clipboard.min.js
  - assets/js/clipboardrouge.js
```

### 2. clipboardrouge.js 추가

assets/js/이 경로에 다음 스크립트 추가.

- [clipboardrouge.js](https://github.com/dieghernan/mmistakes-clipboard/blob/master/assets/js/clipboardrouge.js)

### 3. 적용

일반 코드 적용과 마찬가지로 ```사이에 넣어서 사용.

```python
# This is a sample code for testing
print("Hello World")

```

## Reference

- dieghernan, [Adding Copy to Clipboard button to minimal-mistakes with clipboard.js(issue 2795)](https://github.com/mmistakes/minimal-mistakes/discussions/2795)
- dieghernan, [mmistakes-clipboard](https://github.com/dieghernan/mmistakes-clipboard)
