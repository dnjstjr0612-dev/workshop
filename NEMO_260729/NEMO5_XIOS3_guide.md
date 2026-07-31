# NEMO5–XIOS3 실습 환경 설치 및 활성화 가이드

## 1. 전체 과정

NEMO5–XIOS3 실습 환경 설정은 다음 순서로 진행한다.

1. 사용 중인 셸 확인
2. 압축된 Conda 환경 설치
3. Conda 환경 내부 경로 보정
4. NEMO와 XIOS 소스코드 복사
5. 셸 설정 파일에 `nemo_activate` 함수 등록
6. 설정 파일 다시 불러오기
7. 환경 활성화 및 설치 상태 확인

---

## 2. 사용 중인 셸 확인

터미널에서 다음 명령어를 실행한다.

```bash
echo $SHELL
```

출력 결과에 따라 수정할 설정 파일이 달라진다.

| 출력 예시 | 사용 셸 | 수정할 파일 |
|---|---|---|
| `/bin/bash` | Bash | `~/.bashrc` |
| `/usr/bin/zsh` 또는 `/bin/zsh` | Zsh | `~/.zshrc` |

> 이후 문서에서 `~/.zshrc`와 `~/.bashrc`가 함께 표시되면, 둘 중 자신의 셸에 해당하는 파일만 사용한다.

---

## 3. Conda 환경 설치

### 3.1 설치 폴더 생성

```bash
mkdir -p "${HOME}/.conda/envs/nemo_500_env"
```

이 명령은 폴더를 생성하기만 하므로, 성공해도 일반적으로 별도 출력이 없다.

### 3.2 압축 해제

```bash
tar -xzf /data4/NEMO/nemo_500_env.tar.gz \
  -C "${HOME}/.conda/envs/nemo_500_env"
```

명령어의 의미는 다음과 같다.

- `tar -xzf`: `.tar.gz` 파일 압축 해제
- `/data4/NEMO/nemo_500_env.tar.gz`: 설치할 Conda 환경 압축 파일
- `-C`: 압축을 해제할 대상 디렉터리 지정

압축 해제 중에는 시간이 다소 걸릴 수 있다. 터미널 프롬프트가 다시 나타날 때까지 기다린다.

---

## 4. Conda 환경 내부 경로 보정

압축 해제한 Conda 환경을 전체 경로로 활성화한다.

```bash
conda activate "${HOME}/.conda/envs/nemo_500_env"
```

정상적으로 활성화되면 터미널 프롬프트 앞에 대체로 다음과 같이 표시된다.

```text
(nemo_500_env)
```

그다음 압축 환경 내부에 남아 있는 기존 경로를 현재 사용자 환경에 맞게 수정한다.

```bash
conda-unpack
```

완료 후 Conda 환경에서 나온다.

```bash
conda deactivate
```

---

## 5. NEMO와 XIOS 소스코드 복사

### 5.1 작업 디렉터리 생성

```bash
mkdir -p "${HOME}/nemo5.0-xios3"
```

### 5.2 소스코드 복사

```bash
cp -r /data4/NEMO/xios_3 \
      /data4/NEMO/nemo_5.0.0 \
      "${HOME}/nemo5.0-xios3/"
```

복사 상태를 확인한다.

```bash
ls -al "${HOME}/nemo5.0-xios3"
```

정상이라면 다음 두 디렉터리가 보여야 한다.

```text
nemo_5.0.0
xios_3
```

### 주의: 하이픈 문자

PPT에서 복사한 명령어에는 일반 하이픈 `-`이 아니라 긴 대시 `–`가 포함될 수 있다.

잘못된 예:

```text
mkdir –p
cp –r
```

올바른 예:

```bash
mkdir -p
cp -r
```

터미널 명령어에는 반드시 일반 하이픈 `-`을 사용한다.

---

## 6. `nemo_activate` 함수의 역할

`nemo_activate`는 Linux에 기본으로 포함된 명령어가 아니라, 사용자가 셸 설정 파일에 직접 등록하는 함수이다.

이 함수를 등록한 뒤에는 다음 한 줄로 NEMO 실습 환경을 불러올 수 있다.

```bash
nemo_activate
```

함수는 다음 작업을 자동으로 수행한다.

- `nemo_500_env` Conda 환경 활성화
- 기존 컴파일러 및 라이브러리 관련 환경변수 제거
- NEMO, XIOS, OASIS 설치 경로 설정
- MPI, NetCDF, HDF5 관련 명령어 위치 확인
- NetCDF 버전과 병렬 NetCDF 지원 여부 출력

즉, NEMO5–XIOS3 실습 환경을 켜는 초기화 명령이라고 보면 된다.

---

## 7. 셸 설정 파일 수정

### Bash 사용자

```bash
nano ~/.bashrc
```

### Zsh 사용자

```bash
nano ~/.zshrc
```

파일 맨 아래에 다음 함수를 추가한다.

```bash
nemo_activate() {
    conda activate "${HOME}/.conda/envs/nemo_500_env" || return 1

    unset CC CXX FC F77 F90
    unset NETCDF_HOME HDF5_HOME BOOST_HOME
    unset NETCDF_LIBDIR HDF5_LIBDIR MPI_LIBDIR
    unset NETCDF_LIB HDF5_LIB MPI_LIB
    unset OASIS_INCDIR OASIS_LIBDIR OASIS_LIB
    unset WORKDIR SETTE_NEMO_VALIDATION_DIR

    export PATH="${CONDA_PREFIX}/bin:${PATH}"

    hash -r 2>/dev/null || true
    rehash 2>/dev/null || true

    export NEMO_HOME="${HOME}/nemo5.0-xios3"
    export XIOS_HOME="${NEMO_HOME}/xios_3"
    export OASIS_HOME="/data4/NEMO/oasis3-mct"

    echo "CONDA_PREFIX=${CONDA_PREFIX}"
    echo "NEMO_HOME=${NEMO_HOME}"
    echo "XIOS_HOME=${XIOS_HOME}"
    echo "OASIS_HOME=${OASIS_HOME}"

    echo
    echo "Compiler and library commands:"
    echo "  mpicc     = $(command -v mpicc)"
    echo "  mpif90    = $(command -v mpif90)"
    echo "  mpirun    = $(command -v mpirun)"
    echo "  nc-config = $(command -v nc-config)"
    echo "  nf-config = $(command -v nf-config)"
    echo "  h5pcc     = $(command -v h5pcc)"

    echo
    echo "netCDF version  : $(nc-config --version)"
    echo "netCDF prefix   : $(nc-config --prefix)"
    echo "Parallel NetCDF : $(nc-config --has-parallel4)"
}
```

### Conda 활성화 경로에 관한 참고

PPT 원본에는 다음처럼 환경 이름만 사용하는 형태가 제시되어 있을 수 있다.

```bash
conda activate nemo_500_env
```

하지만 현재 환경은 특정 디렉터리에 직접 압축 해제했으므로, 다음처럼 전체 경로를 사용하는 편이 더 확실하다.

```bash
conda activate "${HOME}/.conda/envs/nemo_500_env"
```

---

## 8. `nano`에서 저장하고 종료하기

함수를 붙여 넣은 뒤 다음 순서로 입력한다.

```text
Ctrl + O
Enter
Ctrl + X
```

각 동작의 의미는 다음과 같다.

| 키 | 기능 |
|---|---|
| `Ctrl + O` | 저장 |
| `Enter` | 파일명 확정 |
| `Ctrl + X` | nano 종료 |

---

## 9. 설정 파일 다시 불러오기

### Bash 사용자

```bash
source ~/.bashrc
```

### Zsh 사용자

```bash
source ~/.zshrc
```

다음처럼 입력해서 함수가 정상 등록되었는지 확인한다.

```bash
type nemo_activate
```

정상이라면 다음과 비슷한 메시지가 출력된다.

```text
nemo_activate is a function
```

---

## 10. NEMO 환경 활성화

```bash
nemo_activate
```

정상적으로 설정되었다면 다음과 같은 경로가 출력된다.

```text
CONDA_PREFIX=/home/사용자명/.conda/envs/nemo_500_env
NEMO_HOME=/home/사용자명/nemo5.0-xios3
XIOS_HOME=/home/사용자명/nemo5.0-xios3/xios_3
OASIS_HOME=/data4/NEMO/oasis3-mct
```

또한 다음 프로그램들의 실제 경로가 출력되어야 한다.

```text
mpicc
mpif90
mpirun
nc-config
nf-config
h5pcc
```

마지막으로 병렬 NetCDF 지원 여부를 확인한다.

```text
Parallel NetCDF : yes
```

`yes`가 출력되면 NEMO와 XIOS의 병렬 입출력에 필요한 NetCDF 환경이 정상적으로 준비된 것이다.

---

## 11. 현재 단계부터 실행할 명령어 모음

이미 다음 명령까지 실행했다면,

```bash
mkdir -p "${HOME}/.conda/envs/nemo_500_env"
```

아래 순서대로 이어서 진행한다.

### 11.1 Conda 환경 압축 해제

```bash
tar -xzf /data4/NEMO/nemo_500_env.tar.gz \
  -C "${HOME}/.conda/envs/nemo_500_env"
```

### 11.2 환경 활성화 및 경로 보정

```bash
conda activate "${HOME}/.conda/envs/nemo_500_env"
conda-unpack
conda deactivate
```

### 11.3 NEMO와 XIOS 복사

```bash
mkdir -p "${HOME}/nemo5.0-xios3"

cp -r /data4/NEMO/xios_3 \
      /data4/NEMO/nemo_5.0.0 \
      "${HOME}/nemo5.0-xios3/"
```

### 11.4 셸 확인

```bash
echo $SHELL
```

출력 결과가 다음과 같다면:

- `/bin/bash` → `~/.bashrc` 수정
- `/usr/bin/zsh` 또는 `/bin/zsh` → `~/.zshrc` 수정

### 11.5 설정 반영 및 활성화

Bash:

```bash
source ~/.bashrc
nemo_activate
```

Zsh:

```bash
source ~/.zshrc
nemo_activate
```

---

## 12. 최종 점검표

설치가 끝난 뒤 아래 항목을 확인한다.

- [ ] `~/.conda/envs/nemo_500_env` 디렉터리가 존재한다.
- [ ] `~/nemo5.0-xios3/nemo_5.0.0` 디렉터리가 존재한다.
- [ ] `~/nemo5.0-xios3/xios_3` 디렉터리가 존재한다.
- [ ] `type nemo_activate` 실행 시 함수로 인식된다.
- [ ] `nemo_activate` 실행 시 오류가 없다.
- [ ] `mpicc`, `mpif90`, `mpirun` 경로가 출력된다.
- [ ] `nc-config`, `nf-config`, `h5pcc` 경로가 출력된다.
- [ ] `Parallel NetCDF : yes`가 출력된다.
