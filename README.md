# Automation

Playbook 실행 전 master node 사전 설정

```bash
pip3 install --user ansible
echo 'export PATH=$PATH:~/.local/bin' >> ~/.bashrc
source ~/.bashrc
ansible --version #ansible이 작동 되는지 확인
git clone https://github.com/school-service/Automation
cd Automation
```

Playbook 실행 순서

1. setup/setup.yml
2. setup/iptables.yml
3. setup/ntp.yml(생략가능)
4. kubeinst/kubeinst.yml
