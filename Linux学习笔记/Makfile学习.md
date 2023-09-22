# C 语言编译原理：

## 编译过程：

1. 预处理：

```shell
    //默认不生成.i文件
    gcc -E add.c
    gcc -E add.c -o add.i
```

- -E 选项告诉编译器只进行预处理操作
- -o 选项把预处理的结果输出到指定文件

2. 编译：

```shell
    gcc -S add.i
    gcc -S add.c
    gcc -S add.c -o add.s
```

- -S 选项告诉编译器，进行预处理和编译成汇编语言操作

3. 汇编:

```shell
    gcc -c add.s
    gcc -c add.c
    gcc -c add.c -o add.o
```

- -c 选项告诉编译器，进行预处理、编译和汇编操作

4. 链接：

```shell
    gcc add.o
    gcc add.c
    gcc add.c -o add
```

还可以编译多个文件：

```shell
    gcc add.c minus.c main.c -o exec
```

- 将尝试使用 gcc 编译器编译三个源文件（add.c、minus.c 和 main.c），并将生成的可执行文件命名为 exec。

# 静态库和动态库

- 编译时链接：库通常是一组已编译好的二进制代码，可以包含函数、类、对象等。当你使用库时，编译器会在编译时将库的二进制代码链接到你的程序中。这意味着库的代码在你的程序编译时已经被合并到了最终的可执行文件中。

- 代码封装：库通常会将相关的功能封装在一个或多个文件中，隐藏了内部的实现细节。这意味着你只需要了解库的接口，而不需要关心内部实现。

- 共享性：多个程序可以共享同一个库，这可以减少磁盘空间的占用，并且如果库需要更新或修复，只需要更新库文件而不需要修改每个使用该库的程序。

- 静态库和动态库：库可以分为静态库和动态库。静态库在编译时被链接到可执行文件中，而动态库在运行时被加载。

## 编静态库：

1. 编库：

```shell
    ar -r [lib自定义库名.a] [.o] [.o] ...
```

2. 链接成可执行文件：

```shell
    gcc [.c] [.a] -o [自定义输出文件名]
    gcc [.c] -o [自定义输出文件名] -l[库名] -L[库所在路径]
```

## 编动态库：

1. 编库：

```shell
    gcc -shared [.o][.o]... -o [lib自定义库
    gcc -fpic -shared [.c源文件] [.c源文件] [...] -o lib[库名].so
```

2. 链接成可执行文件：

```shell
    gcc [.c/.cpp] -o [自定义可执行文件名]  -l[库名] -L[库路径] -Wl,-rpath=[库路径]
```

- `-L[库所在路径]` 是用于告诉编译器查找库文件的路径，而 `-Wl,-rpath=[库所在路径]` 是用于告诉链接器将库文件链接到可执行文件时使用的路径。两次说明库路径是为了确保编译器和链接器都能找到并使用库文件。

---

# Mkefile

- make 会在当前目录下找到一个名字叫 Makefile 或 makefile 的文件
  如果找到，它会找文件中第一个目标文件（target），并把这个文件作为最终的目标文件
  如果 target 文件不存在，或是 target 文件依赖的 .o 文件(prerequities)的文件修改时间要比 target 这个文件新，就会执行后面所定义的命令 command 来生成 target 这个文件
  如果 target 依赖的 .o 文件（prerequisties）也存在，make 会在当前文件中找到 target 为 .o 文件的依赖性，如果找到，再根据那个规则生成 .o 文件
  **总得来说就是 Makefile 会根据第一个目标文件（target），一步一步找到最终依赖并生成第一个目标**

## Makefile 基本语法：

### 规则（Rules）：

```makefile
target : dependencies
[tab键]command
```

- target 是目标文件的名称，通常是一个可执行文件、库文件或中间文件的名称。
- dependencies 是 target 依赖的文件列表，这些文件的修改会触发 target 的重新构建。
- command 是用于生成 target 的命令，通常是编译器或链接器命令。

### 变量（Variables）:

```makefile
CC = gcc
CFLAGS = -Wall -O2
```

### 注释（Comments）:

```makefile
# 这是一个示例 Makefile
```

### 伪目标（Phony Targets）:

```makefile
.PHONY: clean
clean:
    rm -f *.o my_program
```

- 伪目标是不与实际文件关联的目标，通常用于执行一些操作，例如清理或删除文件。伪目标前面通常会加上 .PHONY.

### 通配符（Wildcards）:

```makefile
SOURCES = *.c
```

## Makefile 变量：

变量在声明时需要给予初值，而在使用时，需要给在变量名前加上 `$` 符号，并用小括号 `()` 把变量给包括起来。

&emsp;

### 1 变量的定义

```makefile
cpp := src/main.cpp
obj := objs/main.o
```

### 2 变量的引用

- 可以用 `()` 或 `{}`

```makefile
cpp := src/main.cpp
obj := objs/main.o

$(obj) : ${cpp}
	@g++ -c $(cpp) -o $(obj)

compile : $(obj)
```

&emsp;

### 3 预定义变量

- `$@`: 目标(target)的完整名称
- `$<`: 第一个依赖文件（prerequisties）的名称
- `$^`: 所有的依赖文件（prerequisties），以空格分开，不包含重复的依赖文件

```make
cpp := src/main.cpp
obj := objs/main.o

$(obj) : ${cpp}
	@g++ -c $< -o $@
	@echo $^

compile : $(obj)
.PHONY : compile
```

&emsp;

## Makefile 常用符号

### 1 =

- 简单的赋值运算符
- 用于将右边的值分配给左边的变量
- 如果在后面的语句中重新定义了该变量，则将使用新的值

> 示例

```make
HOST_ARCH   = aarch64
TARGET_ARCH = $(HOST_ARCH)

# 更改了变量 a
HOST_ARCH   = amd64

debug:
	@echo $(TARGET_ARCH)
```

### 2 :=

- 立即赋值运算符
- 用于在定义变量时立即求值
- 该值在定义后不再更改
- 即使在后面的语句中重新定义了该变量

> 示例

```make
HOST_ARCH   := aarch64
TARGET_ARCH := $(HOST_ARCH)

# 更改了变量 a
HOST_ARCH := amd64

debug:
	@echo $(TARGET_ARCH)
```

&emsp;

### 3 ?=

- 默认赋值运算符
- 如果该变量已经定义，则不进行任何操作
- 如果该变量尚未定义，则求值并分配

```make
HOST_ARCH  = aarch64
HOST_ARCH ?= amd64

debug:
    @echo $(HOST_ARCH)
```

&emsp;

### 4 累加 +=

```makefile
CXXFLAGS := -m64 -fPIC -g -O0 -std=c++11 -w -fopenmp

CXXFLAGS += $(include_paths)
```

&emsp;

### 5 \

- 续行符
  > 示例

```make
LDLIBS := cudart opencv_core \
          gomp nvinfer protobuf cudnn pthread \
          cublas nvcaffe_parser nvinfer_plugin
```

&emsp;

### 6 \* 与 %

- `*`: 通配符表示匹配任意字符串，可以用在目录名或文件名中
- `%`: 通配符表示匹配任意字符串，并将匹配到的字符串作为变量使用

---

&emsp;

## Makefile 常用函数:

函数调用，很像变量的使用，也是以 “$” 来标识的，其语法如下：

```makefile
$(fn, arguments) or ${fn, arguments}
```

- fn: 函数名
- arguments: 函数参数，参数间以逗号 `,` 分隔，而函数名和参数之间以“空格”分隔

&emsp;

### 1 shell

```makefile
$(shell <command> <arguments>)
```

- 名称：shell 命令函数 —— shell
- 功能：调用 shell 命令 command
- 返回：函数返回 shell 命令 command 的执行结果

> 示例

```makefile
# shell 指令，src 文件夹下找到 .cpp 文件
cpp_srcs := $(shell find src -name "*.cpp")
# shell 指令, 获取计算机架构
HOST_ARCH := $(shell uname -m)
```

&emsp;

### 2 subst

```makefile
$(subst <from>,<to>,<text>)
```

- 名称：字符串替换函数——subst
- 功能：把字串 \<text> 中的 \<from> 字符串替换成 \<to>
- 返回：函数返回被替换过后的字符串
  > 示例：

```makefile

cpp_srcs := $(shell find src -name "*.cpp")
cpp_objs := $(subst src/,objs/,$(cpp_objs))

```

&emsp;

### 3 patsubst

```makefile
$(patsubst <pattern>,<replacement>,<text>)
```

- 名称：模式字符串替换函数 —— patsubst
- 功能：通配符 `%`，表示任意长度的字串，从 text 中取出 patttern， 替换成 replacement
- 返回：函数返回被替换过后的字符串
  > 示例

```makefile
cpp_srcs := $(shell find src -name "*.cpp") #shell指令，src文件夹下找到.cpp文件
cpp_objs := $(patsubst %.cpp,%.o,$(cpp_srcs)) #cpp_srcs变量下cpp文件替换成 .o文件
```

&emsp;

### 4 foreach

```makefile
$(foreach <var>,<list>,<text>)
```

- 名称：循环函数——foreach。
- 功能：把字串\<list>中的元素逐一取出来，执行\<text>包含的表达式
- 返回：\<text>所返回的每个字符串所组成的整个字符串（以空格分隔）

> 示例：

```makefile
library_paths := /datav/shared/100_du/03.08/lean/protobuf-3.11.4/lib \
                 /usr/local/cuda-10.1/lib64

library_paths := $(foreach item,$(library_paths),-L$(item))
```

> 同等效果

```makefile
I_flag := $(include_paths:%=-I%)
```

&emsp;

### 5 dir

```makefile
$(dir <names...>)
```

- 名称：取目录函数——dir。
- 功能：从文件名序列<names>中取出目录部分。目录部分是指最后一个反斜杠（“/”）之前
  的部分。如果没有反斜杠，那么返回“./”。
- 返回：返回文件名序列<names>的目录部分。
- 示例：

```makefile
$(dir src/foo.c hacks)    # 返回值是“src/ ./”。
```

&emsp;

### 6 notdir

```makefile
$(notdir <names...>)
```

> 示例

```makefile
libs   := $(notdir $(shell find /usr/lib -name lib*))
```

&emsp;

### 7 filter

```makefile
$(filter <names...>)
```

```makefile
libs    := $(notdir $(shell find /usr/lib -name lib*))
a_libs  := $(filter %.a,$(libs))
so_libs := $(filter %.so,$(libs))
```

&emsp;

### 8 basename

```makefile
$(basename <names...>)
```

```makefile
libs    := $(notdir $(shell find /usr/lib -name lib*))
a_libs  := $(subst lib,,$(basename $(filter %.a,$(libs))))
so_libs := $(subst lib,,$(basename $(filter %.so,$(libs))))
```

&emsp;

### 9 filter-out

- 剔除不想要的字符串

```makefile
objs := objs/add.o objs/minus.o objs/main.o
cpp_objs := $(filter-out objs/main.o, $(objs))
```

&emsp;

### 10 wildcard

- The wildcard function expands to a space-separated list of filenames that match the given patterns

```makefile
cpp_srcs := $(wildcard src/*.cc src/*.cpp src/*.c)
```

## 一个示例：

```makefile
src := $(filter-out src/main.c,$(shell find src -name '*.c'))
objs := $(patsubst src/%.c,obj/%.o,${src})

include_dirs := includes
include_paths := $(include_dirs:%=-I%)

#编静态库

obj/%.o: src/%.c
	@mkdir -p $(dir $@)
	@gcc -c $< -o $@ ${include_paths}

lib/libxxx.a: ${objs}
	@mkdir -p $(dir $@)
	@ar -r $@ ${objs}

lib_a:lib/libxxx.a

exec:lib_a
	@gcc -o $@ src/main.c -Llib -lxxx ${include_paths}

#链接成可执行文件
all:exec
	@rm -rf lib obj

debug:
	@echo src: ${src}
	@echo include_paths: ${include_paths}

clean:
	@rm -rf lib obj

.PHONY: debug clean compile lib_a all
```
