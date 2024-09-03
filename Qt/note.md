# Установка:

```bash
sudo apt update
sudo apt install build-essential libgl1-mesa-dev

sudo apt install qtbase5-dev qtchooser qt5-qmake qtbase5-dev-tools qttools5-dev-tools qtdeclarative5-dev qtcreator

qmake -v
```

# Hello Worl

![Hello.png](_img_note/Hello.png)

main.cpp

```cpp
#include <QtWidgets>

int main(int argc, char **argv)
{
    QApplication app(argc, argv);
    QLabel lbl("Hello, World!");
    lbl.show();
    return app.exec();
}
```

.vscode/c_cpp_properties.json

```json
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**",
                "/usr/include/x86_64-linux-gnu/qt5",
                "/usr/include/x86_64-linux-gnu/qt5/QtCore",
                "/usr/include/x86_64-linux-gnu/qt5/QtGui",
                "/usr/include/x86_64-linux-gnu/qt5/QtWidgets"
            ],
            "defines": [],
            "compilerPath": "/usr/bin/gcc",
            "cStandard": "c17",
            "cppStandard": "c++17",
            "intelliSenseMode": "gcc-x64"
        }
    ],
    "version": 4
}
```

Создание файла проекта:

`qmake -project`

В файле проекта *.pro вставляю строку:

`QT += core gui widgets`

 Генерация Makefile:
 `qmake`

Компиляция:

`make`
