
#GitLab

Demo instance <http://demo.gitlab.com/>

Up to 10 private repositories for free <https://gitlab.com/dashboard/projects>



h1. GitLab

pronunciation 发音 /ɡɪtlæb/ 

Home page http://gitlab.org/

Demo server is http://demo.gitlab.com/

Our test server http://gitlab.funshion.com/gitlab/   

"Working with remote Repositories at Eclipse Wiki":http://wiki.eclipse.org/EGit/User_Guide/Remote

LDAP authentication not yet working.

现在，可以了解一下界面和功能

h2. GitLab on Windows use case

h3. Windows环境下安装GIT客户端的步骤

* 1.下载GIT安装包：http://code.google.com/p/msysgit/

* 2.安装并执行Git Bash

* 3.登录http://gitlab.funshion.com/gitlab/users/sign_in ，点击Other Sign in，输入UserName和Password（可联系保罗提供）

* 4.如果是第一次登录，点击New Project，输入zhoumotest，执行创建

* 5.点击右上角My Profile菜单，点击Add Public Key

* 6.在Git Bash中输入命令，一直回车，会得到如下信息：
<pre>ssh-keygen -t rsa -C "zhoumo1@function.com"</pre>
<pre>
The key fingerprint is:
07:b0:17:0f:3b:4a:bb:dd:5f:de:59:bb:02:a0:40:97 zhoumo1@function.com
</pre>
这就是Add an SSH Key页面中的Title

* 7.打开C:\Users\zhoumo1\.ssh\id_rsa.pub，里面的信息就是Add an SSH Key页面中的Key

* 8.点击Save保存SSH Key

* 9.在Git Bash中输入如下一组命令：
<pre>
git config --global user.name "zhoumo1"
git config --global user.email "zhoumo1@funshion.com"
mkdir zhoumotest
cd zhoumotest
git init
touch README
git add README
git commit -m 'first commit'
git remote add origin git@192.168.133.10:zhoumo1/zhoumotest.git
git push -u origin master
</pre>
* 10.如果整个过程不需要输入Password，则证明安装成功。

h2. FAQ

Q: I am requested to enter git password.
A: It is sign of not correct configuration, please check with steps above.

h2. Admin notes

gitlab.funshion.com (IP： 192.168.133.10      mask：255.255.255.0      gateway:192.168.133.1   dns：192.168.1.14  192.168.1.15)

See/Configure gitlab.yml for LDAP & Redmine integration
$ less /opt/bitnami/apps/gitlab/htdocs/config/gitlab.yml

See example at https://github.com/gitlabhq/gitlabhq/blob/master/config/gitlab.yml.example#L82-L91

h3. Installation

Download from http://bitnami.com/stack/gitlab/virtual-machine
Check readme & changelog on website.
After download, check readme inside .zip

http://wiki.bitnami.com/Virtual_Appliances_Quick_Start_Guide 有一点儿满

Virtual Box

BitNami virtual appliances ship an OVF file which makes it easy to import the machine in VirtualBox 3 or previous versions. If you are using Virtual Box 4 or later,  you would like to import the Virtual Appliance manually. You can do it following these steps:

    Create a new Virtual Machine and set that it is a Linux Ubuntu 64bit machine.
    Configure RAM to 512 or higher.
    Choose "existing hard disk" and select the BitNami "vmdk" file. Be sure that you select the main one (without s00X in the file name, not any of the other vmdk files: s001.vmdk, s002.vmdk, etc).
    If you are using a previous VMware image with Ubuntu 10.10, go to Settings -> System -> Processor -> Enable PAE. For Ubuntu 12.04 images it is not necessary.
    Check that the Network configuration is "Bridged Adapter" to have access from remote machines in your network.
    Start your machine

h4. IP address

IP： 192.168.133.10      mask：255.255.255.0      gateway:192.168.133.1   dns：192.168.1.14  192.168.1.15

1) Temporarily until reboot (takes affect immediately)
sudo ifconfig eth0 192.168.133.10 netmask 255.255.255.0

5) Fixed (takes affect after @reboot@)

the appropriate interface in the file /etc/network/interfaces
> sudo vi /etc/network/interfaces

auto eth0
iface eth0 inet static
address 192.168.133.10
netmask 255.255.255.0
gateway 192.168.133.1

Esc :w - write/save file
Esc :q - quit

h4. configure IP address in GitLab

http://wiki.bitnami.com/Applications/BitNami_GitLab#How_to_log_in_the_GitLab_application.3f

$ sudo /opt/bitnami/apps/gitlab/updateip --machine_hostname gitlab.funshion.com

If you have configured GitLab to use an static domain name, you *must rename* the "/opt/bitnami/apps/gitlab/updateip" to prevent it's automatic execution after reboot. See http://stackoverflow.com/questions/16539993/bitnami-gitlab-stack-5-1-0-2-rewrites-gitlab-yml-after-reboot

$ cd /opt/bitnami/apps/gitlab/
$ sudo mv updateip updateip0

Open http://gitlab.funshion.com/ to check

h4. How to enable sshd?

http://wiki.bitnami.com/Virtual_Appliances_Quick_Start_Guide#How_to_enable_sshd.3f

<pre>
$ sudo mv /etc/init/ssh.conf.back /etc/init/ssh.conf
$ sudo start ssh
</pre>

h4. LDAP

change /opt/bitnami/apps/gitlab/htdocs/config/gitlab.yml to have 

  ## LDAP settings
  ldap:
    enabled: true
    host: '192.168.1.14'
    base: 'DC=funshion,DC=com'
    port: 389
    uid: 'sAMAccountName'
    method: "plain"
    bind_dn: 'gitlab-ldap@funshion.com'
    password: '不告诉你'

https://raymii.org/s/tutorials/Gitlab_and_Active_Directory_LDAP_Authentication.html


How to restart GitLab for changed gitlab.yml to take effect?
<pre>
sudo /opt/bitnami/ctlscript.sh restart gitlab_sidekiq
</pre>

http://stackoverflow.com/questions/16539993/bitnami-gitlab-stack-5-1-0-2-rewrites-gitlab-yml-after-reboot

h4. Redmine integration

https://github.com/gitlabhq/gitlabhq/blob/master/config/gitlab.yml.example#L50-L68

	redmine:
		# ## If not nil, link 'Issues' on project page will be replaced with this
		# ## Use placeholders:
		# ## :project_id - GitLab project identifier
		# ## :issues_tracker_id - Project Name or Id in external issue tracker
		project_url: "http://redmine.funshion.com/redmine/projects/:issues_tracker_id"
		#
		# ## If not nil, links from /#\d/ entities from commit messages will replaced with this
		# ## Use placeholders:
		# ## :project_id - GitLab project identifier
		# ## :issues_tracker_id - Project Name or Id in external issue tracker
		# ## :id - Issue id (from commit messages)
		issues_url: "http://redmine.funshion.com/redmine/issues/:id"
		#
		# ## If not nil, linkis to creating new issues will be replaced with this
		# ## Use placeholders:
		# ## :project_id - GitLab project identifier
		# ## :issues_tracker_id - Project Name or Id in external issue tracker
		new_issue_url: "http://redmine.funshion.com/redmine/projects/:issues_tracker_id/issues/new"

h3. Update

Standard GitLab update instructions https://github.com/gitlabhq/gitlabhq/tree/master/doc/update are not application

@sudo -u git -H bundle exec rake gitlab:env:info RAILS_ENV=production

@sudo -u git -H bundle exec rake gitlab:check RAILS_ENV=production

both fail to show environment status

h3. Support

https://github.com/gitlabhq/gitlab-public-wiki/wiki/Trouble-Shooting-Guide

h3. Links

https://wiki.archlinux.org/index.php/Gitlab

from https://github.com/gitlabhq/gitlab-public-wiki/wiki

> https://raymii.org/s/tutorials/Gitlab_and_Active_Directory_LDAP_Authentication.html