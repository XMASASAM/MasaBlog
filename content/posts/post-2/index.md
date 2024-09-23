+++
title = 'WinUIをexeファイルから実行できるように設定する'
date = 2024-09-24T00:34:04+09:00
draft = false
tags = ['WinUI']
+++
WinUIはデフォルトの設定でビルドを行ってもexeファイルから直接起動できません。そこで、いろいろな設定を加えます。
<!--more-->

# 環境
- Windows11
- VisualStudio2022

# やること
1. 「空のアプリ、パッケージ化（デスクトップのWinUI 3）」を選択してプロジェクトを作る。
1. プロジェクトのxamlファイルを開き、下記の設定を加える。
```html{name="<プロジェクト名>.csproj"}
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        ...
        <!--以下を追加 -->
	    <WindowsSdkPackageVersion>10.0.19041.38</WindowsSdkPackageVersion>
	    <WindowsAppSDKSelfContained>true</WindowsAppSDKSelfContained>
	    <WindowsPackageType>None</WindowsPackageType>
        <!--以上を追加 -->
    </PropertyGroup>
    ...
</Project>
```

 >**tip** :
 >	\<WindowsSdkPackageVersion>10.0.19041.38\</WindowsSdkPackageVersion> は完全に俺的設定です。

3. ビルド時に「<プロジェクト名>(Unpackaged)」を選択する。

以上でビルドを行うとexeファイルから直接起動できる。