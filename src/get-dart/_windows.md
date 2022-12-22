You can install the Dart SDK using [Chocolatey.][Chocolatey]

{{site.alert.important}}

  These commands require administrator rights.
  Here's one way to open a Command Prompt window
  that has admin rights:

  這些命令需要以管理員身份執行。
  以下是一種以管理員身份執行命令列的方式：

  1. Press <kbd>Windows+R</kbd> to open the **Run** window.

     按下 <kbd>Windows+R</kbd> 開啟 **執行** 視窗。

  2. Type `cmd` into the box.

     在輸入框中輸入 `cmd`。

  3. Press <kbd>Ctrl+Shift+Enter</kbd>.

     按下 <kbd>Ctrl+Shift+Enter</kbd>。

{{site.alert.end}}

To install the Dart SDK:

安裝 Dart SDK：

```terminal
C:\> choco install dart-sdk
```

To upgrade the Dart SDK:

升級 Dart SDK：

```terminal
C:\> choco upgrade dart-sdk
```

By default, the SDK is installed at `C:\tools\dart-sdk`.
You can change that location by setting
the [`ChocolateyToolsLocation`][] environment variable
to your chosen installation directory.

SDK 預設會安裝在 `C:\tools\dart-sdk`。
你可以更改 [`ChocolateyToolsLocation`][] 環境變數來選擇安裝目錄。

If you can't use the Dart SDK executables,
add the SDK location to your PATH:

如果安裝後你無法使用 Dart SDK 的可執行檔案，
請將 SDK 的路徑新增到 PATH：

1. In the Windows search box, type `env`.

   開啟 Windows 搜尋，輸入 `env`。

2. Click **Edit the system environment variables**.

   點選 **編輯系統環境變數**。

3. Click **Environment Variables...**.

   點選 **環境變數(N)...**。

4. In the user variable section, select **Path** and click **Edit...**.

   在使用者變數部分，選擇 **Path** 並點選 **編輯(E)...**。

5. Click **New**, and enter the path to the `dart-sdk` directory.

   點選 **新建(N)**，輸入 `dart-sdk` 的路徑。

6. In each window that you just opened,
   click **Apply** or **OK** to dismiss it and apply the path change.

   在每個開啟的視窗點選 **應用(A)** 或 **確定**，關閉彈窗並應用路徑修改。

[Chocolatey]: https://chocolatey.org
[`ChocolateyToolsLocation`]: https://stackoverflow.com/questions/19752533/how-do-i-set-chocolatey-to-install-applications-onto-another-drive/68314437#68314437
