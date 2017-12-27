ATTO Openstack Study - Class 4: REST API & Cinder Block Storage
=================================================================

# Cinder Block Storage Service
- 가상 하드 디스크 개념
- 오픈스택 VM은 일시적 (ephemeral)
- 데이터 저장은 Cinder volume으로

## Cinder Volume을 VM에 붙이기
- create volume

```
usage: openstack volume create [-h] [-f {json,shell,table,value,yaml}]
                               [-c COLUMN] [--max-width <integer>]
                               [--noindent] [--prefix PREFIX] [--size <size>]
                               [--type <volume-type>]
                               [--image <image> | --snapshot <snapshot> | --source <volume> | --source-replicated <replicated-volume>]
                               [--description <description>] [--user <user>]
                               [--project <project>]
                               [--availability-zone <availability-zone>]
                               [--consistency-group consistency-group>]
                               [--property <key=value>] [--hint <key=value>]
                               [--multi-attach]
                               <name>
```
    - `openstack volume create --size 1 myVolume` 1G volume 만들기

- attach volume to VM

```
usage: openstack server add volume [-h] [--device <device>] <server> <volume>
```
    - `openstack server add volume bcf6ac48-7bb2-4db6-834d-49bdbc4c3115 6fe88ea2-88b8-482f-abc3-ca24e4319b66`

- log into VM
- create partition on block volume
    - `sudo fdisk /dev/vdb`
- create file system on partition
    - `sudo mkfs.ext4 /dev/vdb1`
- create mountpoint
    - `sudo mkdir /mnt/myDrive`
- mount partition
    - `sudo mount /dev/vdb1 /mnt/myDrive`
- create file on `/mnt/myDrive`
- edit `/etc/fstab` to make mount permanent
- detach volume
- delete VM
- create new VM
- attach volume
- verify that file still exists on volume

# Openstack REST API
- 각 각 서비스 API 이용하기 위해 먼저 Keystone에서 인증을 받아야 함
- Keystone 서비스 인증이 되면 Token이 발급 됨

## Keystone으로 부터 Token 발급 받기

```
```
