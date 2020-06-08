## Laboratory work VII


<a href="https://yandex.ru/efir/?stream_id=vDHLoKtKoa3o"><img src="https://raw.githubusercontent.com/tp-labs/lab07/master/preview.png" width="640"/></a>

Данная лабораторная работа посвещена изучению систем управления пакетами на примере **Hunter**

```sh
$ open https://github.com/ruslo/hunter
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab07** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial
Создание переменных среды и установка их значений, а также связывание команд с их "новыми" названиями.
```sh
$ export GITHUB_USERNAME=DaniilRyb
$ alias gsed=sed
```
Начало работы в каталоге workspace
```sh

$ cd ${GITHUB_USERNAME}/workspace
$ pushd . 
$ source scripts/activate
```

```sh

$ git clone https://github.com/${GITHUB_USERNAME}/lab06 projects/lab07
$ cd projects/lab07
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab07
```

```sh
$ mkdir -p cmake 
# Скачивание данных из файла в удаленном репозитории и их запись в файл HunterGate.cmake
$ wget https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake -O cmake/HunterGate.cmake
--2020-06-05 19:29:51--  https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 151.101.244.133
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|151.101.244.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 17070 (17K) [text/plain]
Saving to: 'cmake/HunterGate.cmake'

cmake/HunterGate.cmake                                   100%[===============================================================================================================================>]  16,67K  --.-KB/s    in 0,001s

2020-06-05 19:29:52 (11,0 MB/s) - 'cmake/HunterGate.cmake' saved [17070/17070]


$ gsed -i "" '/cmake_minimum_required(VERSION 3.4)/a\

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https:\//github.com/cpp-pm/hunter/archive/v0.23.251.tar.gz"
    SHA1 "5659b15dc0884d4b03dbd95710e6a1fa0fc3258d"
)
' CMakeLists.txt
```
Т
```sh

$ git rm -rf third-party/gtest
rm 'third-party/gtest'

$ gsed -i "" '/set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")/a\

hunter_add_package(GTest)
find_package(GTest CONFIG REQUIRED)
' CMakeLists.txt

$ gsed -i "" 's/add_subdirectory(third-party/gtest)//' CMakeLists.txt

$ gsed -i "" 's/gtest_main/GTest::gtest_main/' CMakeLists.txt
```
Сборка прокта при помощи **Hunter**.
```sh

$ cmake -H. -B_builds -DBUILD_TESTS=ON
$ cmake --build

Total Test time (real) =   0.02 sec

$ ls -la $HOME/.hunter
```
Установка **Hunter** в систему
```sh
$ git clone https://github.com/cpp-pm/hunter $HOME/projects/hunter
$ export HUNTER_ROOT=$HOME/projects/hunter
$ rm -rf _builds
$ cmake -H. -B_builds -DBUILD_TESTS=ON
$ cmake --build _builds
Scanning dependencies of target print
...
[100%] Linking CXX executable check
[100%] Built target check
$ cmake --build _builds --target test
Running tests...
T
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
```

```sh

$ cat $HUNTER_ROOT/cmake/configs/default.cmake | grep GTest
hunter_default_version(GTest VERSION 1.7.0-hunter-6)
hunter_default_version(GTest VERSION 1.10.0)

$ cat $HUNTER_ROOT/cmake/projects/GTest/hunter.cmake
$ mkdir cmake/Hunter

$ cat > cmake/Hunter/config.cmake <<EOF
hunter_config(GTest VERSION 1.7.0-hunter-9)
EOF

```

```sh
$ mkdir demo
#
$ cat > demo/main.cpp <<EOF
#include <print.hpp>

#include <cstdlib>

int main(int argc, char* argv[])
{
  const char* log_path = std::getenv("LOG_PATH");
  if (log_path == nullptr)
  {
    std::cerr << "undefined environment variable: LOG_PATH" << std::endl;
    return 1;
  }
  std::string text;
  while (std::cin >> text)
  {
    std::ofstream out{log_path, std::ios_base::app};
    print(text, out);
    out << std::endl;
  }
}
EOF

$ gsed -i '/endif()/a\

add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/demo/main.cpp)
target_link_libraries(demo print)
install(TARGETS demo RUNTIME DESTINATION bin)
' CMakeLists.txt
```
Добавление подмодуля **polly**, который содержит инструкции для сборки проектов с установленным **Hunter**.
```sh
$ mkdir tools
$ git submodule add https://github.com/ruslo/polly tools/polly
Cloning into 'C:/Users/Daniil-PC/Daniil_Rip/Daniil_Rip/tools/polly'...
remote: Enumerating objects: 29, done.
remote: Counting objects: 100% (29/29), done.
remote: Compressing objects: 100% (19/19), done.
remote: Total 6423 (delta 10), reused 24 (delta 10), pack-reused 6394
Receiving objects: 100% (6423/6423), 1.64 MiB | 3.01 MiB/s, done.
Resolving deltas: 100% (4418/4418), done.
$ tools/polly/bin/polly.py --test
Python version: 3.7

$ tools/polly/bin/polly.py --install

$ tools/polly/bin/polly.py --toolchain clang-cxx14
```
## Report

```sh
$ popd
$ export LAB_NUMBER=07
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Links

- [Create Hunter package](https://docs.hunter.sh/en/latest/creating-new/create.html)
- [Custom Hunter config](https://github.com/ruslo/hunter/wiki/example.custom.config.id)
- [Polly](https://github.com/ruslo/polly)

```
Copyright (c) 2015-2020 The ISC Authors
```
