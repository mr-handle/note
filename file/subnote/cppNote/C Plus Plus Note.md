# C/C++ Note

C++是对C的扩展，完全兼容C，但是C也在不断发展，严格上来说某个版本的C++兼容某个版本的C

就像Kotlin兼容某个版本的Java一样

- 安装C/C++编译器

```sh
# ArchLinux安装base-devel就行了，它包含了C编译器gcc、C++编译器g++和CMake等
sudo pacman -S base-devel

# 查看C编译器版本
gcc --version

# 查看C++编译器版本
g++ --version

# 编译C源文件，生成可执行文件（可以不指定文件扩展名），如果不指定-o，则生成的是a.out
gcc source.c -o source

# 编译C++源文件，生成可执行文件（可以不指定文件扩展名），如果不指定-o，则生成的是a.out
g++ source.cpp -o source
```
