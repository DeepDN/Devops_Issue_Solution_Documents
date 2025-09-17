# **How To Upgrade Jenkins Version on Linux OS**

### Step 1:-

- login jenkins server
- goto jenkins manager
- go to system information
- check the pacth of ([Executable war]

### Step 2:-

- go to terminal
- take ssh access of jenkins server
- go to the (Ececutable war) path eg:- cd /usr/share/java/
- you can able to see jenkins.war file
- replace old file file with new file name for backup
- cmd:—  mv jenkins.war jenkins.war-bkp
- then stop jenkins cmd:— sudo systemctl stop jenkins
- for check jenkins status:- sudo systemctl status jenkins
- then download new file to same location
- cmd:— sudo wget https://sg.mirror.servanamanaged.com/jenkins/war/2.433/jenkins.war
- then you can able to see new jenkins.war file is downloaded
- then start jenkins
- cmd:- sudo systemctl start jenkins
- check jenkins status
- for check jenkins status:- sudo systemctl status jenkins
- for check version :- jenkins —version

you can able to see your jenkins version is upgraded to new jenkins version

Youtube link for refrence➖

[https://www.notion.so/How-To-Upgrade-Jenkins-Version-on-Linux-OS-6d559b633fc54a8d92a5e81bcba4a75c?pvs=4#431be93fb4ca4c5bb1672b9ebd9a440b]

(https://www.notion.so/How-To-Upgrade-Jenkins-Version-on-Linux-OS-6d559b633fc54a8d92a5e81bcba4a75c?pvs=21)