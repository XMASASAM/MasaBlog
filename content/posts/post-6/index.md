+++
title = 'BlazorServerでComponentsフォルダー内でしかTwoWayBindingが効かない理由'
date = 2024-10-28T01:53:58+09:00
draft = false
tags=['Blazor']
+++
BlazorWebAppテンプレートから生成されるサーバー側のプロジェクトではComponentsフォルダー以外の.razorでは@bind:get,@bind:setが有効になりません。その理由は
<!--more-->
Microsoft.AspNetCore.Components.Webが参照できていないからです。@bind:get,@bind:setを有効にするためにはMicrosoft.AspNetCore.Components.Webを参照する必要があります。

Componentsフォルダー内には_Imports.razorが存在しており、その中でMicrosoft.AspNetCore.Components.Webが参照されています。[_Imports.razorの説明](https://learn.microsoft.com/ja-jp/aspnet/core/blazor/components/layouts?view=aspnetcore-8.0#:~:text=%E3%82%B3%E3%83%B3%E3%83%91%E3%82%A4%E3%83%A9%E3%81%AB%E3%82%88%E3%82%8A%E3%80%81%E3%82%A4%E3%83%B3%E3%83%9D%E3%83%BC%E3%83%88%20%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%AB%E6%8C%87%E5%AE%9A%E3%81%95%E3%82%8C%E3%81%9F%E3%83%87%E3%82%A3%E3%83%AC%E3%82%AF%E3%83%86%E3%82%A3%E3%83%96%E3%81%8C%E3%80%81%E5%90%8C%E3%81%98%E3%83%95%E3%82%A9%E3%83%AB%E3%83%80%E3%83%BC%E5%86%85%E3%81%A8%E3%81%9D%E3%81%AE%E3%81%99%E3%81%B9%E3%81%A6%E3%81%AE%E3%82%B5%E3%83%96%E3%83%95%E3%82%A9%E3%83%AB%E3%83%80%E3%83%BC%E5%86%85%E3%81%A7%E5%86%8D%E5%B8%B0%E7%9A%84%E3%81%AB%E3%81%99%E3%81%B9%E3%81%A6%E3%81%AE%20Razor%20%E3%83%86%E3%83%B3%E3%83%97%E3%83%AC%E3%83%BC%E3%83%88%E3%81%AB%E5%90%AB%E3%81%BE%E3%82%8C%E3%81%BE%E3%81%99%E3%80%82)を見ると
> コンパイラにより、インポート ファイルに指定されたディレクティブが、同じフォルダー内とそのすべてのサブフォルダー内で再帰的にすべての Razor テンプレートに含まれます。

とある。

よって、_Import.razorをrootフォルダへ移動させるとComponentsフォルダーに関係なく@bind:get,@bind:setが利用できます。