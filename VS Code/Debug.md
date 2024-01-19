# Заметки по дебагеру VS Code

## Аргументы
Если при запуске нужны аргументы, то они указываются в "args" файле в .vscode/tasks.json.
Например что бы добавить -lm:
```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: gcc сборка активного файла",
            "command": "/usr/bin/gcc",
            "args": [
                "-fdiagnostics-color=always",
                "-g",
                "${file}",
                "-o",
                "${fileDirname}/${fileBasenameNoExtension}",
                "-lm"
            ],
            "options": {
                "cwd": "${fileDirname}"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "Задача создана отладчиком."
        }
    ],
    "version": "2.0.0"
}
```