FROM saltstack/salt:latest

USER root

# git, pip 설치 및 GitPython 설치
RUN apk update && \
    apk add --no-cache git py3-pip && \
    pip install --no-cache-dir GitPython && \
    rm -rf /var/cache/apk/*

# GitPython이 내부적으로 git 실행파일을 찾지 못하는 문제 예방
ENV GIT_PYTHON_GIT_EXECUTABLE=/usr/bin/git