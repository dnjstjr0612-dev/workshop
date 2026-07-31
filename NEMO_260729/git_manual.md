# Git 초보자 매뉴얼

이 문서는 데스크탑과 노트북에서 `KO_prediction` 작업을 같은 상태로 이어가기 위한
Git 절차를 정리한다. 이 프로젝트의 GitHub 원격 저장소는 다음과 같다.

```text
https://github.com/dnjstjr0612-dev/Korea_ocean.git
```

가장 중요한 규칙은 **컴퓨터를 바꾸기 전에 commit과 push를 하고, 다른 컴퓨터에서
작업을 시작하기 전에 pull하는 것**이다. 두 컴퓨터에서 같은 파일을 동시에 수정하지
않는다.

데스크탑에서 명령을 실행할 때는 먼저 현재 저장소 폴더로 이동한다.

```powershell
cd C:\Users\dnjst\Documents\GitHub\Oceanography\KO_prediction
```

## 1. 꼭 알아둘 개념

- 작업 폴더(working tree): 현재 편집 중인 실제 파일이다.
- 스테이징 영역(staging area): 다음 커밋에 포함할 변경을 모아두는 곳이다.
- 커밋(commit): 스테이징한 변경의 로컬 이력이다.
- 원격 저장소(remote): GitHub처럼 커밋을 공유·백업하는 서버다.
- push: 로컬 커밋을 원격 저장소로 전송한다.

`git add`는 GitHub에 업로드하는 명령이 아니다. 변경을 다음 커밋 대상으로
선택할 뿐이다. 실제 원격 전송은 `git push`가 담당한다.

## 2. 가장 안전한 일상 작업 순서

### 변경 상태 확인

```powershell
git status --short
```

주요 표시는 다음과 같다.

- `??`: 아직 추적하지 않는 새 파일
- `M`: 수정된 파일
- `A`: 스테이징된 새 파일
- `D`: 삭제된 파일

### 변경 내용 검토

아직 스테이징하지 않은 변경을 확인한다.

```powershell
git diff
```

새 파일이나 특정 파일을 스테이징한다.

```powershell
git add docs/gate_commands.md
git add note/git_초보자_매뉴얼.md
```

현재 저장소의 모든 비무시 변경을 스테이징하려면 다음 명령을 사용한다.

```powershell
git add -A
```

전체 추가 전후에는 반드시 `git status --short`로 예상하지 못한 파일이 섞이지
않았는지 확인한다.

### 커밋 직전 검토

```powershell
git diff --cached
git diff --cached --stat
```

`--cached`는 이번 커밋에 실제로 들어갈 내용만 보여준다.

### 커밋

```powershell
git commit -m "docs: update Gate 5B research plan"
```

커밋 메시지는 변경의 목적을 짧게 적는다. 자주 쓰는 접두사는 다음과 같다.

- `docs:` 문서 변경
- `feat:` 기능 추가
- `fix:` 오류 수정
- `test:` 테스트 추가·수정
- `data:` 데이터 계약이나 추적 가능한 자료 변경
- `chore:` 설정·정리 작업

커밋 후 상태와 최근 이력을 확인한다.

```powershell
git status
git log --oneline -5
```

## 3. 이 저장소에서 추적하지 않는 파일

`.gitignore`는 다음 파일을 의도적으로 Git 추적 대상에서 제외한다.

- Python·Jupyter 캐시와 가상환경
- `.env` 같은 로컬 비밀정보 파일
- `data/raw/`
- `data/processed/`
- `data/results/`
- `figures/forecast_skill/`

이 디렉터리들은 다운로드 원본 또는 재현 가능한 생성 결과이므로 일반적인
`git add -A`로는 추가되지 않는다. 대신 생성 스크립트, 설정, checksum·manifest,
schema, QC 보고서를 추적해 재현성을 유지한다. 특히 `data/raw/`의 파일은 Git이
백업하지 않으므로 별도의 안전한 저장 위치가 필요하다.

따라서 현재 설정 그대로 GitHub에 push한 뒤 다른 컴퓨터에서 clone하면 코드·설정·
문서는 오지만 위 네 디렉터리의 실제 데이터와 결과는 오지 않는다. 완전히 같은 분석
상태를 이어가려면 아래의 "분석 데이터 동기화 정책"도 결정해야 한다.

무시 여부와 이유를 확인하려면 다음을 사용한다.

```powershell
git status --ignored --short
git check-ignore -v data/results/파일명.csv
```

`git add -f`는 ignore 정책을 우회하므로 사용하지 않는 것을 원칙으로 한다. 정말
추적해야 한다면 먼저 파일 크기, 재배포 권한, 개인정보·비밀정보 포함 여부를 검토하고
`.gitignore` 정책을 명시적으로 수정한다.

## 4. 실수했을 때 안전하게 되돌리기

스테이징만 취소하고 작업 파일은 그대로 두려면 다음을 사용한다.

```powershell
git restore --staged 경로/파일명
```

추적 중인 파일을 Git에서만 제외하고 로컬 파일은 보존하려면 다음을 사용한다.

```powershell
git rm --cached 경로/파일명
```

작업 파일의 변경 자체를 버리는 `git restore 경로/파일명`은 복구가 어려울 수 있다.
실행 전 반드시 `git diff`로 내용을 확인한다. `git reset --hard`와 강제 push는 초보
단계에서는 사용하지 않는다.

이미 공유한 커밋을 취소할 때는 이력을 덮어쓰기보다 새 되돌림 커밋을 만든다.

```powershell
git revert 커밋해시
```

## 5. 현재 데스크탑을 GitHub에 처음 연결하기

이 절차는 현재 데스크탑에서 한 번만 수행한다. 먼저 이 매뉴얼 수정본처럼 아직
커밋하지 않은 변경을 확인하고 커밋한다.

```powershell
git status --short
git diff -- note/git_초보자_매뉴얼.md
git add note/git_초보자_매뉴얼.md
git diff --cached --stat
git commit -m "docs: add multi-device Git workflow"
```

그다음 등록된 원격 저장소가 없는지 확인한다.

```powershell
git remote -v
```

아무것도 출력되지 않으면 다음 명령으로 `origin`을 등록하고 첫 커밋을 push한다.

```powershell
git remote add origin https://github.com/dnjstjr0612-dev/Korea_ocean.git
git remote -v
git push -u origin main
```

GitHub 저장소를 만들 때 README 등을 자동 생성했다면 원격에 별도 커밋이 있을 수
있다. 이 경우 push가 거부될 수 있으므로 `--force`를 사용하지 말고 먼저 도움을
요청한다.

이미 `origin`이 있다고 나오면 다시 `git remote add`하지 않는다. 주소를 확인한다.

```powershell
git remote get-url origin
```

주소가 잘못된 경우에만 다음 명령으로 교체한다.

```powershell
git remote set-url origin https://github.com/dnjstjr0612-dev/Korea_ocean.git
```

최초의 `git push -u origin main`이 성공한 뒤에는 이 데스크탑에서 보통 다음 명령만
사용하면 된다.

```powershell
git push
```

GitHub 비밀번호를 Git 명령이나 파일에 직접 적지 않는다. HTTPS 인증 창이 나타나면
Git Credential Manager의 브라우저 로그인을 사용한다.

## 6. 노트북에 처음 설치하기

노트북에 Git을 설치하고, 저장소를 둘 상위 폴더에서 다음을 실행한다. 마지막 인수로
`KO_prediction`을 지정하면 GitHub 저장소 이름과 무관하게 기존과 같은 폴더 이름을
사용할 수 있다.

```powershell
git clone https://github.com/dnjstjr0612-dev/Korea_ocean.git KO_prediction
cd KO_prediction
git status
git log --oneline -5
```
노트북에서 처음 Git을 사용한다면 커밋 작성자 정보를 확인한다.

```powershell
git config --global user.name
git config --global user.email
```

비어 있다면 본인의 이름과 GitHub에 사용할 이메일을 설정한다.

```powershell
git config --global user.name "본인 이름"
git config --global user.email "본인 이메일"
```

그다음 Python 환경을 준비하고 테스트한다.

```powershell
python -m unittest discover -s tests -v
```

현재 ignored 데이터가 필요한 테스트는 해당 데이터가 동기화되기 전까지 실패할 수
있다. 이는 Git 오류가 아니라 아래 데이터 동기화 정책의 문제다.

## 7. 데스크탑과 노트북을 오가는 일상 루틴

### 작업을 시작할 때

저장소 폴더에서 로컬 변경이 없는지 확인한 다음 원격 커밋을 받는다.

```powershell
git status
git pull --ff-only
```

`git pull --ff-only`는 예상하지 못한 자동 merge commit을 만들지 않고, 수동 판단이
필요한 상황에서 멈춘다. `git status`에 수정 파일이 표시되면 바로 pull하지 말고,
그 파일을 먼저 커밋할지 검토한다.

### 작업을 마치거나 다른 컴퓨터로 이동하기 전

```powershell
python -m unittest discover -s tests -v
git status --short
git diff
git add -A
git diff --cached --stat
git commit -m "작업 내용을 설명하는 메시지"
git push
git status
```

마지막 `git status`가 `working tree clean`인지 확인한 뒤 컴퓨터를 바꾼다.

작업이 덜 끝났더라도 다른 컴퓨터에서 이어가야 한다면 임시 상태임을 분명히 한
커밋을 만들어 push하는 편이, 커밋하지 않은 파일을 남기는 것보다 안전하다.

```powershell
git add -A
git commit -m "wip: continue Gate 5B design on another machine"
git push
```

다른 컴퓨터에서는 반드시 `git pull --ff-only` 후 이어간다. 같은 브랜치에서 두
컴퓨터가 동시에 서로 다른 커밋을 만들면 push가 거부되거나 충돌이 생길 수 있다.

## 8. 분석 데이터 동기화 정책

현재 `.gitignore`는 `data/raw`, `data/processed`, `data/results`,
`figures/forecast_skill`을 제외한다. 따라서 GitHub만으로 두 컴퓨터의 분석 상태를
완전히 동일하게 만들 수 없다.

현재 프로젝트 규모에서는 다음 정책이 가장 단순하다.

1. 공개·재배포 가능한 현재 raw snapshot과 재현에 필요한 processed/results를
   별도의 명시적 커밋으로 추적한다.
2. `.gitignore`에서 추적할 디렉터리 규칙을 먼저 수정한다.
3. 파일 크기, 개인정보·비밀정보, 데이터 재배포 조건을 확인한다.
4. 원본 raw 파일은 어느 컴퓨터에서도 수정·덮어쓰지 않는다.
5. 향후 결과가 크게 증가하면 Git LFS나 별도 데이터 저장소로 이전한다.

정책을 바꾼 뒤의 명령 형식은 다음과 같다. `.gitignore`를 수정하지 않은 상태에서
`git add -f`로 강제 추가하지 않는다.

먼저 `.gitignore`에서 다음 네 줄을 삭제하거나 줄 앞에 `#`을 붙여 비활성화한다.

```gitignore
data/raw/
data/processed/
data/results/
figures/forecast_skill/
```

그 후 스테이징과 검토를 수행한다.

```powershell
git add .gitignore data/raw data/processed data/results figures/forecast_skill
git diff --cached --stat
git commit -m "data: track reproducibility artifacts for multi-device work"
git push
```

이 변경은 데이터 정책을 바꾸므로 파일 범위와 크기를 검토한 뒤 별도 커밋으로
수행한다. 그 전까지는 ignored 디렉터리를 OneDrive 등으로 별도 복사해야 하지만,
동시에 두 동기화 체계를 쓰면 충돌하기 쉬우므로 Git 저장소 폴더 자체를 OneDrive로
실시간 동기화하는 방식은 권장하지 않는다.

## 9. 이 프로젝트의 권장 체크리스트

커밋할 때마다 다음을 확인한다.

1. `git status --short`로 변경 범위를 확인한다.
2. 원본 `data/oisst_month_sst.csv`와 `data/raw/`를 수정하지 않았는지 확인한다.
3. API key, token, 비밀번호, 개인정보가 없는지 확인한다.
4. 관련 테스트를 실행한다.
5. `git add` 후 `git diff --cached`를 검토한다.
6. 한 커밋에는 하나의 논리적 목적만 담는다.
7. 커밋 후 `git status`가 예상대로 깨끗한지 확인한다.
8. `git push`하고 GitHub에서 커밋을 확인한다.
9. 다른 컴퓨터로 이동하기 전에 `git status`가 clean인지 확인한다.

## 10. 도움을 요청할 때 같이 보여줄 정보

문제가 생기면 다음 명령의 출력과 하려던 작업을 함께 전달한다.

```powershell
git status
git log --oneline -5
git remote -v
```

비밀번호, token, `.env` 내용은 공유하지 않는다.
