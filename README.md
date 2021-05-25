### tcbdesafioansiblehero

## How we use IaC to migrate on-premises users to the AWS cloud through Ansible..

Ansible role to create users, groups and policies

#Motivation

The bore of migrating tons of users on-premise to the cloud, particularly to AWS.

Who needs this? 
All companies and businesses need to migrate several users on-prem to AWS.

How: Instead of manually creating the users in AWS IAM, a quick way is to automate the task using Ansible.

#Requirements

*Ansible 
*AWS account 
*Linux

```yml
What it does:
- [X] Creates groups
- [X] Creates Users
- [X] Sets password to Users
- [X] Adds Users to Groups
- [X] Add Policies
- [X] Enable Multi-factor authentication (MFA)
  
```

## aws-mfa-enforce.yaml
```yml
---
aws_mfaenforce: arn:aws:iam::<Account ID>:policy/EnforceMFAPolicy

```
## aws-create-groups-users-csv.yaml

```yml
---
- name: Create AWS resources
  hosts: local
  connection: local
  gather_facts: False
  no_log: False
  vars_files: aws-mfa-enforce.yaml

  tasks:

# Create groups
    - name: Read new AWS groups from CSV file
      read_csv:
        path: groups-users-to-aws.csv
        key: grupo
      register: grupo

    - name: Creating new AWS groups from CSV file
      community.aws.iam_group:
        name: "{{ item.value.grupo }}"
        managed_policies:
          - "{{ item.value.politica }}"
          - "{{aws_mfaenforce}}"
        state: present
      loop: "{{ grupo.dict|dict2items }}"

# Create users
    - name: Read new AWS users from CSV file
      read_csv:
        path: groups-users-to-aws.csv
      register: users

    - name: Creating new AWS users
      iam:
        iam_type: user
        name: "{{ item.usuario }}"
        state: present
        groups: "{{ item.grupo }}"
        password: "{{ item.senha}}"
      loop: "{{ users.list }}"

```

## groups-users-to-aws.csv
```yml
usuario,senha,grupo,politica
bootcamper.tcb,MeMuda123456!,CloudAdmin,arn:aws:iam::aws:policy/AdministratorAccess
Stefano.Edge,MeMuda123456!,DBA,arn:aws:iam::aws:policy/AmazonRDSFullAccess
Cosmo.Meyer,MeMuda123456!,Estagiarios,arn:aws:iam::aws:policy/ReadOnlyAccess
Olivia-Rose.Huang,MeMuda123456!,LinuxAdmin,arn:aws:iam::aws:policy/AmazonEC2FullAccess
Karolina.Cassidy,MeMuda123456!,RedesAdmin,arn:aws:iam::aws:policy/AmazonVPCFullAccess

```
## aws-delete-users-csv.yaml
```yml
---
- name: Create AWS resources
  hosts: local
  connection: local
  gather_facts: False
  no_log: False
  vars_files: aws-mfa-enforce.yaml

  tasks:

# Create groups
    - name: Read new AWS groups from CSV file
      read_csv:
        path: groups-users-to-aws.csv
        key: grupo
      register: grupo

    - name: Creating new AWS groups from CSV file
      community.aws.iam_group:
        name: "{{ item.value.grupo }}"
        managed_policies:
          - "{{ item.value.politica }}"
          - "{{aws_mfaenforce}}"
        state: absent
      loop: "{{ grupo.dict|dict2items }}"

# Create users
    - name: Read new AWS users from CSV file
      read_csv:
        path: groups-users-to-aws.csv
      register: users

    - name: Creating new AWS users
      iam:
        iam_type: user
        name: "{{ item.usuario }}"
        state: absent
        groups: "{{ item.grupo }}"
        password: "{{ item.senha}}"
      loop: "{{ users.list }}"

```

*Live :v:
*Love :heart:
*Learn :see_no_evil: :hear_no_evil: :speak_no_evil:

:rocket::rocket::rocket:
*ansible
*aws
*iam
*pracima 
*thecloudbootcamp
