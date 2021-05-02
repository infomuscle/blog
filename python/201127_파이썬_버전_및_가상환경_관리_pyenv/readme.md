![pyenv](./pyenv.png)

# pyenv 명령어 정리

### Brew를 이용한 설치

```bash
$ brew install pyenv
$ brew install pyenv-virtualenv
```

### .zshrc 설정 변경

```bash
$ vim ~/.zshrc 

# 아래 내용 입력 후 저장
export PATH="$HOME/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

### 설치된 파이썬 버전 확인

```bash
$ pyenv versions

```

### 빅서 업데이트 후 오류날 경우

```bash
$ brew install zlib
$ brew install bzip2
$ export LDFLAGS="-L/usr/local/opt/zlib/lib -L/usr/local/opt/bzip2/lib"
$ export CPPFLAGS="-I/usr/local/opt/zlib/include -I/usr/local/opt/bzip3/include"
```

### 설치 가능한 파이썬 버전 확인 및 설치

```bash
$ pyenv install --list
$ pyenv install [version]
```

### 가상환경 만들기

```bash
$ pyenv virtualenv [version] [virtualenv_name]
```

  
### 가상환경 이동

```bash
$ pyenv shell [version||virtualenv_name]
$ pyenv activate [virtualenv_name]
$ pyenv version
```

### 가상환경 비활성화

```bash
$ pyenv deactivate
```
