---
layout: post
title: "다른 원격 저장소 나의 원격 저장소로 커밋하기"
date: 2021-11-11
categories: git
---

나의 원격저장소가 다른 원격저장소의 미러링한 결과일 경우, 다른 원격저장소의 업데이트 사항을 나의 원격저장소에 반영 할 필요가 있을 때가 있다. 해당 포스팅에서는 다른 원격저장소의 커밋내역을 나의 원격저장소에 반영하는 방법을 포스팅 한다.

## 전제조건

* 나의 원격저장소는 다른 원격저장소의 미러링한 결과이다.(미러링을 통해서 나의 원격저장소에 push를 하면 다른 원격 저장소에 있는 commit내역들이 모두 나의 원격저장소에 반영된다.)

* 커밋되는 과정에 일어나는 병합충돌은 해당 포스팅에서 다루지 않는다.(나의 원격저장소가 미러링을 한 결과가 아닌 경우, 병합충돌이 발생한다.)


### 1.git remote add {다른 원격저장소 명} {원격저장소 주소}

로컬저장소에 다른 원격저장소 주소를 참조 할 수 있도록 추가한다.

```

➜  flowerduet-test git:(develop) ✗ git remote add fd https://gyujinan@bitbucket.org/okestrolab/flowerduet.git

```

### 2.git remote -v

다른 원격저장소 주소가 등록되었는지 확인한다.

```

flowerduet-test git:(develop) ✗ git remote -v
fd	https://gyujinan@bitbucket.org/okestrolab/flowerduet.git (fetch)
fd	https://gyujinan@bitbucket.org/okestrolab/flowerduet.git (push)
origin	https://gyujinan@bitbucket.org/okestrolab/flowerduet-test.git (fetch)
origin	https://gyujinan@bitbucket.org/okestrolab/flowerduet-test.git (push)

```

### 3.git fetch {다른 원격저장소 명}

다른 원격저장소 상태(branch 와 commit내역)을 나의 로컬 저장소로 가지고 온다.

```

➜  flowerduet-test git:(develop) ✗ git fetch fd
remote: Enumerating objects: 574, done.
remote: Counting objects: 100% (275/275), done.
remote: Compressing objects: 100% (153/153), done.
remote: Total 574 (delta 166), reused 113 (delta 113), pack-reused 299
오브젝트를 받는 중: 100% (574/574), 181.75 KiB | 468.00 KiB/s, 완료.
델타를 알아내는 중: 100% (244/244), 로컬 오브젝트 56개 마침.
https://bitbucket.org/okestrolab/flowerduet URL에서
 * [새로운 브랜치]     develop                             -> fd/develop
 * [새로운 브랜치]     feature-gtpark-웹소켓-20110125   -> fd/feature-gtpark-웹소켓-20110125
 * [새로운 브랜치]     hotfix-ndfass-메뉴안뜨는문제-210514 -> fd/hotfix-ndfass-메뉴안뜨는문제-210514
 * [새로운 브랜치]     master                              -> fd/master
 * [새로운 브랜치]     ndfass_master                       -> fd/ndfass_master

```

### 4.git merge {다른 원격저장소 명}/{원격저장소 브랜치 명}

다른 원격저장소의 특정 브랜치를 나의 로컬 저장소의 특정 브런치로 merge시킨다.
```

➜  flowerduet-test git:(develop) ✗ git merge fd/develop 
Merge made by the 'recursive' strategy.
 AdminApp/frontend/src/assets/css/default.css                                                           |    4 +-
 AdminApp/frontend/src/views/layout/main/MainSidenav.vue                                                |    8 +-
 DashboardApp/frontend/src/assets/css/style.css                                                         |   44 +-
 DashboardApp/frontend/src/assets/images/ic_check.svg                                                   |    3 +
 DashboardApp/frontend/src/assets/images/ic_check_on.svg                                                |    6 +
 DashboardApp/frontend/src/assets/images/wt/bg-tw-arr-h.png                                             |  Bin 1127 -> 1200 bytes
 DashboardApp/frontend/src/assets/images/wt/bg-tw-arr.png                                               |  Bin 1101 -> 1225 bytes
 DashboardApp/frontend/src/assets/images/wt/btn-minusw-h.png                                            |  Bin 975 -> 956 bytes
 DashboardApp/frontend/src/assets/images/wt/btn-minusw.png                                              |  Bin 975 -> 956 bytes
 DashboardApp/frontend/src/assets/images/wt/btn-plusw-h.png                                             |  Bin 1019 -> 978 bytes
 DashboardApp/frontend/src/assets/images/wt/btn-plusw.png                                               |  Bin 1014 -> 974 bytes
 DashboardApp/frontend/src/assets/images/wt/ic_check.svg                                                |    6 +
 DashboardApp/frontend/src/assets/images/wt/ic_check_on.svg                                             |    6 +
 DashboardApp/frontend/src/assets/images/wt/ic_del_b.svg                                                |   10 +
 DashboardApp/frontend/src/assets/images/wt/ic_del_bh.svg                                               |   10 +
 DashboardApp/frontend/src/assets/images/wt/ic_del_r.svg                                                |   10 +
 DashboardApp/frontend/src/assets/images/wt/ic_del_rh.svg                                               |   10 +
 DashboardApp/frontend/src/assets/images/wt/ic_edit_b.svg                                               |    6 +
 DashboardApp/frontend/src/assets/images/wt/ic_edit_bh.svg                                              |    6 +
 DashboardApp/frontend/src/assets/images/wt/ic_edit_r.svg                                               |    6 +
 DashboardApp/frontend/src/assets/images/wt/ic_edit_rh.svg                                              |    6 +
 DashboardApp/frontend/src/assets/images/wt/ic_loader.svg                                               |    3 +
 DashboardApp/frontend/src/components/element/chart/DmChart.vue                                         |  326 +++---
...

```

### 5.git commit -m "{커밋 메세지}"

merge 내역에 문제가 없다고 판단되면 merge 내역을 커밋한다.

```

➜  flowerduet-test git:(develop) ✗ git commit -m "update"
[develop e4a5d59f] update

```

### 6.git push

로컬저장소에 다른 원격저장소의 업데이트 사항이 반영되었으므로 나의 원격저장소에 푸쉬한다.

```
➜  flowerduet-test git:(develop) git push
오브젝트 나열하는 중: 382, 완료.
오브젝트 개수 세는 중: 100% (269/269), 완료.
Delta compression using up to 8 threads
오브젝트 압축하는 중: 100% (129/129), 완료.
오브젝트 쓰는 중: 100% (195/195), 49.69 KiB | 9.94 MiB/s, 완료.
Total 195 (delta 109), reused 129 (delta 56), pack-reused 0
remote: Resolving deltas: 100% (109/109), completed with 41 local objects.
remote: 
remote: Create pull request for develop:
remote:   https://bitbucket.org/okestrolab/flowerduet-test/pull-requests/new?source=develop&t=1
remote: 
To https://bitbucket.org/okestrolab/flowerduet-test.git
   540968f4..e4a5d59f  develop -> develop

```
