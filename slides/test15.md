例外発生させちゃえ!

```csharp
async void SearchButton_Click(object sendor, EventArgs e)
{
    try
    {
        // 例外のテストのために一時的に入れています
        throw new InvalidOperationException("何かしらのエラーが発生しました");

        // API 呼び出し
        var resonse = await Client.GetAsync($"http://api.zipaddresss.net/?...");
        // ... 以下省略
    }
    catch (Exception ex)
    {
        MessageBox.Show(ex.Message, "エラー",
            MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}
```
<!-- .element: style="font-size: 50%;" -->
