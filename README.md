## 📋 前提条件
1：一台24小时在线运行的win系统，可以安装ztasker，ztasker的官网为：https://www.everauto.net/  
2：如果是国内的机器，上面已经装好代理软件，比如我例子里的代理软件运行在127.0.0.1:7890  
3：idx上已经装好argo隧道，并且正常运行  
4：一个专门用来保活的chrome核心的浏览器，这里推荐Superium或者Thorium，例子里使用Thorium  

## 🛠️ 如何使用
一个gmail账号可以开3个工作区，所以我这里假设你也开了3个工作区，每个工作区必须对应一个Thorium的单独用户文件夹，假设三个工作区分别叫789a 789b 789c  
打开CMD输入以下命令  
start "" "C:\Users\Administrator\AppData\Local\Thorium\Application\thorium.exe" --user-data-dir="C:\789a" --new-window https://idx.google.com  
注意：这里路径里的Administrator是我主机的用户名，所以这个根据你自己机器的用户名是什么，就改成什么，不要无脑复制我的路径。  
如果你已经设置好系统代理，这时候会弹出新窗口，从全新开始设置Thorium，自动打开的是idx.google.com网站，然后登录你的google账号，登录设置完成后关闭。  
重复上面的步骤，不过这次用户名部分改成789b，依然要登录你的google账号以后才关闭这个窗口  
start "" "C:\Users\Administrator\AppData\Local\Thorium\Application\thorium.exe" --user-data-dir="C:\789b" --new-window https://idx.google.com  
继续重复上面的步骤，不过这次用户名部分改成789c，依然要登录你的google账号以后才关闭这个窗口  
start "" "C:\Users\Administrator\AppData\Local\Thorium\Application\thorium.exe" --user-data-dir="C:\789c" --new-window https://idx.google.com  

这时候最好去检查一下你的C盘根目录下是否已经有名为789a 789b 789c三个文件夹，如果没有，说明你上面操作不成功，重新操作，或者根据网上其它方法给Thorium创建独立的用户文件夹。  

打开ztasker，选择 导入任务，导入提供的 idx保活lizi.ztt，导入以后在上面点右键，再复制出两份，这样就可以对应3个工作区了，下面开始进行修改，把里面的信息改成你的工作区的信息。  

双击第一个“idx保活lizi”  
你可以看到一共16个任务，我下面只说需要修改的地方，最后再做整体的保活思路说明  
序号1：curl -I -s --socks5 127.0.0.1:7890 "https://lizi.man.us.kg" | findstr /R "^HTTP\/" | findstr /R "[0-9][0-9][0-9]"  
127.0.0.1:7890修改成你代理软件的端口，https://lizi.man.us.kg 这个域名修改成你需要保活的argo隧道的域名，其它部分不变  
序号3：窗口标题 lizi，把这里修改成你工作区的名字  
序号4：start "" "C:\Users\Administrator\AppData\Local\Thorium\Application\thorium.exe" --user-data-dir="C:\789a" --new-window https://idx.google.com/lizi-88888888  
同理C:\Users\Administrator这个Administrator修改成你win的用户名，--user-data-dir="C:\789a" 路径修改成你实际的路径（我例子里是789a在C盘根目录），https://idx.google.com/lizi-88888888 这个链接修改成你需要保活的工作区链接，可以去你自己的idx首页去复制。  
序号8：curl -I -s --socks5 127.0.0.1:7890 "https://lizi.man.us.kg" | findstr /R "^HTTP\/" | findstr /R "[0-9][0-9][0-9]"  
这里和序号1的任务是一致的，做同样的修改  
序号13：窗口标题 lizi - Firebase Studio - Thorium 这里把lizi改成你自己需要保活的工作区名字  
序号15：窗口标题 lizi - Firebase Studio - Thorium 这里和序号13一样把lizi改成你自己需要保活的工作区名字  

点击确定保存，这时候任务会马上启动，保活间隔是2分钟。  
这时候你可以去修改第二个“idx保活lizi”，修改的方式和上面相同，然后再更改第三个“idx保活lizi”  

## ⚙️ 保活机制说明
序号1：curl -I -s --socks5 127.0.0.1:7890 "https://lizi.man.us.kg" | findstr /R "^HTTP\/" | findstr /R "[0-9][0-9][0-9]"  
通过代理去检查隧道名为lizi.man.us.kg的argo通道是否在线，如果在线，会获得字串“HTTP/1.1 404 Not Found” 并把获取的内容存入名为work1的变量中  
问：为什么一定要用代理去获取argo通道状态？不用代理不也可以吗？  
答：国内大环境不好，很多ISP对于CF的官方IP有干扰，直连检测有时候会造成检测失败，导致无意义的进入保活流程，所以使用代理检测，如果你的win不是国内机器，那么可以去掉--socks5 127.0.0.1:7890部分  
序号2：检测变量work1是否包含404字符，如果包含，那么条件成立，跳转到任务序号15，如果不包含，条件不成立，跳转到任务序号6  
这里要注意，我自己的隧道Catch-all rule是http_status:404，不代表你的也是，根据你的隧道和服务设置，比如你的也可能是200，所以你最好先手动在CMD里执行一次序号1的命令，确认一下，你得到的代码到底是404还是200  
序号3：结束窗口和进程 窗口标题 lizi  
我这里做了简化，用了部分匹配，如果我的工作区名字叫lizi，那么检测到lizi，就认为窗口存在，所以你给你的工作区起名的时候，最好不要起 特别相似的名字，以免造成误判，这个任务会杀掉窗口标题符合lizi的Thorium进程  
序号4：start "" "C:\Users\Administrator\AppData\Local\Thorium\Application\thorium.exe" --user-data-dir="C:\789a" --new-window https://idx.google.com/lizi-88888888  
这个前面说过就不赘述了，用对应的Thorium用户去打开对应的需要保活的工作区  
序号5：退出流程  
字面意思，退出任务流程  
序号6：延迟，固定间隔5000毫秒  
序号7：设置变量workf1，指定值 1  
这是记录检测隧道在线失败了，记录为1次，写入变量workf1  
序号8：curl -I -s --socks5 127.0.0.1:7890 "https://lizi.man.us.kg" | findstr /R "^HTTP\/" | findstr /R "[0-9][0-9][0-9]"  
前面说过就不赘述了，再次检测隧道在线情况，并且把结果写入work2变量  
序号9：检测变量work2是否包含404字符，如果包含，那么条件成立，跳转到任务序号16，如果不包含，条件不成立，退出任务流程，结束任务  
序号10：设置变量workf2，指定值 1  
序号11：变量运算workf1+workf2，并且把结果放入workre变量  
序号12：变量判断 判断workre变量的数值，如果数值等于2，那么条件成立，继续任务流程，如果不等于2，条件不成立，退出任务流程，结束任务  
序号13：判断窗口是否存在 lizi - Firebase Studio - Thorium  
如果窗口存在，条件成立，跳转到序号3，如果窗口不存在，条件不成立，跳转到任务4  
序号14：退出流程  
字面意思，退出任务流程  
序号15：判断窗口是否存在 lizi - Firebase Studio - Thorium  
如果窗口存在，条件成立，退出流程，结束任务，如果窗口不存在，条件不成立，跳转到任务4  
序号16：退出流程  
字面意思，退出任务流程  

## 📌 保活流程总结
如果你觉得上面的说明看了头晕，那么总结一下，具体的保活流程就是：  
先检查argo通道的在线状态，并且要记录一下检测结果，先放到变量里，如果结果里有404，那就证明隧道在线，隧道在线就说明工作区是活的，但是为了保险，即使有404了，也再去检查一下浏览器的窗口是否存在，如果存在，那么退出流程，不进行保活，如果窗口不存在，打开工作区窗口。  
如果检测结果内容不包含404，那么等待5000毫秒，记录检测失败1次，再进行一次检测，如果结果里有404，那就证明隧道在线，上一次失败可能是其它原因造成的检测失败而已，退出流程，不进行保活。如果这次检测结果依然不包含404，记录检测失败1次，然后变量计算两次结果相加是否等于2，如果不等于2，那么不进行保活，退出流程。如果等于2，继续进入保活流程。  
这时再次检测名称为lizi的工作区窗口是否存在，如果存在就杀掉窗口和进程，重新打开工作区，如果窗口不存在，那么就直接打开工作区，这样就激活工作区了。这么做主要是多重保险，而且用的不是关闭窗口，而是直接杀掉工作区所属的进程和窗口来避免重复打开相同的窗口，造成系统资源耗尽。这就是为什么要每个工作区都使用单独的Thorium用户。  
这个复合任务是2分钟运行一次，经过测试，在网络代理工作正常的情况下，足够在工作区24小时休眠后，在1分钟内马上拉起保活，做到无断线感觉，已经良好运行数个月不需要人工干预。
