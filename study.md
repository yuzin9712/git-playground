- 해당 커밋에서 수정된 것만 보기
```
$ git show 6c8e2ba | grep "diff --git"
diff --git a/dcgan/main.py b/dcgan/main.py
diff --git a/fast_neural_style/download_saved_models.py b/fast_neural_style/download_saved_mo
dels.py
diff --git a/run_python_examples.sh b/run_python_examples.sh
diff --git a/word_language_model/main.py b/word_language_model/main.py
```

- 몇 개가 수정되었는가?
```
$ git show 6c8e2ba | grep "diff --git" | wc -l
4
```

- 실제 코드 수정 내역은 없지만, 다른 사람의 커밋이 우리 깃 프로젝트에 병합이 되었다
```
$ git show 976a903
Merge: 92df8fc 08f6dd5
Author: Taeung Song <treeze.taeung@gmail.com>
Date:   Fri Apr 23 14:36:56 2021 +0900

    Merge pull request #294 from gentlelinuxer/fix-mnist

    PR test

```

- --no-merges 라는 명령어 추가 
```
$ git shortlog -sn --no-merges
```

- 머지 제외 상위 5명 보기
```
$ git shortlog -sn --no-merges | nl | head -5
     1      63  Bryan Marcus McCann
     2      46  Soumith Chintala
     3      34  Adam Lerer
     4      23  soumith
     5      20  Rohan Varma
```

- 해당 폴더에서 merge 제외한 커밋만 확인한다.
```
$ git log --oneline --no-merges -- mnist/
33af28c Revert "Remove unused argument for test method. (#746)"
ddc2d56 Revert "added missing non-linearity"
2e43ba9 Revert "Correct typo in default value within help (#667)"
49ec0bd tweak running examples without cuda (#794)
59423c5 Delete unnecessary blank lines (#785)
80fcb66 Remove unused argument for test method. (#746)
dce9319 added missing non-linearity
6c51ca5 Correct typo in default value within help (#667)
0634306 Improving mnist example model (#658)
60108ed Fix formatting to comply with PEP8 (#654)
```

- 특정 날짜 기준 커밋 내역 보기
```
$ git log --oneline --after=2020-01-01 --before=2020-06-30
59caa16 Merge pull request #791 from seemethere/fix_transformer
13acec6 word_language_model: Fix Transformer init_weights
edfa9f2 Fix errors executing run_python_examples.sh "install_deps,run_all" (#787)
7016919 Update word-level LM arguments in README (#786)
59423c5 Delete unnecessary blank lines (#785)
a4b0aba fix the issue PR #799 left (#781)
```

- 6월 한달간 커밋 수
```
$ git log --oneline --after=2020-06-01 --before=2020-06-30 | wc -l
5
```

- 예전 것 부터 보기
```
$ git log --reverse
$ git log --reverse | head -1
```

- 계정 등록
```
$ git config --global user.email "본인 이메일"
$ git config --global user.name "본인 이름"
$ git config --global --unset credential.helper
$ git config -l
```
명령어 한번 더 입력하면 그냥 덮어쓰기 되니까 잘못입력했을 때 다시 하면 됨

- 편집기 설정
```
git config --global core.editor nano
sudo apt install -y nano
```

- 작업의 시작 = "브랜치 생성" 같은 폴더 안에서 다른 세상!!!!!!!
같은 폴더인데 전혀 다르게 사용이됨 ... 뭐야 신기해 어떻게 만든거지
add commit 까지 해줘야지 브랜치에 반영이 됩니다.
https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EA%B8%B0%EC%B4%88-%EC%88%98%EC%A0%95%ED%95%98%EA%B3%A0-%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90-%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0


```
$ git diff
```

- upstream 과 origin
upstream으로 되어있는건 팀 프로젝트..? - 다같이 작업하는 내용
내가 fork뜬 프로젝트를 origin으로 둔다.

```
$ git remote remove upstream
$ git remote add upstream https://github.com/taeung/pytorch-example.git
```

- rebase가 필요한 상황
오픈소스 프로젝트 = commit1 + commit2 + commit3 + commitC + commitD
나의 프로젝트 = commit1 + commit2 + commit3 + commitA + commitB

-> upstream에서 달라진 내용을 먼저 가져와야 함
git fetch upstream master = 가져와서 바로 merge 하지는 않음
일단 최신 내용을 가져오고 ....

pull = fetch + merge

pull  = fetch + merge  입니다.
fetch  는 최신 내용을 가져오기,
pull 은 최신 내용을 가져와서 현재 branch에 합치는 과정입니다.


```
# 공식 upstream 저장소에서 최신 commit history 가져옴
$ git fetch upstream master

# 최신 commit history 기준으로 베이스 갱신 (내 로컬에서만 된거니까 fork한 프로젝트에 반영해야함)
$ git rebase upstream/master
=> commit1 + commit2 + commit3 + commitC + commitD + commitA + commitB

이것을 나의 fork한 저장소에 적용해야 한다. (나만 쓰는 저장소다 내가 히스토리 관리 직접함)
그냥 push하면 안되고, force push 해야 함 (PR 자동 갱신됨!!!)
# Fork한 저장소도 수정하고 PR도 자동 갱신됨
$ git push --force origin fix-mnist
```

add 하고 나서 diff 하면 차이점에 잡히지 않음
git add 취소는 git reset하면 된다.

git log --oneline -5 == 커밋내역 5개 본다 
commit 삭제는 git reset --hard HEAD~1 == 맨 위에서부터 1개 삭제함 (취소가 아니라 삭제 하는 것임)
git reset --hard origin/fix-mnist (잘못 삭제했을 때 원상복구한다)

stash = 수정한걸 commit으로 남기기 전에 임시 저장하는 방법
이전 버그 상태를 재현하기 위해서 stash로 내가 수정한 내용을 잠깐 킵해두고 버그재현 후 stash pop해서 내가 적용한게 잘 되는ㄴ지 
확인할 수 있음!!!! 좋은 기능이구나!!

아예 원래대로 돌려놓음 = git checkout -- mnist/main.py

- stash랑 checkout이랑 무어싱 다른가?
공통 = 원상복구!
stash는 아예 날려버리는게 아니라 임시저장해두는 것

checkout = 히스토리 창구에서 가져온다..!!
