## 定义文件
1、查看kernel版本，确定在哪里加入sys call
uname -r
    5.11.0-27-generic

2、kernel保存在/usr/src
cd /usr/src
    linux-headers-5.11.0-27-generic

3、设定system call table
路徑位置：/usr/src/linux-headers-5.11.0-27-generic/arch/x86/kernel/
cd /usr/src/linux-headers-5.11.0-27-generic/arch/x86/kernel/

4、加入 system call文件（我的目录下没有system call文件syscall_table_32.S）
新建sys call文件 
nano syscall_table_32.S
    .long sys mycall    /* yan add row */

5、增加sys call 定义
路徑位置：/usr/src/linux-headers-5.11.0-27-generic/arch/x86/include/asm/unistd.h
（与kernel-2.6.28区别：没有unistd_32.h文件，文件更名：unistd.h ）
cd /usr/src/linux-headers-5.11.0-27-generic/arch/x86/include/asm/
nano unistd.h

解释：在unistd.h這個檔案中，增加system call number，
也就是增加#define __NR_mycall，並定義為第333個system call number，
    在代码是中是__NR_syscall_max + 1
增加完敘述後再順便增加#define NR_syscalls     334，
    在代码是中是__NR_syscall_max + 2
這一行敘述，讓system call number提供上下界


6、定义system call的函式原型
路徑位置：/usr/src/linux-headers-5.11.0-27-generic/arch/x86/include/asm/syscalls.h
新增asmlinkage int sys_mycall(void);的函式原型
cd /usr/src/linux-headers-5.11.0-27-generic/arch/x86/include/asm/
nano syscalls.h
    asmlinkage int sys_mycall(void);

7、新增system call的主程式碼
路徑位置：/usr/src/linux-headers-5.11.0-27-generic/kernel/
cd /usr/src/linux-headers-5.11.0-27-generic/kernel/
這邊的目的就是撰寫新增的system call的程式碼，
注意新增的程式碼檔名得要跟system call的名稱一樣，
像在這邊就得要新增一個名為mycall.c的檔案，
否則則系統無法找到正確的 system call 對應
nano mycall.c

8、Makefile的設定
路徑位置：/usr/src/linux-headers-5.11.0-27-generic/kernel/Makefile
cd /usr/src/linux-headers-5.11.0-27-generic/kernel/
在obj-y後面新增自己新增要編譯的檔， 變數 obj-y 表示需要編繹到內核中的目標檔案名集合，如下範例新增mycall.o
nano Makefile
    
## 编译文件
位置：/usr/src/linux-headers-5.11.0-27-generic/
cd /usr/src/linux-headers-5.11.0-27-generic/
1、删除中间档案
make mrproper
make clean

2、通过使用menuconfig
我的电脑缺少组件：
sudo apt-get install flex
apt-get install bison -y
启动： make menuconfig
卡住了~
