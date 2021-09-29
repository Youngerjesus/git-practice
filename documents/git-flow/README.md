# git-flow 

https://techblog.woowahan.com/2553/

git-flow 는 브런치 전략이다. 

git-flow 가 어떻게 브런치를 관리하는지 알아보자. 

***

## Git Repository 구성 살펴보기 

Repository 는 다음과 같이 구성된다. 

- Upstream Remote Repository

- Origin Remote Repository 

- Local Repository 

Local Repository 는 내 컴퓨터에 있는 개인적인 작업공간으로 독립적으로 구성되어 있다. 

Origin Remote Repository 는 원격에 저장된 개인 작업공간으로 합치기 전에 원격으로 올리는 용도로 사용된다. 

Upstream Remote Repository 는 개발자들이 공유하는 소스 공간이다. 

***

## 작업을 할 때 지켜야할 서로 간의 약속

- 작업을 하기전에 JIRA 티켓을 생성하는 것

- 하나의 티켓은 하나의 커밋으로 하는 것 

- 커밋 그래프는 최대한 단순하게 가져가는 것 

- 서로 공유하는 브랜치의 커밋 그래프는 함부로 변경하지 않는 것 

- 리뷰어에게 리뷰를 꼭 받는 것

- 자신이 시작한 Pull Request 는 자신이 마무리 짓는 것. 코드 충돌은 스스로 해결하는 것.

***

## git-flow 전략 간단하게 살펴보기 

Git Flow 에는 5가지의 브런치를 각각의 목적에 맞게 사용한다. 이를 알아보자. 

- main

  - 제품으로 출시될 브런치
  
- develop 

  - 다음 출시 버전을 개발하는 브런치
  
- feature

  - 기능을 개발하는 브런치 __(내가 잘못 생각한 것 중 하나가 feature -> develop 브런치로만 머지를 생각했느데 feature 끼리도 머지가 충분히 할 수 있다. 상위 하위의 기능이 있을 것이므로)__
  
- release

  - 이번 출시 버전을 준비하는 브랜치. 출시하기 전에 버그를 고치는 용도로 사용한다. 
  
  - 이 브런치에선 버그를 고치는데 집중해서 커밋을 한다. 
  
- hotfix

  - 출시 버전에서 발생한 버그를 수정하는 브런치
  
  - Main 브런치에서 갑작스럽게 버그가 발생하면 이 브런치를 Main 에서 만들어서 버그를 고치고 이를 develop 브런치와 마스터 브랜치 모두 머지한다. 릴리즈 브런치에서는 머지하지 않는데 출시를 한 시점부터는 마스터 모델을 사용하면 되니까. 
 
***

## git-flow 를 어떻게 사용하면 될까? 

개발 프로세스의 전체 과정을 한번 다뤄보자. 

### 1. 티켓 처리하기 

feature 브런치를 만들거나 feature 브런치에서 세부 하위 기능을 만드는 경우를 말한다. 

여기서는 쉬운 이해를 위해서 구체적인 예시를 가지고 설명해보자. 

티켓 중 '로그인 레이아웃 생성' 이라는 티켓을 가지고 처리한다고 살펴보자. 

1. feature-user 브런치에서 작업 브런치인 feature/user/BFM_100-login-layout 를 만들어서 작업한다. 

  - `git checkout -b feature/user/BFM_100-login-layout` 
  
2. feature/user/BFM_100-login-layout 에서 실제 작업을 한다. (뚝딱뚝딱)

3. 작업 브런치에서 변경 사항을 커밋한다. 

  - `git commit -m "[BFM-100] 로그인 화면 레이아웃 생성"`
  
4. 만약 커밋이 불필요하게 여러 개로 나눠져있다면 squash 를 한다. (커밋 여러개를 합치는 작업을 말한다.)

  - `git rebase -i HEAD~2`
  
5. 작업 브랜치를 feature-user 에 rebase 한다. 

  - `git pull -rebase origin feature-user`
  
6. 작업 브런치를 origin 에 push 한다. 

  - `git push origin feature/user/BFM_100-login-layout`
  
7. Github 로 가서 feature/user/BFM_100-login-layout 을 feature-user 에 merge 하는 pull request 를 생성한다.

8. 같은 feature 를 개발하는 동료에게 가서 리뷰 승인을 요청하고 리뷰를 받는다. 이후 리뷰가 완료되면 Pull Request 를 merge 한다. 


__이 과정에서 4번은 의미있는 커밋들로 관리하기 위해서 squash 하는 것이다.__

__5번 같은 경우는 커밋 그래프를 굉장히 단순하게 가져가기 위해서 저렇게 한다.__

### 2, develop 변경 사항을 feature 로 가져오기

작업을 하다보면 develop 브런치에도 수정사항이 생기고 (새로운 작업이 추가되거나 버그를 고친 일이 있거나 하면)

이런 커밋들이 필요한 경우가 있다. 

그래서 현재 작업중인 feature-user 로 develop 의 커밋들을 가지고 올 필요가 있다. 

#### feature/user 브런치에 원격에 있는 origin/develop 브런치를 merge 하는 것 

- `(feature-user) $ git fetch origin`

- `(feature-user) $ git merge -no-ff develop`

#### merge 된 feature-user 브런치를 origin/develop 에 push 하는 것

- `(feature-user) $ git push origin feature-user`

### 3. 완료된 기능을 이번 출시 버전에 포함시키기 

feature-user 브런치의 모든 기능이 완료되었다면 이를 출시 버전에 포함시켜야 한다. 

이를 위해 develop 브런치로 머지해야한다. 

#### develop 브런치에 origin/feature-user 브런치를 merge 한다. 

- `(develop) $ git fetch origin`

- `(develop) $ git merge -no-ff origin/feature-user`

#### origin/feature 기능이 머지된 develop 을 origin 에 push 한다. 

- `(develop) $ git push origin develop`

### 4. QA 시작하기

이렇게 기능을 하나하나 작성되서 출시할 버전까지 완료되었다고 가정해보자. 

이제부터 출시 담당자가 해야할 일은 QA 를 진행하기 위해 release 브런치를 생성하고 origin 에 push 해서 release 브런치를 공유해야 한다. 

#### release-1.0.0 브랜치를 생성한다.

- `(develop) $ git fetch origin`

- `(develop) $ git checkout -b release-1.0.0 -track origin/develop`

#### release-1.0.0 브랜치를 origin push 한다. 

- `(release-1.0.0) $ git push origin release-1.0.0`

### 5. QA 수정하기

QA 환경에서 버그가 하나씩 발생할 수 있는데 이것들을 모두 해결해야지 출시를 할 수 있다.

버그들도 티켓으로 치므로 티켓들을 만들어서 처리하자.

#### 1. release 브런치에서 버그 티켓에 대한 브랜치를 생성한다.

- `(release-1.0.0) $ git checkout -b release-1.0.0/(BFM-101)-bug-login-id-max-length`

#### 2. 버그를 수정한다. (뚝딱뚝딱)

#### 3. 작업 브랜치에서 버그 수정 사항을 커밋한다. 

- `(release-1.0.0/(BFM-101)-bug-login-id-max-length) $ git commit -m "[BFM-101] 로그인 아이디 길이 제한 버그 수정"`

#### 4. 작업 브랜치를 origin 에 push 한다.

-  `(release-1.0.0/(BFM-101)-bug-login-id-max-length) $ git push`

#### 5. github 에서 해당 브런치를 release-1.0.0 브런치로 머지하기 위해 Pull Request 를 생성한다.

#### 6. 동료에게 승인 받은 후 자신의 Pull Request 를 merge 한다.

### 6. 앱 출시 

발생하는 버그를 모두 수정했다면 이제 출시하면 된다. 

release 브런치를 main 브런치와 develop 브런치에 모두 merge 하고 main 브런치에 태그를 달아두자. (언제든지 돌아갈 수 있도록 + 출시 버전을 기억하기 위해서)

#### 1. release 브랜치를 최신 상태로 갱신한다.

- `(release-1.0.0) $ git pull origin release-1.0.0`

#### 2. release 브런치를 develop 브런치로 머지한다. 

- `(release-1.0.0) $ git checkout develop`

- `(develop) $ git pull origin develop`

- `(develop) $ git merge -no-ff release-1.0.0`

#### 3. develop 브런치를 origin 에 push 한다. 

- `(develop) $ git push origin develop`

#### 4. release 브런치를 main 브런치에 merge 한다.

- `(develop) $ git checkout main`

- `(main) $ git pull origin main`

- `(main) $ git merge -no-ff release-1.0.0`

#### 5. 태그를 추가한다. 

- `(main) $ git tag 1.0.0`

#### 6. main 브런치와 1.0.0 태그를 origin 에 푸시한다. 

- `(main) $ git push origin main 1.0.0`



 
 
