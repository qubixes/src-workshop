# src-workshop




Steps:  
Perform steps 1 and 2 as soon as possible

## 1. Create a storage volume
Find instructions [here](https://utrechtuniversity.github.io/vre-docs/docs/getting-started.html)

## 2. Create a simple workspace (Ubuntu 20, 1 CPU core)
Find instructions [here](https://servicedesk.surf.nl/wiki/display/WIKI/Start+a+simple+workspace)

## 3. Install Ansible on your local PC  
You can [install Ansible via pip](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible)  
of [install Ansible via apt](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-ubuntu-20-04)  

[Further reading about ansible](https://www.digitalocean.com/community/conceptual-articles/an-introduction-to-configuration-management-with-ansible)

## 4. Create an inventory file
In this file you specify the server addresses and if necessary you can group them. In our case we add the IP address of the Research Cloud workspace.

Open the file `/etc/ansible/hosts` with a text editor (e.g. `nano`):

`sudo nano /etc/ansible/hosts`

Add the following contents:
```
[researchcloud]
server1 ansible_host=<IP-address>

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

## 4. Test the connection 
Use the following command to test your connection (find your username in the research cloud portal under the profile tab).

`ansible all -m ping -u <researchcloud username>`

You should see this response:
```
server1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Now you can use Ansible to run tasks on the Research Cloud workspace.

## 5. Install something on the remote machine

Run an `apt install` task on the workspace using ansible (e.g. install zip).

`ansible server1 -m apt -a "name=zip state=latest" -u <username> –become`

`-m` stands for Ansible module, in this case we use the `apt` module to run apt install remotely.  
`-a` The -a option accepts options either through the `key=value` syntax or a JSON string   
`-u` specify user  
`-become` run the command as root  

Now we know how to run commands remotely via ansible, now let's create a script.
In Ansible this is called a 'playbook'.

## 6. Create a playbook

Create a new directory and an ‘ansible playbook’ called e.g. `test.yml`
Add the following script:
```
---
- name: Some playbook
  hosts: server1
  tasks:
  - name: Print something
    debug:
      msg: "Printing a test message"
```

Run it as follows:
`ansible-playbook test.yml -u <username>`

Now extend the script with some other commands, feel free to experiment 

It is good practice to use [built-in modules](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/index.html) of ansible to perform tasks on a server (in our case the research cloud workspace). However, it is also possible to [run shell commands](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/shell_module.html) or start a script.

In this example we use the `pip` built-in module to install asreview:
```
---
- name: Some playbook
  hosts: server1
  tasks:
  - name: Install ASReview LAB through pip
    pip:
      name: asreview
  - name: Print something
    debug:
      msg: "Printing a test message"
``` 
Now extend the script with some other commands, feel free to experiment :smiley:

## 7. Create a script 
Now create your own playbook in a public GitHub repository.

The playbook should do the following

1. Check if git is installed
2. If it is not, install it
3. Clone a public GitHub repository that can be specified by the user of this plug in
Hint: it is possible to use variables in your ansible script. You need to create the variable in the Plug in as well so the user can define the value when he creates a workspace. [More info](https://servicedesk.surf.nl/wiki/display/WIKI/Using+parameters)

## 8. Create a plugin 
