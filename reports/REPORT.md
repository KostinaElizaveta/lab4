Laborotory work 4

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере CMake
$ open https://cmake.org/

Tasks
1. Создать публичный репозиторий с названием lab04 на сервисе GitHub https://github.com/KostinaElizaveta/lab04-1
2. Ознакомиться со ссылками учебного материала
3. Выполнить инструкцию учебного материала
4. Составить отчет и отправить ссылку личным сообщением в Slack

Tutorial

$ export GITHUB_USERNAME=a346560 #настройка имени пользователя
$ git clone https://github.com/${GITHUB_USERNAME}/lab03 lab04 # копирование репозитория lab3  в папку lab04
$ cd lab04 # переход в рабочий каталог ЛР
$ git remote remove origin # удаление сслыки на удаленный репозитарий
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab04 #установка ссылки на удаленный репозитарий
$ g++ -I./include -std=c++11 -c sources/print.cpp # сборки в объектный код
$ ls print.o # проверка наличия файла объектного кода
$ ar rvs print.a print.o # создание статической библиотеки
$ file print.a #проверка архива
$ g++ -I./include -std=c++11 -c examples/example1.cpp # сборка примера1
$ ls example1.o # проверка наличия файла объектного кода
$ g++ example1.o print.a -o example1 # сборка примера с статической библиотекой
$ ./example1 && echo # запуск приложения
hello
$ g++ -I./include -std=c++11 -c examples/example2.cpp # сборка примера2
$ ls example2.o # проверка объектного кода
$ g++ example2.o print.a -o example2 # сборка примера2 со статической библиотекой
$ ./example2 # запуск приложения
$ cat log.txt && echo # просмотр содержимого файла на экране
hello
$ rm -rf example1.o example2.o print.o # удаление объектного кода
$ rm -rf print.a  #удаление статюиблиотеки
$ rm -rf example1 example2 #удаление скомилированных примеров
$ rm -rf log.txt # удаление файла
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.0) # установление минимальной версии cmake
project(print) # наименование проекта print
EOF
$ cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11) # установка переменных
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
$ cat >> CMakeLists.txt <<EOF
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp) # подключение статической библиотеки
EOF
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include) # задание файлов подключения
EOF
$ cmake -H. -B_build # создание файла проекта
$ cmake --build _build # компиляция проекта
$ cat >> CMakeLists.txt <<EOF

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp) #добавление исходников примера1
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp) #добавление исходников примера2
EOF
$ cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print) # линковка примера1 с библиотекой print
target_link_libraries(example2 print) # линковка примера2 с библиотекой print
EOF
$ cmake --build _build # создание проекта
$ cmake --build _build --target print #создание библиотеки
$ cmake --build _build --target example1 # создание примера1
$ cmake --build _build --target example2 # создание примера2
$ ls -la _build/libprint.a # проверка
-rw-rw-r-- 1 user user 2302 июн 23 15:00 _build/libprint.a
$ _build/example1 && echo
hello
$ _build/example2
$ cat log.txt && echo
hello
$ git clone https://github.com/tp-labs/lab04 tmp # клонирование проекта задания в папку tmp
$ mv -f tmp/CMakeLists.txt .# перемещение файла
$ rm -rf tmp #удаление каталога tmp
$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)

$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install # компиляция с переменной install
$ cmake --build _build --target install #создание проекта с целью install
Install the project...
-- Install configuration: ""
-- Up-to-date: /home/user/vershinin/lab04/_install/lib/libprint.a
-- Up-to-date: /home/user/vershinin/lab04/_install/include
-- Up-to-date: /home/user/vershinin/lab04/_install/include/print.hpp
-- Up-to-date: /home/user/vershinin/lab04/_install/cmake/print-config.cmake
-- Installing: /home/user/vershinin/lab04/_install/cmake/print-config-noconfig.cmake
$ tree _install
_install
├── cmake
│   ├── print-config.cmake
│   └── print-config-noconfig.cmake
├── include
│   └── print.hpp
└── lib
    └── libprint.a

3 directories, 4 files
$ git add CMakeLists.txt # добавление в ветку файла
$ git commit -m"added CMakeLists.txt" #комментарий
$ git push origin master #отправка изменений на сервер

Report

$ cd ~/workspace/labs/
$ export LAB_NUMBER=04
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m"lab${LAB_NUMBER}"

Links

Autotools
CMake
