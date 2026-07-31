# Shell Programming 생존 매뉴얼

연세대 ACC 서버(`acc.yonsei.ac.kr`, 계정 `leewonseok`)에서 실제로 마주치는 상황을
기준으로 쓴 초보자용 셸 매뉴얼이다. 예제 경로와 에러 메시지는 대부분 실제 접속
기록에서 가져왔다.

**이 문서를 읽는 방법**: 처음부터 끝까지 읽지 말고, 1~5장을 먼저 익힌 뒤
막힐 때마다 18장(에러 사전)과 20장(치트시트)을 찾아본다.

**가장 중요한 세 가지 습관**

1. 명령을 실행하기 전에 `pwd`로 지금 어디에 있는지 확인한다.
2. 변수를 쓸 때는 항상 큰따옴표로 감싼다. `"$HOME"`, `"$CONDA_PREFIX"`
3. 파괴적인 명령(`rm`, `mv`, `>`) 전에는 `ls`로 대상을 먼저 눈으로 본다.

---

## 목차

| 장 | 내용 |
|---|---|
| 1 | 셸이란 무엇인가 / 프롬프트 읽기 |
| 2 | 서버 접속과 파일 전송 |
| 3 | 서버 파일시스템 지도 |
| 4 | 기본 명령 15개 |
| 5 | 경로와 와일드카드 |
| 6 | 권한과 소유권 |
| 7 | 리다이렉션과 파이프 |
| 8 | 변수와 따옴표 |
| 9 | 명령 이어쓰기와 줄바꿈 |
| 10 | vi 생존 가이드 |
| 11 | `.bashrc` / `.bash_profile` |
| 12 | 서버에서 conda 다루기 |
| 13 | 셸 스크립트 작성 |
| 14 | 텍스트 처리 도구 |
| 15 | 프로세스와 장시간 작업 |
| 16 | PBS 작업 스케줄러 |
| 17 | 디스크 용량 관리 |
| 18 | 에러 사전 |
| 19 | 안전 수칙 |
| 20 | 치트시트 |
| 21 | 2주 연습 계획 |

---

## 1. 셸이란 무엇인가 / 프롬프트 읽기

셸(shell)은 사람이 입력한 문장을 해석해서 프로그램을 실행해 주는 프로그램이다.
서버에서 쓰는 셸은 대부분 **bash**다. 로컬 윈도우의 PowerShell과는 문법이 다르니
섞어 쓰지 않는다.

### 프롬프트 해부

```text
(nemo_500_env) [leewonseok@acc00 ~]$
 └─ (1) ──┘  └─(2)─┘ └(3)┘ (4) (5)
```

| 번호 | 의미 |
|---|---|
| (1) | 현재 활성화된 conda 환경 이름. 아무것도 없으면 conda 환경 밖 |
| (2) | 로그인한 사용자 이름 |
| (3) | 접속한 호스트(노드) 이름 |
| (4) | 현재 디렉토리. `~`는 홈 디렉토리 `/home/leewonseok` |
| (5) | `$`는 일반 사용자, `#`이면 root(관리자) |

**행동명령** — 접속 직후 항상 이 세 줄로 현재 위치를 확인한다.

```bash
whoami          # 나는 누구인가
hostname        # 어느 노드에 있는가
pwd             # 어느 디렉토리에 있는가
```

### 명령의 구조

```bash
tar -xzf /data4/NEMO/nemo_500_env.tar.gz -C "$HOME/.conda/envs/nemo_500_env"
└─┬─┘ └┬─┘ └──────────┬──────────────┘ └┬┘ └──────────┬──────────────────┘
 명령  옵션          인자              옵션          옵션의 값
```

- 옵션은 `-` 하나 + 알파벳 한 글자(`-l`), 또는 `--` + 단어(`--version`)다.
- `-xzf`처럼 짧은 옵션은 붙여 쓸 수 있다(`-x -z -f`와 같다).
- 공백이 구분자다. **공백을 잘못 넣으면 완전히 다른 명령이 된다.**

실제로 겪은 실수:

```bash
cd. .                       # bash: cd.: command not found
```

`cd`와 `.` 사이에 공백이 없어서 셸이 `cd.`라는 이름의 명령을 찾으려 했다.
올바른 명령은 `cd ..`(상위 디렉토리로) 또는 `cd .`(제자리)다.

### 도움말 보는 법

```bash
man tar             # 매뉴얼 (q로 종료, /검색어로 검색, n으로 다음 결과)
tar --help | less   # 짧은 도움말
help cd             # bash 내장 명령의 도움말 (cd, export 등은 man이 없다)
type ls             # ls가 실제 무엇인지 (별칭인지 프로그램인지)
which python        # 실행 파일의 실제 경로
```

---

## 2. 서버 접속과 파일 전송

### 접속

```powershell
# 로컬 (Windows PowerShell)
ssh leewonseok@acc.yonsei.ac.kr
```

종료는 `exit` 또는 `Ctrl-D`.

### 비밀번호 없이 접속하기 (강력 추천)

매번 비밀번호를 치는 것은 시간 낭비이고, 스크립트 자동화도 막는다.

**행동명령** — 로컬 PowerShell에서 1회만 실행한다.

```powershell
# 1. 키가 없으면 생성 (이미 있으면 건너뛴다)
ssh-keygen -t ed25519 -C "dnjstjr0612@gmail.com"
# 저장 위치, 암호구절(passphrase)을 묻는다. 엔터 3번이면 기본값

# 2. 공개키를 서버에 등록
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh leewonseok@acc.yonsei.ac.kr "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"

# 3. 확인
ssh leewonseok@acc.yonsei.ac.kr
```

`~/.ssh/authorized_keys`의 권한이 600이 아니면 SSH가 키를 무시한다. 위 명령에
`chmod`가 포함된 이유다.

### 접속 별칭 만들기

로컬의 `C:\Users\dnjst\.ssh\config` 파일에 다음을 추가한다.

```text
Host acc
    HostName acc.yonsei.ac.kr
    User leewonseok
    ServerAliveInterval 60
    ServerAliveCountMax 5
```

이제 `ssh acc` 한 줄로 접속된다. `ServerAliveInterval`은 오래 놔둬도 연결이
끊기지 않게 해 준다.

### 파일 전송

```bash
# 로컬 → 서버 (파일 하나)
scp ./figures/gate5_map.png acc:/home/leewonseok/work/

# 서버 → 로컬
scp acc:/home/leewonseok/work/output.nc ./data/raw/

# 디렉토리 통째로 (-r)
scp -r ./src acc:/home/leewonseok/work/

# 대용량/재개 가능/변경분만 — rsync가 훨씬 낫다
rsync -avzP ./src/ acc:/home/leewonseok/work/src/
```

`rsync` 옵션 의미: `-a` 속성 유지, `-v` 진행 표시, `-z` 압축 전송, `-P` 중단 시
이어받기. **경로 끝의 `/`가 중요하다**: `./src/`는 "src 안의 내용을",
`./src`는 "src 디렉토리 자체를" 보낸다.

### VS Code로 서버 붙기

홈에 `.vscode-server` 디렉토리가 이미 있으니 전에 써 본 적이 있다.
VS Code에서 `Remote-SSH: Connect to Host` → `acc` 선택. 파일 편집은 이게 가장
편하지만, **명령 실행과 스크립트 이해는 터미널로 익혀야 한다.**

---

## 3. 서버 파일시스템 지도

리눅스는 드라이브 문자(`C:`) 없이 `/`(루트) 하나에서 모든 것이 뻗어 나간다.

내 서버의 주요 경로:

```text
/                                       루트
├── home/
│   └── leewonseok/                     ← 내 홈. $HOME, ~ 와 같다
│       ├── .bashrc                     셸 설정 (11장)
│       ├── .bash_profile               로그인 셸 설정
│       ├── .ssh/                       SSH 키
│       ├── .conda/envs/                ← 내가 만들 수 있는 conda 환경
│       │   ├── geo/
│       │   └── nemo_500_env/
│       ├── nwpacific/                  연구 작업 디렉토리
│       ├── git/                        코드 저장소
│       └── work/                       작업 디렉토리
├── data4/
│   └── NEMO/nemo_500_env.tar.gz        ← 남이 만들어 공유한 환경 압축본
└── usr/local/python/anaconda3/         ← 시스템 conda. root 소유, 쓰기 불가
    └── envs/{nemo420, nemo_asmlys}     읽고 쓸 수는 있지만 수정은 불가
```

**핵심 구분**

| 경로 | 소유자 | 내가 할 수 있는 일 |
|---|---|---|
| `/home/leewonseok` | 나 | 읽기/쓰기/삭제 전부 |
| `/data4/NEMO` | jhyun | 읽기(공유 설정에 따라) |
| `/usr/local/python/anaconda3` | root | 읽기·실행만. **설치 불가** |

**행동명령** — 어떤 디렉토리를 만나면 먼저 소유자와 권한을 확인한다.

```bash
ls -ld /usr/local/python/anaconda3
stat -c 'owner=%U group=%G perm=%A path=%n' /usr/local/python/anaconda3
test -w /usr/local/python/anaconda3 && echo "쓰기 가능" || echo "쓰기 불가"
```

---

## 4. 기본 명령 15개

이 15개로 서버 작업의 90%를 한다.

### 이동과 확인

```bash
pwd                     # 현재 디렉토리 출력
cd /data4/NEMO          # 절대경로로 이동
cd nwpacific            # 상대경로로 이동 (현재 위치 기준)
cd ..                   # 상위 디렉토리
cd ~                    # 홈으로. 그냥 cd 만 쳐도 홈
cd -                    # 직전 디렉토리로 되돌아가기 (아주 유용)
```

### 목록 보기

```bash
ls                      # 이름만
ls -l                   # 자세히 (권한, 소유자, 크기, 날짜)
ls -a                   # 숨김 파일(.으로 시작)까지
ls -h                   # 크기를 사람이 읽기 좋게 (1.2G)
ls -t                   # 수정 시각 순
ls -r                   # 역순

# 조합해서 쓴다. 가장 자주 쓰는 두 가지:
ls -alh                 # 전부 + 자세히 + 읽기 좋은 크기
ls -altr                # 전부 + 자세히 + 시간순 + 역순 → 최근 파일이 맨 아래
```

`ls -altr`은 "방금 무슨 파일이 생겼지?"를 확인할 때 최고다. 화면 맨 아래가
가장 최근 파일이므로 스크롤할 필요가 없다.

`ll`은 `ls -l`의 별칭(alias)이다. 서버마다 있을 수도, 없을 수도 있다.

### 내용 보기

```bash
cat file.txt            # 전체 출력 (짧은 파일만)
less file.txt           # 페이지 단위 (q 종료, / 검색, G 끝으로, g 처음으로)
head -20 file.txt       # 앞 20줄
tail -20 file.txt       # 뒤 20줄
tail -f run.log         # 파일이 커지는 것을 실시간으로 따라가기 (Ctrl-C 종료)
wc -l file.txt          # 줄 수 세기
```

`tail -f`는 계산 작업 로그를 지켜볼 때 필수다.

### 만들기, 복사, 이동, 삭제

```bash
mkdir mydir                             # 디렉토리 생성
mkdir -p work/nemo/run01/output         # 중간 경로까지 한번에 생성 (-p)
touch note.txt                          # 빈 파일 생성 / 수정시각 갱신

cp a.txt b.txt                          # 복사
cp -r src/ backup_src/                  # 디렉토리 복사 (-r)
cp -i a.txt b.txt                       # 덮어쓰기 전에 물어보기 (-i)

mv old.txt new.txt                      # 이름 변경
mv result.nc work/output/               # 이동

rm file.txt                             # 삭제 (휴지통 없음. 복구 불가)
rm -r mydir/                            # 디렉토리 삭제
rm -i *.tmp                             # 하나씩 확인하며 삭제
```

**`mkdir -p`는 서버 작업의 필수 습관이다.** 실제 겪은 에러:

```bash
tar -xzf /data4/NEMO/nemo_500_env.tar.gz -C ${HOME}/.conda/envs/nemo_500_env
# tar: /home/leewonseok/.conda/envs/nemo_500_env: Cannot open: No such file or directory
```

`tar -C`는 "이미 존재하는 디렉토리로 이동해서 풀어라"라는 뜻이다. 디렉토리를
만들어 주지 않는다. 올바른 순서:

```bash
mkdir -p "$HOME/.conda/envs/nemo_500_env"
tar -xzf /data4/NEMO/nemo_500_env.tar.gz -C "$HOME/.conda/envs/nemo_500_env"
```

### 검색

```bash
find ~/work -name "*.nc"                    # 이름으로 파일 찾기
find ~/work -name "*.nc" -mtime -7          # 최근 7일 내 수정된 것만
grep "ERROR" run.log                        # 파일 안에서 문자열 찾기
grep -rn "nemo" ~/.bashrc                   # 재귀(-r) + 줄번호(-n)
```

---

## 5. 경로와 와일드카드

### 절대경로 vs 상대경로

```bash
/home/leewonseok/work/out.nc    # 절대경로. 어디서 실행해도 같은 파일
work/out.nc                     # 상대경로. 현재 위치에 따라 달라진다
./run.sh                        # ./ 는 "현재 디렉토리의". 스크립트 실행 시 필수
../data/                        # .. 는 상위 디렉토리
~/work/                         # ~ 는 홈 디렉토리
```

**스크립트 안에서는 절대경로 또는 `$HOME` 기반 경로를 쓴다.** 상대경로는
스크립트를 어디서 실행하느냐에 따라 결과가 달라져서 디버깅이 지옥이 된다.

### 와일드카드(글로브)

셸이 명령을 실행하기 **전에** 파일 이름으로 펼쳐 준다.

| 패턴 | 의미 | 예 |
|---|---|---|
| `*` | 0글자 이상 아무거나 | `*.nc` → 모든 nc 파일 |
| `?` | 정확히 한 글자 | `run?.sh` → run1.sh, run2.sh |
| `[abc]` | 괄호 안 한 글자 | `data[123].txt` |
| `[0-9]` | 범위 | `out_[0-9][0-9].nc` |
| `{a,b}` | 목록 전개 (bash 확장) | `file.{nc,txt}` |

**행동명령** — 위험한 명령에 와일드카드를 쓰기 전에 `echo`나 `ls`로 먼저 확인한다.

```bash
echo rm -r work/tmp_*        # 실제로 지우기 전에 무엇이 지워질지 출력만
ls work/tmp_*                # 대상 확인
rm -r work/tmp_*             # 확인한 뒤에 실행
```

---

## 6. 권한과 소유권

`ls -l` 출력을 읽는 법:

```text
drwxrwsr-x 12 leewonseok leewonseok 4096 2026-04-06 14:38 geo
│└┬┘└┬┘└┬┘    └────┬────┘ └───┬────┘
│ │  │  │          소유자     그룹
│ │  │  └── others(그 외 모두)의 권한
│ │  └───── group(그룹)의 권한
│ └──────── user(소유자)의 권한
└────────── 타입: d=디렉토리, -=일반파일, l=심볼릭 링크
```

권한 문자: `r` 읽기(4), `w` 쓰기(2), `x` 실행(1). 디렉토리에서 `x`는
"들어갈 수 있음"을 뜻한다.

| 표기 | 숫자 | 의미 |
|---|---|---|
| `rwxr-xr-x` | 755 | 소유자 전부, 나머지 읽기·실행 (스크립트/디렉토리 기본) |
| `rw-r--r--` | 644 | 소유자 읽기·쓰기, 나머지 읽기만 (일반 파일 기본) |
| `rwx------` | 700 | 소유자만 (`.ssh` 디렉토리) |
| `rw-------` | 600 | 소유자만 읽기·쓰기 (SSH 키, `authorized_keys`) |

```bash
chmod +x run.sh          # 실행 권한 부여 (스크립트를 만들면 반드시)
chmod 755 run.sh         # 숫자로 지정
chmod 600 ~/.ssh/id_ed25519
chmod -R 755 mydir/      # 재귀 적용
```

### 쓰기 가능 여부 확인 습관

실제로 겪은 실패:

```bash
conda install -c conda-forge cmake
# EnvironmentNotWritableError: The current user does not have write permissions
#   to the target environment.
#   environment location: /usr/local/python/anaconda3
```

`base` 환경이 root 소유의 `/usr/local/python/anaconda3`라서 설치가 불가능했다.
**패키지를 설치하기 전에 항상 대상 환경의 쓰기 권한을 확인한다.**

```bash
echo "$CONDA_PREFIX"
test -w "$CONDA_PREFIX" && echo "환경 수정 가능" || echo "환경 수정 불가"
```

---

## 7. 리다이렉션과 파이프

명령의 입출력은 세 갈래다: stdin(0), stdout(1), stderr(2).

```bash
command > out.txt        # stdout을 파일로. 기존 내용 삭제 후 덮어쓰기
command >> out.txt       # stdout을 파일 끝에 추가
command 2> err.txt       # stderr만 파일로
command > all.txt 2>&1   # stdout과 stderr를 함께 한 파일로
command &> all.txt       # 위와 같음 (bash 축약)
command < input.txt      # 파일을 stdin으로 넣기
command > /dev/null 2>&1 # 출력 전부 버리기
```

**`>`는 즉시 파일을 비운다.** `command > important.txt`에서 command가 실패해도
important.txt는 이미 빈 파일이 되어 있다. 기존 파일에는 `>>`를 쓰는 습관을 들인다.

### 파이프

앞 명령의 stdout을 뒤 명령의 stdin으로 넘긴다. 셸의 가장 강력한 기능이다.

```bash
tar -tzf /data4/NEMO/nemo_500_env.tar.gz | head -20
# 574MB 압축 파일의 내용 목록 중 앞 20줄만 본다. 압축을 풀지 않고 미리보기

conda list | grep -E '^(cmake|make|hdf5|netcdf-fortran)([[:space:]]|$)'
# 설치된 패키지 목록에서 필요한 것만 걸러낸다

qstat | grep leewonseok
# 전체 작업 목록에서 내 작업만

ls -1 *.nc | wc -l
# nc 파일 개수 세기

du -sh ~/* | sort -h | tail -10
# 홈 디렉토리에서 용량 큰 항목 10개
```

**행동명령** — 압축을 풀기 전에 항상 내용을 먼저 확인한다.

```bash
ls -lh /data4/NEMO/nemo_500_env.tar.gz          # 크기 확인
tar -tzf /data4/NEMO/nemo_500_env.tar.gz | head # 구조 확인
df -h "$HOME"                                   # 남은 용량 확인
```

압축본이 `bin/`, `lib/` 로 시작하면 "지정한 디렉토리 안에" 풀린다는 뜻이고,
`nemo_500_env/bin/` 처럼 최상위 폴더가 있으면 그 폴더가 통째로 생긴다는 뜻이다.
`-C` 대상을 정할 때 이 차이가 결정적이다.

---

## 8. 변수와 따옴표

### 변수

```bash
name="nemo_500_env"          # = 좌우에 공백을 넣으면 안 된다
echo "$name"                 # 사용할 때는 $ 를 붙인다
echo "${name}_backup"        # 뒤에 문자가 붙으면 중괄호로 감싼다
```

`name = "x"`는 에러다. `name`이라는 명령을 실행하려고 시도한다.

### 중요한 환경 변수

```bash
echo "$HOME"          # /home/leewonseok
echo "$USER"          # leewonseok
echo "$PWD"           # 현재 디렉토리
echo "$PATH"          # 실행 파일을 찾는 경로 목록 (: 로 구분)
echo "$CONDA_PREFIX"  # 현재 활성 conda 환경 경로
echo "$?"             # 직전 명령의 종료 코드 (0=성공, 그 외=실패)

export MY_VAR="value"  # 자식 프로세스에도 전달되도록 내보내기
env | sort | less      # 모든 환경 변수 보기
```

`$?`는 디버깅의 핵심이다.

```bash
tar -xzf missing.tar.gz
echo "$?"     # 2 → 실패했다
```

### 따옴표 — 가장 많이 틀리는 부분

| 표기 | 동작 |
|---|---|
| `"$var"` | 변수는 펼쳐지고, 공백은 하나의 값으로 보호된다. **기본값으로 쓴다** |
| `'$var'` | 아무것도 펼쳐지지 않는다. `$var` 글자 그대로 |
| `$var` | 변수는 펼쳐지지만 공백에서 쪼개진다. **위험** |

```bash
dir="my data"          # 공백이 들어간 이름
ls $dir                # ls my data  → "my"와 "data" 두 개를 찾는다. 실패
ls "$dir"              # ls "my data" → 정상
```

이 프로젝트 폴더에 `for multi-device work` 같은 공백 포함 이름이 실제로 있다.
**변수는 무조건 큰따옴표로 감싼다**고 외워 두면 이 부류의 버그를 통째로 피한다.

### 명령 치환

```bash
today=$(date +%Y%m%d)
echo "$today"                       # 20260729

outdir="$HOME/work/run_$(date +%Y%m%d_%H%M)"
mkdir -p "$outdir"
echo "결과는 $outdir 에 저장된다"

nfiles=$(ls -1 *.nc | wc -l)
echo "nc 파일 $nfiles 개"
```

`` `명령` ``(백틱)도 같은 기능이지만 중첩이 안 되고 읽기 어렵다. `$(...)`만 쓴다.

---

## 9. 명령 이어쓰기와 줄바꿈

### 여러 명령 연결

```bash
cmd1 ; cmd2       # cmd1 실행 후 무조건 cmd2
cmd1 && cmd2      # cmd1이 성공(종료코드 0)했을 때만 cmd2
cmd1 || cmd2      # cmd1이 실패했을 때만 cmd2
```

`&&`가 실전에서 제일 유용하다.

```bash
mkdir -p "$HOME/.conda/envs/nemo_500_env" \
  && tar -xzf /data4/NEMO/nemo_500_env.tar.gz -C "$HOME/.conda/envs/nemo_500_env" \
  && echo "압축 해제 성공"
```

디렉토리 생성이 실패하면 tar를 시도조차 하지 않는다.

### 긴 명령 줄바꿈 — 백슬래시 규칙

역슬래시 `\`는 "줄바꿈을 무시하고 다음 줄에 이어 붙이라"는 뜻이다.

**규칙: `\`는 그 줄의 맨 마지막 글자여야 한다. 뒤에 공백조차 있으면 안 된다.**

두 번째 줄부터는 `>` 프롬프트가 뜬다. 이 `>`는 셸이 출력하는 것이지 내가 치는 게
아니다.

실제로 겪은 실패:

```bash
tar -xzf \
  /data4/NEMO/nemo_500_env.tar.gz \
  .-C "$HOME/.conda/envs/nemo_500_env"
# tar: .-C: Not found in archive
```

`-C` 앞에 점(`.`)이 붙어서 `.-C`가 옵션이 아닌 "압축 파일 안에서 찾을 파일 이름"으로
해석됐다. 오타 하나로 명령의 의미가 완전히 달라진다.

바로 다음에 점을 뺐더니 성공했다.

```bash
tar -xzf \
  /data4/NEMO/nemo_500_env.tar.gz \
  -C "$HOME/.conda/envs/nemo_500_env"
```

**초보 단계에서는 줄바꿈을 쓰지 말고 한 줄로 쓰거나, 아예 스크립트 파일로
만들어서 편집기로 확인하는 편이 안전하다.**

### 잘못 입력한 명령 취소

`>` 프롬프트에 갇혔거나 명령이 이상해졌을 때:

- `Ctrl-C` : 입력 중인 명령 취소, 실행 중인 프로그램 중단
- `Ctrl-U` : 커서 앞 전부 지우기
- `Ctrl-A` / `Ctrl-E` : 줄의 처음 / 끝으로 이동
- `Ctrl-L` : 화면 지우기 (`clear`와 같음)
- `Ctrl-R` : 과거 명령 검색 (치다 만 문자열로 히스토리 뒤지기)
- `↑` `↓` : 이전 명령 불러오기

`Ctrl-R`은 익혀 두면 타이핑이 절반으로 준다.

---

## 10. vi 생존 가이드

서버에서는 vi(vim)를 피할 수 없다. 최소한 다음은 알아야 한다.

vi는 **모드**가 있다. 이게 초보자를 가장 혼란스럽게 한다.

```text
[일반 모드] ── i ──→ [입력 모드]
     ↑                    │
     └──── Esc ───────────┘
```

| 상황 | 키 |
|---|---|
| 파일 열기 | `vi ~/.bashrc` |
| 글자 입력 시작 | `i` (커서 위치), `a` (커서 다음), `o` (아래 새 줄) |
| 입력 끝내기 | `Esc` |
| 저장하고 종료 | `Esc` 누른 뒤 `:wq` 엔터 |
| **저장하지 않고 종료** | `Esc` 누른 뒤 `:q!` 엔터 |
| 한 줄 삭제 | 일반 모드에서 `dd` |
| 실행 취소 | 일반 모드에서 `u` |
| 검색 | `/검색어` 엔터, `n`으로 다음 |
| 줄 번호 표시 | `:set number` |
| N번째 줄로 이동 | `:14` |

**제일 중요한 것은 `:q!`다.** 파일을 망가뜨렸다 싶으면 저장하지 말고 빠져나온
다음 다시 연다.

### 실제로 벌어진 사고

현재 서버의 `~/.bashrc` 24번째 줄이 이렇게 되어 있다.

```text
unset __conda_setupspecific aliases and functions
```

원래는 두 줄이어야 한다.

```bash
unset __conda_setup
# User specific aliases and functions
```

vi에서 편집하다가 줄 사이의 개행이 지워져 두 줄이 하나로 붙었고, 주석 기호 `#`도
사라졌다. 지금은 `unset`이 존재하지 않는 변수 이름을 지우려 할 뿐이라 큰 사고는
아니지만, 이런 식으로 붙은 줄이 `export PATH=...` 근처였다면 로그인 자체가
망가진다.

**교훈: 설정 파일은 반드시 백업하고 편집한다.** (11장)

---

## 11. `.bashrc` / `.bash_profile`

### 무엇이 언제 읽히나

| 파일 | 실행 시점 |
|---|---|
| `~/.bash_profile` | **로그인** 셸 시작 시 (ssh 접속) |
| `~/.bashrc` | **비로그인** 셸 시작 시 (스크립트, 새 터미널, 계산 노드 작업) |

관례상 `.bash_profile` 안에서 `.bashrc`를 불러오도록 해 둔다. 그래야 두 경로에서
같은 환경이 된다.

```bash
# ~/.bash_profile 안에 이 부분이 있는지 확인
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi
```

확인:

```bash
grep -n "bashrc" ~/.bash_profile
```

**PBS 작업 스크립트는 비로그인 셸로 돌기 때문에 `.bashrc`만 읽는다.**
그래서 conda 초기화 블록이 `.bashrc`에 있어야 계산 노드에서도 환경이 활성화된다.

### 안전한 편집 절차 (반드시 이 순서로)

```bash
# 1. 백업 — 날짜를 붙여서
cp ~/.bashrc ~/.bashrc.bak.$(date +%Y%m%d_%H%M)

# 2. 편집
vi ~/.bashrc

# 3. 문법 검사 — 실행하지 않고 문법만 본다 (매우 중요)
bash -n ~/.bashrc && echo "문법 OK"

# 4. 적용
source ~/.bashrc

# 5. 망가졌으면 복구
cp ~/.bashrc.bak.20260729_1100 ~/.bashrc
```

`bash -n`은 `.bashrc`를 실행하지 않고 문법만 검사한다. 로그인 불가 사태를 막는
안전장치다.

### 지금 내 `.bashrc`에서 고칠 것

현재 상태:

```text
13:# !! Contents within this block are managed by 'conda init' !!
14:__conda_setup="$('/usr/local/python/anaconda3/bin/conda' 'shell.zsh' 'hook' 2> /dev/null)"
...
24:unset __conda_setupspecific aliases and functions
```

두 가지 문제가 있다.

1. 14번 줄이 `shell.zsh`다. bash를 쓰는데 zsh용 초기화 코드를 받아 오고 있다.
   `shell.bash`가 맞다.
2. 24번 줄이 두 줄이 붙어 버렸다.

**행동명령** — 백업 후 다음처럼 고친다.

```bash
cp ~/.bashrc ~/.bashrc.bak.$(date +%Y%m%d_%H%M)
vi ~/.bashrc
```

14번 줄의 `'shell.zsh'`를 `'shell.bash'`로, 24번 줄을 다음 두 줄로 나눈다.

```bash
unset __conda_setup
# User specific aliases and functions
```

그리고:

```bash
bash -n ~/.bashrc && echo "문법 OK"
source ~/.bashrc
conda env list
```

`conda init bash`를 다시 실행해서 블록 전체를 새로 쓰게 하는 방법도 있지만,
직접 고치는 편이 무엇이 바뀌는지 눈으로 볼 수 있어 학습에 낫다.

### 편리한 별칭 추가

`.bashrc` 맨 아래에 붙여 두면 매일 쓰는 명령이 짧아진다.

```bash
# ---- 사용자 설정 ----
alias ll='ls -alF'
alias lt='ls -altr'
alias ..='cd ..'
alias ...='cd ../..'
alias rm='rm -i'          # 삭제 전 확인 (초보 단계에서 강력 추천)
alias cp='cp -i'
alias mv='mv -i'
alias df='df -h'
alias du='du -h'
alias qs='qstat -u leewonseok'
alias grep='grep --color=auto'

# 자주 가는 곳
alias cdw='cd $HOME/work'
alias cdn='cd $HOME/nwpacific'
alias cdd='cd /data4/NEMO'

# conda 환경 빠른 활성화
alias nemo='source $HOME/.conda/envs/nemo_500_env/bin/activate'
```

`alias rm='rm -i'`는 습관이 붙기 전까지 켜 두기를 권한다. 스크립트 안에서는
별칭이 적용되지 않으니 자동화에는 영향이 없다.

---

## 12. 서버에서 conda 다루기

### 왜 `base`에 설치할 수 없나

```bash
conda env list
# geo          /home/leewonseok/.conda/envs/geo          ← 내 것. 수정 가능
# base      *  /usr/local/python/anaconda3               ← root 것. 수정 불가
# nemo420      /usr/local/python/anaconda3/envs/nemo420  ← root 것. 수정 불가
```

`base`는 시스템 전체가 공유하는 환경이라 root만 건드릴 수 있다. `conda install`이
`EnvironmentNotWritableError`로 실패한 이유다.

**규칙: 서버에서는 항상 내 홈 아래(`~/.conda/envs/`)에 내 환경을 만들어 쓴다.**

```bash
# 새로 만들기
conda create -n myenv python=3.11 -y
conda activate myenv

# 기존 환경을 내 것으로 복제해서 수정하기
conda create -n nemo_mine --clone nemo_asmlys
conda activate nemo_mine
conda install -c conda-forge cmake
```

### conda-pack으로 만든 환경 풀기 (지금 하고 있는 작업)

`/data4/NEMO/nemo_500_env.tar.gz`는 `conda-pack`으로 만든 환경 압축본이다.
일반 conda 환경과 달리 **압축을 푼 뒤 `conda-unpack`을 반드시 실행해야 한다.**

풀린 환경 안의 스크립트들은 원래 만들어진 컴퓨터의 경로(`/home/conda/feedstock_root/...`)를
그대로 박아 두고 있다. `conda-unpack`이 이 경로들을 지금 위치로 고쳐 쓴다.

**전체 절차 (행동명령)**

```bash
# 0. 사전 확인
ls -lh /data4/NEMO/nemo_500_env.tar.gz
tar -tzf /data4/NEMO/nemo_500_env.tar.gz | head -5
df -h "$HOME"                       # 574MB 압축 → 풀면 수 GB. 여유 확인

# 1. 대상 디렉토리 생성 (tar가 만들어 주지 않는다)
mkdir -p "$HOME/.conda/envs/nemo_500_env"

# 2. 압축 해제
tar -xzf /data4/NEMO/nemo_500_env.tar.gz -C "$HOME/.conda/envs/nemo_500_env"

# 3. 확인
ls "$HOME/.conda/envs/nemo_500_env"     # bin, lib, share ... 가 보이면 성공

# 4. 활성화 (conda activate가 아니라 activate 스크립트를 source 한다)
source "$HOME/.conda/envs/nemo_500_env/bin/activate"

# 5. ★ 경로 재작성 — 이 단계를 빠뜨리면 나중에 이상한 에러가 난다
conda-unpack

# 6. 검증
echo "$CONDA_PREFIX"
which python cmake mpirun
nc-config --prefix                       # 여기가 내 홈 경로로 바뀌어야 정상
```

6단계에서 `nc-config --prefix`가 여전히 `/home/conda/feedstock_root/...`를
가리킨다면 `conda-unpack`이 실행되지 않았거나 실패한 것이다.

`conda-unpack`은 **환경당 한 번만** 실행한다. 두 번 실행하면 에러가 난다.

### 활성화 / 비활성화

```bash
conda activate geo                                   # conda init이 된 환경에서
source "$HOME/.conda/envs/nemo_500_env/bin/activate" # conda-pack 환경
conda deactivate                                     # 한 단계 빠져나오기
```

`(base)`에서 `conda deactivate`를 한 번 더 하면 conda 밖으로 완전히 나간다.
프롬프트에 아무 환경 표시가 없어진다.

### 환경 상태 점검 3종

```bash
echo "$CONDA_PREFIX"                     # 지금 어느 환경인가
which python && python --version         # 어느 python이 잡히는가
test -w "$CONDA_PREFIX" && echo "수정 가능" || echo "수정 불가"
```

---

## 13. 셸 스크립트 작성

한 번 이상 반복할 명령은 스크립트로 만든다. 기록이 남고, 재현이 되고, 오타가
줄어든다.

### 첫 스크립트

**행동명령**

```bash
mkdir -p "$HOME/work/scripts"
vi "$HOME/work/scripts/hello.sh"
```

내용:

```bash
#!/bin/bash
set -euo pipefail

echo "안녕하세요, $USER 님"
echo "현재 노드: $(hostname)"
echo "현재 시각: $(date '+%Y-%m-%d %H:%M:%S')"
echo "홈 용량:"
df -h "$HOME"
```

실행:

```bash
chmod +x "$HOME/work/scripts/hello.sh"
"$HOME/work/scripts/hello.sh"
```

### 반드시 넣어야 할 두 줄

```bash
#!/bin/bash              # shebang. 이 파일을 bash로 실행하라
set -euo pipefail        # 안전장치
```

`set -euo pipefail`의 의미:

| 옵션 | 효과 |
|---|---|
| `-e` | 명령이 하나라도 실패하면 즉시 중단 |
| `-u` | 정의되지 않은 변수를 쓰면 에러 |
| `-o pipefail` | 파이프 중간이 실패해도 전체를 실패로 처리 |

이게 없으면 스크립트가 중간에 실패해도 계속 진행해서, 빈 파일이나 잘못된 결과를
만들어 놓고 "성공"으로 끝난다. **계산 결과를 다루는 스크립트에서는 필수다.**

### 조건문

```bash
if [ -f "$file" ]; then
    echo "파일이 있다"
elif [ -d "$file" ]; then
    echo "디렉토리다"
else
    echo "없다"
fi
```

자주 쓰는 검사:

| 검사 | 의미 |
|---|---|
| `-f "$p"` | 일반 파일로 존재 |
| `-d "$p"` | 디렉토리로 존재 |
| `-e "$p"` | 무엇이든 존재 |
| `-s "$p"` | 존재하고 크기가 0이 아님 |
| `-w "$p"` | 쓰기 가능 |
| `-z "$s"` | 문자열이 비었음 |
| `-n "$s"` | 문자열이 비어 있지 않음 |
| `"$a" = "$b"` | 문자열이 같음 |
| `"$a" -eq "$b"` | 숫자가 같음 (`-ne -lt -le -gt -ge`) |

`[` 뒤와 `]` 앞에 **공백이 반드시 있어야 한다**. `[-f "$file"]`은 에러다.

### 반복문

```bash
# 목록 순회
for f in ~/work/data/*.nc; do
    echo "처리 중: $f"
    ncdump -h "$f" | head -5
done

# 숫자 범위
for i in {1..12}; do
    printf "month %02d\n" "$i"
done

# 파일을 한 줄씩 읽기
while IFS= read -r line; do
    echo "줄: $line"
done < filelist.txt
```

`while read`에서 `IFS=`와 `-r`은 공백과 백슬래시를 그대로 읽기 위한 관용구다.
외워서 쓴다.

### 인자 받기

```bash
#!/bin/bash
set -euo pipefail

if [ "$#" -lt 2 ]; then
    echo "사용법: $0 <입력디렉토리> <출력디렉토리>" >&2
    exit 1
fi

indir="$1"
outdir="$2"

echo "입력: $indir"
echo "출력: $outdir"
```

| 변수 | 의미 |
|---|---|
| `$0` | 스크립트 이름 |
| `$1 $2 ...` | 첫 번째, 두 번째 인자 |
| `$#` | 인자 개수 |
| `$@` | 전체 인자 (`"$@"`로 쓴다) |
| `$?` | 직전 명령의 종료 코드 |
| `$$` | 현재 프로세스 ID |

`>&2`는 "이 메시지를 stderr로 보내라"는 뜻이다. 에러 메시지는 stderr로 보내야
정상 출력과 섞이지 않는다.

### 함수

```bash
log() {
    echo "[$(date '+%H:%M:%S')] $*"
}

check_file() {
    local path="$1"
    if [ ! -f "$path" ]; then
        log "에러: $path 없음"
        return 1
    fi
    log "확인: $path"
    return 0
}

log "시작"
check_file "$HOME/work/input.nc" || exit 1
log "완료"
```

`local`은 함수 안에서만 쓰이는 변수를 만든다. 안 쓰면 전역 변수를 덮어써서
디버깅이 어려워진다.

### 실전 예제: NEMO 환경 준비 스크립트

`$HOME/work/scripts/setup_nemo_env.sh`

```bash
#!/bin/bash
set -euo pipefail

# NEMO 5.0 conda-pack 환경을 홈에 설치한다.
# 사용법: ./setup_nemo_env.sh

SRC="/data4/NEMO/nemo_500_env.tar.gz"
DEST="$HOME/.conda/envs/nemo_500_env"

log() { echo "[$(date '+%H:%M:%S')] $*"; }

# 1. 원본 확인
if [ ! -f "$SRC" ]; then
    echo "에러: $SRC 를 찾을 수 없다" >&2
    exit 1
fi
log "원본 확인: $(ls -lh "$SRC" | awk '{print $5}')"

# 2. 이미 설치되어 있으면 중단 (덮어쓰기 사고 방지)
if [ -d "$DEST" ] && [ -n "$(ls -A "$DEST" 2>/dev/null)" ]; then
    echo "에러: $DEST 가 이미 비어 있지 않다. 확인 후 직접 지워라" >&2
    exit 1
fi

# 3. 용량 확인
log "홈 디스크 상태:"
df -h "$HOME"

# 4. 설치
log "디렉토리 생성"
mkdir -p "$DEST"

log "압축 해제 (몇 분 걸린다)"
tar -xzf "$SRC" -C "$DEST"

# 5. 경로 재작성
log "환경 활성화 및 conda-unpack"
# shellcheck disable=SC1091
source "$DEST/bin/activate"
conda-unpack

# 6. 검증
log "검증"
echo "  CONDA_PREFIX = $CONDA_PREFIX"
echo "  python       = $(which python)"
echo "  cmake        = $(which cmake)"
echo "  netcdf prefix= $(nc-config --prefix)"

log "완료. 앞으로는 다음 명령으로 활성화한다:"
echo "  source $DEST/bin/activate"
```

### 스크립트 디버깅

```bash
bash -n script.sh       # 실행 없이 문법만 검사
bash -x script.sh       # 각 줄을 실행 전에 출력 (무엇이 실제로 실행됐는지 보기)
set -x                  # 스크립트 중간부터 추적 켜기
set +x                  # 끄기
```

`bash -x`는 "왜 이 명령이 이상하게 동작하지?"를 풀 때 가장 빠른 길이다.
변수가 어떤 값으로 펼쳐졌는지 그대로 보여 준다.

---

## 14. 텍스트 처리 도구

관측 자료, 로그, 설정 파일을 다루는 데 매일 쓴다.

### grep — 찾기

```bash
grep "ERROR" run.log                    # 문자열 포함 줄
grep -i "error" run.log                 # 대소문자 무시
grep -n "ERROR" run.log                 # 줄 번호 표시
grep -c "ERROR" run.log                 # 개수만
grep -v "DEBUG" run.log                 # 포함하지 않는 줄 (제외)
grep -r "nemo" ~/work                   # 디렉토리 재귀 검색
grep -A 3 -B 1 "ERROR" run.log          # 매치 앞 1줄, 뒤 3줄까지 함께
grep -E "^(cmake|make|hdf5)" list.txt   # 확장 정규식 (-E)
```

정규식 기본:

| 기호 | 의미 |
|---|---|
| `^` | 줄의 시작 |
| `$` | 줄의 끝 |
| `.` | 아무 글자 하나 |
| `.*` | 아무거나 0개 이상 |
| `[0-9]` | 숫자 하나 |
| `\|` | 또는 (`-E`에서는 `\|` 대신 `|`) |

### sed — 치환

```bash
sed 's/old/new/' file.txt          # 각 줄의 첫 번째만 치환 (출력만, 파일 안 바뀜)
sed 's/old/new/g' file.txt         # 각 줄의 전부 (g = global)
sed -i 's/old/new/g' file.txt      # 파일을 직접 수정 (-i). 위험. 백업하고 쓴다
sed -i.bak 's/old/new/g' file.txt  # .bak 백업을 남기면서 수정
sed -n '10,20p' file.txt           # 10~20번째 줄만 출력
sed '/^#/d' file.txt               # # 로 시작하는 줄 삭제
```

**`sed -i`를 쓰기 전에 반드시 `-i` 없이 먼저 실행해서 결과를 눈으로 확인한다.**

```bash
sed 's/jpo=1/jpo=4/g' namelist_cfg          # 먼저 확인
sed -i.bak 's/jpo=1/jpo=4/g' namelist_cfg   # 확인했으면 적용
```

### awk — 열 다루기

```bash
awk '{print $1}' file.txt                    # 첫 번째 열
awk '{print $1, $3}' file.txt                # 1, 3번째 열
awk -F',' '{print $2}' data.csv              # 쉼표 구분 (-F)
awk 'NR > 1 {print $2}' data.csv             # 헤더(1줄) 건너뛰기
awk '$3 > 20 {print $0}' data.txt            # 3번째 열이 20 초과인 줄
awk '{sum += $2} END {print sum/NR}' data.txt # 2번째 열 평균
```

`NR`은 현재 줄 번호, `NF`는 열 개수, `$0`은 줄 전체다.

실전 예:

```bash
# qstat에서 내 작업의 ID만 뽑기
qstat | awk '$3 == "leewonseok" {print $1}'

# du 결과에서 1GB 넘는 것만
du -h --max-depth=1 ~/ | awk '$1 ~ /G$/ {print}'
```

### 기타

```bash
cut -d',' -f2,3 data.csv         # 쉼표 구분 2,3번째 필드
sort file.txt                    # 정렬
sort -n file.txt                 # 숫자 정렬
sort -h                          # 1K, 2M, 3G 같은 크기 정렬
sort -u file.txt                 # 정렬 + 중복 제거
uniq -c sorted.txt               # 중복 개수 세기 (미리 sort 필요)
tr 'a-z' 'A-Z' < file.txt        # 문자 치환
paste a.txt b.txt                # 두 파일을 열로 나란히
diff a.txt b.txt                 # 두 파일 차이
```

조합 예시 — 로그에서 가장 많이 나온 에러 5종:

```bash
grep "ERROR" run.log | awk '{print $4}' | sort | uniq -c | sort -rn | head -5
```

### find + xargs

```bash
find ~/work -name "*.nc" -size +1G                    # 1GB 넘는 nc 파일
find ~/work -name "*.tmp" -mtime +30 -delete          # 30일 지난 임시 파일 삭제
find ~/work -name "*.nc" | xargs -I{} ncdump -h {}    # 찾은 파일마다 명령 실행

# 공백 포함 파일 이름에 안전한 형태 (권장)
find ~/work -name "*.nc" -print0 | xargs -0 -I{} ls -lh {}
```

`-delete`를 쓰기 전에 항상 `-delete` 없이 먼저 실행해서 목록을 확인한다.

---

## 15. 프로세스와 장시간 작업

### SSH가 끊기면 작업도 죽는다

이게 서버 초보자가 가장 크게 데는 부분이다. 터미널을 닫거나 네트워크가 끊기면
그 터미널에서 돌던 프로그램도 함께 죽는다. 12시간 계산이 11시간째에 날아간다.

해결책은 두 가지: **tmux/screen** 또는 **작업 스케줄러(16장)**.

### tmux — 끊겨도 살아남는 세션

```bash
tmux new -s nemo          # nemo 라는 이름의 세션 생성
# ... 작업 실행 ...
# Ctrl-b 를 누르고 손을 뗀 뒤 d  → 분리(detach). 작업은 계속 돈다

# 나중에, 다른 컴퓨터에서 접속해도
tmux ls                   # 세션 목록
tmux attach -t nemo       # 다시 붙기
```

주요 키 (모두 `Ctrl-b`를 먼저 누르고 손을 뗀 뒤):

| 키 | 동작 |
|---|---|
| `d` | 분리 |
| `c` | 새 창 |
| `n` / `p` | 다음 / 이전 창 |
| `%` | 세로 분할 |
| `"` | 가로 분할 |
| `방향키` | 분할된 창 사이 이동 |
| `[` | 스크롤 모드 (`q`로 종료) |

tmux가 없으면 `screen`을 쓴다 (`screen -S nemo`, `Ctrl-a d`, `screen -r nemo`).

**행동명령: 5분 넘게 걸릴 작업은 무조건 tmux 안에서 시작한다.**

### 백그라운드 실행

```bash
./long_job.sh &                                  # 백그라운드로 실행
jobs                                             # 백그라운드 작업 목록
fg %1                                            # 전면으로 가져오기
bg %1                                            # 후면으로 보내기

nohup ./long_job.sh > run.log 2>&1 &             # 로그아웃해도 계속 실행
echo "$!"                                        # 방금 띄운 프로세스의 PID
```

`nohup ... &`는 tmux보다 단순하지만 나중에 진행 상황을 보려면 로그 파일에
의존해야 한다. 로그는 `tail -f run.log`로 본다.

### 프로세스 관리

```bash
ps aux | grep leewonseok        # 내 프로세스
ps -u leewonseok                # 같은 의미, 더 간단
top                             # 실시간 (q 종료, u 입력 후 사용자명으로 필터)
htop                            # 있으면 top보다 훨씬 보기 좋다

kill 12345                      # 정상 종료 신호 (PID 12345)
kill -9 12345                   # 강제 종료 (최후 수단)
pkill -u leewonseok python      # 이름으로 종료
```

**공용 서버에서 `kill`은 신중하게.** PID를 잘못 보면 남의 작업을 죽인다.
`ps aux | grep`으로 대상이 정말 내 것인지 사용자 이름을 확인하고 실행한다.

### 자원 사용 확인

```bash
free -h              # 메모리
nproc                # CPU 코어 수
uptime               # 부하 (load average)
df -h                # 디스크
```

공용 노드에서 무거운 계산을 로그인 노드(`acc00`)에서 직접 돌리면 다른 사람에게
피해가 간다. **무거운 계산은 반드시 작업 스케줄러로 제출한다.**

---

## 16. PBS 작업 스케줄러

ACC 서버는 PBS/Torque를 쓴다(`qstat` 출력의 `22372.acc00` 형식과 `batch` 큐로
확인된다). 계산 노드에 작업을 맡기는 시스템이다.

### 상태 확인

```bash
qstat                        # 전체 작업 목록
qstat -u leewonseok          # 내 작업만
qstat -f 22386               # 특정 작업 상세 정보
qstat -q                     # 큐 목록과 상태
pbsnodes -a | head -40       # 노드 상태 (코어 수 확인에 유용)
```

`qstat` 출력의 `S` 열이 상태다.

| 상태 | 의미 |
|---|---|
| `Q` | 대기 중 (queued) |
| `R` | 실행 중 (running) |
| `C` | 완료 (completed) |
| `E` | 종료 처리 중 |
| `H` | 보류 (held) |

### 작업 스크립트 템플릿

`$HOME/work/scripts/job_nemo.pbs`

```bash
#!/bin/bash
#PBS -N nemo_test              # 작업 이름
#PBS -q batch                  # 큐 이름
#PBS -l nodes=1:ppn=16         # 노드 1개, 노드당 코어 16개
#PBS -l walltime=24:00:00      # 최대 실행 시간 (넘으면 강제 종료)
#PBS -j oe                     # 표준출력과 에러를 한 파일로
#PBS -o /home/leewonseok/work/logs/nemo_test.log
#PBS -M dnjstjr0612@gmail.com  # 알림 메일 주소
#PBS -m ae                     # a=중단 시, b=시작 시, e=종료 시 메일

set -euo pipefail

# PBS는 홈에서 시작한다. 제출한 디렉토리로 이동해야 한다
cd "$PBS_O_WORKDIR"

echo "=== 작업 시작 $(date) ==="
echo "노드: $(hostname)"
echo "작업 ID: $PBS_JOBID"
echo "작업 디렉토리: $PWD"

# 환경 활성화 (PBS는 .bashrc를 읽는 비로그인 셸이다)
source "$HOME/.conda/envs/nemo_500_env/bin/activate"

# 코어 수를 PBS가 준 노드 파일에서 계산
NCORE=$(wc -l < "$PBS_NODEFILE")
echo "할당된 코어: $NCORE"

# 실제 계산
mpirun -np "$NCORE" ./nemo

echo "=== 작업 종료 $(date) ==="
```

**주의**: `nodes=1:ppn=16`은 Torque 문법이다. 서버마다 다를 수 있으니 처음에는
**연구실 선배의 기존 `.pbs` 파일을 복사해서 이름만 바꿔 쓰는 것이 가장 안전하다.**
`qstat -f`로 남의 작업이 어떤 자원을 요청했는지 볼 수도 있다.

### 제출과 관리

```bash
mkdir -p "$HOME/work/logs"

qsub job_nemo.pbs             # 제출. 작업 ID를 출력한다
qstat -u leewonseok           # 확인
qdel 22390                    # 취소
tail -f ~/work/logs/nemo_test.log   # 로그 실시간 확인

# 짧은 테스트를 대화형으로 (디버깅에 유용)
qsub -I -l nodes=1:ppn=4 -l walltime=01:00:00
```

### PBS 환경 변수

| 변수 | 의미 |
|---|---|
| `$PBS_O_WORKDIR` | qsub을 실행한 디렉토리 |
| `$PBS_JOBID` | 작업 ID |
| `$PBS_JOBNAME` | 작업 이름 |
| `$PBS_NODEFILE` | 할당된 노드 목록 파일 |
| `$PBS_O_HOST` | 제출한 호스트 |

**제출 전 체크리스트**

- [ ] `bash -n job_nemo.pbs`로 문법 확인했나
- [ ] 로그 출력 디렉토리(`~/work/logs`)를 미리 만들었나
- [ ] `walltime`이 실제 필요 시간보다 넉넉한가 (짧으면 중간에 강제 종료)
- [ ] 출력 파일이 저장될 디스크에 여유가 있나 (`df -h`)
- [ ] 먼저 walltime 10분짜리 소규모 테스트를 돌려 봤나

마지막 항목이 가장 중요하다. **48시간 작업을 바로 던지지 말고, 항상 짧은
테스트부터 통과시킨다.**

---

## 17. 디스크 용량 관리

홈 파일시스템이 이미 94% 찼다(`36T` 중 `2.3T` 남음). 공용 자원이므로 관리가
필요하다.

```bash
df -h "$HOME"                        # 파일시스템 전체 사용률
du -sh ~/                            # 내 홈 전체 크기 (오래 걸린다)
du -h --max-depth=1 ~/ | sort -h     # 홈 바로 아래 항목별 크기, 정렬
du -sh ~/* | sort -h | tail -10      # 큰 것 10개
quota -s                             # 내 할당량 (출력이 없으면 할당량 미설정)
```

**행동명령** — 큰 작업 전에 항상 확인한다.

```bash
df -h "$HOME" && du -sh "$HOME/.conda" "$HOME/work" "$HOME/nwpacific"
```

### 정리 대상

```bash
conda clean --all                     # conda 캐시 (수 GB 나온다)
rm -rf ~/.cache/pip                   # pip 캐시
find ~/work -name "core.*" -delete    # 코어 덤프 파일
find ~/ -name "*.log" -mtime +90 -size +100M   # 오래된 대용량 로그 (확인 후 삭제)
```

### 압축

```bash
tar -czf backup.tar.gz mydir/         # 압축 생성 (c=create, z=gzip, f=file)
tar -tzf backup.tar.gz | head         # 내용 확인 (t=list)
tar -xzf backup.tar.gz                # 현재 위치에 해제 (x=extract)
tar -xzf backup.tar.gz -C /target/dir # 지정한 (이미 존재하는) 디렉토리에 해제

gzip big.nc                           # big.nc.gz 생성, 원본 삭제
gunzip big.nc.gz                      # 되돌리기
```

`tar` 옵션 외우는 법: **c**reate, e**x**tract, lis**t** 중 하나 + **z**(gzip) +
**f**(파일 이름 지정). `f`는 항상 마지막에 오고 바로 뒤에 파일 이름이 온다.

---

## 18. 에러 사전

실제로 만난 것 + 앞으로 만날 것.

### `No such file or directory`

```text
tar: /home/leewonseok/.conda/envs/nemo_500_env: Cannot open: No such file or directory
```

경로가 없다. 원인 세 가지 중 하나다.

1. 디렉토리를 안 만들었다 → `mkdir -p "경로"`
2. 오타 → `ls -ld "경로"` 로 확인, `Tab` 자동완성 활용
3. 상대경로인데 다른 디렉토리에 있다 → `pwd`로 확인

**진단**

```bash
ls -ld "$HOME/.conda/envs"      # 부모 디렉토리는 있나
echo "$HOME"                    # 변수가 제대로 펼쳐지나
```

### `command not found`

```text
bash: cd.: command not found...
```

1. 오타 또는 공백 누락 (`cd.` → `cd .`)
2. 프로그램이 설치되지 않았다
3. 설치됐지만 `PATH`에 없다 (conda 환경을 활성화하지 않았다)

**진단**

```bash
which cmake                    # 어디에 있나
echo "$PATH" | tr ':' '\n'     # PATH를 줄 단위로 보기
echo "$CONDA_PREFIX"           # 환경이 활성화됐나
```

### `Permission denied`

```text
EnvironmentNotWritableError: The current user does not have write permissions
```

권한이 없다.

```bash
ls -ld "대상경로"
test -w "대상경로" && echo "쓰기 가능" || echo "쓰기 불가"
```

내 홈(`/home/leewonseok`) 아래로 대상을 옮기면 대부분 해결된다.
스크립트에 실행 권한이 없어서 나는 경우는 `chmod +x script.sh`.

### `Not found in archive`

```text
tar: .-C: Not found in archive
```

옵션에 오타가 있어서 옵션이 아니라 "압축 파일 안에서 찾을 파일 이름"으로
해석됐다. 줄바꿈 `\` 뒤에 이어지는 줄의 첫 글자를 확인한다.

### `The environment is inconsistent`

conda가 환경의 의존성이 꼬였다고 경고한다. 남의 `base` 환경이라면 내가 고칠
수 없고 고치려 해서도 안 된다. **내 환경을 새로 만든다.**

```bash
conda create -n mine --clone nemo_asmlys
```

### 종료 코드로 진단

```bash
어떤명령
echo "종료 코드: $?"
```

| 코드 | 의미 |
|---|---|
| 0 | 성공 |
| 1 | 일반 실패 |
| 2 | 사용법 오류 / 파일 없음 |
| 126 | 실행 권한 없음 |
| 127 | 명령을 찾을 수 없음 |
| 130 | Ctrl-C로 중단됨 |

### 막혔을 때의 일반 절차

```bash
pwd                          # 1. 어디에 있나
ls -al                       # 2. 무엇이 있나
echo "$CONDA_PREFIX"         # 3. 어떤 환경인가
which 문제의명령              # 4. 실행되는 것이 진짜 그것인가
bash -x 문제의스크립트.sh      # 5. 실제로 무엇이 실행되나
```

---

## 19. 안전 수칙

공용 서버는 실수 한 번의 대가가 크다.

### 절대 하지 말 것

```bash
rm -rf /              # 시스템 파괴
rm -rf ~              # 내 모든 파일 삭제
rm -rf $VAR/          # $VAR가 비어 있으면 rm -rf / 가 된다 (★)
chmod -R 777 ~        # 모든 사람에게 내 파일 쓰기 권한 부여
sudo 아무거나          # 권한이 없기도 하고, 있어도 쓰지 않는다
```

`rm -rf "$VAR"/`에서 `$VAR`가 비어 있으면 `rm -rf /`가 된다. 이것 하나만으로
연구실이 마비된 사례가 세계적으로 많다. **스크립트에 `set -u`를 넣는 진짜
이유가 이것이다** — 정의되지 않은 변수를 쓰면 실행 전에 멈춰 준다.

### 습관으로 만들 것

1. **삭제 전 `ls`** — `rm` 대신 같은 인자로 `ls`를 먼저 쳐 본다.
2. **`.bashrc` 편집 전 백업** — `cp ~/.bashrc ~/.bashrc.bak.$(date +%Y%m%d)`
3. **`sed -i` 전에 `-i` 없이** — 결과를 눈으로 본 뒤 적용한다.
4. **큰 작업 전 `df -h`** — 디스크가 가득 차면 계산 결과가 깨진 채 저장된다.
5. **스크립트 맨 위에 `set -euo pipefail`**
6. **변수는 항상 `"$var"`**
7. **긴 작업은 tmux 또는 qsub**
8. **로그인 노드에서 무거운 계산 금지** — 다른 사람이 접속조차 못 하게 된다.
9. **남의 디렉토리에 쓰지 않기** — `/data4/NEMO`는 jhyun의 것이다.
10. **중요한 결과는 로컬로 복사** — 서버는 백업되지 않을 수 있다.

### 홈 디렉토리 구조 제안

```bash
mkdir -p "$HOME"/work/{scripts,logs,run,output,tmp}
```

```text
~/work/
├── scripts/    재사용할 셸 스크립트와 PBS 작업 파일
├── logs/       작업 로그 (PBS -o 출력 위치)
├── run/        실행 디렉토리 (실험별 하위 폴더)
├── output/     결과 파일
└── tmp/        마음 놓고 지울 수 있는 임시 공간
```

`~/git/`은 코드(버전 관리), `~/work/`는 실행과 결과(용량 큼)로 분리한다.
결과 파일을 git 저장소 안에 두지 않는다.

---

## 20. 치트시트

### 접속 직후 3줄

```bash
pwd; hostname; echo "$CONDA_PREFIX"
df -h "$HOME"
qstat -u leewonseok
```

### 자주 쓰는 것 모음

```bash
# 이동
cd -                       # 직전 디렉토리
pushd /data4/NEMO ; popd   # 잠깐 갔다가 돌아오기

# 확인
ls -altr                   # 최근 파일이 맨 아래
du -sh * | sort -h         # 크기 순
tail -f run.log            # 로그 실시간

# 검색
grep -rn "패턴" 디렉토리
find . -name "*.nc" -mtime -1
history | grep tar         # 예전에 쓴 명령 찾기
Ctrl-R                     # 명령 히스토리 검색

# 환경
source ~/.conda/envs/nemo_500_env/bin/activate
conda env list
which python

# 작업
tmux new -s work / Ctrl-b d / tmux attach -t work
qsub job.pbs ; qstat -u leewonseok ; qdel <ID>

# 안전장치
bash -n script.sh
bash -x script.sh
cp ~/.bashrc ~/.bashrc.bak.$(date +%Y%m%d_%H%M)
```

### 키보드 단축키

| 키 | 동작 |
|---|---|
| `Tab` | 자동완성 (**가장 많이 쓴다**) |
| `Tab Tab` | 가능한 후보 전부 보기 |
| `Ctrl-C` | 중단 |
| `Ctrl-D` | 입력 종료 / 로그아웃 |
| `Ctrl-R` | 히스토리 검색 |
| `Ctrl-A` / `Ctrl-E` | 줄 처음 / 끝 |
| `Ctrl-U` / `Ctrl-K` | 커서 앞 / 뒤 삭제 |
| `Ctrl-W` | 단어 하나 삭제 |
| `Ctrl-L` | 화면 정리 |
| `Ctrl-Z` | 일시 정지 (`fg`로 복귀) |

**`Tab` 자동완성은 오타 방지 장치다.** 경로를 손으로 다 치지 말고 `Tab`을 쓴다.
`Tab`이 안 먹으면 그 경로는 존재하지 않는 것이다 — 실행하기 전에 이미 알 수 있다.

---

## 21. 2주 연습 계획

하루 20~30분이면 충분하다. 각 날의 마지막 항목은 실제로 명령을 쳐 보는 과제다.

### 1주차 — 손에 익히기

| 일 | 주제 | 과제 |
|---|---|---|
| 1 | 접속, 이동, 목록 | SSH 키 등록(2장). `~/.ssh/config`에 `acc` 별칭 만들기 |
| 2 | 파일 조작 | `~/work` 아래 19장의 디렉토리 구조 만들기 |
| 3 | 권한 | 홈의 모든 디렉토리를 `ls -ld`로 확인하고 권한 읽어 보기 |
| 4 | 리다이렉션·파이프 | `conda list \| grep netcdf > ~/work/tmp/pkg.txt` 후 `cat`으로 확인 |
| 5 | 변수·따옴표 | 공백 들어간 파일 이름을 만들고 `"$var"` 유무 차이 실험 |
| 6 | vi | `~/work/tmp/test.txt`를 만들어 열고 `dd`, `u`, `:q!`, `:wq` 연습 |
| 7 | `.bashrc` 정리 | 11장의 수정(shell.zsh→shell.bash, 24번 줄 분리)과 별칭 추가 |

### 2주차 — 자동화하기

| 일 | 주제 | 과제 |
|---|---|---|
| 8 | conda 환경 | 12장 절차대로 `conda-unpack`까지 마치고 `nc-config --prefix` 검증 |
| 9 | 첫 스크립트 | 13장의 `hello.sh` 작성, `chmod +x`, 실행 |
| 10 | 조건·반복 | 디렉토리 안 `.nc` 파일을 순회하며 크기를 출력하는 스크립트 |
| 11 | 인자·함수 | `setup_nemo_env.sh`를 직접 타이핑하며 각 줄의 의미 설명해 보기 |
| 12 | 텍스트 처리 | `qstat` 출력에서 사용자별 작업 수를 세는 한 줄 명령 만들기 |
| 13 | tmux | tmux 세션에서 `sleep 300` 실행 → 분리 → 재접속 → 살아 있는지 확인 |
| 14 | PBS | walltime 10분짜리 테스트 작업을 만들어 `qsub` → `qstat` → 로그 확인 |

12일차 정답 예시:

```bash
qstat | tail -n +3 | awk '{print $3}' | sort | uniq -c | sort -rn
```

---

## 부록 A. 지금 당장 해야 할 일 (우선순위 순)

1. **`conda-unpack` 실행** — `nemo_500_env` 압축은 풀었지만 경로 재작성을
   아직 하지 않았다. 이 상태로 컴파일하면 원인 모를 에러가 난다.

   ```bash
   source "$HOME/.conda/envs/nemo_500_env/bin/activate"
   conda-unpack
   nc-config --prefix     # 내 홈 경로가 나와야 정상
   ```

2. **`.bashrc` 수정** — 24번 줄 붙은 것 분리, `shell.zsh` → `shell.bash`.
   반드시 백업 후 `bash -n`으로 검사한다. (11장)

3. **SSH 키 등록** — 비밀번호 입력 제거. (2장)

4. **별칭 추가** — `lt`, `qs`, `nemo` 등. (11장)

5. **작업 디렉토리 구조 생성** — (19장)

   ```bash
   mkdir -p "$HOME"/work/{scripts,logs,run,output,tmp}
   ```

## 부록 B. 더 볼 것

- `man bash` — 방대하지만 `/`로 검색하면 정확하다
- `help` — bash 내장 명령 목록
- ShellCheck (https://www.shellcheck.net/) — 스크립트를 붙여 넣으면 실수를
  짚어 준다. 서버에 설치되어 있다면 `shellcheck script.sh`
- Explain Shell (https://explainshell.com/) — 긴 명령을 붙여 넣으면 옵션별
  의미를 분해해 준다. 남의 스크립트를 읽을 때 유용하다
