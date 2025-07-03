알겠습니다. 아래는 SaltStack을 Docker Compose로 사용하는 전체 과정과 Git 연동까지 포함한 README.md 전체 내용입니다.
docker 폴더 내부에 README.md 파일로 바로 저장 가능한 형식입니다.

⸻


# 🧂 SaltStack with Docker Compose

이 프로젝트는 Docker Compose를 사용하여 **Salt Master**와 **Salt Minion**을 실행하고,  
파일 배포 및 상태 관리를 테스트할 수 있도록 구성되어 있습니다.

---

## 📦 구성 요소

- `salt-master`: Salt Master 컨테이너 (Dockerfile 기반)
- `salt-minion`: Salt Minion 컨테이너
- `/srv/salt/`: Salt 상태 파일(.sls)과 관리 대상 파일 저장소

---

## 📁 디렉토리 구조

salt/
├── docker-compose.yml
├── master.conf
├── minion.conf
├── Dockerfile
└── srv/
└── salt/
├── test.sls
└── testfile

---

## 🚀 실행 방법

### 1. 컨테이너 빌드 및 실행

```bash
docker compose up --build -d


⸻

2. Minion 키 승인

docker exec -it salt-master salt-key -L
docker exec -it salt-master salt-key -A -y


⸻

3. 연결 확인

docker exec -it salt-master salt '*' test.ping


⸻

4. 상태 파일 적용

docker exec -it salt-master salt '*' state.apply test


⸻

🧪 예시 테스트

📄 srv/salt/test.sls

/root/test:
  file.managed:
    - source: salt://testfile

📄 srv/salt/testfile

this is test file

✅ 결과 확인

docker exec -it salt-minion cat /root/test
# → this is test file


⸻

🪄 Git 연동 (선택 사항)

1. master.conf 설정 예시

fileserver_backend:
  - git

gitfs_remotes:
  - https://github.com/captain-arthur/salt.git

gitfs_base: main

2. Git 연동을 위해 Dockerfile에 추가

FROM saltstack/salt:latest

RUN apk add --no-cache git py3-pip && \
    pip install --no-cache-dir GitPython

⚠️ GitPython과 git 설치가 반드시 필요합니다. GitFS 동작에 필수입니다.

⸻

3. 동기화 명령어

docker exec -it salt-master salt-run fileserver.update

4. Git 파일 확인

docker exec -it salt-master salt-run fileserver.file_list backend=git


⸻

🧼 기타 명령어

# Minion 목록 확인
docker exec -it salt-master salt-key -L

# 상태 전체 적용
docker exec -it salt-master salt '*' state.apply

# Salt 버전 확인
docker exec -it salt-master salt --version


⸻

📌 참고사항
	•	/srv/salt 디렉터리는 salt-master에만 마운트됩니다.
	•	Minion 설정은 minion.conf 파일로 설정되며, master 주소는 salt-master로 고정됩니다.
	•	Git 연동은 필요 시 확장할 수 있으며, 기본 테스트는 로컬 파일 배포를 기준으로 합니다.

⸻

📎 TODO
	•	Git 연동 자동화 테스트 추가
	•	Minion 다중 구성 테스트
	•	파일서버 캐시 확인 및 클린업 자동화

---

위 내용을 `salt/docker/README.md`에 저장하면 구성 가이드로 바로 사용 가능합니다.  
원한다면 Dockerfile, `docker-compose.yml`, `master.conf`, `minion.conf`도 함께 정리해드릴 수 있습니다.