## Laboratory work VI

Данная лабораторная работа посвещена изучению средств пакетирования на примере **CPack**

```ShellSession
$ open https://cmake.org/Wiki/CMake:CPackPackageGenerators
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием lab06 на сервисе GitHub
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в Slack

## Tutorial

Настройка окружения

```ShellSession
[danila@Dellic ~]$ export GITHUB_USERNAME=FalaleevDanila 
[danila@Dellic ~]$ export GITHUB_EMAIL=falaleevdanila@gmail.ru  
[danila@Dellic ~]$ alias edit=nano     
[danila@Dellic ~]$ alias gsed=sed  
```

Подготовка окружения

```ShellSession
[danila@Dellic ~]$ cd ${GITHUB_USERNAME}/workspace           # Перемещение в указанную папку
[danila@Dellic workspace]$  pushd .                          # Сохранение указанной папки
~/FalaleevDanila/workspace ~/FalaleevDanila/workspace 
[danila@Dellic workspace]$ source scripts/activate           # Выполнение скрипта активации
```

Скачивание файлов из прошлой лабораторной

```ShellSession
danila@Dellic workspace]$ git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab06      # Копирование файлов из удаленного репозитория
Клонирование в «projects/lab06»… 
remote: Enumerating objects: 49, done. 
remote: Counting objects: 100% (49/49), done. 
remote: Compressing objects: 100% (29/29), done. 
remote: Total 49 (delta 13), reused 45 (delta 12), pack-reused 0 
Распаковка объектов: 100% (49/49), готово. 
[danila@Dellic workspace]$ cd projects/lab06                                                        # Переход в папку с проектом
[danila@Dellic lab06]$ git remote remove origin                                                     # Удаление ссылки на старый удаленный репозиторий
[danila@Dellic lab06]$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06            # Добавление ссылки на новый удаленный репозиторий
```


Редактирование CMakeLists.txt. Добавление переменных с версией

```ShellSession
[danila@Dellic lab06]$ gsed -i '/project(print)/a\  # Дописывание в указанный файл указанной строки
> set(PRINT_VERSION_STRING "v\${PRINT_VERSION}") 
> ' CMakeLists.txt 
[danila@Dellic lab06]$ gsed -i '/project(print)/a\  # Дописывание в указанный файл указанной строки
> set(PRINT_VERSION\ 
>   \${PRINT_VERSION_MAJOR}.\${PRINT_VERSION_MINOR}.\${PRINT_VERSION_PATCH}.\${PRINT_VERSION_TWEAK}) 
> ' CMakeLists.txt 
[danila@Dellic lab06]$ gsed -i '/project(print)/a\  # Дописывание в указанный файл указанной строки
> set(PRINT_VERSION_TWEAK 0) 
> ' CMakeLists.txt 
[danila@Dellic lab06]$ gsed -i '/project(print)/a\  # Дописывание в указанный файл указанной строки
> set(PRINT_VERSION_PATCH 0) 
> ' CMakeLists.txt 
[danila@Dellic lab06]$ gsed -i '/project(print)/a\  # Дописывание в указанный файл указанной строки
set(PRINT_VERSION_MINOR 1)
> set(PRINT_VERSION_MINOR 1) 
> ' CMakeLists.txt 
[danila@Dellic lab06]$ gsed -i '/project(print)/a\  # Дописывание в указанный файл указанной строки
set(PRINT_VERSION_MAJOR 0)
> set(PRINT_VERSION_MAJOR 0) 
> ' CMakeLists.txt 
[danila@Dellic lab06]$ git diff                     # Просмотр отличий локальной версии от последнего коммита
diff --git a/CMakeLists.txt b/CMakeLists.txt 
index 176b7ba..eb75e94 100644 
--- a/CMakeLists.txt 
+++ b/CMakeLists.txt 
@@ -7,6 +7,13 @@ option(BUILD_EXAMPLES "Build examples" OFF) 
option(BUILD_TESTS "Build tests" OFF) 
 
project(print) 
+set(PRINT_VERSION_MAJOR 0) 
+set(PRINT_VERSION_MINOR 1) 
+set(PRINT_VERSION_PATCH 0) 
+set(PRINT_VERSION_TWEAK 0) 
+set(PRINT_VERSION 
+  ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK}) 
+set(PRINT_VERSION_STRING "v${PRINT_VERSION}") 
 
add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp) 
 
```


Создание файлов, необходимых для создания RPM пакета

```ShellSession
[danila@Dellic lab06]$ touch DESCRIPTION && edit DESCRIPTION           # Создание и редактирование DESCRIPTION
[danila@Dellic lab06]$ touch ChangeLog.md                              # Создание ChangeLog.md
[danila@Dellic lab06]$ export DATE="`LANG=en_US date +'%a %b %d %Y'`"  # Создание переменной DATE
[danila@Dellic lab06]$ cat > ChangeLog.md <<EOF                        # Запись в файл ChangeLog.md
> * ${DATE} ${GITHUB_USERNAME} <${GITHUB_EMAIL}> 0.1.0.0 
> - Initial RPM release 
> EOF 
```

Создание отдельного CMake файла с конфигурацией

```ShellSession
[danila@Dellic lab06]$ cat > CPackConfig.cmake <<EOF                   # Запись в указанный файл указанной строки
> include(InstallRequiredSystemLibraries) 
> EOF 
```

Дописывание в файл конфигурации переменных (почта, версия, описание (краткое, полное))

```ShellSession
[danila@Dellic lab06]$ cat >> CPackConfig.cmake <<EOF                     # Дописывание в указанный файл указанных строк
> set(CPACK_PACKAGE_CONTACT ${GITHUB_EMAIL}) 
> set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR}) 
> set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR}) 
> set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH}) 
> set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK}) 
> set(CPACK_PACKAGE_VERSION \${PRINT_VERSION}) 
> set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION) 
> set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing") 
> EOF 
```
Дополнение конфигурации переменными с путями к файлам лицензии и README

```ShellSession
[danila@Dellic lab06]$ cat >> CPackConfig.cmake <<EOF                     # Дописывание в указанный файл указанных строк
>  
> set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE) 
> set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md) 
> EOF 
```
Добавление в конфигурацию информации о RPM

```ShellSession
[danila@Dellic lab06]$ cat >> CPackConfig.cmake <<EOF                     # Дописывание в указанный файл указанных строк
>  
> set(CPACK_RPM_PACKAGE_NAME "print-devel") 
> set(CPACK_RPM_PACKAGE_LICENSE "MIT") 
> set(CPACK_RPM_PACKAGE_GROUP "print") 
> set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md) 
> set(CPACK_RPM_PACKAGE_RELEASE 1) 
> EOF 
```

Добавление в конфигурацию информации о DEBIAN

```ShellSession
[danila@Dellic lab06]$ cat >> CPackConfig.cmake <<EOF                     # Дописывание в указанный файл указанных строк

>  
> set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev") 
> set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0") 
> set(CPACK_DEBIAN_PACKAGE_RELEASE 1) 
> EOF 
```

Подключение CPack в CMake конфиг

```ShellSession
[danila@Dellic lab06]$ cat >> CPackConfig.cmake <<EOF              # Дописывание в указанный файл указанной строки

>  
> include(CPack) 
> EOF 
```

Подключение дополнительной конфигурации

```ShellSession
[danila@Dellic lab06]$ cat >> CMakeLists.txt <<EOF                 # Дописывание в указанный файл указанной строки
>  
> include(CPackConfig.cmake) 
> EOF 
```

Изменение README.md

```ShellSession
[danila@Dellic lab06]$ gsed -i 's/lab05/lab06/g' README.md 
```

Фиксация и отправка изменений

```ShellSession
[danila@Dellic lab06]$ git add .                             # Фиксация изменений
[danila@Dellic lab06]$ git commit -m"added cpack config"     # Коммит зафиксированных изменений
[master f4ea183] added cpack config 
5 files changed, 80 insertions(+), 45 deletions(-) 
create mode 100644 CPackConfig.cmake 
create mode 100644 ChangeLog.md 
create mode 100644 DESCRIPTION 
[danila@Dellic lab06]$ git tag v0.1.0.0                      # Установка тэга
[danila@Dellic lab06]$ git push origin master --tags         # Отправка изменений
Username for 'https://github.com': FalaleevDanila 
Password for 'https://FalaleevDanila@github.com':  
Перечисление объектов: 55, готово. 
Подсчет объектов: 100% (55/55), готово. 
При сжатии изменений используется до 12 потоков 
Сжатие объектов: 100% (47/47), готово. 
Запись объектов: 100% (55/55), 19.23 KiB | 1.75 MiB/s, готово. 
Всего 55 (изменения 15), повторно использовано 0 (изменения 0) 
remote: Resolving deltas: 100% (15/15), done. 
To https://github.com/FalaleevDanila/lab06 
* [new branch]      master -> master 
* [new tag]         v0.1.0.0 -> v0.1.0.0 
```

Активация репозитория в Travis CI

```ShellSession
[danila@Dellic lab06]$  travis login --auto # Авторизация в Travis CI
We need your GitHub login to identify you. 
This information will not be sent to Travis CI, only to api.github.com. 
The password will not be displayed. 

Try running with --github-token or --auto if you don't want to enter your password anyway. 

Username: FalaleevDanila 
Password for FalaleevDanila: 
Successfully logged in as FalaleevDanila! 
$ travis enable                                 # Включение обработки в Travis CI
```


Сборка и компиляция через CMake. Создание пакета через CPack

```ShellSession
[danila@Dellic lab06]$ cmake -H. -B_build                # Сборка (CMakeLists.txt берется из текущей директории, сборка в директорию _build)
-- Configuring done 
-- Generating done 
-- Build files have been written to: /home/danila/FalaleevDanila/workspace/projects/lab06/_build 
[danila@Dellic lab06]$ cmake --build _build              # Компиляция в директории _build
[100%] Built target print 
[danila@Dellic lab06]$ cd _build                         # Переход в указанную директорию
[danila@Dellic _build]$ cpack -G "TGZ"                   # Упаковка с использованием CPack
CPack: Create package using TGZ 
CPack: Install projects 
CPack: - Run preinstall target for: print 
CPack: - Install project: print 
CPack: Create package 
CPack: - package: /home/danila/FalaleevDanila/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz ge
nerated. 
[danila@Dellic _build]$ cd ..                            # Переход на уровень выше
```

Сборка и создание пакета через CMake

```ShellSession
[danila@Dellic lab06]$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"          # Сборка (CMakeLists.txt берется из текущей директории, сборка в директорию _build). Установка 
-- Configuring done
-- Generating done
-- Build files have been written to: /home/danila/FalaleevDanila/workspace/projects/lab06/_build
[danila@Dellic lab06]$ cmake --build _build --target package               # Компиляция цели package
[100%] Built target print
[100%] Built target print
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /home/danila/FalaleevDanila/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.

```

Перемещение собранного пакета

```ShellSession
[danila@Dellic lab06]$ mkdir artifacts                            # Создание указанной папки
mkdir: невозможно создать каталог «artifacts»: Файл существует  
[danila@Dellic lab06]$ mv _build/*.tar.gz artifacts               # Перемещение собранного пакета
[danila@Dellic lab06]$ tree artifacts                             # Отображения дерева указанной папки
artifacts
└── print-0.1.0.0-Linux.tar.gz

```


## Report

```ShellSession
$ popd
$ export LAB_NUMBER=06
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

### Задание
После того, как вы настроили взаимодействие с системой непрерывной интеграции,
обеспечив автоматическую сборку и тестирование ваших изменений, стоит задуматься
о создание пакетов для измениний, которые помечаются тэгами (см. вкладку releases).
Пакет должен содержать приложение solver из предыдущего задания Таким образом, каждый новый релиз будет состоять из следующих компонентов:

  архивы с файлами исходного кода (.tar.gz, .zip)
  пакеты с бинарным файлом solver (.deb, .rpm, .msi, .dmg)

В качестве подсказки:

```ShellSession
$ cat .travis.yml
os: osx
script:
...
- cpack -G DragNDrop # dmg

$ cat .travis.yml
os: linux
script:
...
- cpack -G DEB # deb

$ cat .travis.yml
os: linux
addons:
  apt:
    packages:
    - rpm
script:
...
- cpack -G RPM # rpm

$ cat appveyor.yml
platform:
- x86
- x64
build_script:
...
- cpack -G WIX # msi
```

Для этого нужно добавить ветвление в конфигурационные файлы для CI со следующей логикой:
если commit помечен тэгом, то необходимо собрать пакеты (DEB, RPM, WIX, DragNDrop, ...) 
и разместить их на сервисе GitHub. (см. пример для Travi CI)

## Links

- DMG
- DEB
- RPM
- NSIS


```
Copyright (c) 2015-2019 The ISC Authors
```
