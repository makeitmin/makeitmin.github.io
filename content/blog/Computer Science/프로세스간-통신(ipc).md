---
title: 프로세스간 통신(IPC)
date: 2021-10-24 09:10:14
category: Computer Science
thumbnail: { thumbnailSrc }
draft: false
---

## 프로세스간 통신의 필요성

하나의 프로그램이 여러 프로세스로 구성되어 있는 경우 프로세스간 통신이 중요

- 프로세스간 데이터를 송수신하는 것으로, 메모리를 공유하는 작업
- 일반적으로 각각의 프로세스는 OS에 의해 다른 영역(메모리)에 접근 불가
- OS는 프로세스간 통신이 가능하도록 IPC(Inner Process Communication) 기법을 제공

## 메일슬롯 방식

- 함수 호출을 통해 Sender 프로세스와 Receiver 프로세스가 단방향 통신
- 하나의 Sender 프로세스에서 여러 Receiver 프로세스로 브로드캐스팅 통신
- Windows의 File System 기반으로 동작

---

1. Receiver 프로세스가 CreateMailSlot 함수를 통해 공유 메모리를 생성
2. Sender 프로세스가 CreateFile을 통해 공유 메모리에 연결
3. Sender 프로세스가 WriteFile 함수를 통해 송신하고, Receiver 프로세스가 ReadFile 함수를 통해 수신

## 참고자료

- 뇌를 자극하는 윈도우즈 시스템 프로그래밍 ([도서](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788979144611&orderClick=LEa&Kc=), [강의](https://www.inflearn.com/course/%EC%8B%9C%EC%8A%A4%ED%85%9C-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D))