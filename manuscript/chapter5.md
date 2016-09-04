# Chapter 5  
## Writing modularizing code - Roles  

### 5.1 Creating site-wide playbook  
Create *site.yml* in chapter 5 directory and put  

{title="Listing ", lang=html, linenos=off}
~~~~~~~
---
- include: app.yml
~~~~~~~

Create *app.yml* in the same directory with the following content

{title="Listing ", lang=html, linenos=off}
~~~~~~~
---
- include: app.yml
~~~~~~~

### 5.2 Building our first *role*  
Change your directory to chapter 5. This directory contains the files that we have already created  
Now create a directory called *roles*  
Inside *roles* folder, create another folder called *apache*  
It should contain following tree structure. Create these files and folders.  

{title="Listing ", lang=html, linenos=off}
~~~~~~~
.
├── app.yml
├── roles
│   ├── apache
│   │   ├── files
│   │   │   ├── httpd.conf
│   │   │   └── index.html
│   │   ├── handlers
│   │   │   └── main.yml
│   │   ├── meta
│   │   │   └── main.yml
│   │   └── tasks
│   │       ├── config.yml
│   │       ├── install.yml
│   │       ├── main.yml
│   │       └── start.yml
│   └── base
│       ├── meta
│       │   └── main.yml
│       └── tasks
│           └── main.yml
└── site.yml
~~~~~~~  

#### 5.2.1 Tasks directory

Edit *roles/apache/tasks/install.yml*,  

{title="Listing ", lang=html, linenos=off}
~~~~~~~
---
- name: Installing Apache...
  yum: name=httpd state=latest
~~~~~~~  

In *tasks/start.yml*, put  

{title="Listing ", lang=html, linenos=off}
~~~~~~~
---
- name: Starting Apache...
  service: name=httpd state=started
~~~~~~~  

In *tasks/config.yml*, put  

{title="Listing ", lang=html, linenos=off}
~~~~~~~
---
- name: Copying configuration files...
  copy: src=/code/chp5/roles/apache/files/httpd.conf
        dest=/etc/httpd.conf
        owner=root group=root mode=0644
        state=started
~~~~~~~  

#### 5.2.2 Files directory

Copy *index.html* and *httpd.conf* from chapter5 to */roles/apache/files/* directory  

#### 5.2.3 Meta directory
*main.yml* in this directory, should contain...  

{title="Listing ", lang=html, linenos=off}
~~~~~~~
---
dependencies: [role:base]
~~~~~~~  

#### 5.2.4 Handlers directory  
Put following content in */handlers/main.yml*  

{title="Listing ", lang=html, linenos=off}
~~~~~~~
---
depedencies:
  - {role: base}
~~~~~~~

### 5.3 Testing our role...  
Now let's test the role that we have created...  
Create a file *app.yml* inside */roles*
Edit *app.yml* file and make sure it **only** contains following content.  

{title="Listing ", lang=html, linenos=off}
~~~~~~~
---
  - name: App server configurations
    hosts: app
    become: true
    roles:
      - apache

~~~~~~~  


Then run the ansible-playbook command  
{title="Listing ", lang=html, linenos=off}
~~~~~~~
ansible-playbook playbook.yml
~~~~~~~  


The output will be,  

{title="Listing ", lang=html, linenos=off}
~~~~~~~
PLAY [App server configurations] ***********************************************

TASK [setup] *******************************************************************
ok: [192.168.61.13]
ok: [192.168.61.12]

TASK [apache : Installing Apache...] *******************************************
changed: [192.168.61.13]
changed: [192.168.61.12]

PLAY RECAP *********************************************************************
192.168.61.12              : ok=2    changed=1    unreachable=0    failed=0
192.168.61.13              : ok=2    changed=1    unreachable=0    failed=0
~~~~~~~

## 5.3 Exercises
1. Create roles for *db* and *loadbalancer* following the same workflow