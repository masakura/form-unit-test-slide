そうだ!

最初に表示されるようにしちゃえ!

```csharp
public void Main()
{
    Application.EnableVisualStyles();
    Application.SetCompatibleTextRenderingDefault(false);

    // 郵便番号検索画面テストのために
    // 一時的にそっちが立ち上がるように修正しています
    // Application.Run(new LoginForm());
    Application.Run(new AddressSearchForm());
}
```
