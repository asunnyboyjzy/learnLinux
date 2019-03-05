### linux
	1.例题：
    在变量的配置当中，单引号与双引号的用途有何不同？
    答：
    单引号与双引号的最大不同在于双引号仍然可以保有变量的内容，但单引号内仅能是一般字符 ，而不会有特殊符号。我们以底下的例子做说明：假设您定义了一个变量， name=VBird ，现在想以 name 这个变量的内容定义出 myname 显示 VBird its me 这个内容，要如何订定呢？
    [root@www ~]# name=VBird
    [root@www ~]# echo $name
    VBird
    [root@www ~]# myname="$name its me"
    [root@www ~]# echo $myname
    VBird its me
    [root@www ~]# myname='$name its me'
    [root@www ~]# echo $myname
    $name its me
    发现了吗？没错！使用了单引号的时候，那么 $name 将失去原有的变量内容，仅为一般字符的显示型态而已！这里必需要特别小心在意！
    
-------------------------------------------------------------
    2.例题：
    在命令下达的过程中，反单引号( ` )这个符号代表的意义为何？
    答：
    在一串命令中，在 ` 之内的命令将会被先运行，而其运行出来的结果将做为外部的输入信息！例如 uname -r 会显示出目前的核心版本，而我们的核心版本在 /lib/modules 里面，因此，你可以先运行 uname -r 找出核心版本，然后再以『 cd 目录』到该目录下，当然也可以运行如同上面范例六的运行内容啰。 

    另外再举个例子，我们也知道， locate 命令可以列出所有的相关文件档名，但是，如果我想要知道各个文件的权限呢？举例来说，我想要知道每个 crontab 相关档名的权限：
    [root@www ~]# ls -l `locate crontab`
    如此一来，先以 locate 将文件名数据都列出来，再以 ls 命令来处理的意思啦！瞭了吗？ ^_^
--------------------------------------------------------------
    3.例题：
    若你有一个常去的工作目录名称为：『/cluster/server/work/taiwan_2005/003/』，如何进行该目录的简化？
    答：
    在一般的情况下，如果你想要进入上述的目录得要『cd /cluster/server/work/taiwan_2005/003/』， 以鸟哥自己的案例来说，鸟哥跑数值模式常常会配置很长的目录名称(避免忘记)，但如此一来变换目录就很麻烦。 此时，鸟哥习惯利用底下的方式来降低命令下达错误的问题：
    [root@www ~]# work="/cluster/server/work/taiwan_2005/003/"
    [root@www ~]# cd $work
    未来我想要使用其他目录作为我的模式工作目录时，只要变更 work 这个变量即可！而这个变量又可以在 bash 的配置文件中直接指定，那我每次登陆只要运行『 cd $work 』就能够去到数值模式仿真的工作目录了！是否很方便呢？ ^_^
    鳥哥的 Linux 私房菜 第十二章、學習 Shell Scripts 繁體主站 基礎篇 伺服器 企業應用 桌面應用 安全管理 書籍勘誤 鳥哥我 


12.1 什麼是 Shell Script
12.1.1 幹嘛學習 shell scripts
12.1.2 第一支 script 的撰寫與執行
12.1.3 撰寫 shell script 的良好習慣建立
12.2 簡單的 shell script 練習
12.2.1 簡單範例： 對談式腳本, 隨日期變化, 數值運算, 計算 pi
12.2.2 script 的執行方式差異 (source, sh script, ./script)
12.3 善用判斷式
12.3.1 利用 test 指令的測試功能
12.3.2 利用判斷符號 [ ]
12.3.3 Shell script 的預設變數($0, $1...)： shift
12.4 條件判斷式
12.4.1 利用 if .... then： 單層簡單條件, 多重複雜條件, 檢驗$1內容, 網路狀態, 退伍
12.4.2 利用 case ..... esac 判斷
12.4.3 利用 function 功能
12.5 迴圈 (loop)
12.5.1 while...do...done, until...do...done (不定迴圈)
12.5.2 for...do...done (固定迴圈)： 帳號檢查, 網路狀態 $(seq )
12.5.3 for...do...done 的數值處理
12.5.4 搭配亂數與陣列的實驗
12.6 shell script 的追蹤與 debug
12.7 重點回顧
12.8 本章習題
12.1 什麼是 Shell scripts

什麼是 shell script (程式化腳本) 呢？就字面上的意義，我們將他分為兩部份。 在『 shell 』部分，我們在 十章的 BASH 當中已經提過了，那是一個文字介面底下讓我們與系統溝通的一個工具介面。那麼『 script 』是啥？ 字面上的意義， script 是『腳本、劇本』的意思。整句話是說， shell script 是針對 shell 所寫的『劇本！』

什麼東西啊？其實， shell script 是利用 shell 的功能所寫的一個『程式 (program)』，這個程式是使用純文字檔，將一些 shell 的語法與指令(含外部指令)寫在裡面， 搭配正規表示法、管線命令與資料流重導向等功能，以達到我們所想要的處理目的。

所以，簡單的說， shell script 就像是早期 DOS 年代的批次檔 (.bat) ，最簡單的功能就是將許多指令彙整寫在一起， 讓使用者很輕易的就能夠 one touch 的方法去處理複雜的動作 (執行一個檔案 "shell script" ，就能夠一次執行多個指令)。 而且 shell script 更提供陣列、迴圈、條件與邏輯判斷等重要功能，讓使用者也可以直接以 shell 來撰寫程式，而不必使用類似 C 程式語言等傳統程式撰寫的語法呢！

這麼說你可以瞭解了嗎？是的！ shell script 可以簡單的被看成是批次檔， 也可以被說成是一個程式語言，且這個程式語言由於都是利用 shell 與相關工具指令， 所以不需要編譯即可執行，且擁有不錯的除錯 (debug) 工具，所以，他可以幫助系統管理員快速的管理好主機。

T



Top
12.1.2 第一支 script 的撰寫與執行

如同前面講到的，shell script 其實就是純文字檔，我們可以編輯這個檔案，然後讓這個檔案來幫我們一次執行多個指令， 或者是利用一些運算與邏輯判斷來幫我們達成某些功能。所以啦，要編輯這個檔案的內容時，當然就需要具備有 bash 指令下達的相關認識。下達指令需要注意的事項在第四章的開始下達指令小節內已經提過，有疑問請自行回去翻閱。 在 shell script 的撰寫中還需要用到底下的注意事項：

指令的執行是從上而下、從左而右的分析與執行；
指令的下達就如同第四章內提到的： 指令、選項與參數間的多個空白都會被忽略掉；
空白行也將被忽略掉，並且 [tab] 按鍵所推開的空白同樣視為空白鍵；
如果讀取到一個 Enter 符號 (CR) ，就嘗試開始執行該行 (或該串) 命令；
至於如果一行的內容太多，則可以使用『 \[Enter] 』來延伸至下一行；
『 # 』可做為註解！任何加在 # 後面的資料將全部被視為註解文字而被忽略！
如此一來，我們在 script 內所撰寫的程式，就會被一行一行的執行。現在我們假設你寫的這個程式檔名是 /home/dmtsai/shell.sh 好了，那如何執行這個檔案？很簡單，可以有底下幾個方法：

直接指令下達： shell.sh 檔案必須要具備可讀與可執行 (rx) 的權限，然後：
絕對路徑：使用 /home/dmtsai/shell.sh 來下達指令；
相對路徑：假設工作目錄在 /home/dmtsai/ ，則使用 ./shell.sh 來執行
變數『PATH』功能：將 shell.sh 放在 PATH 指定的目錄內，例如： ~/bin/

以 bash 程式來執行：透過『 bash shell.sh 』或『 sh shell.sh 』來執行
反正重點就是要讓那個 shell.sh 內的指令可以被執行的意思啦！ 咦！那我為何需要使用 『./shell.sh 』來下達指令？忘記了嗎？回去第十章內的指令搜尋順序察看一下， 你就會知道原因了！同時，由於 CentOS 預設使用者家目錄下的 ~/bin 目錄會被設定到 ${PATH} 內，所以你也可以將 shell.sh 建立在 /home/dmtsai/bin/ 底下 ( ~/bin 目錄需要自行設定) 。此時，若 shell.sh 在 ~/bin 內且具有 rx 的權限，那就直接輸入 shell.sh 即可執行該腳本程式！

那為何『 sh shell.sh 』也可以執行呢？這是因為 /bin/sh 其實就是 /bin/bash (連結檔)，使用 sh shell.sh 亦即告訴系統，我想要直接以 bash 的功能來執行 shell.sh 這個檔案內的相關指令的意思，所以此時你的 shell.sh 只要有 r 的權限即可被執行喔！而我們也可以利用 sh 的參數，如 -n 及 -x 來檢查與追蹤 shell.sh 的語法是否正確呢！ ^_^

撰寫第一支 script
在武俠世界中，不論是那個門派，要學武功要從掃地與蹲馬步做起，那麼要學程式呢？呵呵，肯定是由『秀出 Hello World！』 這個字眼開始的！OK！那麼鳥哥就先寫一支 script 給大家瞧一瞧：

[dmtsai@study ~]$ mkdir bin; cd bin
[dmtsai@study bin]$ vim hello.sh
     #!/bin/bash
     # Program:
     #       This program shows "Hello World!" in your screen.
     # History:
     # 2015/07/16	VBird	First release
     PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH
     echo -e "Hello World! \a \n"
     exit 0
在本章當中，請將所有撰寫的 script 放置到你家目錄的 ~/bin 這個目錄內，未來比較好管理啦！上面的寫法當中，鳥哥主要將整個程式的撰寫分成數段，大致是這樣：

第一行 #!/bin/bash 在宣告這個 script 使用的 shell 名稱：
因為我們使用的是 bash ，所以，必須要以『 #!/bin/bash 』來宣告這個檔案內的語法使用 bash 的語法！那麼當這個程式被執行時，他就能夠載入 bash 的相關環境設定檔 (一般來說就是 non-login shell 的 ~/.bashrc)， 並且執行 bash 來使我們底下的指令能夠執行！這很重要的！(在很多狀況中，如果沒有設定好這一行， 那麼該程式很可能會無法執行，因為系統可能無法判斷該程式需要使用什麼 shell 來執行啊！)

程式內容的說明：
整個 script 當中，除了第一行的『 #! 』是用來宣告 shell 的之外，其他的 # 都是『註解』用途！ 所以上面的程式當中，第二行以下就是用來說明整個程式的基本資料。一般來說， 建議你一定要養成說明該 script 的：1. 內容與功能； 2. 版本資訊； 3. 作者與聯絡方式； 4. 建檔日期；5. 歷史紀錄 等等。這將有助於未來程式的改寫與 debug 呢！

主要環境變數的宣告：
建議務必要將一些重要的環境變數設定好，鳥哥個人認為， PATH 與 LANG (如果有使用到輸出相關的資訊時) 是當中最重要的！ 如此一來，則可讓我們這支程式在進行時，可以直接下達一些外部指令，而不必寫絕對路徑呢！比較方便啦！

主要程式部分
就將主要的程式寫好即可！在這個例子當中，就是 echo 那一行啦！

執行成果告知 (定義回傳值)
是否記得我們在第十章裡面要討論一個指令的執行成功與否，可以使用 $? 這個變數來觀察～ 那麼我們也可以利用 exit 這個指令來讓程式中斷，並且回傳一個數值給系統。 在我們這個例子當中，鳥哥使用 exit 0 ，這代表離開 script 並且回傳一個 0 給系統， 所以我執行完這個 script 後，若接著下達 echo $? 則可得到 0 的值喔！ 更聰明的讀者應該也知道了，呵呵！利用這個 exit n (n 是數字) 的功能，我們還可以自訂錯誤訊息， 讓這支程式變得更加的 smart 呢！
接下來透過剛剛上頭介紹的執行方法來執行看看結果吧！

[dmtsai@study bin]$ sh hello.sh
Hello World !
你會看到螢幕是這樣，而且應該還會聽到『咚』的一聲，為什麼呢？還記得前一章提到的 printf 吧？用 echo 接著那些特殊的按鍵也可以發生同樣的事情～ 不過， echo 必須要加上 -e 的選項才行！呵呵！在你寫完這個小 script 之後，你就可以大聲的說：『我也會寫程式了』！哈哈！ 很簡單有趣吧～ ^_^

另外，你也可以利用：『chmod a+x hello.sh; ./hello.sh』來執行這個 script 的呢！



Top
12.1.3 撰寫 shell script 的良好習慣建立

一個良好習慣的養成是很重要的～大家在剛開始撰寫程式的時候，最容易忽略這部分， 認為程式寫出來就好了，其他的不重要。其實，如果程式的說明能夠更清楚，那麼對你自己是有很大的幫助的。

舉例來說，鳥哥自己為了自己的需求，曾經撰寫了不少的 script 來幫我進行主機 IP 的偵測啊、 登錄檔分析與管理啊、自動上傳下載重要設定檔啊等等的，不過，早期就是因為太懶了， 管理的主機又太多了，常常同一個程式在不同的主機上面進行更改，到最後，到底哪一支才是最新的都記不起來， 而且，重點是，我到底是改了哪裡？為什麼做那樣的修改？都忘的一乾二淨～真要命～

所以，後來鳥哥在寫程式的時候，通常會比較仔細的將程式的設計過程給他記錄下來，而且還會記錄一些歷史紀錄， 如此一來，好多了～至少很容易知道我修改了哪些資料，以及程式修改的理念與邏輯概念等等， 在維護上面是輕鬆很多很多的喔！

另外，在一些環境的設定上面，畢竟每個人的環境都不相同，為了取得較佳的執行環境， 我都會自行先定義好一些一定會被用到的環境變數，例如 PATH 這個玩意兒！ 這樣比較好啦～所以說，建議你一定要養成良好的 script 撰寫習慣，在每個 script 的檔頭處記錄好：

script 的功能；
script 的版本資訊；
script 的作者與聯絡方式；
script 的版權宣告方式；
script 的 History (歷史紀錄)；
script 內較特殊的指令，使用『絕對路徑』的方式來下達；
script 運作時需要的環境變數預先宣告與設定。
除了記錄這些資訊之外，在較為特殊的程式碼部分，個人建議務必要加上註解說明，可以幫助你非常非常多！ 此外，程式碼的撰寫最好使用巢狀方式，在包覆的內部程式碼最好能以 [tab] 按鍵的空格向後推， 這樣你的程式碼會顯的非常的漂亮與有條理！在查閱與 debug 上較為輕鬆愉快喔！ 另外，使用撰寫 script 的工具最好使用 vim 而不是 vi ，因為 vim 會有額外的語法檢驗機制，能夠在第一階段撰寫時就發現語法方面的問題喔！



12.2 簡單的 shell script 練習

在第一支 shell script 撰寫完畢之後，相信你應該具有基本的撰寫功力了。接下來，在開始更深入的程式概念之前，我們先來玩一些簡單的小範例好了。 底下的範例中，達成結果的方式相當的多，建議你先自行撰寫看看，寫完之後再與鳥哥寫的內容比對， 這樣才能更加深概念喔！好！不囉唆，我們就一個一個來玩吧！

Top
12.2.1 簡單範例

底下的範例在很多的腳本程式中都會用到，而底下的範例又都很簡單！值得參考看看喔！

對談式腳本：變數內容由使用者決定
很多時候我們需要使用者輸入一些內容，好讓程式可以順利運作。 簡單的來說，大家應該都有安裝過軟體的經驗，安裝的時候，他不是會問你『要安裝到那個目錄去』嗎？ 那個讓使用者輸入資料的動作，就是讓使用者輸入變數內容啦。

你應該還記得在十章 bash 的時候，我們有學到一個 read 指令吧？現在，請你以 read 指令的用途，撰寫一個 script ，他可以讓使用者輸入：1. first name 與 2. last name， 最後並且在螢幕上顯示：『Your full name is: 』的內容：

[dmtsai@study bin]$ vim showname.sh
     #!/bin/bash
     # Program:
     #	User inputs his first name and last name.  Program shows his full name.
     # History:
     # 2015/07/16	VBird	First release
     PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH

     read -p "Please input your first name: " firstname      # 提示使用者輸入
read -p "Please input your last name:  " lastname       # 提示使用者輸入
echo -e "\nYour full name is: ${firstname} ${lastname}" # 結果由螢幕輸出
將上面這個 showname.sh 執行一下，你就能夠發現使用者自己輸入的變數可以讓程式所取用，並且將他顯示到螢幕上！ 接下來，如果想要製作一個每次執行都會依據不同的日期而變化結果的腳本呢？

隨日期變化：利用 date 進行檔案的建立
想像一個狀況，假設我的伺服器內有資料庫，資料庫每天的資料都不太一樣，因此當我備份時，希望將每天的資料都備份成不同的檔名， 這樣才能夠讓舊的資料也能夠保存下來不被覆蓋。哇！不同檔名呢！這真困擾啊？難道要我每天去修改 script ？

不需要啊！考慮每天的『日期』並不相同，所以我可以將檔名取成類似： backup.2015-07-16.data ， 不就可以每天一個不同檔名了嗎？呵呵！確實如此。那個 2015-07-16 怎麼來的？那就是重點啦！接下來出個相關的例子： 假設我想要建立三個空的檔案 (透過 touch) ，檔名最開頭由使用者輸入決定，假設使用者輸入 filename 好了，那今天的日期是 2015/07/16 ， 我想要以前天、昨天、今天的日期來建立這些檔案，亦即 filename_20150714, filename_20150715, filename_20150716 ，該如何是好？

[dmtsai@study bin]$ vim create_3_filename.sh
      #!/bin/bash
      # Program:
      #	Program creates three files, which named by user's input and date command.
      # History:
      # 2015/07/16	VBird	First release
      PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
      export PATH

###### 1. 讓使用者輸入檔案名稱，並取得 fileuser 這個變數；
echo -e "I will use 'touch' command to create 3 files." # 純粹顯示資訊
read -p "Please input your filename: " fileuser         # 提示使用者輸入

###### 2. 為了避免使用者隨意按 Enter ，利用變數功能分析檔名是否有設定？
filename=${fileuser:-"filename"}           # 開始判斷有否設定檔名

###### 3. 開始利用 date 指令來取得所需要的檔名了；
date1=$(date --date='2 days ago' +%Y%m%d)  # 前兩天的日期
date2=$(date --date='1 days ago' +%Y%m%d)  # 前一天的日期
date3=$(date +%Y%m%d)                      # 今天的日期
file1=${filename}${date1}                  # 底下三行在設定檔名
file2=${filename}${date2}
file3=${filename}${date3}

###### 4. 將檔名建立吧！
touch "${file1}"                           # 底下三行在建立檔案
touch "${file2}"
touch "${file3}"
上面的範例鳥哥使用了很多在第十章介紹過的概念： 包括小指令『 $(command) 』的取得訊息、變數的設定功能、變數的累加以及利用 touch 指令輔助！ 如果你開始執行這個 create_3_filename.sh 之後，你可以進行兩次執行：一次直接按 [Enter] 來查閱檔名是啥？ 一次可以輸入一些字元，這樣可以判斷你的腳本是否設計正確喔！

數值運算：簡單的加減乘除
各位看官應該還記得，我們可以使用 declare 來定義變數的類型吧？ 當變數定義成為整數後才能夠進行加減運算啊！此外，我們也可以利用『 $((計算式)) 』來進行數值運算的。 可惜的是， bash shell 裡頭預設僅支援到整數的資料而已。OK！那我們來玩玩看，如果我們要使用者輸入兩個變數， 然後將兩個變數的內容相乘，最後輸出相乘的結果，那可以怎麼做？

[dmtsai@study bin]$ vim multiplying.sh
     #!/bin/bash
     # Program:
     #	User inputs 2 integer numbers; program will cross these two numbers.
     # History:
     # 2015/07/16	VBird	First release
     PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH
     echo -e "You SHOULD input 2 numbers, I will multiplying them! \n"
     read -p "first number:  " firstnu
     read -p "second number: " secnu
     total=$((${firstnu}*${secnu}))
     echo -e "\nThe result of ${firstnu} x ${secnu} is ==> ${total}"
在數值的運算上，我們可以使用『 declare -i total=${firstnu}*${secnu} 』 也可以使用上面的方式來進行！基本上，鳥哥比較建議使用這樣的方式來進行運算：

var=$((運算內容))
不但容易記憶，而且也比較方便的多，因為兩個小括號內可以加上空白字元喔！ 未來你可以使用這種方式來計算的呀！至於數值運算上的處理，則有：『 +, -, *, /, % 』等等。 那個 % 是取餘數啦～舉例來說， 13 對 3 取餘數，結果是 13=4*3+1，所以餘數是 1 啊！就是：

[dmtsai@study bin]$ echo $(( 13 % 3 ))
1
這樣瞭解了吧？另外，如果你想要計算含有小數點的資料時，其實可以透過 bc 這個指令的協助喔！ 例如可以這樣做：

[dmtsai@study bin]$ echo "123.123*55.9" | bc
6882.575
了解了 bc 的妙用之後，來讓我們測試一下如何計算 pi 這個東西呢？

數值運算：透過 bc 計算 pi
其實計算 pi 時，小數點以下位數可以無限制的延伸下去！而 bc 有提供一個運算 pi 的函式，只是想要使用該函式必須要使用 bc -l 來呼叫才行。 也因為這個小數點以下位數可以無線延伸運算的特性存在，所以我們可以透過底下這隻小腳本來讓使用者輸入一個『小數點為數值』， 以讓 pi 能夠更準確！

[dmtsai@study bin]$ vim cal_pi.sh
     #!/bin/bash
     # Program:
     #	User input a scale number to calculate pi number.
     # History:
     # 2015/07/16	VBird	First release
      PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH
     echo -e "This program will calculate pi value. \n"
     echo -e "You should input a float number to calculate pi value.\n"
     read -p "The scale number (10~10000) ? " checking
     num=${checking:-"10"}           # 開始判斷有否有輸入數值
     echo -e "Starting calculate pi value.  Be patient."
     time echo "scale=${num}; 4*a(1)" | bc -lq
上述資料中，那個 4*a(1) 是 bc 主動提供的一個計算 pi 的函數，至於 scale 就是要 bc 計算幾個小數點下位數的意思。當 scale 的數值越大， 代表 pi 要被計算的越精確，當然用掉的時間就會越多！因此，你可以嘗試輸入不同的數值看看！不過，最好是不要超過 5000 啦！因為會算很久！ 如果你要讓你的 CPU 隨時保持在高負載，這個程式算下去你就會知道有多操 CPU 囉！ ^_^

Tips
鳥哥的圖示 鳥哥的實驗室中，為了要確認虛擬機的效率問題，所以很多時候需要保持虛擬機在忙碌的狀態～鳥哥的學生就是丟這隻程式進去系統跑！ 但是將 scale 調高一些，那計算就得要花比較多時間～用以達到我們需要 CPU 忙碌的狀態喔！


Top
12.2.2 script 的執行方式差異 (source, sh script, ./script)

不同的 script 執行方式會造成不一樣的結果喔！尤其影響 bash 的環境很大呢！腳本的執行方式除了前面小節談到的方式之外，還可以利用 source 或小數點 (.) 來執行喔！那麼這種執行方式有何不同呢？當然是不同的啦！讓我們來說說！

利用直接執行的方式來執行 script
當使用前一小節提到的直接指令下達 (不論是絕對路徑/相對路徑還是 ${PATH} 內)，或者是利用 bash (或 sh) 來下達腳本時， 該 script 都會使用一個新的 bash 環境來執行腳本內的指令！也就是說，使用這種執行方式時， 其實 script 是在子程序的 bash 內執行的！我們在第十章 BASH 內談到 export 的功能時，曾經就父程序/子程序談過一些概念性的問題， 重點在於：『當子程序完成後，在子程序內的各項變數或動作將會結束而不會傳回到父程序中』！ 這是什麼意思呢？

我們舉剛剛提到過的 showname.sh 這個腳本來說明好了，這個腳本可以讓使用者自行設定兩個變數，分別是 firstname 與 lastname，想一想，如果你直接執行該指令時，該指令幫你設定的 firstname 會不會生效？看一下底下的執行結果：

[dmtsai@study bin]$ echo ${firstname} ${lastname}
    <==確認了，這兩個變數並不存在喔！
[dmtsai@study bin]$ sh showname.sh
Please input your first name: VBird <==這個名字是鳥哥自己輸入的
Please input your last name:  Tsai 

Your full name is: VBird Tsai      <==看吧！在 script 運作中，這兩個變數有生效
[dmtsai@study bin]$ echo ${firstname} ${lastname}
    <==事實上，這兩個變數在父程序的 bash 中還是不存在的！
上面的結果你應該會覺得很奇怪，怎麼我已經利用 showname.sh 設定好的變數竟然在 bash 環境底下無效！怎麼回事呢？ 如果將程序相關性繪製成圖的話，我們以下圖來說明。當你使用直接執行的方法來處理時，系統會給予一支新的 bash 讓我們來執行 showname.sh 裡面的指令，因此你的 firstname, lastname 等變數其實是在下圖中的子程序 bash 內執行的。 當 showname.sh 執行完畢後，子程序 bash 內的所有資料便被移除，因此上表的練習中，在父程序底下 echo ${firstname} 時， 就看不到任何東西了！這樣可以理解嗎？

 showname.sh 在子程序當中運作的示意圖
圖12.2.1、showname.sh 在子程序當中運作的示意圖
利用 source 來執行腳本：在父程序中執行
如果你使用 source 來執行指令那就不一樣了！同樣的腳本我們來執行看看：

[dmtsai@study bin]$ source showname.sh
Please input your first name: VBird
Please input your last name:  Tsai

Your full name is: VBird Tsai
[dmtsai@study bin]$ echo ${firstname} ${lastname}
VBird Tsai  <==嘿嘿！有資料產生喔！
竟然生效了！沒錯啊！因為 source 對 script 的執行方式可以使用底下的圖示來說明！ showname.sh 會在父程序中執行的，因此各項動作都會在原本的 bash 內生效！這也是為啥你不登出系統而要讓某些寫入 ~/.bashrc 的設定生效時，需要使用『 source ~/.bashrc 』而不能使用『 bash ~/.bashrc 』是一樣的啊！

 showname.sh 在父程序當中運作的示意圖
圖12.2.2、showname.sh 在父程序當中運作的示意圖

12.3 善用判斷式

在第十章中，我們提到過 $? 這個變數所代表的意義， 此外，也透過 && 及 || 來作為前一個指令執行回傳值對於後一個指令是否要進行的依據。第十章的討論中，如果想要判斷一個目錄是否存在， 當時我們使用的是 ls 這個指令搭配資料流重導向，最後配合 $? 來決定後續的指令進行與否。 但是否有更簡單的方式可以來進行『條件判斷』呢？有的～那就是『 test 』這個指令。

Top
12.3.1 利用 test 指令的測試功能

當我要檢測系統上面某些檔案或者是相關的屬性時，利用 test 這個指令來工作真是好用得不得了， 舉例來說，我要檢查 /dmtsai 是否存在時，使用：

[dmtsai@study ~]$ test -e /dmtsai
執行結果並不會顯示任何訊息，但最後我們可以透過 $? 或 && 及 || 來展現整個結果呢！ 例如我們在將上面的例子改寫成這樣：

[dmtsai@study ~]$ test -e /dmtsai && echo "exist" || echo "Not exist"
Not exist  <==結果顯示不存在啊！
最終的結果可以告知我們是『exist』還是『Not exist』呢！那我知道 -e 是測試一個『東西』在不在， 如果還想要測試一下該檔名是啥玩意兒時，還有哪些標誌可以來判斷的呢？呵呵！有底下這些東西喔！

測試的標誌	代表意義
1. 關於某個檔名的『檔案類型』判斷，如 test -e filename 表示存在否
-e	該『檔名』是否存在？(常用)
-f	該『檔名』是否存在且為檔案(file)？(常用)
-d	該『檔名』是否存在且為目錄(directory)？(常用)
-b	該『檔名』是否存在且為一個 block device 裝置？
-c	該『檔名』是否存在且為一個 character device 裝置？
-S	該『檔名』是否存在且為一個 Socket 檔案？
-p	該『檔名』是否存在且為一個 FIFO (pipe) 檔案？
-L	該『檔名』是否存在且為一個連結檔？
2. 關於檔案的權限偵測，如 test -r filename 表示可讀否 (但 root 權限常有例外)
-r	偵測該檔名是否存在且具有『可讀』的權限？
-w	偵測該檔名是否存在且具有『可寫』的權限？
-x	偵測該檔名是否存在且具有『可執行』的權限？
-u	偵測該檔名是否存在且具有『SUID』的屬性？
-g	偵測該檔名是否存在且具有『SGID』的屬性？
-k	偵測該檔名是否存在且具有『Sticky bit』的屬性？
-s	偵測該檔名是否存在且為『非空白檔案』？
3. 兩個檔案之間的比較，如： test file1 -nt file2
-nt	(newer than)判斷 file1 是否比 file2 新
-ot	(older than)判斷 file1 是否比 file2 舊
-ef	判斷 file1 與 file2 是否為同一檔案，可用在判斷 hard link 的判定上。 主要意義在判定，兩個檔案是否均指向同一個 inode 哩！
4. 關於兩個整數之間的判定，例如 test n1 -eq n2
-eq	兩數值相等 (equal)
-ne	兩數值不等 (not equal)
-gt	n1 大於 n2 (greater than)
-lt	n1 小於 n2 (less than)
-ge	n1 大於等於 n2 (greater than or equal)
-le	n1 小於等於 n2 (less than or equal)
5. 判定字串的資料
test -z string	判定字串是否為 0 ？若 string 為空字串，則為 true
test -n string	判定字串是否非為 0 ？若 string 為空字串，則為 false。
註： -n 亦可省略
test str1 == str2	判定 str1 是否等於 str2 ，若相等，則回傳 true
test str1 != str2	判定 str1 是否不等於 str2 ，若相等，則回傳 false
6. 多重條件判定，例如： test -r filename -a -x filename
-a	(and)兩狀況同時成立！例如 test -r file -a -x file，則 file 同時具有 r 與 x 權限時，才回傳 true。
-o	(or)兩狀況任何一個成立！例如 test -r file -o -x file，則 file 具有 r 或 x 權限時，就可回傳 true。
!	反相狀態，如 test ! -x file ，當 file 不具有 x 時，回傳 true
OK！現在我們就利用 test 來幫我們寫幾個簡單的例子。首先，判斷一下，讓使用者輸入一個檔名，我們判斷：

這個檔案是否存在，若不存在則給予一個『Filename does not exist』的訊息，並中斷程式；
若這個檔案存在，則判斷他是個檔案或目錄，結果輸出『Filename is regular file』或 『Filename is directory』
判斷一下，執行者的身份對這個檔案或目錄所擁有的權限，並輸出權限資料！
你可以先自行創作看看，然後再跟底下的結果討論討論。注意利用 test 與 && 還有 || 等標誌！

[dmtsai@study bin]$ vim file_perm.sh
     #!/bin/bash
     # Program:
     #	User input a filename, program will check the flowing:
     #	1.) exist? 2.) file/directory? 3.) file permissions 
     # History:
     # 2015/07/16	VBird	First release
     PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH

     # 1. 讓使用者輸入檔名，並且判斷使用者是否真的有輸入字串？
     echo -e "Please input a filename, I will check the      filename's type and permission. \n\n"
     read -p "Input a filename : " filename
     test -z ${filename} && echo "You MUST input a filename." && exit 0
     # 2. 判斷檔案是否存在？若不存在則顯示訊息並結束腳本
     test ! -e ${filename} && echo "The filename '${filename}' DO NOT exist" && exit 0
     # 3. 開始判斷檔案類型與屬性
     test -f ${filename} && filetype="regulare file"
     test -d ${filename} && filetype="directory"
     test -r ${filename} && perm="readable"
     test -w ${filename} && perm="${perm} writable"
     test -x ${filename} && perm="${perm} executable"
     # 4. 開始輸出資訊！
     echo "The filename: ${filename} is a ${filetype}"
     echo "And the permissions for you are : ${perm}"
如果你執行這個腳本後，他會依據你輸入的檔名來進行檢查喔！先看是否存在，再看為檔案或目錄類型，最後判斷權限。 但是你必須要注意的是，由於 root 在很多權限的限制上面都是無效的，所以使用 root 執行這個腳本時， 常常會發現與 ls -l 觀察到的結果並不相同！所以，建議使用一般使用者來執行這個腳本試看看。



Top
12.3.2 利用判斷符號 [ ]

除了我們很喜歡使用的 test 之外，其實，我們還可以利用判斷符號『 [ ] 』(就是中括號啦) 來進行資料的判斷呢！ 舉例來說，如果我想要知道 ${HOME} 這個變數是否為空的，可以這樣做：

[dmtsai@study ~]$ [ -z "${HOME}" ] ; echo $?
使用中括號必須要特別注意，因為中括號用在很多地方，包括萬用字元與正規表示法等等，所以如果要在 bash 的語法當中使用中括號作為 shell 的判斷式時，必須要注意中括號的兩端需要有空白字元來分隔喔！ 假設我空白鍵使用『□』符號來表示，那麼，在這些地方你都需要有空白鍵：

[  "$HOME"  ==  "$MAIL"  ]
[□"$HOME"□==□"$MAIL"□]
 ↑       ↑  ↑       ↑
Tips
鳥哥的圖示 你會發現鳥哥在上面的判斷式當中使用了兩個等號『 == 』。其實在 bash 當中使用一個等號與兩個等號的結果是一樣的！ 不過在一般慣用程式的寫法中，一個等號代表『變數的設定』，兩個等號則是代表『邏輯判斷 (是與否之意)』。 由於我們在中括號內重點在於『判斷』而非『設定變數』，因此鳥哥建議您還是使用兩個等號較佳！

上面的例子在說明，兩個字串 ${HOME} 與 ${MAIL} 是否相同的意思，相當於 test ${HOME} == ${MAIL} 的意思啦！ 而如果沒有空白分隔，例如 [${HOME}==${MAIL}] 時，我們的 bash 就會顯示錯誤訊息了！這可要很注意啊！ 所以說，你最好要注意：

在中括號 [] 內的每個元件都需要有空白鍵來分隔；
在中括號內的變數，最好都以雙引號括號起來；
在中括號內的常數，最好都以單或雙引號括號起來。
為什麼要這麼麻煩啊？直接舉例來說，假如我設定了 name="VBird Tsai" ，然後這樣判定：

[dmtsai@study ~]$ name="VBird Tsai"
[dmtsai@study ~]$ [ ${name} == "VBird" ]
bash: [: too many arguments
見鬼了！怎麼會發生錯誤啊？bash 還跟我說錯誤是由於『太多參數 (arguments)』所致！ 為什麼呢？因為 ${name} 如果沒有使用雙引號刮起來，那麼上面的判定式會變成：

[ VBird Tsai == "VBird" ]
上面肯定不對嘛！因為一個判斷式僅能有兩個資料的比對，上面 VBird 與 Tsai 還有 "VBird" 就有三個資料！ 這不是我們要的！我們要的應該是底下這個樣子：

[ "VBird Tsai" == "VBird" ]
這可是差很多的喔！另外，中括號的使用方法與 test 幾乎一模一樣啊～ 只是中括號比較常用在條件判斷式 if ..... then ..... fi 的情況中就是了。 好，那我們也使用中括號的判斷來做一個小案例好了，案例設定如下：

當執行一個程式的時候，這個程式會讓使用者選擇 Y 或 N ，
如果使用者輸入 Y 或 y 時，就顯示『 OK, continue 』
如果使用者輸入 n 或 N 時，就顯示『 Oh, interrupt ！』
如果不是 Y/y/N/n 之內的其他字元，就顯示『 I don't know what your choice is 』
利用中括號、 && 與 || 來繼續吧！
 [dmtsai@study bin]$ vim ans_yn.sh
     #!/bin/bash
     # Program:
     # 	This program shows the user's choice
     # History:
     # 2015/07/16	VBird	First release
     PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH

     read -p "Please input (Y/N): " yn
     [ "${yn}" == "Y" -o "${yn}" == "y" ] && echo "OK, continue" && exit 0
     [ "${yn}" == "N" -o "${yn}" == "n" ] && echo "Oh, interrupt!" && exit 0
echo "I don't know what your choice is" && exit 0
由於輸入正確 (Yes) 的方法有大小寫之分，不論輸入大寫 Y 或小寫 y 都是可以的，此時判斷式內就得要有兩個判斷才行！ 由於是任何一個成立即可 (大寫或小寫的 y) ，所以這裡使用 -o (或) 連結兩個判斷喔！ 很有趣吧！利用這個字串判別的方法，我們就可以很輕鬆的將使用者想要進行的工作分門別類呢！ 接下來，我們再來談一些其他有的沒有的東西吧！



Top
12.3.3 Shell script 的預設變數($0, $1...)

我們知道指令可以帶有選項與參數，例如 ls -la 可以察看包含隱藏檔的所有屬性與權限。那麼 shell script 能不能在腳本檔名後面帶有參數呢？很有趣喔！舉例來說，如果你想要重新啟動系統的網路，可以這樣做：

[dmtsai@study ~]$ file /etc/init.d/network
/etc/init.d/network: Bourne-Again shell script, ASCII text executable
     # 使用 file 來查詢後，系統告知這個檔案是個 bash 的可執行 script 喔！
[dmtsai@study ~]$ /etc/init.d/network restart
restart 是重新啟動的意思，上面的指令可以『重新啟動 /etc/init.d/network 這支程式』的意思！ 唔！那麼如果你在 /etc/init.d/network 後面加上 stop 呢？沒錯！就可以直接關閉該服務了！這麼神奇啊？ 沒錯啊！如果你要依據程式的執行給予一些變數去進行不同的任務時，本章一開始是使用 read 的功能！但 read 功能的問題是你得要手動由鍵盤輸入一些判斷式。如果透過指令後面接參數， 那麼一個指令就能夠處理完畢而不需要手動再次輸入一些變數行為！這樣下達指令會比較簡單方便啦！

script 是怎麼達成這個功能的呢？其實 script 針對參數已經有設定好一些變數名稱了！對應如下：

/path/to/scriptname  opt1  opt2  opt3  opt4 
       $0             $1    $2    $3    $4
這樣夠清楚了吧？執行的腳本檔名為 $0 這個變數，第一個接的參數就是 $1 啊～ 所以，只要我們在 script 裡面善用 $1 的話，就可以很簡單的立即下達某些指令功能了！除了這些數字的變數之外， 我們還有一些較為特殊的變數可以在 script 內使用來呼叫這些參數喔！

$# ：代表後接的參數『個數』，以上表為例這裡顯示為『 4 』；
"$@" ：代表『 "$1" "$2" "$3" "$4" 』之意，每個變數是獨立的(用雙引號括起來)；
"$*" ：代表『 "$1c$2c$3c$4" 』，其中 c 為分隔字元，預設為空白鍵， 所以本例中代表『 "$1 $2 $3 $4" 』之意。
那個 "$@" 與 "$*" 基本上還是有所不同啦！不過，一般使用情況下可以直接記憶 "$@" 即可！ 好了，來做個例子吧～假設我要執行一個可以攜帶參數的 script ，執行該腳本後螢幕會顯示如下的資料：

Tips
鳥哥的圖示 有點怪異的是， $@ 與 "$@" 的結果並不一樣喔！當你輸入的參數內帶有雙引號 (") 時，建議還是得要使用 "$@" 來帶入腳本中， 否則雙引號會被取消，這樣執行結果的差異會相當大喔！尤其是像『 ./script one "a   to   b" 』這種僅有兩個參數，但是參數內還有空白字元的， 最容易出現莫名的問題喔！

程式的檔名為何？
共有幾個參數？
若參數的個數小於 2 則告知使用者參數數量太少
全部的參數內容為何？
第一個參數為何？
第二個參數為何
[dmtsai@study bin]$ vim how_paras.sh
     #!/bin/bash
     # Program:
     #	Program shows the script name, parameters...
     # History:
     # 2015/07/16	VBird	First release
     PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH

     echo "The script name is        ==> ${0}"
     echo "Total parameter number is ==> $#"
     [ "$#" -lt 2 ] && echo "The number of parameter is less than 2.  Stop here." && exit 0
     echo "Your whole parameter is   ==> '$@'"
     echo "The 1st parameter         ==> ${1}"
     echo "The 2nd parameter         ==> ${2}"
執行結果如下：

[dmtsai@study bin]$ sh how_paras.sh theone haha quot
     The script name is        ==> how_paras.sh       <==檔名
     Total parameter number is ==> 3                  <==果然有三個參數
     Your whole parameter is   ==> 'theone haha quot' <==參數的內容全部
     The 1st parameter         ==> theone             <==第一個參數
     The 2nd parameter         ==> haha               <==第二個參數
shift：造成參數變數號碼偏移
除此之外，腳本後面所接的變數是否能夠進行偏移 (shift) 呢？什麼是偏移啊？我們直接以底下的範例來說明好了， 用範例說明比較好解釋！我們將 how_paras.sh 的內容稍作變化一下，用來顯示每次偏移後參數的變化情況：

[dmtsai@study bin]$ vim shift_paras.sh
     #!/bin/bash
     # Program:
     #	Program shows the effect of shift function.
     # History:
     # 2009/02/17	VBird	First release
     PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH

     echo "Total parameter number is ==> $#"
     echo "Your whole parameter is   ==> '$@'"
     shift   # 進行第一次『一個變數的 shift 』
     echo "Total parameter number is ==> $#"
     echo "Your whole parameter is   ==> '$@'"
     shift 3 # 進行第二次『三個變數的 shift 』
     echo "Total parameter number is ==> $#"
     echo "Your whole parameter is   ==> '$@'"
這玩意的執行成果如下：

[dmtsai@study bin]$ sh shift_paras.sh one two three four five six <==給予六個參數
     Total parameter number is ==> 6   <==最原始的參數變數情況
     Your whole parameter is   ==> 'one two three four five six'
     Total parameter number is ==> 5   <==第一次偏移，看底下發現第一個 one 不見了
     Your whole parameter is   ==> 'two three four five six'
     Total parameter number is ==> 2   <==第二次偏移掉三個，two three four 不見了
     Your whole parameter is   ==> 'five six'
光看結果你就可以知道啦，那個 shift 會移動變數，而且 shift 後面可以接數字，代表拿掉最前面的幾個參數的意思。 上面的執行結果中，第一次進行 shift 後他的顯示情況是『 one two three four five six』，所以就剩下五個啦！第二次直接拿掉三個，就變成『 two three four five six 』啦！ 這樣這個案例可以瞭解了嗎？理解了 shift 的功能了嗎？

上面這幾個例子都很簡單吧？幾乎都是利用 bash 的相關功能而已～ 不難啦～底下我們就要使用條件判斷式來進行一些分別功能的設定了，好好瞧一瞧先～


12.4 條件判斷式

只要講到『程式』的話，那麼條件判斷式，亦即是『 if then 』這種判別式肯定一定要學習的！ 因為很多時候，我們都必須要依據某些資料來判斷程式該如何進行。舉例來說，我們在上頭的 ans_yn.sh 討論輸入回應的範例中不是有練習當使用者輸入 Y/N 時，必須要執行不同的訊息輸出嗎？簡單的方式可以利用 && 與 || ，但如果我還想要執行一堆指令呢？那真的得要 if then 來幫忙囉～底下我們就來聊一聊！

Top
12.4.1 利用 if .... then

這個 if .... then 是最常見的條件判斷式了～簡單的說，就是當符合某個條件判斷的時候， 就予以進行某項工作就是了。這個 if ... then 的判斷還有多層次的情況！我們分別介紹如下：

單層、簡單條件判斷式
如果你只有一個判斷式要進行，那麼我們可以簡單的這樣看：

if [ 條件判斷式 ]; then
	當條件判斷式成立時，可以進行的指令工作內容；
fi   <==將 if 反過來寫，就成為 fi 啦！結束 if 之意！
至於條件判斷式的判斷方法，與前一小節的介紹相同啊！較特別的是，如果我有多個條件要判別時， 除了 ans_yn.sh 那個案例所寫的，也就是『將多個條件寫入一個中括號內的情況』之外， 我還可以有多個中括號來隔開喔！而括號與括號之間，則以 && 或 || 來隔開，他們的意義是：

&& 代表 AND ；
|| 代表 or ；
所以，在使用中括號的判斷式中， && 及 || 就與指令下達的狀態不同了。舉例來說， ans_yn.sh 裡面的判斷式可以這樣修改：

[ "${yn}" == "Y" -o "${yn}" == "y" ]
上式可替換為
[ "${yn}" == "Y" ] || [ "${yn}" == "y" ]
之所以這樣改，很多人是習慣問題！很多人則是喜歡一個中括號僅有一個判別式的原因。好了， 現在我們來將 ans_yn.sh 這個腳本修改成為 if ... then 的樣式來看看：

[dmtsai@study bin]$ cp ans_yn.sh ans_yn-2.sh  <==用複製來修改的比較快！
[dmtsai@study bin]$ vim ans_yn-2.sh
     #!/bin/bash
     # Program:
     #       This program shows the user's choice
     # History:
     # 2015/07/16    VBird   First release
     PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH

     read -p "Please input (Y/N): " yn

     if [ "${yn}" == "Y" ] || [ "${yn}" == "y" ]; then
	echo "OK, continue"
	exit 0
     fi
     if [ "${yn}" == "N" ] || [ "${yn}" == "n" ]; then
	echo "Oh, interrupt!"
	exit 0
     fi
     echo "I don't know what your choice is" && exit 0
不過，由這個例子看起來，似乎也沒有什麼了不起吧？原本的 ans_yn.sh 還比較簡單呢～ 但是如果以邏輯概念來看，其實上面的範例中，我們使用了兩個條件判斷呢！明明僅有一個 ${yn} 的變數，為何需要進行兩次比對呢？ 此時，多重條件判斷就能夠來測試測試囉！

多重、複雜條件判斷式
在同一個資料的判斷中，如果該資料需要進行多種不同的判斷時，應該怎麼作？舉例來說，上面的 ans_yn.sh 腳本中，我們只要進行一次 ${yn} 的判斷就好 (僅進行一次 if )，不想要作多次 if 的判斷。 此時你就得要知道底下的語法了：

     # 一個條件判斷，分成功進行與失敗進行 (else)
if [ 條件判斷式 ]; then
	當條件判斷式成立時，可以進行的指令工作內容；
else
	當條件判斷式不成立時，可以進行的指令工作內容；
fi
如果考慮更複雜的情況，則可以使用這個語法：

     # 多個條件判斷 (if ... elif ... elif ... else) 分多種不同情況執行
if [ 條件判斷式一 ]; then
	當條件判斷式一成立時，可以進行的指令工作內容；
elif [ 條件判斷式二 ]; then
	當條件判斷式二成立時，可以進行的指令工作內容；
else
	當條件判斷式一與二均不成立時，可以進行的指令工作內容；
fi
你得要注意的是， elif 也是個判斷式，因此出現 elif 後面都要接 then 來處理！但是 else 已經是最後的沒有成立的結果了， 所以 else 後面並沒有 then 喔！好！我們來將 ans_yn-2.sh 改寫成這樣：

[dmtsai@study bin]$ cp ans_yn-2.sh ans_yn-3.sh
[dmtsai@study bin]$ vim ans_yn-3.sh
     #!/bin/bash
     # Program:
     #       This program shows the user's choice
     # History:
     # 2015/07/16    VBird   First release
     PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH

     read -p "Please input (Y/N): " yn

     if [ "${yn}" == "Y" ] || [ "${yn}" == "y" ]; then
	echo "OK, continue"
     elif [ "${yn}" == "N" ] || [ "${yn}" == "n" ]; then
	echo "Oh, interrupt!"
     else
	echo "I don't know what your choice is"
     fi
是否程式變得很簡單，而且依序判斷，可以避免掉重複判斷的狀況，這樣真的很容易設計程式的啦！ ^_^！ 好了，讓我們再來進行另外一個案例的設計。一般來說，如果你不希望使用者由鍵盤輸入額外的資料時， 可以使用上一節提到的參數功能 ($1)！讓使用者在下達指令時就將參數帶進去！ 現在我們想讓使用者輸入『 hello 』這個關鍵字時，利用參數的方法可以這樣依序設計：

判斷 $1 是否為 hello，如果是的話，就顯示 "Hello, how are you ?"；
如果沒有加任何參數，就提示使用者必須要使用的參數下達法；
而如果加入的參數不是 hello ，就提醒使用者僅能使用 hello 為參數。
整個程式的撰寫可以是這樣的：

[dmtsai@study bin]$ vim hello-2.sh
     #!/bin/bash
     # Program:
     #	Check $1 is equal to "hello"
     # History:
     # 2015/07/16	VBird	First release
     PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH

     if [ "${1}" == "hello" ]; then
	     echo "Hello, how are you ?"
     elif [ "${1}" == "" ]; then
	     echo "You MUST input parameters, ex> {${0} someword}"
     else
	     echo "The only parameter is 'hello', ex> {${0} hello}"
     fi
然後你可以執行這支程式，分別在 $1 的位置輸入 hello, 沒有輸入與隨意輸入， 就可以看到不同的輸出囉～是否還覺得挺簡單的啊！ ^_^。事實上， 學到這裡，也真的很厲害了～好了，底下我們繼續來玩一些比較大一點的計畫囉～

我們在第十章已經學會了 grep 這個好用的玩意兒，那麼多學一個叫做 netstat 的指令，這個指令可以查詢到目前主機有開啟的網路服務埠口 (service ports)， 相關的功能我們會在伺服器架設篇繼續介紹，這裡你只要知道，我可以利用『 netstat -tuln 』來取得目前主機有啟動的服務， 而且取得的資訊有點像這樣：

[dmtsai@study ~]$ netstat -tuln
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN
tcp6       0      0 :::22                   :::*                    LISTEN
tcp6       0      0 ::1:25                  :::*                    LISTEN
udp        0      0 0.0.0.0:123             0.0.0.0:*
udp        0      0 0.0.0.0:5353            0.0.0.0:*
udp        0      0 0.0.0.0:44326           0.0.0.0:*
udp        0      0 127.0.0.1:323           0.0.0.0:*
udp6       0      0 :::123                  :::*
udp6       0      0 ::1:323                 :::*
	#封包格式           本地IP:埠口             遠端IP:埠口             是否監聽
上面的重點是『Local Address (本地主機的IP與埠口對應)』那個欄位，他代表的是本機所啟動的網路服務！ IP的部分說明的是該服務位於那個介面上，若為 127.0.0.1 則是僅針對本機開放，若是 0.0.0.0 或 ::: 則代表對整個 Internet 開放 (更多資訊請參考伺服器架設篇的介紹)。 每個埠口 (port) 都有其特定的網路服務，幾個常見的 port 與相關網路服務的關係是：

80: WWW
22: ssh
21: ftp
25: mail
111: RPC(遠端程序呼叫)
631: CUPS(列印服務功能)
假設我的主機有興趣要偵測的是比較常見的 port 21, 22, 25及 80 時，那我如何透過 netstat 去偵測我的主機是否有開啟這四個主要的網路服務埠口呢？由於每個服務的關鍵字都是接在冒號『 : 』後面， 所以可以藉由擷取類似『 :80 』來偵測的！那我就可以簡單的這樣去寫這個程式喔：

[dmtsai@study bin]$ vim netstat.sh
     #!/bin/bash
     # Program:
     # 	Using netstat and grep to detect WWW,SSH,FTP and Mail services.
     # History:
     # 2015/07/16	VBird	First release
     PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
     export PATH

     # 1. 先作一些告知的動作而已～
echo "Now, I will detect your Linux server's services!"
echo -e "The www, ftp, ssh, and mail(smtp) will be detected! \n"

     # 2. 開始進行一些測試的工作，並且也輸出一些資訊囉！
testfile=/dev/shm/netstat_checking.txt
netstat -tuln > ${testfile}          # 先轉存資料到記憶體當中！不用一直執行 netstat
testing=$(grep ":80 " ${testfile})   # 偵測看 port 80 在否？
if [ "${testing}" != "" ]; then
	echo "WWW is running in your system."
fi
testing=$(grep ":22 " ${testfile})   # 偵測看 port 22 在否？
if [ "${testing}" != "" ]; then
	echo "SSH is running in your system."
fi
testing=$(grep ":21 " ${testfile})   # 偵測看 port 21 在否？
if [ "${testing}" != "" ]; then
	echo "FTP is running in your system."
fi
testing=$(grep ":25 " ${testfile})   # 偵測看 port 25 在否？
if [ "${testing}" != "" ]; then
	echo "Mail is running in your system."
fi
實際執行這支程式你就可以看到你的主機有沒有啟動這些服務啦！是否很有趣呢？ 條件判斷式還可以搞的更複雜！舉例來說，在台灣當兵是國民應盡的義務，不過，在當兵的時候總是很想要退伍的！ 那你能不能寫個腳本程式來跑，讓使用者輸入他的退伍日期，讓你去幫他計算還有幾天才退伍？

由於日期是要用相減的方式來處置，所以我們可以透過使用 date 顯示日期與時間，將他轉為由 1970-01-01 累積而來的秒數， 透過秒數相減來取得剩餘的秒數後，再換算為日數即可。整個腳本的製作流程有點像這樣：

先讓使用者輸入他們的退伍日期；
再由現在日期比對退伍日期；
由兩個日期的比較來顯示『還需要幾天』才能夠退伍的字樣。
似乎挺難的樣子？其實也不會啦，利用『 date --date="YYYYMMDD" +%s 』轉成秒數後，接下來的動作就容易的多了！如果你已經寫完了程式，對照底下的寫法試看看：

[dmtsai@study bin]$ vim cal_retired.sh
	#!/bin/bash
	# Program:
	#	You input your demobilization date, I calculate how many days before you demobilize.
	# History:
	# 2015/07/16	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	# 1. 告知使用者這支程式的用途，並且告知應該如何輸入日期格式？
	echo "This program will try to calculate :"
	echo "How many days before your demobilization date..."
	read -p "Please input your demobilization date (YYYYMMDD ex>20150716): " date2

	# 2. 測試一下，這個輸入的內容是否正確？利用正規表示法囉～
	date_d=$(echo ${date2} |grep '[0-9]\{8\}')   # 看看是否有八個數字
	if [ "${date_d}" == "" ]; then
		echo "You input the wrong date format...."
		exit 1
	fi

	# 3. 開始計算日期囉～
	declare -i date_dem=$(date --date="${date2}" +%s)      # 退伍日期秒數
	declare -i date_now=$(date +%s)                        # 現在日期秒數
	declare -i date_total_s=$((${date_dem}-${date_now}))   # 剩餘秒數統計
	declare -i date_d=$((${date_total_s}/60/60/24))        # 轉為日數
	if [ "${date_total_s}" -lt "0" ]; then                 # 判斷是否已退伍
		echo "You had been demobilization before: " $((-1*${date_d})) " ago"
	else
		declare -i date_h=$(($((${date_total_s}-${date_d}*60*60*24))/60/60))
		echo "You will demobilize after ${date_d} days and ${date_h} hours."
	fi
瞧一瞧，這支程式可以幫你計算退伍日期呢～如果是已經退伍的朋友， 還可以知道已經退伍多久了～哈哈！很可愛吧～腳本中的 date_d 變數宣告那個 /60/60/24 是來自於一天的總秒數 (24小時*60分*60秒) 。瞧～全部的動作都沒有超出我們所學的範圍吧～ ^_^ 還能夠避免使用者輸入錯誤的數字，所以多了一個正規表示法的判斷式呢～ 這個例子比較難，有興趣想要一探究竟的朋友，可以作一下課後練習題 關於計算生日的那一題喔！～加油！



Top
12.4.2 利用 case ..... esac 判斷

上個小節提到的『 if .... then .... fi 』對於變數的判斷是以『比對』的方式來分辨的， 如果符合狀態就進行某些行為，並且透過較多層次 (就是 elif ...) 的方式來進行多個變數的程式碼撰寫，譬如 hello-2.sh 那個小程式，就是用這樣的方式來撰寫的囉。 好，那麼萬一我有多個既定的變數內容，例如 hello-2.sh 當中，我所需要的變數就是 "hello" 及空字串兩個， 那麼我只要針對這兩個變數來設定狀況就好了，對吧？那麼可以使用什麼方式來設計呢？呵呵～就用 case ... in .... esac 吧～，他的語法如下：

case  $變數名稱 in   <==關鍵字為 case ，還有變數前有錢字號
  "第一個變數內容")   <==每個變數內容建議用雙引號括起來，關鍵字則為小括號 )
	程式段
	;;            <==每個類別結尾使用兩個連續的分號來處理！
  "第二個變數內容")
	程式段
	;;
  *)                  <==最後一個變數內容都會用 * 來代表所有其他值
	不包含第一個變數內容與第二個變數內容的其他程式執行段
	exit 1
	;;
esac                  <==最終的 case 結尾！『反過來寫』思考一下！
要注意的是，這個語法以 case (實際案例之意) 為開頭，結尾自然就是將 case 的英文反過來寫！就成為 esac 囉！ 不會很難背啦！另外，每一個變數內容的程式段最後都需要兩個分號 (;;) 來代表該程式段落的結束，這挺重要的喔！ 至於為何需要有 * 這個變數內容在最後呢？這是因為，如果使用者不是輸入變數內容一或二時， 我們可以告知使用者相關的資訊啊！廢話少說，我們拿 hello-2.sh 的案例來修改一下，他應該會變成這樣喔：

[dmtsai@study bin]$ vim hello-3.sh
	#!/bin/bash
	# Program:
	# 	Show "Hello" from $1.... by using case .... esac
	# History:
	# 2015/07/16	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	case ${1} in
      "hello")
		echo "Hello, how are you ?"
		;;
      "")
		echo "You MUST input parameters, ex> {${0} someword}"
		;;
  	*)   # 其實就相當於萬用字元，0~無窮多個任意字元之意！
		echo "Usage ${0} {hello}"
		;;
	esac
在上面這個 hello-3.sh 的案例當中，如果你輸入『 sh hello-3.sh test 』來執行， 那麼螢幕上就會出現『Usage hello-3.sh {hello}』的字樣，告知執行者僅能夠使用 hello 喔～ 這樣的方式對於需要某些固定字串來執行的變數內容就顯的更加的方便呢！ 這種方式你真的要熟悉喔！這是因為早期系統的很多服務的啟動 scripts 都是使用這種寫法的 (CentOS 6.x 以前)。 雖然 CentOS 7 已經使用 systemd，不過仍有數個服務是放在 /etc/init.d/ 目錄下喔！例如有個名為 netconsole 的服務在該目錄下， 那麼你想要重新啟動該服務，是可以這樣做的 (請注意，要成功執行，還是得要具有 root 身份才行！一般帳號能執行，但不會成功！)：

/etc/init.d/netconsole restart
重點是那個 restart 啦！如果你使用『 less /etc/init.d/netconsole 』去查閱一下，就會看到他使用的是 case 語法， 並且會規定某些既定的變數內容，你可以直接下達 /etc/init.d/netconsole ， 該 script 就會告知你有哪些後續接的變數可以使用囉～方便吧！ ^_^

一般來說，使用『 case $變數 in 』這個語法中，當中的那個『 $變數 』大致有兩種取得的方式：

直接下達式：例如上面提到的，利用『 script.sh variable 』 的方式來直接給予 $1 這個變數的內容，這也是在 /etc/init.d 目錄下大多數程式的設計方式。

互動式：透過 read 這個指令來讓使用者輸入變數的內容。
這麼說或許你的感受性還不高，好，我們直接寫個程式來玩玩：讓使用者能夠輸入 one, two, three ， 並且將使用者的變數顯示到螢幕上，如果不是 one, two, three 時，就告知使用者僅有這三種選擇。

[dmtsai@study bin]$ vim show123.sh
	#!/bin/bash
	# Program:
	#	This script only accepts the flowing parameter: one, two or three.
	# History:
	# 2015/07/17	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	echo "This program will print your selection !"
	# read -p "Input your choice: " choice   # 暫時取消，可以替換！
	# case ${choice} in                      # 暫時取消，可以替換！
	case ${1} in                             # 現在使用，可以用上面兩行替換！
  	"one")
		echo "Your choice is ONE"
		;;
  	"two")
		echo "Your choice is TWO"
		;;
  	"three")
		echo "Your choice is THREE"
		;;
  	*)
		echo "Usage ${0} {one|two|three}"
		;;
	esac
此時，你可以使用『 sh show123.sh two 』的方式來下達指令，就可以收到相對應的回應了。 上面使用的是直接下達的方式，而如果使用的是互動式時，那麼將上面第 10, 11 行的 "#" 拿掉， 並將 12 行加上註解 (#)，就可以讓使用者輸入參數囉～這樣是否很有趣啊？



Top
12.4.3 利用 function 功能

什麼是『函數 (function)』功能啊？簡單的說，其實， 函數可以在 shell script 當中做出一個類似自訂執行指令的東西，最大的功能是， 可以簡化我們很多的程式碼～舉例來說，上面的 show123.sh 當中，每個輸入結果 one, two, three 其實輸出的內容都一樣啊～那麼我就可以使用 function 來簡化了！ function 的語法是這樣的：

function fname() {
	程式段
}
那個 fname 就是我們的自訂的執行指令名稱～而程式段就是我們要他執行的內容了。 要注意的是，因為 shell script 的執行方式是由上而下，由左而右， 因此在 shell script 當中的 function 的設定一定要在程式的最前面， 這樣才能夠在執行時被找到可用的程式段喔 (這一點與傳統程式語言差異相當大！初次接觸的朋友要小心！)！ 好～我們將 show123.sh 改寫一下，自訂一個名為 printit 的函數來使用喔：

[dmtsai@study bin]$ vim show123-2.sh
	#!/bin/bash
	# Program:
	#	Use function to repeat information.
	# History:
	# 2015/07/17	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	function printit(){
		echo -n "Your choice is "     # 加上 -n 可以不斷行繼續在同一行顯示
	}

	echo "This program will print your selection !"
	case ${1} in
 	 "one")
		printit; echo ${1} | tr 'a-z' 'A-Z'  # 將參數做大小寫轉換！
		;;
  	"two")
		printit; echo ${1} | tr 'a-z' 'A-Z'
		;;
  	"three")
		printit; echo ${1} | tr 'a-z' 'A-Z'
		;;
  	*)
		echo "Usage ${0} {one|two|three}"
		;;
	esac
以上面的例子來說，鳥哥做了一個函數名稱為 printit ，所以，當我在後續的程式段裡面， 只要執行 printit 的話，就表示我的 shell script 要去執行『 function printit .... 』 裡面的那幾個程式段落囉！當然囉，上面這個例子舉得太簡單了，所以你不會覺得 function 有什麼好厲害的， 不過，如果某些程式碼一再地在 script 當中重複時，這個 function 可就重要的多囉～ 不但可以簡化程式碼，而且可以做成類似『模組』的玩意兒，真的很棒啦！

Tips
鳥哥的圖示 建議讀者可以使用類似 vim 的編輯器到 /etc/init.d/ 目錄下去查閱一下你所看到的檔案， 並且自行追蹤一下每個檔案的執行情況，相信會更有心得！

另外， function 也是擁有內建變數的～他的內建變數與 shell script 很類似， 函數名稱代表示 $0 ，而後續接的變數也是以 $1, $2... 來取代的～ 這裡很容易搞錯喔～因為『 function fname() { 程式段 } 』內的 $0, $1... 等等與 shell script 的 $0 是不同的。以上面 show123-2.sh 來說，假如我下達：『 sh show123-2.sh one 』 這表示在 shell script 內的 $1 為 "one" 這個字串。但是在 printit() 內的 $1 則與這個 one 無關。 我們將上面的例子再次的改寫一下，讓你更清楚！

[dmtsai@study bin]$ vim show123-3.sh
	#!/bin/bash
	# Program:
	#	Use function to repeat information.
	# History:
	# 2015/07/17	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	function printit(){
		echo "Your choice is ${1}"   # 這個 $1 必須要參考底下指令的下達
	}

	echo "This program will print your selection !"
	case ${1} in
  	"one")
		printit 1  # 請注意， printit 指令後面還有接參數！
		;;
  	"two")
		printit 2
		;;
  	"three")
		printit 3
		;;
  	*)
		echo "Usage ${0} {one|two|three}"
		;;
	esac
在上面的例子當中，如果你輸入『 sh show123-3.sh one 』就會出現『 Your choice is 1 』的字樣～ 為什麼是 1 呢？因為在程式段落當中，我們是寫了『 printit 1 』那個 1 就會成為 function 當中的 $1 喔～ 這樣是否理解呢？ function 本身其實比較困難一點，如果你還想要進行其他的撰寫的話。 不過，我們僅是想要更加瞭解 shell script 而已，所以，這裡看看即可～瞭解原理就好囉～ ^_^


12.5 迴圈 (loop)

除了 if...then...fi 這種條件判斷式之外，迴圈可能是程式當中最重要的一環了～ 迴圈可以不斷的執行某個程式段落，直到使用者設定的條件達成為止。 所以，重點是那個『條件的達成』是什麼。除了這種依據判斷式達成與否的不定迴圈之外， 還有另外一種已經固定要跑多少次的迴圈形態，可稱為固定迴圈的形態呢！底下我們就來談一談：

Top
12.5.1 while do done, until do done (不定迴圈)

一般來說，不定迴圈最常見的就是底下這兩種狀態了：

while [ condition ]  <==中括號內的狀態就是判斷式
do            <==do 是迴圈的開始！
	程式段落
done          <==done 是迴圈的結束
while 的中文是『當....時』，所以，這種方式說的是『當 condition 條件成立時，就進行迴圈，直到 condition 的條件不成立才停止』的意思。還有另外一種不定迴圈的方式：

until [ condition ]
do
	程式段落
done
這種方式恰恰與 while 相反，它說的是『當 condition 條件成立時，就終止迴圈， 否則就持續進行迴圈的程式段。』是否剛好相反啊～我們以 while 來做個簡單的練習好了。 假設我要讓使用者輸入 yes 或者是 YES 才結束程式的執行，否則就一直進行告知使用者輸入字串。

[dmtsai@study bin]$ vim yes_to_stop.sh
	#!/bin/bash
	# Program:
	#	Repeat question until user input correct answer.
	# History:
	# 2015/07/17	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	while [ "${yn}" != "yes" -a "${yn}" != "YES" ]
	do
		read -p "Please input yes/YES to stop this program: " yn
	done
	echo "OK! you input the correct answer."
上面這個例題的說明是『當 ${yn} 這個變數不是 "yes" 且 ${yn} 也不是 "YES" 時，才進行迴圈內的程式。』 而如果 ${yn} 是 "yes" 或 "YES" 時，就會離開迴圈囉～那如果使用 until 呢？呵呵有趣囉～ 他的條件會變成這樣：

[dmtsai@study bin]$ vim yes_to_stop-2.sh
	#!/bin/bash
	# Program:
	#	Repeat question until user input correct answer.
	# History:
	# 2015/07/17	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	until [ "${yn}" == "yes" -o "${yn}" == "YES" ]
	do
		read -p "Please input yes/YES to stop this program: " yn
	done
	echo "OK! you input the correct answer."
仔細比對一下這兩個東西有啥不同喔！ ^_^再來，如果我想要計算 1+2+3+....+100 這個數據呢？ 利用迴圈啊～他是這樣的：

[dmtsai@study bin]$ vim cal_1_100.sh
	#!/bin/bash
	# Program:
	#	Use loop to calculate "1+2+3+...+100" result.
	# History:
	# 2015/07/17	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	s=0  # 這是加總的數值變數
	i=0  # 這是累計的數值，亦即是 1, 2, 3....
	while [ "${i}" != "100" ]
	do
		i=$(($i+1))   # 每次 i 都會增加 1 
		s=$(($s+$i))  # 每次都會加總一次！
	done
	echo "The result of '1+2+3+...+100' is ==> $s"
嘿嘿！當你執行了『 sh cal_1_100.sh 』之後，就可以得到 5050 這個數據才對啊！這樣瞭呼～ 那麼讓你自行做一下，如果想要讓使用者自行輸入一個數字，讓程式由 1+2+... 直到你輸入的數字為止， 該如何撰寫呢？應該很簡單吧？答案可以參考一下習題練習裡面的一題喔！



Top
12.5.2 for...do...done (固定迴圈)

相對於 while, until 的迴圈方式是必須要『符合某個條件』的狀態， for 這種語法，則是『 已經知道要進行幾次迴圈』的狀態！他的語法是：

for var in con1 con2 con3 ...
do
	程式段
done
以上面的例子來說，這個 $var 的變數內容在迴圈工作時：

第一次迴圈時， $var 的內容為 con1 ；
第二次迴圈時， $var 的內容為 con2 ；
第三次迴圈時， $var 的內容為 con3 ；
....
我們可以做個簡單的練習。假設我有三種動物，分別是 dog, cat, elephant 三種， 我想每一行都輸出這樣：『There are dogs...』之類的字樣，則可以：

[dmtsai@study bin]$ vim show_animal.sh
	#!/bin/bash
	# Program:
	# 	Using for .... loop to print 3 animals
	# History:
	# 2015/07/17	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	for animal in dog cat elephant
	do
		echo "There are ${animal}s.... "
	done
等你執行之後就能夠發現這個程式運作的情況啦！讓我們想像另外一種狀況，由於系統上面的各種帳號都是寫在 /etc/passwd 內的第一個欄位，你能不能透過管線命令的 cut 捉出單純的帳號名稱後，以 id 分別檢查使用者的識別碼與特殊參數呢？由於不同的 Linux 系統上面的帳號都不一樣！此時實際去捉 /etc/passwd 並使用迴圈處理，就是一個可行的方案了！程式可以如下：

[dmtsai@study bin]$ vim userid.sh
	#!/bin/bash
	# Program
	#       Use id, finger command to check system account's information.
	# History
	# 2015/07/17    VBird   first release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH
	users=$(cut -d ':' -f1 /etc/passwd)    # 擷取帳號名稱
	for username in ${users}               # 開始迴圈進行！
	do
        id ${username}
	done
執行上面的腳本後，你的系統帳號就會被捉出來檢查啦！這個動作還可以用在每個帳號的刪除、重整上面呢！ 換個角度來看，如果我現在需要一連串的數字來進行迴圈呢？舉例來說，我想要利用 ping 這個可以判斷網路狀態的指令， 來進行網路狀態的實際偵測時，我想要偵測的網域是本機所在的 192.168.1.1~192.168.1.100，由於有 100 台主機， 總不會要我在 for 後面輸入 1 到 100 吧？此時你可以這樣做喔！

[dmtsai@study bin]$ vim pingip.sh
	#!/bin/bash
	# Program
	#       Use ping command to check the network's PC state.
	# History
	# 2015/07/17    VBird   first release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH
	network="192.168.1"              # 先定義一個網域的前面部分！
	for sitenu in $(seq 1 100)       # seq 為 sequence(連續) 的縮寫之意
	do
		# 底下的程式在取得 ping 的回傳值是正確的還是失敗的！
        ping -c 1 -w 1 ${network}.${sitenu} &> /dev/null && result=0 || result=1
		# 開始顯示結果是正確的啟動 (UP) 還是錯誤的沒有連通 (DOWN)
        if [ "${result}" == 0 ]; then
                echo "Server ${network}.${sitenu} is UP."
        else
                echo "Server ${network}.${sitenu} is DOWN."
        fi
	done
上面這一串指令執行之後就可以顯示出 192.168.1.1~192.168.1.100 共 100 部主機目前是否能與你的機器連通！ 如果你的網域與鳥哥所在的位置不同，則直接修改上頭那個 network 的變數內容即可！其實這個範例的重點在 $(seq ..) 那個位置！那個 seq 是連續 (sequence) 的縮寫之意！代表後面接的兩個數值是一直連續的！ 如此一來，就能夠輕鬆的將連續數字帶入程式中囉！

Tips
鳥哥的圖示 除了使用 $(seq 1 100) 之外，你也可以直接使用 bash 的內建機制來處理喔！可以使用 {1..100} 來取代 $(seq 1 100) ！ 那個大括號內的前面/後面用兩個字元，中間以兩個小數點來代表連續出現的意思！例如要持續輸出 a, b, c...g 的話， 就可以使用『 echo {a..g} 』這樣的表示方式！

最後，讓我們來玩判斷式加上迴圈的功能！我想要讓使用者輸入某個目錄檔名， 然後我找出某目錄內的檔名的權限，該如何是好？呵呵！可以這樣做啦～

[dmtsai@study bin]$ vim dir_perm.sh
	#!/bin/bash
	# Program:
	#	User input dir name, I find the permission of files.
	# History:
	# 2015/07/17	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	# 1. 先看看這個目錄是否存在啊？
	read -p "Please input a directory: " dir
	if [ "${dir}" == "" -o ! -d "${dir}" ]; then
		echo "The ${dir} is NOT exist in your system."
		exit 1
	fi

	# 2. 開始測試檔案囉～
	filelist=$(ls ${dir})        # 列出所有在該目錄下的檔案名稱
	for filename in ${filelist}
	do
		perm=""
		test -r "${dir}/${filename}" && perm="${perm} readable"
		test -w "${dir}/${filename}" && perm="${perm} writable"
		test -x "${dir}/${filename}" && perm="${perm} executable"
		echo "The file ${dir}/${filename}'s permission is ${perm} "
done
呵呵！很有趣的例子吧～利用這種方式，你可以很輕易的來處理一些檔案的特性呢。接下來，讓我們來玩玩另一種 for 迴圈的功能吧！主要用在數值方面的處理喔！



Top
12.5.3 for...do...done 的數值處理

除了上述的方法之外，for 迴圈還有另外一種寫法！語法如下：

for (( 初始值; 限制值; 執行步階 ))
do
	程式段
done
這種語法適合於數值方式的運算當中，在 for 後面的括號內的三串內容意義為：

初始值：某個變數在迴圈當中的起始值，直接以類似 i=1 設定好；
限制值：當變數的值在這個限制值的範圍內，就繼續進行迴圈。例如 i<=100；
執行步階：每作一次迴圈時，變數的變化量。例如 i=i+1。
值得注意的是，在『執行步階』的設定上，如果每次增加 1 ，則可以使用類似『i++』的方式，亦即是 i 每次迴圈都會增加一的意思。好，我們以這種方式來進行 1 累加到使用者輸入的迴圈吧！

[dmtsai@study bin]$ vim cal_1_100-2.sh
	#!/bin/bash
	# Program:
	# 	Try do calculate 1+2+....+${your_input}
	# History:
	# 2015/07/17	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	read -p "Please input a number, I will count for 1+2+...+your_input: " nu

	s=0
		for (( i=1; i<=${nu}; i=i+1 ))
	do
		s=$((${s}+${i}))
	done
	echo "The result of '1+2+3+...+${nu}' is ==> ${s}"
一樣也是很簡單吧！利用這個 for 則可以直接限制迴圈要進行幾次呢！



Top
12.5.4 搭配亂數與陣列的實驗

現在你大概已經能夠掌握 shell script 了！好了！讓我們來做個小實驗！假設你們公司的團隊中，經常為了今天中午要吃啥搞到頭很昏！ 每次都用猜拳的～好煩喔～有沒有辦法寫支腳本，用腳本搭配亂數來告訴我們，今天中午吃啥好？呵呵！執行這隻腳本後， 直接跟你說要吃啥～那比猜拳好多了吧？哈哈！

要達成這個任務，首先你得要將全部的店家輸入到一組陣列當中，再透過亂數的處理，去取得可能的數值，再將搭配到該數值的店家秀出來即可！ 其實也很簡單！讓我們來實驗看看：

[dmtsai@study bin]$ vim what_to_eat.sh
	#!/bin/bash
	# Program:
	# 	Try do tell you what you may eat.
	# History:
	# 2015/07/17	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	eat[1]="賣噹噹漢堡"       # 寫下你所收集到的店家！
	eat[2]="肯爺爺炸雞"
	eat[3]="彩虹日式便當"
	eat[4]="越油越好吃大雅"
	eat[5]="想不出吃啥學餐"
	eat[6]="太師父便當"
	eat[7]="池上便當"
	eat[8]="懷念火車便當"
	eat[9]="一起吃泡麵"
	eatnum=9                  # 需要輸入有幾個可用的餐廳數！

	check=$(( ${RANDOM} * ${eatnum} / 32767 + 1 ))
	echo "your may eat ${eat[${check}]}"
立刻執行看看，你就知道該吃啥了！非常有趣吧！不過，這個例子中只選擇一個樣本，不夠看！如果想要每次都秀出 3 個店家呢？ 而且這個店家不能重複喔！重複當然就沒啥意義了！所以，你可以這樣作！

[dmtsai@study bin]$ vim what_to_eat-2.sh
	#!/bin/bash
	# Program:
	# 	Try do tell you what you may eat.
	# History:
	# 2015/07/17	VBird	First release
	PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
	export PATH

	eat[1]="賣噹噹漢堡"
	eat[2]="肯爺爺炸雞"
	eat[3]="彩虹日式便當"
	eat[4]="越油越好吃大雅"
	eat[5]="想不出吃啥學餐"
	eat[6]="太師父便當"
	eat[7]="池上便當"
	eat[8]="懷念火車便當"
	eat[9]="一起吃泡麵"
	eatnum=9

	eated=0
	while [ "${eated}" -lt 3 ]; do
        check=$(( ${RANDOM} * ${eatnum} / 32767 + 1 ))
        mycheck=0
        if [ "${eated}" -ge 1 ]; then
                for i in $(seq 1 ${eated} )
                do
                        if [ ${eatedcon[$i]} == $check ]; then
                                mycheck=1
                        fi
                done
        fi
        if [ ${mycheck} == 0 ]; then
                echo "your may eat ${eat[${check}]}"
                eated=$(( ${eated} + 1 ))
                eatedcon[${eated}]=${check}
        fi
	done 
透過亂數、陣列、迴圈與條件判斷，你可以做出很多很特別的東西！還不用寫傳統程式語言～試看看～挺有趣的呦！


12.6 shell script 的追蹤與 debug

scripts 在執行之前，最怕的就是出現語法錯誤的問題了！那麼我們如何 debug 呢？有沒有辦法不需要透過直接執行該 scripts 就可以來判斷是否有問題呢？呵呵！當然是有的！我們就直接以 bash 的相關參數來進行判斷吧！

[dmtsai@study ~]$ sh [-nvx] scripts.sh
選項與參數：
-n  ：不要執行 script，僅查詢語法的問題；
-v  ：再執行 sccript 前，先將 scripts 的內容輸出到螢幕上；
-x  ：將使用到的 script 內容顯示到螢幕上，這是很有用的參數！

範例一：測試 dir_perm.sh 有無語法的問題？
[dmtsai@study ~]$ sh -n dir_perm.sh 
	# 若語法沒有問題，則不會顯示任何資訊！

範例二：將 show_animal.sh 的執行過程全部列出來～
[dmtsai@study ~]$ sh -x show_animal.sh 
+ PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:/root/bin
+ export PATH
+ for animal in dog cat elephant
+ echo 'There are dogs.... '
There are dogs....
+ for animal in dog cat elephant
+ echo 'There are cats.... '
There are cats....
+ for animal in dog cat elephant
+ echo 'There are elephants.... '
There are elephants....
請注意，上面範例二中執行的結果並不會有顏色的顯示！鳥哥為了方便說明所以在 + 號之後的資料都加上顏色了！ 在輸出的訊息中，在加號後面的資料其實都是指令串，由於 sh -x 的方式來將指令執行過程也顯示出來， 如此使用者可以判斷程式碼執行到哪一段時會出現相關的資訊！這個功能非常的棒！透過顯示完整的指令串， 你就能夠依據輸出的錯誤資訊來訂正你的腳本了！

熟悉 sh 的用法，將可以使你在管理 Linux 的過程中得心應手！至於在 Shell scripts 的學習方法上面，需要『多看、多模仿、並加以修改成自己的樣式！』 是最快的學習手段了！網路上有相當多的朋友在開發一些相當有用的 scripts ，若是你可以將對方的 scripts 拿來，並且改成適合自己主機的樣子！那麼學習的效果會是最快的呢！

另外，我們 Linux 系統本來就有很多的服務啟動腳本，如果你想要知道每個 script 所代表的功能是什麼？ 可以直接以 vim 進入該 script 去查閱一下，通常立刻就知道該 script 的目的了。 舉例來說，我們之前一直提到的 /etc/init.d/netconsole ，這個 script 是幹嘛用的？ 利用 vim 去查閱最前面的幾行字，他出現如下資訊：

	# netconsole    This loads the netconsole module with the configured parameters.
	# chkconfig: - 50 50
	# description: Initializes network console logging
	# config: /etc/sysconfig/netconsole
意思是說，這個腳本在設定網路終端機來應付登入的意思，且設定檔在 /etc/sysconfig/netconsole 設定內！ 所以，你寫的腳本如果也能夠很清楚的交待，那就太棒了！

另外，本章所有的範例都可以在 http://linux.vbird.org/linux_basic/0340bashshell-scripts/scripts-20150717.tar.bz2 裡頭找到喔！加油～

12.7 重點回顧

shell script 是利用 shell 的功能所寫的一個『程式 (program)』，這個程式是使用純文字檔，將一些 shell 的語法與指令(含外部指令)寫在裡面， 搭配正規表示法、管線命令與資料流重導向等功能，以達到我們所想要的處理目的
shell script 用在系統管理上面是很好的一項工具，但是用在處理大量數值運算上， 就不夠好了，因為 Shell scripts 的速度較慢，且使用的 CPU 資源較多，造成主機資源的分配不良。
在 Shell script 的檔案中，指令的執行是從上而下、從左而右的分析與執行；
shell script 的執行，至少需要有 r 的權限，若需要直接指令下達，則需要擁有 r 與 x 的權限；
良好的程式撰寫習慣中，第一行要宣告 shell (#!/bin/bash) ，第二行以後則宣告程式用途、版本、作者等
對談式腳本可用 read 指令達成；
要建立每次執行腳本都有不同結果的資料，可使用 date 指令利用日期達成；
script 的執行若以 source 來執行時，代表在父程序的 bash 內執行之意！
若需要進行判斷式，可使用 test 或中括號 ( [] ) 來處理；
在 script 內，$0, $1, $2..., $@ 是有特殊意義的！
條件判斷式可使用 if...then 來判斷，若是固定變數內容的情況下，可使用 case $var in ... esac 來處理
迴圈主要分為不定迴圈 (while, until) 以及固定迴圈 (for) ，配合 do, done 來達成所需任務！
我們可使用 sh -x script.sh 來進行程式的 debug
12.8 本章習題

( 要看答案請將滑鼠移動到『答：』底下的空白處，按下左鍵圈選空白處即可察看 ) 底下皆為實作題，請自行撰寫出程式喔！
請建立一支 script ，當你執行該 script 的時候，該 script 可以顯示： 1. 你目前的身份 (用 whoami ) 2. 你目前所在的目錄 (用 pwd)
	#!/bin/bash
	echo -e "Your name is ==> $(whoami)"
	echo -e "The current directory is ==> $(pwd)"
請自行建立一支程式，該程式可以用來計算『你還有幾天可以過生日』啊？
	#!/bin/bash
	read -p "Pleas input your birthday (MMDD, ex> 0709): " bir
	now=`date +%m%d`
	if [ "$bir" == "$now" ]; then
	echo "Happy Birthday to you!!!"
	elif [ "$bir" -gt "$now" ]; then
	year=`date +%Y`
	total_d=$(($((`date --date="$year$bir" +%s`-`date +%s`))/60/60/24))
	echo "Your birthday will be $total_d later"
	else
	year=$((`date +%Y`+1))
	total_d=$(($((`date --date="$year$bir" +%s`-`date +%s`))/60/60/24))
	echo "Your birthday will be $total_d later"
	fi
讓使用者輸入一個數字，程式可以由 1+2+3... 一直累加到使用者輸入的數字為止。
	#!/bin/bash
	read -p "Please input an integer number: " number
	i=0
	s=0
	while [ "$i" != "$number" ]
	do
	i=$(($i+1))
	s=$(($s+$i))
	done
echo "the result of '1+2+3+...$number' is ==> $s"
撰寫一支程式，他的作用是: 1.) 先查看一下 /root/test/logical 這個名稱是否存在； 2.) 若不存在，則建立一個檔案，使用 touch 來建立，建立完成後離開； 3.) 如果存在的話，判斷該名稱是否為檔案，若為檔案則將之刪除後建立一個目錄，檔名為 logical ，之後離開； 4.) 如果存在的話，而且該名稱為目錄，則移除此目錄！
	#!/bin/bash
	if [ ! -e logical ]; then
	touch logical
	echo "Just make a file logical"
	exit 1
	elif [ -e logical ] && [ -f logical ]; then
	rm logical
	mkdir logical
	echo "remove file ==> logical"
	echo "and make directory logical"
	exit 1
	elif [ -e logical ] && [ -d logical ]; then
	rm -rf logical
	echo "remove directory ==> logical"
	exit 1
	else
	echo "Does here have anything?"
	fi
我們知道 /etc/passwd 裡面以 : 來分隔，第一欄為帳號名稱。請寫一隻程式，可以將 /etc/passwd 的第一欄取出，而且每一欄都以一行字串『The 1 account is "root" 』來顯示，那個 1 表示行數。
	#!/bin/bash
	accounts=`cat /etc/passwd | cut -d':' -f1`
	for account in $accounts
	do
	declare -i i=$i+1
	echo "The $i account is \"$account\" "
	done
