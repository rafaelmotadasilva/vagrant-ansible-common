<h1>
    <img align="center" width="40px" src="https://www.ansible.com/images/project-logos/ansible-core.svg" alt="Ansible logo">
    <span>Criando uma role common no Ansible</span>
</h1>

Repositório desenvolvido para fins educativos.

## Objetivo

Criar uma máquina virtual através de um arquivo do Vagrantfile. Configurar o provisionamento com Ansible e criar uma role para realizar as seguintes tarefas:

- Instalar pacotes essenciais.
- Definir o nome da máquina.
- Criar um usuário com sua chave SSH (sem senha).

## Vagrantfile

Este é um exemplo simples de `Vagrantfile`, que cria uma máquina virtual e configura o provisionamento com Ansible.

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.network "public_network"
  config.vm.hostname = "myhost.local"
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
  end
end
```

Se mais de uma interface de rede estiver disponível na máquina host, o Vagrant solicitará que você escolha qual interface a máquina virtual deve fazer a ponte.

## Executando o script

Após criar o Vagrantfile, você pode iniciar a máquina virtual com o comando:

```
vagrant up
```

Esse comando é fundamental no Vagrant, pois ele cria e configura as máquinas virtuais (VMs) conforme definido no seu Vagrantfile.

## Estrutura do Projeto

Certifique-se de que seu projeto tenha a seguinte estrutura:

```
.
├── playbook.yml
├── README.md
├── roles
│   └── common
│       ├── files
│       │   └── id_rsa.pub
│       └── tasks
│           └── main.yml
└── Vagrantfile
```

## playbook.yml

Um exemplo básico de um playbook Ansible para usar a role `common`:

```
---
- name: Setup common configuration
  hosts: all
  become: yes
  roles:
    - common
```

## roles/common/tasks/main.yml

Crie a role `common` para realizar as seguintes tarefas:

```
---
- name: Install common packages
  apt:
    name:
      - vim
      - curl
      - telnet
      - unzip
      - wget
      - net-tools
      - htop
      - nmap
    state: present
    update_cache: yes

- name: Set hostname
  hostname:
    name: "{{ ansible_hostname }}"

- name: Create user with SSH key
  user:
    name: rafaelmota
    shell: /bin/bash
    state: present

- name: Set authorized key in alternate location
  authorized_key:
    user: rafaelmota
    state: present
    key: "{{ lookup('file', 'roles/common/files/id_rsa.pub') }}"
    ```
