見つからなかったことにしちゃえ!

```csharp
async void SearchButton_Click(object sendor, EventArgs e)
{
    try
    {
        // API 呼び出し
        var resonse = await Client.GetAsync($"http://api.zipaddresss.net/?...");

        // ... 省略

        // 見つからなかった時のテストのために一時的に 404 に
        // var code = json.code;
        var code = "404";
        if (code == "200")
        {
            // ... 以下省略
}
```
<!-- .element: style="font-size: 50%;" -->
