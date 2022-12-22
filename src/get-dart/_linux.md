If you're using Debian/Ubuntu on AMD64 (64-bit Intel), you can choose one of the
following options, both of which can update the SDK automatically when new
versions are released.

如果您使用 64 位的 Debian/Ubuntu 系統，請選擇下面任意一種方式安裝，
在新版本釋出之後，它們都可以自動更新。

* [使用 apt-get 方式安裝](#install-using-apt-get)
* [使用 Debian 安裝包](#install-a-debian-package)

#### Install using apt-get

#### 使用 apt-get 方式安裝

Perform the following **one-time setup**:

請使用如下的方式（只需設定一次）：

```terminal
$ sudo apt-get update
$ sudo apt-get install apt-transport-https
$ wget -qO- https://dl.google.com/linux/linux_signing_key.pub | sudo gpg --dearmor -o /usr/share/keyrings/dart.gpg
$ echo 'deb [signed-by=/usr/share/keyrings/dart.gpg arch=amd64] https://storage.googleapis.com/download.dartlang.org/linux/debian stable main' | sudo tee /etc/apt/sources.list.d/dart_stable.list
```

Developers from China: please considering replace to
the mirror site that you trusted.

中國的開發者，請將上面最後一行命令列的
storage.googleapis.com 替換為
storage.flutter-io.cn，完整命令如下：

```terminal
$ echo 'deb [signed-by=/usr/share/keyrings/dart.gpg arch=amd64] https://storage.flutter-io.cn/download.dartlang.org/linux/debian stable main' | sudo tee /etc/apt/sources.list.d/dart_stable.
```

Then install the Dart SDK:

然後安裝 Dart SDK:

```terminal
$ sudo apt-get update
$ sudo apt-get install dart
```

#### Install a Debian package

#### 使用 Debian 安裝包

Alternatively, download Dart SDK [as a Debian package](#){:.debian-link-stable}
in the `.deb` package format.

透過 [`.deb` 軟體包](#){:.debian-link-stable} 下載 Dart SDK。

#### Modify PATH for access to all Dart binaries

#### 在 PATH 環境變數里加入所有 Dart 二進位制檔案的存取

After installing the SDK, **add its `bin` directory to your `PATH`**. For example,
use the following command to change `PATH` in your active terminal session:

安裝 SDK 之後需要把 SDK 的 `bin` 目錄加入你環境變數的 `PATH` 中去。
比如，透過下面的命令可以在現有命令列視窗中加入 `PATH` 環境變數：

```terminal
$ export PATH="$PATH:/usr/lib/dart/bin"
```

To change the PATH for future terminal sessions, use a command like this:

為未來的命令列視窗執行永久的 `PATH` 環境變數設定，
可以使用下面的命令：

```terminal
$ echo 'export PATH="$PATH:/usr/lib/dart/bin"' >> ~/.profile
```
