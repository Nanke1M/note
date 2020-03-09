2. ps -e |grep ssh查看后只有
    “3748 ?        00:00:00 ssh-agent"
3. 安装
    sudo apt-get install openssh-server
4. 启动sshd。
    sudo /etc/init.d/ssh start
5. 再次查看：
    ps -e |grep ssh
    3748 ? 00:00:00 ssh-agent
    4085 ? 00:00:00 sshd

6.执行ssh localhos