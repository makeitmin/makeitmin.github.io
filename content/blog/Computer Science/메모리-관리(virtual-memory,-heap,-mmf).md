---
title: 메모리 관리(Virtual Memory, Heap, MMF)
date: 2021-11-14 10:11:06
category: Computer Science
thumbnail: { thumbnailSrc }
draft: false
---

## 가상메모리(Virtual Memory) 컨트롤

### 메모리 할당 상태

- **Commit**: 물리 메모리 할당 상태
- **Free**: 물리 메모리 비할당 상태
- **Reserve**: 필요 시 물리 메모리에 매핑 가능한 상태
    - 낭비를 막고 순차적인 메모리 사용

### 메모리 할당의 시작점(Allocation Granularity Boundary)

- 메모리 할당의 지나친 단편화를 방지하기 위해 배수로 페이지 할당

### 할당할 메모리의 크기

- 최소 1페이지 이상

### VirtualAlloc 과 VirtualFree 함수

- malloc 함수와 free 함수로는 Reserve 상태를 설명 불가
- VirtualFree 함수의 반환 기능
    - Commit 상태의 가상 메모리를 Reserve 상태나 Free 상태로 전환

## 힙(Heap) 컨트롤

### Dynamic Heap의 장점

- 데이터 효율보다 데이터 분리(관리)에 집중
- 메모리 단편화 해소
    - 프로그램의 Locality 감소
- 메모리 할당 동기화 문제 해소
    - 쓰레드별 Heap 생성

### Dynamic Heap 생성 및 할당

- **HeapCreate**: 생성
- **HeapDestroy**: 소멸
- **HeapAlloc**: 할당
- **HeapFree**: 해제

## MMF(Memory Mapped File)

- 파일의 일부 메모리 공간을 프로세스의 가상 메모리에 연결
- 프로세스의 가상 메모리에 데이터 할당하고 파일의 해당 공간에 데이터를 반영
- 파일 데이터를 메인 메모리에서 작업하면 파일에 바로 반영

### MMF의 장점

- 가상 메모리에 데이터를 작업하여 가상 메모리에 가장 최신의 데이터 존재
    - 데이터를 읽어올 때 메모리만 조회
- 가상 메모리에는 빈번한 I/O가 발생시켜 가장 최신의 데이터 저장
- 파일에는 주기적, 상황적 I/O를 발생시켜 속도 확보

### MMR 실행 과정

1. **CreateFile**: 파일 생성
2. **CreateFileMapping**: 가상 메모리를 매핑할 파일 정보를 담은 파일 연결 오브젝트 생성
3. **MapViewOfFile**: 가상 메모리에 파일 연결

## 참고자료

- 뇌를 자극하는 윈도우즈 시스템 프로그래밍 ([도서](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788979144611&orderClick=LEa&Kc=), [강의](https://www.inflearn.com/course/%EC%8B%9C%EC%8A%A4%ED%85%9C-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D))