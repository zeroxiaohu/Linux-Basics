# 第一部分 Makefile的介绍
## 1 Makefile的书写规则
```c
  target :  paerequisites
  commond
```
**target** ：目标文件 可以是执行文件，Object File，还可以是一个标签，
**prerequtsites** ： 要生成target所需的文件或目标
**command**    ： make 需要执行的命令
- 当prerequisites中如果有一个以上的文件比target文件要新的话，command命令就会执行

- **“\”** 在Makefile是换行符的意思
- 
## 2 make工作原理
**示例**
```c
  test: main.o
  gcc -o test main.o

  main.o: main.c main.h
  gcc -c main.c
  clean:
  rm test main.o
```
*这里的 clean 在命令行中用 make clean 调用*
- 1、make会在当前目录下找名字叫“Makefile”或“makefile”的文件
- 2、如果找到，他会找文件中的第一个目标文件（target），把这个目标文件当成最终目标文件
- 3、如果这个文件不存在，或者后面的依赖文件比目标文件更新，就会执行所定义的命令来生成最总目标文件
- 4、如果目标文件的.o文件也不存在，那么make会在当前文件中找到目标为.o文件的依赖，再根据后面定义的规则生成一个.o文件

## 3 变量的引入
**示例**
```c
edit : main.o kbd.o 
gcc -o edit main.o kbd.o 

main.o: main.c main.h  kbd.h
gcc -c main.c 

kbd.o: kbd.c kbd.h
gcc -c kbd.c

clean:
rm edit main.o kbd.o
```
在上面的文件中每一个字符串被定义了两次，如果增加新的.o文件，那么需要在两个地方加，这样不便于维护，makefile里面的变量可以理解成c语言中的宏
```c
objects = main.o kbd.o
edit: $(objects)
gcc -o edit $(objects)

main.o: main.c main.h  kbd.h
gcc -c main.c

kbd.o: kbd.c kbd.h
gcc -c kbd.c

clean:
rm edit $(objects)
```
## 4 make自动推导
  只要make看到.o文件，它就会自动把.c文件加在依赖关系关系中，如果make找到一个main.o就会把main.h当成他的依赖文件，并且命令gcc -c main.c就会被推导出来，于是我们可以简化makefile文件
```c
objects = main.o kbd.o
edit: $(objects)
gcc -o edit $(objects)

main.o:  kbd.h

.PHONY: clean
clean:
rm edit $(objects)
```
- 这种方法就是make的==隐晦规则==，上面文件内容中，“.PHONY”表示，clean是个==伪目标文件==

## 5 另类风格的makefile
*如何简化上面的.o和.h文件*
objects= main.o kbd.o
edit: $(object)
gcc -o edit $(objects)