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


