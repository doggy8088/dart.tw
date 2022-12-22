---
title: dart pub publish
description: Use dart pub publish to publish your Dart package to the pub.dev site.
description: 使用 dart pub publish 命令將你的 Dart Package 釋出到 pub.dev 網站。
---

_Publish_ is one of the commands of the [pub tool](/tools/pub/cmd).

_Publish_ 命令是 [Pub 工具](/tools/pub/cmd) 中的一個命令。

```nocode
$ dart pub publish [options]
```

This command publishes your package on the
[pub.dev site]({{site.pub}}) for anyone to download and depend
on. For information on how to prepare your package for publishing,
and what files you should include or exclude,
see [Publishing packages](/tools/pub/publishing).

該命令用於將你的 Package 釋出到 [pub.dev 網站]({{site.pub}}) 以供其他人下載和依賴。
有關如果將你的 Package 釋出以及哪些檔案可以釋出哪些不應該釋出的資訊請查閱
[釋出 Package](/tools/pub/publishing)。

## Options

## 選項

For options that apply to all pub commands, see
[Global options](/tools/pub/cmd#global-options).

你可以查閱 [全域選項](/tools/pub/cmd#global-options) 獲取 Pub 命令所支援的命令選項。

### `--dry-run` or `-n`

### `--dry-run` 選項或 `-n` 選項

With this, pub goes through the validation process but does not actually upload
the package. This is useful if you want to see if your package meets all of the
publishing requirements before you're ready to actually go public.

該選項可以讓你執行上傳 Package 的整個流程但不會真正地上傳任何檔案到 pub.dev 網站。此操作可以讓你在真正上傳到 pub.dev 網站前檢查你的上傳等相關配置是否有誤。

### `--force` or `-f`

### `--force` 選項或 `-f` 選項

With this, pub does not ask for confirmation before publishing. Normally, it
shows you the package contents and asks for you to confirm the upload.

該選項讓 Pub 在上傳時不再向你進行確認。正常情況下，它會在你上傳時向你顯示 Package 的內容以及向你進行確認。

If your package has errors, pub doesn't upload it and exits with an error.
In the event of warnings, your package *is* uploaded.
To ensure that your package has no warnings before uploading,
either don't use `--force`, or use `--dry-run` first.

如果 Package 存在錯誤，Pub 則會退出且不繼續進行上傳。
如果出現的是警告，則 Package 會依舊被上傳。
若你想確保你的 Package 在上傳前沒有警告，
請確保不要使用 `--force` 和 `--dry-run` 選項。

{{site.alert.info}}

  *Problems?*
  See [Troubleshooting Pub](/tools/pub/troubleshoot).

  **有疑問？** 請查閱 [Pub 疑難協助](/tools/pub/troubleshoot)。

{{site.alert.end}}
