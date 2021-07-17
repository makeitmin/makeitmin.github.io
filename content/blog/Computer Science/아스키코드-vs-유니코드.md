---
title: 아스키코드 vs 유니코드
date: 2021-07-17 18:07:55
category: Computer Science
thumbnail: { thumbnailSrc }
draft: false
---
## 문자셋의 종류

1. SBCS(Single Byte Character Set)
    - 1바이트 문자 표현
    - ASCII CODE
    - printf, scanf, fgets, fputs 함수
2. MBCS(Multi Byte Character Set)
    - 1바이트 or 2바이트 문자 표현
3. WBCS(Wide Byte Character Set)
    - 2바이트 문자 표현
    - UNICODE
    - wprintf, wscanf, fgetws, fputws 함수

### MBCS 기반의 프로그래밍의 문제점

- strlen 이 문자열의 길이를 계산할 때 NULL 문자 불포함
- 문자열의 실제 길이와 문자열에 할당된 크기가 다를 때

### WBCS 기반의 프로그래밍

- MBCS 기반 프로그래밍의 문제점 해결 → 모든 문자들을 동일한 2바이트의 크기로 처리
- 자료형 char → 자료형 wchar_t
- 할당되는 문자열 앞에 'L' 표기