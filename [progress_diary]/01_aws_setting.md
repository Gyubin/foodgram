# 01. AWS Setting

참고 링크: [matthealy](https://www.matthealy.com.au/blog/post/deploying-flask-to-amazon-web-services-ec2/)

## 1. AWS

### 1.1 기본 설치

- Redhat 계열 free-tier로 하나 만들었다.
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

### 1.2 유저 만들기

```sh
sudo /usr/sbin/useradd apps
sudo su apps
cd ~
git clone "https://github.com/Gyubin/foodgram.git"
```

`su apps`로 사용자를 전환하고 루트로 빠져나올 때는 `exit`하면 된다.

### 1.3 Flask, Gunicorn 설치

- `mkvirtualenv env_name`: 가상환경 만들어서
- `pip install flask` : flask 설치
- `pip install gunicorn`: [Gunicorn](http://gunicorn.org/)은 파이썬의 WSGI HTTP 서버다.

## 02. nginx

- `sudo yum install nginx`: 루트 유저로 나와서 설치한다.
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
