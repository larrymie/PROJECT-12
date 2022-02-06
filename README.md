# PROJECT-12
ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)

**Step 1 – Jenkins job enhancement
I made some changes to my Jenkins job – now every new change in the codes creates a separate directory which is not very convenient when I want to run some commands from one place. Besides, it consumes space on Jenkins server with each subsequent change. I enhanced it by introducing a new Jenkins project/job which required Copy Artifact plugin.

I created a new directory called ansible-config-artifact on my Jenkins-Ansible server where I stored all artifacts after each build. IMAGE 01
![01](https://user-images.githubusercontent.com/91284177/151956295-c52addef-b60e-4056-b8a6-831599d7d39d.png)

Changed permissions to this directory, so Jenkins could save files there using <chmod -R 0777 /home/ubuntu/ansible-config-artifact>
On my jenkins console I installed Copy artfact following this path -> Manage Jenkins -> Manage Plugins -> on Available tab searched for Copy Artifact and installed the plugin without restarting Jenkins. IMAGE 02
![02](https://user-images.githubusercontent.com/91284177/151956995-184e7c58-78bc-42fc-8495-20b4327b946d.png)
I created a new Freestyle project and named it **save_artifacts.

This project was triggered by completion of my existing ansible project. I Configured it accordingly:
**Note: You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your ansible job. 

Tested my set up by making some changes in README.MD file inside my ansible-config-mgt repository (right inside master branch). I saw my files inside /home/ubuntu/ansible-config-artifact. IMAGE 03
![03](https://user-images.githubusercontent.com/91284177/151957984-2851685a-5cfe-4e0b-8604-61acf33993df.png)
The changes was subsequently updated with every commit to my master branch. IMAGE 04
![04](https://user-images.githubusercontent.com/91284177/151958419-e37b2c17-484a-4dd7-8474-256cfd37a785.png)

**REFACTOR ANSIBLE CODE BY IMPORTING OTHER PLAYBOOKS INTO SITE.YML
Step 2 – Refactor Ansible code by importing other playbooks into site.yml

Within playbooks folder,I created a new file and named it site.yml. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed.
I created a new folder in root of the repository and named it static-assignments. The static-assignments folder is where all other children playbooks will be stored.
I moved common.yml file into the newly created static-assignments folder.
Inside site.yml file, I imported common.yml playbook.
Ran ansible-playbook command against the dev environment. I created another playbook under static-assignments and named it common-del.yml. In this playbook and configured deletion of wireshark utility. IMAGE 05
![05](https://user-images.githubusercontent.com/91284177/152387596-8560b516-d872-4993-8a87-e4ca03d4d6ca.png)

Updated site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers. IMAGE 06
![06](https://user-images.githubusercontent.com/91284177/152387848-3b8afef2-f720-47d5-834e-2c95a0a33ac0.png)
I ran the ansible playbook using <ansible-playbook -i /home/ubuntu/ansible-config-artifact/inventory/dev.yml /home/ubuntu/ansible-config-artifact/playbooks/site.yml> IMAGE 07 showed it ran succeessfully.
![07](https://user-images.githubusercontent.com/91284177/152390640-f670a49b-b173-4dc9-acbb-ab31efe0d0ea.png)

I confirmed in two of the servers (with IP adresses 172.31.31.75 and 172.31.41.130 ) that wireshark was successfully removed. IMAGES 08, 09.
![08](https://user-images.githubusercontent.com/91284177/152390501-9f84a05a-8406-4717-8171-148d688bd9bb.png)
![09](https://user-images.githubusercontent.com/91284177/152390511-73b39d32-1a3a-4a5e-8084-f09137c1e61f.png)

**CONFIGURE UAT WEBSERVERS WITH A ROLE ‘WEBSERVER’

Launched 2 fresh EC2 instances using RHEL 8 image, I used them as my uat servers, gave them names accordingly – **Web1-UAT and Web2-UAT.







