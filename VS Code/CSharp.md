# Стиль C# в VS Code

## Не переносить открывающую фигурную скобку.

Прямо в корне создаём файл: **.editorconfig**
В файл вставляем:

```
C# files
[*.cs]
csharp_new_line_before_open_brace = none
```

Как это выглядит в проекте:

```
## # C# files

[*.cs]

csharp_new_line_before_open_brace = none

# indent_style = space

# indent_size = 2

[*.cs]
```

## Для изменения отступов

В начале открываем настройки ctrl + ,

Вводим **tabsize**
Потом ставим 2 тотом снимаем галочку **Detect Indentation**

Потом возвращаем галочку **Detect Indentation**

## 

## Как выглядит settings.json в проекте «Космическая прогулка»

```json
{

    "bitoAI.codeCompletion.enableAutoCompletion": true,

    "bitoAI.codeCompletion.enableCommentToCode": true,

    "editor.inlineSuggest.showToolbar": "onHover",

    "workbench.activityBar.location": "top",

    "editor.unicodeHighlight.ambiguousCharacters": false,

    "cmake.configureOnOpen": true,

    "hexeditor.columnWidth": 16,

    "hexeditor.showDecodedText": false,

    "hexeditor.defaultEndianness": "little",

    "hexeditor.inspectorType": "aside",

    "window.confirmSaveUntitledWorkspace": false,

    "[csharp]": {

        "editor.defaultFormatter": "ms-dotnettools.csharp"

    },

    "editor.indentSize": "tabSize",

    "editor.tabSize": 2,

    "editor.detectIndentation": false,

    "dotnet.codeLens.enableReferencesCodeLens": false,

    "editor.minimap.enabled": false

}
```

## Отключить подсветку ссылок

"dotnet.codeLens.enableReferencesCodeLens": false
