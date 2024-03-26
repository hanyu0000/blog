# Makefile
**GCC编译器编译**时会使用如下命令：

gcc -o a.out helloworld.c

./a.out

而当一个项目很大，源文件很多时，此时再使用gcc编译的话，每编译一次都要敲进去几万个源文件（悲）

使用make编译程序，不需要每次都输入源文件，直接在命令行下敲击 make 命令，就可一键自动化完成编译
### 步骤
首先在同一个目录下创建一个 Makefile 文件和 helloworld.c 文件

在Makefile文件中输入：

>a.out: helloworld.o
>
>（Tab） @gcc -o a.out helloworld.o
>
>helloworld.o: helloworld.c
>
>（Tab） @gcc -c -o helloworld.o helloworld.c
>
>clean:
>
>（Tab） @rm -f a.out helloworld.o

！！！注意在 Makefile 中，每个命令必须以一个 Tab 字符开头，而不是空格

然后在这个目录下直接敲击 make 命令,会出现 Makefile 文件中的这两行：

gcc -c -o helloworld.o helloworld.c

gcc -o a.out helloworld.o

这时直接编译./a.out

便会直接输出 helloworld！

最后敲击 make clean ,便会将创建的 .c 及 .o 文件删除
## 学习Makefile的重要性
Makefile在整个项目工程管理和程序的编译链接过程中，都扮演了一个非常重要的角色，是linux/Unix环境下开发必备技能，也是系统架构师、项目经理的核心技能。掌握了Makefile，会让你对底层软件的构造系统、程序的编译过程有一个更深的理解。

会不会写Makefile，是侧面可以看出一个人是否具有完成大型项目工程的能力。如果你是在Linux下进行C/C++开发，掌握Makefile可能让你更深地去理解项目，去掌控整个项目的构建和维护。

Makefile也是一个研究开源项目的利器。很多开源项目可能文档不完整，而Makefile就是开源项目的地图，从Makefile入手，可以让你快速窥探整个开源项目的框架和概貌，让你深入代码而不至于迷路。

综上所述，如果你是一名Linux下的开发工程师、嵌入式工程师、内核驱动开发工程师，掌握Makefile是一门必备技能。它和git、vim一样，掌握了这个“Linux三剑客”会让你的工作事半功倍、更加高效。

## Makefile的主要作用

在程序编译过程中，Makefile的主要作用就是：构建生成可执行文件的依赖关系树。一个可执行文件需要哪些源文件、哪些库，我们都可以在Makefile里指定。在程序编译阶段，make工具会首先解析这个Makefile，根据Makefile里的指定，构建出编译可执行文件所需要的完整依赖关系。

根据这个依赖关系，make工具接下来就可以分别去编译需要的文件；通过时间戳，可以动态识别哪些是新添加的文件，哪些是已经编译过的文件，哪些文件做了新的修改，这样就不用每次都重新编译了，只编译那些新修改或新添加的源文件就可以了，大大提升了编译的效率。

### 规则
通过规则 Makefile 构建可执行文件编译所依赖的**关系树**

>目标：目标依赖
>
>a.out: hello.c
>
>命令
>
>gcc -o a.out hello.c

a.out 就是我们要生成的目标,目标一般是一个可执行文件

目标依赖是指生成这个可执行文件所依赖的源文件，如 hello.c

而命令则是如何将这些目标依赖生成对应的目标，一般是gcc命令、链接命令、objcopy命令，一些shell命令等(命令必须使用tab键进行缩进，否则Makefile就会报错)

一个规则中的三个部分并不是都必须要有的:

一个 Makefile 文件中可能包含多个规则，有的规则可能无目标依赖，仅仅是为了实现某种操作。如下面的clean命令：
>clean:
>
>rm -f a.out hello.o

当我们使用 make clean 命令清理编译的文件时，会调用这个规则中的命令，不需要什么依赖，仅仅是执行删除操作，所以这个规则中并没有目标依赖。

当然，一个规则中也可以没有命令，仅仅包含目标和目标依赖，用来描述一种依赖关系。**但一个规则中一定要有一个目标。**

### 默认目标

一个 Makefile 文件里通常会有多个目标，一般会选择**第一个作为默认目标**

正常情况下，当你想编译生成 a.out 时，要使用 make a.out 命令，但为什么直接使用 make ，没有指定参数，也能生成 a.out 的呢？

这是因为在上面的 Makefile 文件中，a.out 是文件中的第一个目标，当我们在 make 编译时没有给make指定要生成的目标，make就会选择Makefile文件中的第一个目标作为默认目标。
### 多目标

一个规则中也可以有多个目标，多个目标具有**相同的**生成命令和依赖文件

如一个目标文件 a.o 都是由其对应的源文件 a.c 编译生成的，生成命令也是相同的：
>a.o: a.c
>
>gcc -o a.o a.c

### 多规则目标

多个规则可能是同一个目标，make 在解析 Makefile 文件时，会将具有相同目标的规则的依赖文件合并
>a.out: hello.c
>
>gcc -o a.out hello.c
>
>a.out: module.c

Make 在解析 Makefile 构建依赖关系树时，会将这两个规则合并为：
>a.out: hello.c module.c
>
>gcc -o a.out hello.c other.c

### 伪目标

有时候我们设置一个目标，并不是真正生成这个文件

如上面的clean目标，而是仅仅为了执行某个操作

当我们执行 make clean 时，clean 这个目标并没有生成对应的目标文件clean

因此，clean也可以设置为伪目标

伪目标并不是一个真正的文件，可以看做是一个标签

    .PHONY: clean
    a.out: hello.o
        gcc -o a.out hello.o
    hello.o: hello.c
        gcc -c -o hello.o hello.c
    clean:
        rm -f a.out hello.o

伪目标一般没有依赖关系，也不会生成对应的目标文件，可以无条件执行

纯粹是为了执行某一个命令，如clean执行清理工作