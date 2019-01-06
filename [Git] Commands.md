# Git Commands



#### linux 기본 명령어

* 현재 작업위치 확인 

  ```
  $ pwd
  ```

* 디렉토리 이동

  ```
  $ cd .. // 상위 디렉토리로 이동
  $ cd DIRECTORY_NAME
  ```

* 디렉토리 생성

  ```
  $ mkdir DIRECTORY_NAME
  ```

* 디렉토리 확인

  ```
  $ ls
  ```

* 파일 생성 및 편집기 실행 : 편집상태(i)에서 내용 입력 후, 입력모두 해제(ESC) 후, 파일 저장 및 종료(:wq)

  ```
  $ vim FILE_NAME
  
  // 1) 편집상태로 전환 : 'i' 입력
  // 2) 입력모드 해제 : ESC
  // 3) 파일 저장 및 종료 : ':wq' 입력
  ```

* 파일 내용 확인

  ```
  $ cat FILE_NAME
  ```



#### Git 명령어

* git 인증을 위한 환경설정

  ```
  $ git config --global user.name USER_NAME
  $ git config --global user.email EMAIL_ADDRESS
  ```

* 로컬 저장소 관리

  * 로컬 저장소 생성 : 로컬 저장소에서 자동으로 master 브랜치 생성

  ```
  $ git init
  ```

  * 로컬 저장소 상태 확인 

  ```
  $ git status
  ```

  * 기록하고자 하는 파일 추적

    Untracked files 를 Changes to be committed 로 전환

  ```
  $ git add FILE_NAME
  $ git add . // 해당 디렉토리 내 모든 파일 적용
  ```

  * 커밋 실행 

  ```
  $ git commit // 편집기 실행 시와 동일하게 커밋메시지 입력
  $ git commit -m "COMMIT_MESSAGE" // 커밋메시지 동시 입력
  $ git commit -a // git add + git commit 동시 수행
  $ git commit -a -m "COMMIT_MESSAGE"
  ```

* 브랜치 관리

  * 새로운 브랜치 생성

  ```
  $ git branch BRANCH_NAME
  $ git checkout -b BRANCH_NAME // 생성하면서 바로 전환
  ```

  * 작업할 브랜치 전환

  ```
  $ git checkout BRANCH_NAME
  ```

  * 브랜치 삭제

  ```
  $ git branch -d BRANCH_NAME
  ```

  * 다른 브랜치와 병합 (merge) 

  ```
  $ git merge BRANCH_NAME
  
  // merge coflict resolve 잘못 했을 때 돌아가는 방법
  $ git reset --hard <돌아가고 싶은 commit>
  // reset한 다음 다시 remote repo로 push
  $ git push --force REMOTE_REPO_NAME BRANCH_NAME
  ```

* GitHub 연동

  * git clone : 원격저장소의 모든 내용을 로컬 저장소로 복사

    git clone을 하면 자동으로 해당 원격저장소와 연결되면서 origin이라는 이름 부여

  ```
  $ git clone REMOTE_REPOSITORY_URL
  ```

  * git remote : 원격저장소와 로컬저장소 연결

  ```
  $ git remote -v // 연결된 원격저장소 확인
  $ git remote add NICKNAME REMOTE_REPOSITORY_URL
  // 관례상 fork해 온 원본 저장소의 NICKNAME을 'upstream'으로 지정한다
  ```

  * git push : 로컬저장소의 내용 및 변경사항을 원격저장소로 보냄(커밋메시지 포함)

  ```
  $ git push REMOTE_REPOSITORY_NICKNAME LOCAL_BRANCH_NAME
  $ git push --all
  ```

  * git pull : git remote 명령을 통해 연결된 원격저장소의 최신 내용을 로컬저장소로 가져오면서 병합

  ```
  $ git pull REMOTE_REPOSITORY_NICKNAME LOCAL_BRANCH_NAME
  $ git pull --rebase // upstream에서 업데이트를 한 경우
  ```

  * git fetch : 로컬저장소와 원격저장소의 변경사항이 다를 때(즉, 병합시 충돌이 발생할 때) 이를 비교 대조하고 git merge 명령어와 함께 최신 데이터를 반영하거나 충돌 문제 해결
  * git diff : 로컬 저장소 브랜치와 원격 저장소 브랜치의 차이점 확인



#### Reference

http://www.devpools.kr/2017/01/31/%EA%B0%9C%EB%B0%9C%EB%B0%94%EB%B3%B4%EB%93%A4-1%ED%99%94-git-back-to-the-future/