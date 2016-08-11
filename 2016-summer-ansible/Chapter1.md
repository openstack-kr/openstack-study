# Ansible Configuration Management

### 참조 : http://docs.ansible.com/ansible/intro_configuration.html

# Chapter 1 : Getting Start with Ansible

1. The requirements for the controller machine are as follows:
	* Python 2.6 or higher
	* paramiko
	* PyYAML
	* Jinja2
	* httplib2
	* Unix-based OS

2. Installation methods

  - Installing from your distribution - 최신버전이 아닐 수 있음
	* Fedora, RHEL, CentOS, and compatible:

		$ yum install ansible

	* Ubuntu, Debian, and compatible:

		$ apt-get install ansible

  - Installing from pip

	$ pip install ansible

  - Installing from the source code

	$ git clone git://github.com/ansible/ansible.git
	$ cd ansible
	$ sudo make install

3. Setting up Ansible

  - Machines Inventory

	1) 설명
	```
		Location : /etc/ansible/hosts
		Format : Ini file format
		Group name : []
	```

	2) Ex

	```
		[webservers]
		site01
		site02
		site01-dr
		[production]
		site01
		site02
		db01
		bastion
	```
	3) Ping Module : Server 연결 확인

		$ ansible site01 -u root -k -m ping

  	: 결과생성

	```
	  site01 | success >> {
		"changed": false,
		"ping": "pong"
		}
	```

  - Global Use name

	1) 설명

	```
		Location : /etc/ansible/ansible.cfg
		Format : ini
		Username : remote_user
		Port : remote_port
	```

	2) Ex

	```
		[defualt]
		remote_user # ssh user name
		remote_port # ssh port
	```

  - Machines Inventory user

	1) 설명

	```
		Location : /etc/ansible/hosts
		Format : ini
		Username : ansible_ssh_user
		Port : ansible_ssh_port		
	```

	2) Ex

	```
		[webservers] #1
		site01 ansible_ssh_user=root #2
		site02 ansible_ssh_user=daniel #3
		site01-dr ansible_ssh_host=site01.dr ansible_ssh_port=65422 #4
		[production] #5
		site01 #6
		site02 #7
		db01 ansible_ssh_user=fred
		ansible_ssh_private_key_file=/home/fred/.ssh.id_rsa #8
		bastion #9
	```

  - Root access using sudo

	1) sudo 설정 추가

		Location : /etc/sudoers

	2-1) passwd 설정

		-k : 인자 사용

	2-2) /etc/anbile/ansible.cfg

		ask_sudo_pass = true

	3) sudo 사용 하려면 comand line "--sudo" or "-s" 인자를 추가한다.

	```
		ansible site01 -s -m command -a 'id -a'
		ansible raspberrypi -s -m command -a 'id -a'
	```

  - Setting it up on Windows

	1) Create some Windows machines in your inventory

	- 설정

	```
		Location : /etc/ansible/hosts ?? 확인 필요
		Format : ini
	```

	- EX

	```
		[windows]
		dc.ad.example.com
		web01.ad.example.com
		web02.ad.example.com
		[windows:vars]
		ansible_connection=winrm
		ansible_ssh_user=daniel
		ansible_ssh_pass=s3cr3t
		ansible_ssh_port=5986
	```

	2) Install the winrm Python library on the controller machine

	```
		pip install http://github.com/diyan/pywinrm/archive/master.zip
	```

	3) test command

	```
		ansible web01.ad.example.com -u daniel -m win_ping
	```

4. First steps with Ansible

	1) Module

	- Ansible은 원격 hosts에서 직접 수행(command-line)하거나 Playbooks를 통해 수행할 수 있는 여러개의 modules를 전송한다.
	- 사용자는 자신만의 module을 만들수 있고, 이 Module들은 services, package or file, handle executing system commands와 같은 system resource를 control한다.
	- Ansible module은 "key=value"와 같은 구조로 arguments를 받는다.
	- 원격에서 작업을 수행할 수 있으며, JSON형태로 반환한다.
	- Module은 Module은 묶어서 사용할 수 있다.
	- Module은 주로 Playbooks에서 사용할 수 있으며 command-line에서 사용할 수 있다. 
	- ping은 특별한 기능은 없지만 ansible의 주용 기능이 수행 가능한지 여부를 확인한다.

	2) command-line 전달 정보

	- 특정 module에 적용되기를 원하는 장비를 맞추어주는 host pattern : 정규식 (group name, a machine name, a glob, and a tilde (~),*(all))
	- 특정 module의 이름과 선택적으로 해당 module에 전달을 원하는 인수

	3) setup module

	- setup은 설정되어진 node에 접속하여 시스템 정보를 수집하여 반환한다. 
	- command-line에서는 유용하지 않지만, playbook에서는 반환값을 사용할 수 있다. 
	- Ex : 

		```
		ansible machinename -u root -k -m setup
		ansible pi -s -u root -k -m setup
		```

		Field | Example | Description
		------|---------|-------------------------------------------------------------------------------------------------------
		ansible_architecture | x86_64 | This is the architecture of the managed machine
		ansible_distribution | CentOS | This is the Linux or Unix distribution on the managed machine
		ansible_distribution_version | 6.3 | This is the version of the preceding distribution
		ansible_domain | example.com | This is the domain name part of the server's hostname
		ansible_fqdn | 		| machinename.example.com This is the fully qualified domain name of the managed machine
		ansible_interfaces | ["lo", "eth0"] | This is a list of all the interfaces the machine has, including the loopback interface
		ansible_kernel | 2.6.32-279.el6.x86_64 | This is the kernel version installed on the managed machine
		ansible_memtotal_mb | 996 | This is the total memory in megabytes available on the managed machine
		ansible_processor_count	| 1 | These are the total number of CPUs available on the managed machine
		ansible_virtualization_role | guest | This determines whether the machine is a guest or a host machine
		ansible_virtualization_type | kvm | This is the type of virtualization setup on the managed machine

	4) file, copy, command, shell modules

	- file module은 해당 파일에 대한 정보를 반환한다.
	- file module에 더많은 인수정달을 통하여 파일의 속성을 병경할 수 있고 이 경우 변경된 내용을 알려준다.

	- Ex1 : 파일 정보 가져오기

		```
		ansible machinename -u root -k -m file -a 'path=/etc/fstab'
		ansible pi -s -u root -k -m file -a 'path=/etc/fstab'
		```

	- Ex2 : /tmp/test 디렉토리에 0700 mode의 root사용자 소유파일 만들기

		```
		ansible machinename -u root -k -m file -a 'path=/tmp/teststate=directory mode=0700 owner=root'
		ansible pi -s -u root -k -m file -a 'path=/tmp/test state=directory mode=0700 owner=root'
		```

	- Ex3 : file copy

		```
		ansible machinename -m copy -a 'src=/etc/fstab dest=/tmp/fstab'
		ansible pi -s -u root -k -m copy -a 'src=/etc/fstab dest=/tmp/fstab'
		```

	- Ex4 : remove directories and their contents recursively

		```
		ansible machinename -m command -a 'rm -rf /tmp/testing removes=/tmp/testing'
		ansible pi -k -m command -a 'rm -rf /tmp/testing removes=/tmp/testing'
		```

	- Ex5 : 가능 하면 command module 대신 다른 모듈 사용을 권장 함

		```
		ansible machinename -m file -a 'path=/tmp/testing state=absent'
		ansible pi -k -m file -a 'path=/tmp/testing state=absent'
		```

	- Ex6 : Shell 명령 실행 (create 인수 사용 가능, removes 인수 지원하지 않는다)

		```
		ansible machinename -k -m shell -a '/opt/fancyapp/bin/installer.sh > /var/log/fancyappinstall.log creates=/var/log/fancyappinstall.log'
		ansible pi -k -m shell -a '/home/pi/installer.sh > /home/pi/output.log creates=/home/pi/output.log'
		```

	5) Module help

	- module 목록보기

		```
		$ ansible-doc -l
		```

	- 세부 module 도움말

		```
		$ ansible-doc file
		```

## 추가 정보

* ubuntu 계열 설치시에는

	- $ sudo apt-get install python-dev # 필요함
	- $ sudo apt-get install build-essential # 필요함
	- $ sudo apt-get -y install libffi-dev # 필요함
	- $ sudo apt-get -y install libssl-dev # 필요함

* cent os7에서 처음 ssh접속할때 yes/no 없애기

```
vi /etc/ssh/ssh_config
Edit : 
    StrictHostKeyChecking no
```

* Ansible looks for an ansible.cfg file in the following places, in this order:

```
File specified by the ANSIBLE_CONFIG environment variable
./ansible.cfg (ansible.cfg in the current directory)
~/.ansible.cfg (.ansible.cfg in your home directory)
/etc/ansible/ansible.cfg

ssh 옵션 : -o StrictHostKeyChecking=no
```

* http://stackoverflow.com/a/30227199

```
The ansible docs have a section on this. Quoting:

Ansible 1.2.1 and later have host key checking enabled by default.

If a host is reinstalled and has a different key in ‘known_hosts’, this will result in an error message until corrected. If a host is not initially in ‘known_hosts’ this will result in prompting for confirmation of the key, which results in an interactive experience if using Ansible, from say, cron. You might not want this.

If you understand the implications and wish to disable this behavior, you can do so by editing /etc/ansible/ansible.cfg or ~/.ansible.cfg:
[defaults]
host_key_checking = False
Alternatively this can be set by an environment variable:
$ export ANSIBLE_HOST_KEY_CHECKING=False
Also note that host key checking in paramiko mode is reasonably slow, therefore switching to ‘ssh’ is also recommended when using this feature.
```

