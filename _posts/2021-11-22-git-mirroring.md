---
layout: post
title: "다른 원격 저장소 나의 저장소에 미러링하기"
date: 2021-11-22
categories: git
---

나의 원격저장소가 다른 원격저장소의 미러링한 결과일 경우, 다른 원격저장소의 업데이트 사항을 나의 원격저장소에 반영 할 필요가 있을 때가 있다. 해당 포스팅에서는 다른 원격저장소의 커밋내역을 나의 원격저장소에 반영하는 방법을 포스팅 한다.

## 전제조건

* 로컬 pc에 git이 설치되어 있다.

* 타 원격 저장소의 주소를 https://github.com/hello-bryan/HelloOpenCV.git라고 정의한다.

* 나의 원격 저장소의 주소를 https://github.com/hello-bryan/HelloOpenCV.git라고 정의한다.




깃 미러링

### 1.git clone --mirror https://github.com/hello-bryan/HelloOpenCV.git

### 2.cd HelloOpenCV.git

### 1.git remote set-url --push origin https://github.com/acsgoh/HelloNew.git

### 1.git push --mirror
