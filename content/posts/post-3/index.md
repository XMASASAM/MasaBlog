+++
title = 'C#からC++のDLLを呼び出すときの準備'
date = 2024-09-24T01:20:27+09:00
draft = false
tags=['C#','C++','VisualStudio']
+++
C#のプロジェクトからC++のプロジェクトを呼び出すときの準備を書いておく。
<!--more-->

# 環境
- Windows11
- VisualStudio2022

# やること
1. C++のプロジェクトを作る。
2. C++のプロジェクトのプロパティページを開き　「構成プロパティ　>　全般　>　構成の種類」を「ダイナミックライブラリ(.dll)」と設定する。
3. C#のプロジェクトを作る。
4. C#のプロジェクトファイルを開き、下記を追加する。
```html (name="")
<Project Sdk="Microsoft.NET.Sdk">
    ...
    <ItemGroup>
        ...
        <!--以下を追加 -->
	    <Content Include="$(SolutionDir)$(Platform)\$(Configuration)\C++のプロジェクト名.dll">
	    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
	    </Content>
        <!--以上を追加 -->
    </ItemGroup>
    ...
</Project>
```
>**tip**
> - $(SolutionDir)は「ソリューションのパス+\」
> - $(Platform)は「x64」など
> - $(Configuration)は「Debug」など
>
> [詳しくはこちら](https://learn.microsoft.com/en-us/cpp/build/reference/common-macros-for-build-commands-and-properties?view=msvc-170&viewFallbackFrom=vs-2017)


>**tip**:C#とC++のプロジェクトが同一のソリューション内に存在することを前提としている。C++のプロジェクト(DLL自体)が全く違う場所に存在する場合はそのDLLのパスを「\<Content Include=」へ入力する。
5. C#のプロジェクトを右クリックし、「プロパティ(R)」をクリックする。
6. 「デバック > 全般 > デバック起動プロファイルUIを開く > ネイティブコードのデバックを有効にする」へチェックを入れる。（C++のDLLもデバックできるようになるため。） 

以上で設定完了！
