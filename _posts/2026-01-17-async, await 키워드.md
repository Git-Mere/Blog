---
title: "async, await 키워드 in C#"
excerpt: " "

categories:
  - 내가_볼려고_만든_것들
tags:
  - [tag1, tag2]

permalink: /categories4/async-await/

toc: true
toc_sticky: true

date: 2026-01-14
last_modified_at: 2025-01-14
---

예전에 godot으로 게임개발할 떄도, async, await 때문에 조금 고생을 했었다.. 그 때는 무엇인지 잘 몰랐고, 부끄럽게도 잘 알아볼려고 하지도 않았다. 그냥 대충 시간을 좀 지연시켜주는? 그런 느낌의 키워드인줄 알았다. 그런데, 이번에 수업을 들으며 서버-클라 구조에서 아주 중요한 개념인걸 깨닫고 정리해본다.

1. 동기(Synchronous) / 비동기(Asynchronous)
  1. 동기
    - 작업이 끝날 떄까지 기다림
    - 다음 코드로 못 넘어감
    - 기다리는 동안 **스레드 점유**
    - 우리가 평소에 쓰는 방식
    ```csharp
    DownloadFile(); //끝날 떄까지 대기
    ProcessFile(); //이후 실행
    ``` 
  2. 비동기
    - 작업을 시작만 하고 기다리지 않음
    - 결과는 나중에 처리
    - 기다리는 동안 **스레드 반환**
    - await키워드가 없다면, DownloadFileAsync함수 호출뒤에 바로 ProcessFile함수 호출
    - await키워드가 있다면, DownloadFileAsync함수 끝날 떄까지 대기 한 뒤, ProcessFile함수 호출
    ```csharp
    await DownloadFileAsync();
    ProcessFile();
    ``` 

2. 비동기를 쓰는 이유
   **기다려야 하지만, 그동안 스레드를 낭비하지 않기 위해서**
   즉 예를 들어서, DownloadFile함수가 웹에서 다운로드를 해야 하는데, 이런 경우 시간이 많이 듬. 그리고 스레드가 일을 하는 것도 아니고, 다운로드 같은 경우는, OS/네트워크 쪽에서 처리해야 함. 결과적으로 스레드는 하는 일도 없는데, 기다려야 해서 낭비되고 있음. 이것을 방지하기 위해서 비동기가 쓰임.
   - 서버 확장성 상승
   - UI 응답성 상승

3. Task
   Task 는 비동기 함수/메소드 들이 반환하는 것. Task는 아직 확정되지 않은 결과값을 가지고 있음. 즉 미래의 결과
   음 일단 비동기 함수/메소드 들은 호출되자마자 task를 반환함. 아직 값이 없을 수도 있음. 이미 끝났을 수도 있음.
   ```csharp
   Task     //결과 없음
   Task<T>  //나중에 T타입 결과
   ```
   Task는 '약속', Thread는 '실행자'

4. 비동기 함수/메서드의 동작 방식
   - 결과를 바로 반환
  ```csharp
   Task<int> GetAsync();
  ```
  즉 이 함수가 실행되면 바로 Task<int>를 반환. 하지만 저기에 결과값이 있는지 없는지는 모름.
  저 Task가 확실하게 결과를 가지도록 기다리게 해주는 키워드가 **await**

5. await
  - await를 쓰는 이유 : **비동기 작업의 결과를 안전하게 사용하기 위해서**
예를 들어 이런 형태로 await를 쓰지 않는 비동기 형태가 있다고 하자.
  ```csharp
   DownloadAsync();
   Process();
  ```
만약 Process함수에서 Download받은 파일을 수정하고 이용하는 함수라면, DownloadAsync의 task가 결과를 가졌을지 안 가졌을지 모르기 때문에, 높은 확률로 기대하지 않은 결과가 발생한다. 하지만 await를 사용한다면
  ```csharp
   await DownloadAsync();
   Process();
  ```
DownloadAsync함수가 끝날떄까지 대기하게 되고 Task의 결과가 확정났을 때, process함수가 실행된다.
여기서 await에서 현재 사용중이던 스레드가 반환된다.
즉 await까지 T1 스레드가 진행을 하다가, await키워드를 보고 반환된다. 그 뒤에, DownloadAsync함수가 끝나면 T2 스레드가 나머지 Process 함수를 진행한다.

6. await와 스레드 관계
   - 현재 메서드 중단
   - 현재 메서드 반환
   - 작업 완료 후 다시 이어서 실행

7. 만약 비동기 함수가 스레드를 요구하는 작업을 할 시
  - 만약 비동기 함수가 CPU가 계산해야 하는, 즉 스레드가 필요한 동작이 요구할 시, 스레드가 필요한데, 여기서 쓰이는 스레드는 스레드 풀에서 하나를 빌린다.
  - 이럴 떄는, Task.Run 문법을 써서 스레드 풀에서 스레드를 하나 빌릴 수 있다.
  ```csharp
  async Task FooAsync()
  {
      Console.WriteLine("A");
      await Task.Run(() => HeavyCompute());
      Console.WriteLine("B");
  }
  ```
  실행 흐름
  - T1: FooAsync() 시작 → "A"
  - T1: Task.Run 호출 → 작업을 스레드 풀에 큐잉
  - T1: await 도달 → T1 반환
  - Tpool: 스레드 풀의 T2가 HeavyCompute() 실행
  - 계산 완료
  - T3 (아무 스레드): FooAsync() 재개 → "B"
  - ❗ 재개 스레드는 T1일 수도, 아닐 수도 있음
