# 커밋 조작하기 

***

## 다른 브랜치의 커밋을 현재 작업 브랜치에 추가하기: git cherry-pick

두 브랜치를 병합하는 대신 다른 브랜치의 커밋을 선택적으로 현재 브런치로 가지고 와야할 때 git cherry-pick 을 사용한다. 

다음과 같이 사용할 수 있다. 

`git cherry-pick "추가하려는 커밋 체크섬"`

git cherry-pick 을 이용하면 다음과 같이 작업하는게 가능해진다. 

배포한 후 결함이 발견되어서 얼른 수정되어야 한다. 

이 경우 결함을 수정하는 코드를 작성하고 커밋한다. 

그 후 배포와 관련되어 있는 브랜치에 해당 커밋을 가지고오면 된다. 

또 커밋을 모르고 다른 브런치에서 한 경우 그 커밋을 가직 올 수 있다. 

***

## 이전 커밋으로 작업 브랜치의 최종 커밋 변경하기: git reset

이미 생성된 커밋을 취소하고 이전 커밋으로 되돌아 가야 하는 일이 생길 수 있다.

이 경우 git reset 을 이용하면 된다. 다음과 같이 사용할 수 있다.

`git reset "이전 커밋 체크섬"`

***

## 변경 사항 되돌리는 커밋 생성하기: git revert

변경 사항을 되돌리는 git revert 와 이전 커밋으로 변경하는 git reset 과는 되게 유사하다. 

차이점은 git revert 의 경우 변경 사항을 되돌렸다는 커밋을 따로 생성한다는 점이다. 

즉 git reset 자체는 커밋 자체를 지우지만 git revert 는 되돌렸다는 커밋을 새로 생성한다.

***

## 브랜치 커밋 이력 재정렬하기: git rebase

기준 브런치에서 여러 브랜치를 생성해서 작업을 하고 병합을 하면 커밋이 너무 많아져서 한눈에 알아보기 힘들 수 있다. 

`git rebase <커밋을 가져올 브랜치>` 를 통해서 커밋을 정렬하면 현재 브런치로 커밋을 가져와서 정렬시킨다. 

이를 통해서 여러 Fast Forward 머지가 가능해지므로 이력이 깔끔하게 보인다.


***

## 커밋 메시지 변경하기 

가장 최근의 커밋 메시지를 변경할려면 다음과 같은 명령어를 사용하면 된다. 

`git commit --amend -m "변경할 커밋 메시지"`

가장 최근 커밋 말고 이전의 커밋까지 메시지를 변경하고 싶다면 다음과 같은 명령어를 이용하자 

`git rebase -i HEAD~[이전 커밋 숫자]` 

그러면 다음과 같은 창이 나온다. 

```
$ git rebase -i HEAD~3

pick 17a439d Git Commit Message Change 
pick 0414e61 Rebase Test 1
pick f88f67f ADD Commit Message Change 2

# Rebase 09727a1..f88f67f onto 09727a1 (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
``` 

- 여기서 pick 이라는 부분을 reword 로 바꿔주면 커밋 메시지를 변경할 수 있는 창이 열린다. __(추가로 맨 밑에 있는 pick 이 가장 최근의 커밋 메시지를 나타낸다.)__

- 만약 커밋 메시지를 변경하는 창이 안나온다면 다음과 같은 메시지를 입력하면 된다. `git rebase --edit-todo` 를 입력하면 편집 창이 나오고 수정한 다음 `:wq` 를 입력하면 그 커밋 메시지는 변경을 한 것이다. 이후 다음 메시지로 가고싶다면 `git rebase --continue` 를 입력하면 다음 창으로 넘어간다. 

- 이후 원격 저장소의 커밋 메시지까지 변경하고 싶다면 `git push -f` 명령어를 입력하면 된다. -f 옵션은 --force 를 나타낸다. 
