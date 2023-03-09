

### **crond 服务管理**

重新启动crond服务

service crond restart



crontab 定时任务设置

crontab [选项]

| 选项 | 功能                          |
| ---- | ----------------------------- |
| -e   | 编辑crontab定时任务           |
| -l   | 查询crontab任务               |
| -r   | 删除当前用户所有的crontab任务 |







Linux crontab 命令

[![Linux 命令大全](https://www.runoob.com/images/up.gif) Linux 命令大全](https://www.runoob.com/linux/linux-command-manual.html)

Linux crontab是用来定期执行程序的命令。

当安装完成操作系统之后，默认便会启动此任务调度命令。

crond 命令每分锺会定期检查是否有要执行的工作，如果有要执行的工作便会自动执行该工作。

**注意：**新创建的 cron 任务，不会马上执行，至少要过 2 分钟后才可以，当然你可以重启 cron 来马上执行。

而 linux 任务调度的工作主要分为以下两类：

- 1、系统执行的工作：系统周期性所要执行的工作，如备份系统数据、清理缓存
- 2、个人执行的工作：某个用户定期要做的工作，例如每隔10分钟检查邮件服务器是否有新信，这些工作可由每个用户自行设置

### 语法

```
crontab [ -u user ] file
```

或

```
crontab [ -u user ] { -l | -r | -e }
```

**说明：**

crontab 是用来让使用者在固定时间或固定间隔执行程序之用，换句话说，也就是类似使用者的时程表。

-u user 是指设定指定 user 的时程表，这个前提是你必须要有其权限(比如说是 root)才能够指定他人的时程表。如果不使用 -u user 的话，就是表示设定自己的时程表。

**参数说明**：

- -e : 执行文字编辑器来设定时程表，内定的文字编辑器是 VI，如果你想用别的文字编辑器，则请先设定 VISUAL 环境变数来指定使用那个文字编辑器(比如说 setenv VISUAL joe)
- -r : 删除目前的时程表
- -l : 列出目前的时程表

时间格式如下：

```
f1 f2 f3 f4 f5 program
```

- 其中 f1 是表示分钟，f2 表示小时，f3 表示一个月份中的第几日，f4 表示月份，f5 表示一个星期中的第几天。program 表示要执行的程序。
- 当 f1 为 * 时表示每分钟都要执行 program，f2 为 * 时表示每小时都要执行程序，其馀类推
- 当 f1 为 a-b 时表示从第 a 分钟到第 b 分钟这段时间内要执行，f2 为 a-b 时表示从第 a 到第 b 小时都要执行，其馀类推
- 当 f1 为 */n 时表示每 n 分钟个时间间隔执行一次，f2 为 */n 表示每 n 小时个时间间隔执行一次，其馀类推
- 当 f1 为 a, b, c,... 时表示第 a, b, c,... 分钟要执行，f2 为 a, b, c,... 时表示第 a, b, c...个小时要执行，其馀类推

```
*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 6) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12) 
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```

使用者也可以将所有的设定先存放在文件中，用 crontab file 的方式来设定执行时间。

### 实例

每一分钟执行一次 /bin/ls：

```
* * * * * /bin/ls
```

在 12 月内, 每天的早上 6 点到 12 点，每隔 3 个小时 0 分钟执行一次 /usr/bin/backup：

```
0 6-12/3 * 12 * /usr/bin/backup
```

周一到周五每天下午 5:00 寄一封信给 alex@domain.name：

```
0 17 * * 1-5 mail -s "hi" alex@domain.name < /tmp/maildata
```

每月每天的午夜 0 点 20 分, 2 点 20 分, 4 点 20 分....执行 echo "haha"：

```
20 0-23/2 * * * echo "haha"
```

下面再看看几个具体的例子：

```
0 */2 * * * /sbin/service httpd restart  意思是每两个小时重启一次apache 

50 7 * * * /sbin/service sshd start  意思是每天7：50开启ssh服务 

50 22 * * * /sbin/service sshd stop  意思是每天22：50关闭ssh服务 

0 0 1,15 * * fsck /home  每月1号和15号检查/home 磁盘 

1 * * * * /home/bruce/backup  每小时的第一分执行 /home/bruce/backup这个文件 

00 03 * * 1-5 find /home "*.xxx" -mtime +4 -exec rm {} \;  每周一至周五3点钟，在目录/home中，查找文件名为*.xxx的文件，并删除4天前的文件。

30 6 */10 * * ls  意思是每月的1、11、21、31日是的6：30执行一次ls命令
```

**注意：**当程序在你所指定的时间执行后，系统会发一封邮件给当前的用户，显示该程序执行的内容，若是你不希望收到这样的邮件，请在每一行空一格之后加上 **> /dev/null 2>&1** 即可，如：

```
20 03 * * * . /etc/profile;/bin/sh /var/www/runoob/test.sh > /dev/null 2>&1 
```

### 脚本无法执行问题

如果我们使用 crontab 来定时执行脚本，无法执行，但是如果直接通过命令（如：./test.sh)又可以正常执行，这主要是因为无法读取环境变量的原因。

**解决方法：**

- 1、所有命令需要写成绝对路径形式，如: **/usr/local/bin/docker**。

- 2、在 shell 脚本开头使用以下代码：

  ```
  #!/bin/sh
  
  . /etc/profile
  . ~/.bash_profile
  ```

  3、在 **/etc/crontab** 中添加环境变量，在可执行命令之前添加命令 **. /etc/profile;/bin/sh**，使得环境变量生效，例如：

  ```
  20 03 * * * . /etc/profile;/bin/sh /var/www/runoob/test.sh
  ```





# [crontab用法与实例](https://www.linuxprobe.com/how-to-crontab.html)



![crontab用法与实例crontab用法与实例](http://n.occc.ml:5244/NEOE/pic/LINUX/crontab组成.jpg)

**crontab配置文件**

Linux下的任务调度分为两类：系统任务调度和用户任务调度。Linux系统任务是由 cron (crond) 这个系统服务来控制的，这个系统服务是默认启动的。用户自己设置的计划任务则使用crontab命令。在CentOS系统中，

```
cat /etc/crontab
```

配置文件可以看到如下解释：

```
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
HOME=/
# For details see man 4 crontabs
# Example of job definition:
# .---------------- minute (0 - 59)
# | .------------- hour (0 - 23)
# | | .---------- day of month (1 - 31)
# | | | .------- month (1 - 12) OR jan,feb,mar,apr ...
# | | | | .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# | | | | |
# * * * * * user-name command to be executed
```

前四行是用来配置crond任务运行的环境变量，第一行SHELL变量指定了系统要使用哪个shell，这里是bash；第二行PATH变量指定了系统执行命令的路径；第三行MAILTO变量指定了crond的任务执行信息将通过电子邮件发送给root用户，如果MAILTO变量的值为空，则表示不发送任务执行信息给用户；第四行的HOME变量指定了在执行命令或者脚本时使用的主目录。

用户定期要执行的工作，比如用户数据备份、定时邮件提醒等。用户可以使用 crontab 工具来定制自己的计划任务。所有用户定义的crontab 文件都被保存在 /var/spool/cron目录中。其文件名与用户名一致。

**crontab文件含义**

用户所建立的crontab文件中，每一行都代表一项任务，每行的每个字段代表一项设置，它的格式共分为六个字段，前五段是时间设定段，第六段是要执行的命令段，格式如下：
minute hour day month week command
![crontab用法与实例crontab用法与实例](http://n.occc.ml:5244/NEOE/pic/LINUX/crontab.png)

在以上各个字段中，还可以使用以下特殊字符：

"*"代表所有的取值范围内的数字，如月份字段为*，则表示1到12个月；

"/"代表每一定时间间隔的意思，如分钟字段为*/10，表示每10分钟执行1次。

"-"代表从某个区间范围，是闭区间。如“2-5”表示“2,3,4,5”，小时字段中0-23/2表示在0~23点范围内每2个小时执行一次。

","分散的数字（不一定连续），如1,2,3,4,7,9。

注：由于各个地方每周第一天不一样，因此Sunday=0（第一天）或Sunday=7（最后1天）。

**crontab命令详解**

格式:

```
crontab [-u user] file
crontab [ -u user ] [ -i ] { -e | -l | -r }
```

- • -u user：用于设定某个用户的crontab服务；
- • file: file为命令文件名，表示将file作为crontab的任务列表文件并载入crontab；
- • -e：编辑某个用户的crontab文件内容，如不指定用户则表示当前用户；
- • -l：显示某个用户的crontab文件内容，如不指定用户则表示当前用户；
- • -r：从/var/spool/cron目录中删除某个用户的crontab文件。
- • -i：在删除用户的crontab文件时给确认提示。

**crontab注意点**

1. crontab有2种编辑方式：直接编辑/etc/crontab文件与crontab –e，其中/etc/crontab里的计划任务是系统中的计划任务，而用户的计划任务需要通过crontab –e来编辑；
2. 每次编辑完某个用户的cron设置后，cron自动在/var/spool/cron下生成一个与此用户同名的文件，此用户的cron信息都记录在这个文件中，这个文件是不可以直接编辑的，只可以用crontab -e 来编辑。
3. crontab中的command尽量使用绝对路径，否则会经常因为路径错误导致任务无法执行。
4. 新创建的cron job不会马上执行，至少要等2分钟才能执行，可从起cron来立即执行。
5. %在crontab文件中表示“换行”，因此假如脚本或命令含有%,需要使用\%来进行转义。

**crontab配置实例**

每一分钟执行一次command（因cron默认每1分钟扫描一次，因此全为*即可）

```
*    *    *    *    *  command
```

每小时的第3和第15分钟执行command

```
3,15   *    *    *    *  command
```

每天上午8-11点的第3和15分钟执行command：

```
3,15  8-11  *  *  *  command
```

每隔2天的上午8-11点的第3和15分钟执行command：

```
3,15  8-11  */2  *   *  command
```

每个星期一的上午8点到11点的第3和第15分钟执行command

```
3,15  8-11   *   *  1 command
```

每晚的21:30重启smb

```
30  21   *   *  *  /etc/init.d/smb restart
```

每月1、10、22日的4 : 45重启smb

```
45  4  1,10,22  *  *  /etc/init.d/smb restart
```

每周六、周日的1 : 10重启smb

```
10  1  *  *  6,0  /etc/init.d/smb restart
```

每天18 : 00至23 : 00之间每隔30分钟重启smb

```
0,30  18-23  *  *  *  /etc/init.d/smb restart
```

每一小时重启smb

```
*  */1  *  *  *  /etc/init.d/smb restart
```

晚上11点到早上7点之间，每隔一小时重启smb

```
*  23-7/1  *   *   *  /etc/init.d/smb restart
```

每月的4号与每周一到周三的11点重启smb

```
0  11  4  *  mon-wed  /etc/init.d/smb restart
```

每小时执行/etc/cron.hourly目录内的脚本

```
0  1   *   *   *     root run-parts /etc/cron.hourly
```

本文主要讲解crontab如何使用和一些配置实例，下次我将给大家带来crontab配置生成的一些网站以及检查crontab配置是否符合预期的网站。