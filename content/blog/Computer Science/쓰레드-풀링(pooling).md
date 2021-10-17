---
title: 쓰레드 풀링(Pooling)
date: 2021-10-17 08:10:77
category: Computer Science
thumbnail: { thumbnailSrc }
draft: false
---

## 쓰레드 풀

쓰레드를 미리 생성해두는 저장소

- 미리 생성한 쓰레드를 작업에 할당하고 작업이 끝나면 반환
- 시스템의 성능 향상에 도움

## 쓰레드 풀의 필요성

쓰레드(커널 오브젝트)의 생성과 소멸은 상당한 자원을 소비

- 메모리 할당 필요
- 커널모드에서 유저모드로의 이동 빈번
- 운영체제에 리소스 정보 등록 필요
- 쓰레드는 스케줄러의 관리 대상이므로 스케줄러에도 부담

쓰레드 풀을 통해 미리 생성해둔 쓰레드 활용 가능

## 쓰레드 풀의 구현

**쓰레드 풀 구조체**

```c
struct___ThreadPool
{
	// Work를 등록하기 위한 배열
	WORK workList[WORK_MAX];

	// Thread 정보와 각 Thread 별 Event 오브젝트
	WorkerThread workerThreadList[THREAD_MAX];
	HANDLE workerEventList[THREAD_MAX];

	// Work에 대한 정보
	DWORD idxOfCurrentWork; // 대기 1순위 Work Index
	DWORD idxOfLastAddedWork; // 마지막 추가 Work Index + 1

	// Number of Thread;
	DWORD threadIdx; // Pool에 존재하는 Thread 개수
}
 gThreadPool;
```

**쓰레드 풀의 쓰레드가 작업을 할당받는 과정**

1. WorkList에 Work이 등록
2. 쓰레드의 **WaitForSingleObject 함수**가 관찰하고 있는 **Event 오브젝트**가 **Signaled**로 변경
3. **Event 오브젝트**가 **Signaled 상태**이면 쓰레드가 활성화
4. **GetWorkFromPool 함수**가 WorkList에서 **Work를 할당**
5. 일이 끝나면 루프를 돌고 **WaitForSingleObject 함수**를 호출하여 **Event 오브젝트**를 **Non-Signaled**로 변경

## 참고자료

- 뇌를 자극하는 윈도우즈 시스템 프로그래밍 ([도서](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788979144611&orderClick=LEa&Kc=), [강의](https://www.inflearn.com/course/%EC%8B%9C%EC%8A%A4%ED%85%9C-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D))