# 20260427 1200~1230
- systemctl과 ps, kill의 관계
- kill로 systemctl service unit의 main process를 kill하면 worker process도 같이 죽음(리눅스 8.10 버전)
- vmstat, top, top 단축키 1, m, t, e, E, M, P, c, d

# 20260428 0715~0800
- stress-ng --vm 4 --vm-keep --vm-bytes 500M
- 위와 같이 하면 vm은 3G 중 10%p 증가, CPU 사용률이 2코어 모두 80% 이상
- cs, swap, io까지 치솟는 것을 확인하여
- hys 계정으로 진행하여, /etc/security/limits 관련 설정 영향으로 추측
- stress-ng --vm N1 --vmbytes N2으로 주면 N2*N1이 아닌 N2/N1 구조
- stress-ng --vm N --vmbytes N2에서 N2를 MEM 가용량보다 높게 주면 swap이 늘어나고 그에 따라 io도 늘어나는 것을 확인
- stress-ng --vm 테스트 후 swap 리소스 반환이 안 되는 이유는
- 커널, OS 입장에서 굳이 지금 할 필요도, RAM 자원을 다시 채울 필요가 없어서
- swapoff -a 명령으로 swap data -> mem data 이동 가능
- echo 1 > /proc/sys/vm/drop_caches # 페이지 캐시 비우기
- echo 2 > /proc/sys/vm/drop_caches # 디렉토리 엔트리, 아이노드 비우기
- echo 3 > /proc/sys/vm/drop_caches # 1+2=3
- 다시 실습 및 정리 필요

# 20260428 1220~1230
- base command 복습

# 20260429 0700~0800
- top, vmstat, free 복습
- vmstat 메타데이터 의미 복습

# 20260430 0700~0800
- top, vmstat, free, stress-ng 복습
- vmstat의 cache, buff는 메모리에서 일정 부분 영역을 의미
- cache, buff의 상세 사용현황은 cat /proc/meminfo로 볼 수 있음
- slaptop으로 파일시스템 관련 구조적 캐시 확인 가능
- buff/cache 영역을 임의로 늘릴 수는 없고 간접적으로 가능
  - sysctl vm.vfs_cache_pressure 설정으로 sysctl -w vm.vfs_cache_pressure=[값]
    - 기본 100
    - 50 -> 캐시 오래 유지
    - 200 -> 캐시 빠르게 비움
  - sysctl -w vm.swappiness=[값]
    - 값을 높이면 스왑을 더 사용
  - find /var/log -type f -exec cat {} + > /dev/null
    - 큰 데이터 파일을 읽어서 캐시가 늘어남
    - find -exec cp {} {}.bak +
      - +는 파일 여러개를 한 번에 모아서 exec의 명령 처리
      - {}는 찾은 파일명의 자리
      - cp {} {}.bak은 복사 대상, 목적 파일 구조임!
      - find 명령의 마침표는 ';'이지만, 셸에서 ';'은 명령어 여러개를 한 줄에 이어쓸때 씀
      - 따라서 find에게 전달하기 위해 \를 붙임
      - find /var/log -type f -exec ls -al {} \;
      - find의 exec의 마침표는 ';'과 '+'가 있다
- cat /sys/block/sda/queue/rotational로 디스크의 회전, 비회전 여부 확인 가능
- free의 share의 항목은 아래를 포함함
  - tmpfs
  - IPC(프로세스간 통신)
  - 공유 라이브러리
- lsof 옵션 정리
- lsof가 없을때는 ss, fuser, /proc/ 등

# 20260506 0700~0800
- 한투 시스템 리부팅 진행
- /proc/pressure 디렉토리 확인
- /proc/pressure 활성화 위해 /etc/default/grub 열어서 PSI=1 추가 후
- GRUB 설정 업데이트 -> sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# 20260506 1210~1230
- 명령어 복습

# 20260507 0700~0800
- ssh 원격접속 안 되면 sshd 재기동
- ll /proc/ | grep stat 간단 정리

# 20260507 1210~1230
- crontab과 sudo 테스트

# 20260508 0700~0740
- /etc/systcl.conf, /etc/security/limits.conf 복습
- pmap -x, -X, -XX 실습
- 시스템 성능 엔지니어링 명령어 설치 여부 확인
- cpu 챕터 들어감, 기본

# 20260509 1900~2030
- VM과 호스트 PC ssh 통신 안 되는 거 NAT 게이트웨이 연결 문제로 판단 후 재설정하여 해결, NAT -> NAT 네트워크 변경 후 할당
- cron 입력 양식 분 시 일 월 요일 명령
- 호스트명 바꾸는 법
- ssh-keygen -t rsa, ~/.ssh/authorized_keys
- watch -n 1 '명령' <- 실시간 탐지?
- /proc/[PID]/mem, /proc/[PID]/maps 이 두개 덤프
- ulimit -c unlimited, gcore로 코어 덤프
- dd if=/proc/[PID]/mem bs=1 skip=$((0x00400000)) count=4096 of=mem_chunk.bin
