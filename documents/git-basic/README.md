# Git 기본 명령어 알아보기 

***

## 깃/깃허브 소스 관리 기본 흐름

깃의 소스 코드 흐름은 다음과 같다. 

- `git init` 을 통해 깃 작업을 시작할 수 있다.

- `git clone` 을 통해서 원격 저장소에 있던 모든 내역들을 가져와서 깃 작업을 하는게 가능하다. 

- `git add` 를 통해서 현재의 작업을 스테이징 영역에 올리는게 가능하다. 

- `git commit` 을 통해서 스테이징 영역에 올라와 있는 작업 내역들을 지역 저장소에 저장하는게 가능하다. 

- `git push` 를 통해서 지역 저장소에 있는 내역들을 원격 저장소로 올리는게 가능하다. 

- `git pull` 을 통해서 원격 저장소에 있는 내역들을 지역 저장소로 가지고 오는게 가능하다.

***

## 환경 설정하기 

### 사용자 등록 하기 

다음 명령어를 통해서 깃을 사용하는 사용자의 정보를 지역 저장소에 등록하는게 가능하다. __(누가 이 작업을 하는지 알아야 하기 때문에 이 과정이 필요한 것.)__

```
git config user.name "사용자 이름"
git config user.email "이메일 주소" 
``` 

이 명령어는 현재 작업중인 지역 저장소에서 사용할 사용자의 정보를 말한다. 

global 하게 모든 저장소에서 사용하는 걸 원한다면 다음 명령어를 이용하자. 

```
git config --global user.name "사용자 이름"
git config --global user.email "이메일 주소"
```

### 깃 설정 파일 확인 

앞에서 사용자 정보를 git 에다 등록을 했는데 그러면 이런 정보는 어디로 저장되고 어떻게 볼 수 있을까? 

이 정보는 .git 이라는 폴더 안에 있는 config 파일을 통해서 저장되고 볼 수 있다. 

현재 프로젝트를 기준으로 `cat .git/config` 라는 명령어를 입력하면 config 파일 내역을 볼 수 있다.

```
$ cat .git/config

[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
[submodule]
        active = .
[remote "origin"]
        url = https://github.com/Youngerjesus/git-practice.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
        remote = origin
        merge = refs/heads/main
```

- 이 영역은 core 와 submodule, remote, branch 영역으로 나뉜다. 

- repositoryformatversion 은 현재 깃 저장소의 형식 및 버전을 식별하기 위해 내부적으로 사용하는 변수다. 

- filemode 는 깃 저장소에 포함된 파일 모드의 변경 감지 여부를 설장하는데 사용된다. __(정확한 설명을 알고 싶다면 따로 찾아봐야 할 듯)__

- bare 는 현재 깃 저장소의 사용 목적이 단순히 파일을 복사흐는 용도로 사용한다면 true 로 설정하면 되고 파일을 변경하는 작업을 할 것이라면 false 로 사용하면 된다. 

- logallrefupdates 는 깃 명령어를 통해 수행되는 작언 내역을 기록하는 reflog 를 활성화 할지를 결정하는 목적으로 사용된다. `git reflog` 명령을 통해서 이를 확인하는게 가능하다. 

- ignorecase 는 대소문자 구분 여부를 결정하는데 사용된다. 기본값은 true 로 대소문자를 구별하지 않는다. 

- precomposeunicode 는 맥OS 에서만 사용하는 설정이다. 맥은 Unicode 정규화 방식이 달라서 파일명이 한글인 경우에 인식하지 못하는 경우가 있는데 이 값을 true 로 설정하면 이 문제를 해결할 수 있다. 

***

## 파일 상태 확인하기

깃으로 관리하는 파일 상태를 이해하려면 깃 작업 트리 (working tree) 를 이해해야 한다. 

### 깃 작업 트리

깃은 먼저 관리하는 파일과 관리하지 않는 파일을 구별한다. __(그래서 관리하지 않을 파일을 .gitignore 에 등록하는 거구나.)__

관리하는 파일은 깃이 상태를 관리해주고 이는 .git 폴더안에서 관리된다. 

깃 작업 트리는 크게 3가지 영역이 존재한다. 

- 작업 디렉토리 (Working Directory)

  - 실제 작업중인 파일들이 존재하는 영역이다. 
  
  - 파일을 생성하거나 기존 파일을 수정한다면 이는 __작업 중인 파일을 말한다.__
  
- 스테이징 영역 (Staging Area) 

  - 작업 디렉토리에서 작업 중인 파일 중 깃이 추적하도록 하는 영역을 말한다. __(추적한다는 말이 정확하게 뭐지?)__
  
  - .git 폴더안에 index 파일에서 이런 추적중인 파일이 저장되어 있다. 
  
- 지역 저장소 (local Repository)
  
  - 스테이징 영역의 변경 내역들을 커밋이라는 하나의 단위로 묶어서 지역 저장소에 올리는 용도로 사용된다. __(스테이징 영역에 올린다는 건 결국 하나의 변경 단위인 커밋으로 묶일 변경 내역들을 올리는 영역이라고 생각한다.)__
  
### 깃으로 파일 상태 확인 

- Untracked 및 Tracked 상태 

  - 깃에서 관리하는 파일은 Untracked 상태와 Tracked 상태로 나뉜다. __(Tracked 상태는 Staging 영역에 있는 상태를 말한다.)__
  
  - 중요한 건 처음 파일을 만들 때는 Untracked 상태이지만 한번이라도 스테이징 영역에 올려서 Tracked 상태가 되면 이후에 커밋을 하고 수정을 해도 Untracked 상태가 되지는 않는다 라는 점이다. 
  
- Unmodified 및 Modified 상태

  - 한번이라도 스테이징 영역에 올라간 파일의 상태는 Unmodified 와 Modified 상태로 나뉜다. 
  
  - Modified 상태는 커밋을 할 수 있는 상태를 말한다.
  
***

## 커밋 이해하기 

`git log` 명령어로 현재 작업하는 브랜치에서 변경 내역들인 커밋 내역들을 보는게 가능하다. __(브랜치는 독립적인 개발 공간을 말한다.)__

깃에는 HEAD 라는 특별한 포인터가 있는데 HEAD 포인터는 브랜치의 최종 커밋을 가리킨다.  

`git log` 의 시용법과 여러 옵션들에 대해서 한번 알아보자. 

### git log 

```
$ git log 

commit 1f62a454039b6e2774da648f9dc824758c28b01b (HEAD -> main, origin/main, origin/HEAD)
Author: Jeongmin Yeo <yjh930919@gmail.com>
Date:   Sat Sep 25 00:33:31 2021 +0900

    Initial commit
```

- 먼저 commit 으로 되어 있는 부분은 커밋 체크섬으로 커밋을 식별하기 위한 고유의 값을 말한다. 

- Author 는 작업한 사람과 이메일을 말한다. 

- Date 는 작업한 일자를 말한다. 

- 밑에 메시지는 커밋 메시지를 말한다. 

### git log -p 
```
$ git log -p 

commit 1f62a454039b6e2774da648f9dc824758c28b01b (HEAD -> main, origin/main, origin/HEAD)
Author: Jeongmin Yeo <yjh930919@gmail.com>
Date:   Sat Sep 25 00:33:31 2021 +0900

    Initial commit

diff --git a/README.md b/README.md
new file mode 100644
index 0000000..d8763bd
--- /dev/null
+++ b/README.md
@@ -0,0 +1,2 @@
+# git-practice
+git 을 잘 쓰는 방법에 대해 정리한 레파지토리 입니다. 
```

- -p 옵션은 patch 를 말하고 변경 내역들에 대해 좀 더 자세하게 보여준다. 

### git log -[숫자]

git 커밋 내역을 숫자 만큼의 개수를 보여준다

### git log --pretty={option}

--pretty 옵션은 출력 내역을 이쁘게 보는 방식을 말한다. 

--pretty 옵션으로 oneline 으로 하면 말 그대로 한 줄씩 출력하게 되서 커밋 메시지들을 확인하는데 좋다. 

```
$ git log --pretty=oneline

1f62a454039b6e2774da648f9dc824758c28b01b (HEAD -> main, origin/main, origin/HEAD) Initial commit
```

### 커밋 수정하기 

작업을 하다보면 커밋 메시지를 잘못 작성하는 경우가 있다.

여기서는 마지막 커밋 메시지만 수정하는 방법을 살펴보겠다. 

이는 `git commit --amend` 명령어를 통해서 가능하다. 그러면 에디 창이 열린다.

또 다른 방법은 `git commit --amend -m "변경 메시지"` 를 통해서 가능하다. 


### 커밋 파일 추가 

이미 커밋을 하고나서 그 커밋에 파일을 추가하고 싶을 때 사용하는 방법을 소개한다. 

.gitignore 에 다음과 같이 입력하자. 

```text
# Runtime data
{파일이름}
```

그 후 `git add .gitignore` 을 한 후 `git commit --amend --no-edit` 명령어를 하면 .gitignore 에 올렸던 파일이 기존 커밋에 추가된다. 

