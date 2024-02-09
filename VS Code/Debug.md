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

## Описание кнопок
В Visual Studio Code (VSCode) отладчик предоставляет несколько кнопок для управления процессом отладки. Вот что означают эти кнопки:

1. **Continue (F5)**: Продолжает выполнение программы до следующей точки останова (breakpoint) или до конца программы, если точек останова нет. Если программа уже находится на точке останова, нажатие этой кнопки заставит программу продолжить выполнение до следующей точки останова.

2. **Step Over (F10)**: Выполняет следующую строку кода, но не заходит внутрь функций. Это означает, что если следующая строка содержит вызов функции, отладчик выполнит всю функцию как один шаг, не останавливаясь на каждой строке внутри функции.

3. **Step Into (F11)**: Выполняет следующую строку кода и, если эта строка содержит вызов функции, отладчик войдет в эту функцию и остановится на первой строке внутри нее. Это позволяет вам исследовать работу функций более детально.

4. **Step Out (Shift+F11)**: Если вы находитесь внутри функции, эта кнопка заставит отладчик выполнить оставшуюся часть функции и остановиться на строке, следующей за строкой, из которой была вызвана функция. Это полезно, когда вы закончили изучение работы функции и хотите вернуться к более высокому уровню абстракции.

5. **Restart (Ctrl+Shift+F5)**: Перезапускает текущую отладочную сессию. Это означает, что текущий запущенный экземпляр вашей программы будет остановлен, и отладчик начнет новую сессию отладки с начала программы. Это полезно, если вы хотите повторно протестировать программу с самого начала, не выходя из режима отладки.

6. **Stop (Shift+F5)**: Останавливает текущую отладочную сессию и завершает работу отладчика. Это приведет к остановке выполнения программы, и вы вернетесь к обычному редактированию кода. Используйте эту кнопку, когда вы закончили отладку и хотите прекратить выполнение программы.

Эти кнопки позволяют управлять жизненным циклом отладочной сессии, давая вам возможность быстро перезапустить тестирование или полностью остановить отладку и вернуться к разработке.