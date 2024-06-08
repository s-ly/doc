# Загрузка данных из json

Структура с данными `TextData.cs`

```csharp
[System.Serializable]
public class TextData
{
  public string Name;
  public string Info;
}
```

Сами данные `texts.json`

```json
{
  "Name": "Sergey",
  "Info": "Develop"
}
```

Скрипт, загрузки данных `LoadText.cs`

```csharp
using System.IO;
using UnityEngine;
using UnityEngine.UI;

public class LoadText : MonoBehaviour
{
  public Text TestText_1;
  public Text TestText_2;
  TextData data;
  void Start()
  {
    LoadJson();
    LoadDataText();
  }

  public void LoadJson()
  {
    string json = File.ReadAllText(Application.dataPath + "/texts.json");
    data = JsonUtility.FromJson<TextData>(json);
  }

  void LoadDataText()
  {
    TestText_1.text = data.Name;
    TestText_2.text = data.Info;
  }
}

```
