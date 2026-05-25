# PTAM-lab07
##Laboratory work VII
Данная лабораторная работа посвещена изучению систем управления пакетами на примере Hunter
```bash
$ export GITHUB_USERNAME=<имя_пользователя>
$ alias gsed=sed

$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate

$ git clone https://github.com/${GITHUB_USERNAME}/lab06 projects/lab07
$ cd projects/lab07
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab07
```
устанавливаем переменную, псевдоним и копируем репозиторий
```bash
$ mkdir -p cmake
$ wget https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake -O cmake/HunterGate.cmake
$ gsed -i '/cmake_minimum_required(VERSION 3.4)/a\

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/cpp-pm/hunter/archive/v0.23.251.tar.gz"
    SHA1 "5659b15dc0884d4b03dbd95710e6a1fa0fc3258d"
)
' CMakeLists.txt
```
Создаем папку скачиваем с менеджер пакетов для с++ и включаем его в cmake.
```
--2025-03-21 12:49:30--  https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.109.133, 185.199.110.133, 185.199.111.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.109.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 17227 (17K) [text/plain]
Saving to: ‘cmake/HunterGate.cmake’

cmake/HunterGate.cmak 100%[=======================>]  16.82K  --.-KB/s    in 0s      

2025-03-21 12:49:31 (66.0 MB/s) - ‘cmake/HunterGate.cmake’ saved [17227/17227]
```
```bash
$ git rm -rf third-party/gtest
$ gsed -i '/set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")/a\

hunter_add_package(GTest)
find_package(GTest CONFIG REQUIRED)
' CMakeLists.txt
$ gsed -i 's/add_subdirectory(third-party/gtest)//' CMakeLists.txt
$ gsed -i 's/gtest_main/GTest::main/' CMakeLists.txt
```
удаляем библиотеку и подключаем ее с помощью нашего пакетного менеджера, удаляем старые зависимости в CMakeList
```
rm 'third-party/gtest'
```
```bash
$ cmake -H. -B_builds -DBUILD_TESTS=ON
$ cmake --build _builds
$ cmake --build _builds --target test
$ ls -la $HOME/.hunter
```
```
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
[ 75%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[100%] Linking CXX executable check
[100%] Built target check

Running tests...
Test project /home/rama/Ramil-caramel/workspace/projects/lab07/_builds
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec

drwxrwxr-x  3 rama rama 4096 Mar 21 14:25 .
drwxr-x--- 20 rama rama 4096 Mar 21 14:26 ..
drwxrwxr-x  6 rama rama 4096 Mar 21 14:26 _Base

```
```bash
$ git clone https://github.com/cpp-pm/hunter $HOME/projects/hunter
$ export HUNTER_ROOT=$HOME/projects/hunter
$ rm -rf _builds
$ cmake -H. -B_builds -DBUILD_TESTS=ON
$ cmake --build _builds
$ cmake --build _builds --target test
```
копируем репозиторий и пересобираем проект
```bash
$ cat $HUNTER_ROOT/cmake/configs/default.cmake | grep GTest
$ cat $HUNTER_ROOT/cmake/projects/GTest/hunter.cmake
$ mkdir cmake/Hunter
$ cat > cmake/Hunter/config.cmake <<EOF
hunter_config(GTest VERSION 1.7.0-hunter-9)
EOF
# add LOCAL in HunterGate function
```
ищем все упоминания GTeat, смотрим конфигурацию, создаем папку записываем в конфиг
```
  hunter_default_version(GTest VERSION 1.7.0-hunter-6)
  hunter_default_version(GTest VERSION 1.15.2)

```
```bash
$ mkdir tools
$ git submodule add https://github.com/ruslo/polly tools/polly
$ tools/polly/bin/polly.py --test
$ tools/polly/bin/polly.py --install
$ tools/polly/bin/polly.py --toolchain clang-cxx14
```
устанавливаем инструмент, проверяем его работоспособность, проводим установку
```
  ctest [options]

-
Log saved: /home/rama/Ramil-caramel/workspace/projects/lab07/_logs/polly/default/log.txt
-
Generate: 0:00:02.964655s
Build: 0:00:01.951332s
Test: 0:00:00.019306s
-
Total: 0:00:04.935524s
-
SUCCESS

"-DCMAKE_TOOLCHAIN_FILE=/home/rama/Ramil-caramel/workspace/projects/lab07/tools/polly/default.cmake" "-DCMAKE_INSTALL_PREFIX=/home/rama/Ramil-caramel/workspace/projects/lab07/_install/default"
?                   ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Log saved: /home/rama/Ramil-caramel/workspace/projects/lab07/_logs/polly/clang-cxx14/log.txt
-
Generate: 0:00:24.950721s
Build: 0:00:01.736528s
-
Total: 0:00:26.687506s
-
SUCCESS


```
