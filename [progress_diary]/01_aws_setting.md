# 01. AWS Setting

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

- flask 설치: `sudo pip install flask`
