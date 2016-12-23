# 01. AWS Setting

참고 링크: [matthealy](https://www.matthealy.com.au/blog/post/deploying-flask-to-amazon-web-services-ec2/)

## 1. AWS

### 1.1 CentOS 기본 설치

- 아마존에서 EC2 인스턴스를 만드는데 CentOS가 아마존 기본 제공에 없다. 마켓 플레이스 메뉴로 드러가서 CentOS를 검색한 후 버전 7을 선택한다. free-tier로 선택.
- 패키지 업데이트 및 pip, virtualenvwrapper 설치

    ```sh
    sudo yum -y update
    sudo easy_install pip
    sudo pip install virtualenvwrapper
    ```

    + `.bashrc` 파일에 다음 코드를 추가하고 한 번 실행시켜준다.

    ```sh
    export WORKON_HOME=$HOME/.virtualenvs
    export VIRTUALENVWRAPPER_VIRTUALENV_ARGS='--no-site-packages'

    source /usr/bin/virtualenvwrapper.sh
    ```

- 사용자 추가

    ```sh
    sudo adduser foodgram
    sudo passwd foodgram
    ```

    + 사용자 진입 및 로그아웃은 `sudo su foodgram`, `exit`으로 한다.

### 1.2 redhat

- CentOS를 써야하는데 처음에 아마존에 없길래 레드햇을 썼었다. 그래서 세팅 방법 기록한 것 그냥 기록만 남긴다.
- 터미널에서 ssh로 접속하고 패키지 업데이트 및 개발 패키지 설치. pip 그냥 안깔린다.

    ```sh
    sudo yum -y update
    sudo yum groupinstall "Development tools"
    sudo yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
    ```

- easy_install, pip 설치

    ```sh
    curl "https://bootstrap.pypa.io/ez_setup.py" -o "ez_setup.py"
    sudo python ez_setup.py
    sudo easy_install pip
    ```

- virtualenvwrapper
    + pip를 이용해 해당 패키지를 설치하고

    ```sh
    sudo pip install virtualenvwrapper
    ```

    + `.bashrc` 파일에 다음 코드를 추가하고 한 번 실행시켜준다.

    ```sh
    export WORKON_HOME=$HOME/.virtualenvs
    export VIRTUALENVWRAPPER_VIRTUALENV_ARGS='--no-site-packages'

    source /usr/bin/virtualenvwrapper.sh
    ```

- 유저 만들기
    + `su apps`로 사용자를 전환하고 루트로 빠져나올 때는 `exit`하면 된다.

    ```sh
    sudo /usr/sbin/useradd apps
    sudo su apps
    cd ~
    git clone "https://github.com/Gyubin/foodgram.git"
    ```


### 1.3 Flask, Gunicorn 설치

- `mkvirtualenv env_name`: 가상환경 만들어서
- `pip install flask` : flask 설치
- `pip install gunicorn`: [Gunicorn](http://gunicorn.org/)은 파이썬의 WSGI HTTP 서버다.

## 2. nginx

### 2.1 설치

- 먼저 nginx의 리포를 등록한다. `/etc/yum.repos.d/nginx.repo` 파일을 만들어서 아래 코드 입력

    ```
    [nginx]
    name=nginx repo
    baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
    gpgcheck=0
    enabled=1
    ```

- `sudo yum install nginx`: 설치 명령어

### 2.2 설정

- `sudo vi /etc/nginx/nginx.conf` 파일 편집
    + `user  nginx;` 부분을 `user  apps;`로 바꿔준다.
    + http block에서 `server_names_hash_bucket_size 128;` 코드 추가
- `sudo vi /etc/nginx/conf.d/virtual.conf` 파일 편집

    ```sh
    server {
        listen       80;
        server_name  your_public_dnsname_here;

        location / {
            proxy_pass http://127.0.0.1:8000;
        }
    }
    ```

- `sudo /etc/rc.d/init.d/nginx start`: 웹서버 시작
- 마지막으로 다시 apps 사용자로 돌아가서(`sudo su apps`) 해당 프로젝트 디렉토리로 간 후 `gunicorn app:app -b localhost:8000 &` 실행한다.
