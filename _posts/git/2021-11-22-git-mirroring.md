---
layout: post
title: "다른 원격 저장소 나의 저장소에 미러링하기"
date: 2021-11-22
categories: git
---

레거시 시스템의 새로운 버전을 위해 기존 저장소에 있는 소스코드를 다른 저장소에 옮겨야 되는 경우가 발생한다. 그러한 경우 git의 미러링이라는 기능읕 통해서 기존의 소스코드를 다른 저장소 똑같이 옮길 수 있다. 해당 포스팅은 타 원격 저장소를 나의 원격 저장소로 미러링 하는 방법을 포스팅한다.

## 전제조건

* 로컬 pc에 git이 설치되어 있다.

* 원격 저장소는 bitbucket이라고 가정한다.

* 타 원격 저장소의 주소를 https://gyujinan@bitbucket.org/okestrolab/a-repo.git라고 가정한다.

* 나의 원격 저장소의 주소를 https://gyujinan@bitbucket.org/okestrolab/my-repo.git라고 가정한다.



깃 미러링

### 1. git clone --mirror https://gyujinan@bitbucket.org/okestrolab/a-repo.git

타 원격 저장소를 --mirror 옵션을 붙여서 클론한다. --mirror 옵션은 타 원격 저장소의 간략한 정보를 로컬pc로 클론 해준다.


```
# git clone --mirror https://gyujinan@bitbucket.org/okestrolab/a-repo.git
간략한 저장소로('a-repo.git') 복제합니다...
remote: Enumerating objects: 46556, done.
remote: Counting objects: 100% (46556/46556), done.
remote: Compressing objects: 100% (16345/16345), done.
remote: Total 46556 (delta 25961), reused 44686 (delta 24770), pack-reused 0
오브젝트를 받는 중: 100% (46556/46556), 137.30 MiB | 10.40 MiB/s, 완료.
델타를 알아내는 중: 100% (25961/25961), 완료.

```

### 2. cd a-repo.git; ls -al

타 원격 저장소로 이동한 후 클론 결과를 확인한다.


```
total 32
drwxr-xr-x  10 gyujinan  staff  320 11 29 12:11 .
drwxr-xr-x  14 gyujinan  staff  448 11 29 12:11 ..
-rw-r--r--   1 gyujinan  staff   23 11 29 12:11 HEAD
-rw-r--r--   1 gyujinan  staff  237 11 29 12:11 config
-rw-r--r--   1 gyujinan  staff   73 11 29 12:11 description
drwxr-xr-x  15 gyujinan  staff  480 11 29 12:11 hooks
drwxr-xr-x   3 gyujinan  staff   96 11 29 12:11 info
drwxr-xr-x   4 gyujinan  staff  128 11 29 12:11 objects
-rw-r--r--   1 gyujinan  staff  552 11 29 12:11 packed-refs
drwxr-xr-x   4 gyujinan  staff  128 11 29 12:11 refs
```

### 3. git remote set-url --push origin https://gyujinan@bitbucket.org/okestrolab/my-repo.git; git remote -v

타 원격 저장소의 origin의 url를 나의 원격 저장소의 url로 변경한다. 

```
# git remote set-url --push origin https://gyujinan@bitbucket.org/okestrolab/my-repo.git; git remote -v
origin	https://gyujinan@bitbucket.org/okestrolab/a-repo.git (fetch)
origin	https://gyujinan@bitbucket.org/okestrolab/my-repo.git (push)

```

### 4. git push --mirror

mirror된 간략 저장소를 나의 원격 저장소 push하여 올린다.

```
➜  flowerduet-test.git git:(master) git push --mirror
오브젝트 나열하는 중: 46556, 완료.
오브젝트 개수 세는 중: 100% (46556/46556), 완료.
Delta compression using up to 8 threads
오브젝트 압축하는 중: 100% (15154/15154), 완료.
오브젝트 쓰는 중: 100% (46556/46556), 137.30 MiB | 334.00 KiB/s, 완료.
Total 46556 (delta 25961), reused 46556 (delta 25961), pack-reused 0
remote: Resolving deltas: 100% (25961/25961), done.
To https://bitbucket.org/okestrolab/province-tax-cmp.git
 + ea87f6a1...816c8ddb master -> master (forced update)
 * [new branch]        develop -> develop
 * [new tag]           v1.0 -> v1.0

```
