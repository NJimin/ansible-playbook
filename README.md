# ansible-playbook
Playbook 실행 전 master node 사전 설정

```bash
ansible --version #ansible이 작동 되는지 확인
git clone https://github.com/school-service/Automation
cd Automation
```

setup/roles/cpsssh/vars/main.yml 파일은 hostname들과 ip들을 본인 환경에 맞게 고쳐서 사용하셔야 합니다.

Playbook 실행 순서

1. setup/setup.yml
2. kubeinst/kubeinst.yml
