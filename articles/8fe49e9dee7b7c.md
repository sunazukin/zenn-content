---
title: "WSL2でUbuntu-22.04を追加インストールする"
emoji: "🧐"
type: "tech"
topics: ['WSL2','Ubuntu']
published: false
---

# はじめに

WSL2 で構築していた Ubuntu-20.04 の環境がゴチャゴチャしてきたので、新しく Ubuntu-22.04 の環境を追加しました。便利だったので、手順を残しておきます。

# WSL2でUbuntu-22.04を追加インストールする

## 前提

- OS
  - Windows10 64bit
- ターミナル
  - PowerShell
- WSL2 + Ubuntu-20.04
  - インストール済み

## 手順

### WSL2の最新化

WSL2が古くなっている可能性があるので、最新化します。今回は最新化されていました。

```powershell
> wsl --update
更新プログラムを確認しています。
Linux 用 Windows サブシステムの最新バージョンは既にインストールされています。
```

念のため、WSL2 の version を確認します。

```powershell
> wsl --version
WSL バージョン: 1.2.5.0
カーネル バージョン: 5.15.90.1
WSLg バージョン: 1.0.51
MSRDC バージョン: 1.2.3770
Direct3D バージョン: 1.608.2-61064218
DXCore バージョン: 10.0.25131.1002-220531-1700.rs-onecore-base2-hyp
Windows バージョン: 10.0.19045.3324
```

### Ubuntuのインストール

現在のインストール状況を確認します。

```powershell
> wsl -l -v
  NAME                   STATE           VERSION
* Ubuntu-20.04           Running         2
```

インストールできるディストリビューションに Ubuntu-22.04 が含まれていることを確認します。

```powershell
> wsl --list --online
インストールできる有効なディストリビューションの一覧を次に示します。
'wsl --install -d <Distro>' を使用してインストールします。
NAME                                   FRIENDLY NAME
(略)
Ubuntu-18.04                           Ubuntu 18.04 LTS
Ubuntu-20.04                           Ubuntu 20.04 LTS
Ubuntu-22.04                           Ubuntu 22.04 LTS
(略)
```

Ubuntu-22.04 をインストールし、ユーザ名とパスワードを入力したら Ubuntu に接続できます。

```powershell
> wsl --install -d Ubuntu-22.04
(略)
Enter new UNIX username: username
New password:password
Retype new password:password
passwd: password updated successfully
この操作を正しく終了しました。
Installation successful!
(略)
```

## 既定のディストリビューションの変更

`Ctrl + D` で切断し、現在のインストール状況を確認します。

```powershell
> wsl -l -v
  NAME                   STATE           VERSION
* Ubuntu-20.04           Running         2
  Ubuntu-22.04           Running         2
```

既定のディストリビューションを Ubuntu-22.04 に変更します。

```powershell
> wsl --set-default Ubuntu-22.04
この操作を正しく終了しました。
```

「*」が Ubuntu-22.04 と同じ行に表示されていれば、既定のディストリビューションの変更成功です。

```powershell
> wsl -l -v
  NAME                   STATE           VERSION
* Ubuntu-22.04           Running         2
  Ubuntu-20.04           Running         2
```

# おわりに

VirtualBox などで VM 構築していた頃とは比べ物にならないほど、さっと環境構築できるようになって嬉しい限りですね。

# 参考

- [WSL2 のインストール，WSL2 上への Ubuntu のインストールと利用（Windows 11 対応の記事）（Windows 上）](https://www.kkaneko.jp/tools/wsl/wsl2.html)
- [Basic commands for WSL](https://learn.microsoft.com/en-us/windows/wsl/basic-commands)
