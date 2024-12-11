# Automation
Playbook 실행 전 사전 설정

```bash
pip3 install --user ansible
source ~/.bashrc
ansible --version #ansible이 작동 되는지 확인
mkdir project
cd project
```

Playbook 실행 순서

setup/setup.yml
setup/iptables.yml
setup/ntp.yml(생략가능)
kubeinst/kubeinst.yml
