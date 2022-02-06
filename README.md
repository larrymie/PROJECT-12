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

Launched 2 fresh EC2 instances using RHEL 8 image, I used them as my uat servers, gave them names accordingly – **Web1-UAT<172.31.40.43> and Web2-UAT <172.31.33.168>.

I created a directory called roles manually. the structure is represented in IMAGE 10
![10](https://user-images.githubusercontent.com/91284177/152699130-4ede956d-25ba-4f54-b41f-7c4e74e41f9b.png)
my inventory ansible-config-mgt/inventory/uat.yml file with IP addresses of my 2 UAT Web servers. IMAGE11 

![11](https://user-images.githubusercontent.com/91284177/152699286-9f730929-f8bd-456a-a2ce-677e5c794c92.png)
In /etc/ansible/ansible.cfg file i uncommented roles_path string and provided a full path to my roles directory roles_path    = /home/ubuntu/ansible-config-mgt/roles, so Ansible could know where to find configured roles.
 In my tasks directory, within the main.yml file, I  wrote configuration tasks to do the following:
 Install and configure Apache (httpd service)
Clone Tooling website from GitHub https://github.com/larrymie/tooling.git.
Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
Make sure httpd service is started. IMAGE12
![12](https://user-images.githubusercontent.com/91284177/152699460-a46ae49b-2b2a-4a19-935b-69a7bc7f70bd.png)

**REFERENCE WEBSERVER ROLE

Within the static-assignments folder, I created a new assignment for uat-webservers uat-webservers.yml. This is where you I referenced the role.
![13](https://user-images.githubusercontent.com/91284177/152699765-648d3d14-48b2-4a32-ae7c-7ee77d6535b3.png)
 I refered my uat-webservers.yml role inside site.yml. IMAGE 14
![14](https://user-images.githubusercontent.com/91284177/152699861-93309eb5-624d-49e5-bf7d-6702dcd011a2.png)

Commited my changes, created a Pull Request and merge them to master branch, made sure webhook triggered two consequent Jenkins jobs, ran successfully and copied all the files to my Jenkins-Ansible server into /home/ubuntu/ansible-config-mgt/ directory. IMAGES 15-19
![15](https://user-images.githubusercontent.com/91284177/152699995-7d5b3def-7a20-45f3-99c1-b892308c4605.png)
![16](https://user-images.githubusercontent.com/91284177/152700005-9673d9db-a32c-4334-b0c7-0a88ec02bcb0.png)
![17](https://user-images.githubusercontent.com/91284177/152700012-cdc129c6-4f79-4277-90af-de88b476b306.png)
![18](https://user-images.githubusercontent.com/91284177/152700014-c47b6968-1f44-46f9-9906-9b9e15d244a3.png)
![19](https://user-images.githubusercontent.com/91284177/152700019-61a9e481-4bf6-4e52-bf94-7d6ccf912e18.png)

I ran the playbook against my uat inventory, and was successful. IMAGE 20

![20](https://user-images.githubusercontent.com/91284177/152700087-24aa4c2d-3326-4e6b-9116-5674a446e6d5.png)

I saw both of my UAT Web servers configured and you tried to reach them from my browser using <http://172.31.40.43/index.php and http:/172.31.33.168/index.php> IMAGES 21 & 22
![21](https://user-images.githubusercontent.com/91284177/152700225-bef5f82c-0b61-4458-b355-c18cc85862f4.png)
![22](https://user-images.githubusercontent.com/91284177/152700233-0b71bf3e-cd20-4382-92a7-0e355543c449.png)








