<h1>
    <a href="https://www.dio.me/">
     <img align="center" width="40px" src="https://www.ansible.com/images/project-logos/ansible-core.svg"></a>
    <span> Criando uma role common no Ansible</span>
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

A maneira mais simples de usar o script acima é copiar e colar o conteúdo em um arquivo chamado `Vagrantfile`.

Em seguida, em um terminal, execute o seguinte comando:

```
vagrant up
```

Isso criará e iniciará a máquina virtual e aplicará a configuração do Ansible.

## Estrutura do Projeto

Certifique-se de que seu projeto tenha a seguinte estrutura:

```
.
├── Vagrantfile
├── playbook.yml
└── roles
    └── common
        ├── tasks
        │   └── main.yml
        └── files
            └── id_rsa.pub
```

## playbook.yml

Um exemplo básico de um playbook Ansible para usar a role `common`:

```
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