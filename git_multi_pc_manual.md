# 여러 컴퓨터에서 Git 사용하기

저장소: <https://github.com/dnjstjr0612-dev/workshop>

## 새 컴퓨터에서 처음 한 번

```powershell
cd 원하는\상위\폴더
git clone https://github.com/dnjstjr0612-dev/workshop.git
cd workshop
```

커밋 사용자 정보가 없다면 한 번 설정한다.

```powershell
git config --global user.name "GitHub 사용자명"
git config --global user.email "GitHub 등록 이메일"
```

## 작업을 시작할 때마다

다른 컴퓨터에서 올린 최신 변경사항부터 받는다.

```powershell
cd workshop
git status
git pull --rebase
```

## 작업을 마칠 때마다

변경 파일을 확인하고 커밋한 뒤 GitHub에 올린다.

```powershell
git status
git add .
git status
git commit -m "변경 내용 요약"
git pull --rebase
git push
```

## 핵심 규칙

1. 작업 시작 전 `pull`, 작업 종료 후 `push`한다.
2. 컴퓨터를 옮기기 전에 반드시 현재 컴퓨터에서 `push`한다.
3. 비밀번호, API 키, 개인정보, 대용량 데이터는 `add`하지 않는다.
4. 충돌이 발생하면 `push --force`하지 말고 충돌을 해결한 뒤 다시 커밋한다.

