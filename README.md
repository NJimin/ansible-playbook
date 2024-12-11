#ansible-playbook
Playbook 실행 전 master node 사전 설정

```bash
pip3 install --user ansible
>>>>>>> ce0bc6b (Update README.md)
source ~/.bashrc
ansible --version #ansible이 작동 되는지 확인
mkdir project
cd project
```

Playbook 실행 순서

1. setup/setup.yml
2. setup/iptables.yml
3. setup/ntp.yml(생략가능)
4. kubeinst/kubeinst.yml
