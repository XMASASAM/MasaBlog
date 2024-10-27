+++
title = 'ReactivePropertyとR3のパフォーマンス比較'
date = 2024-10-27T23:18:38+09:00
draft = false
tags=['C#','ReactiveProperty','R3']
+++


MVVMを意識してコードを書くときにとても役立つReactivePropertyですが、最近その最新版としてR3なるものが出たようです。そこで、パフォーマンスを測り記録します。
<!--more-->

- ReactivePropertyについてはこちらの記事がとても参考になります。  
→[MVVM をリアクティブプログラミングで快適に ReactiveProperty オーバービュー 2020 年版](https://qiita.com/okazuki/items/7572f46848d0e93516b1)

- R3についてはこちらの記事がとても参考になります。  
→[次世代Rx「R3」解説](https://qiita.com/toRisouP/items/e7be5a5a43058556db8f)

# 環境
- Windows11
- VisualStudio2022

# ライブラリ
- BenchmarkDotNet.Diagnostics.Windows
- ObservableCollections
- ObservableCollections.R3
- R3
- ReactiveProperty

上記のライブラリはNugetから入手できます。

# 記録
まず、ベンチマークする関数の前に下記の処理を行います。
```csharp {name="前処理"}
int n = 任意の数;
Random r = new Random();
List<Reactive.Bindings.ReactivePropertySlim<int>> props = new List<Reactive.Bindings.ReactivePropertySlim<int>>();
for (int i = 0; i < n; i++)
    props.Add(new Reactive.Bindings.ReactivePropertySlim<int>());
System.Reactive.Linq.Observable.CombineLatest(props).Subscribe(_=> { });

List<R3.ReactiveProperty<int>> props2 = new List<R3.ReactiveProperty<int>>();
for (int i = 0; i < n; i++)
    props2.Add(new R3.ReactiveProperty<int>());
R3.Observable.CombineLatest(props2).Subscribe(_=> { });
```

## Subscribe(n=1)の比較
```csharp {name="fn1,fn2"}
[Benchmark]
public int fn1(){
    props[0].Value = r.Next(10000);
    return props[0].Value;
}

[Benchmark]
public int fn2(){
    props2[0].Value = r.Next(10000);
    return props2[0].Value;
}
```
| Method | Mean      | Error     | StdDev    | Allocated |
|:---: |:---:|:---:|:---:|:---:|
| fn1   |  9.986 ns | 0.5170 ns | 0.0283 ns |         - |
| fn2   | 11.393 ns | 0.9914 ns | 0.0543 ns |         - |

## Subscribe(n=1000)の比較
```csharp {name="fn1,fn2"}
[Benchmark]
public int fn1(){
    props[r.Next(props.Count)].Value = r.Next(10000);
    return prop.Value;
}

[Benchmark]
public int fn2(){
    props2[r.Next(props.Count)].Value = r.Next(10000);
    return prop2.Value;
}
```

| Method | Mean     | Error     | StdDev   | Gen0   | Gen1   | Allocated |
|:---: |:---:|:---:|:---:|:---:|:---:|:---:|
| fn1   | 228.3 ns |  95.46 ns |  5.23 ns | 0.4873 | 0.0024 |   3.98 KB |
| fn2   | 872.5 ns | 390.67 ns | 21.41 ns | 0.4807 |      - |   3.93 KB |

## CombineLatest(n=5)の比較
```csharp {name="fn1,fn2"}
[Benchmark]
public int fn1(){
    System.Reactive.Linq.Observable.CombineLatest(props).Subscribe(_=> { });
    return props[0].Value;
}

[Benchmark]
public int fn2(){
    R3.Observable.CombineLatest(props2).Subscribe(_=> {});
    return props2[0].Value;
}
```

| Method | Mean     | Error     | StdDev    | Gen0   | Gen1   | Allocated |
|:---: |:---:|:---:|:---:|:---:|:---:|:---:|
| fn1   | 2.217 us | 0.8524 us | 0.0467 us | 0.1450 | 0.0687 |    1216 B |
| fn2   | 1.846 us | 0.9377 us | 0.0514 us | 0.1144 | 0.0572 |     968 B |

# 結論
R3のReactivePropertyは  
ReactivePropertyのReactivePropertySlimより若干遅いが、メモリ消費量はほとんど同じで、その他色々な実装が改善されているようなので、R3でも問題なさそう。


# おまけ
## foreachですべてをSubscribe(n=5)
```csharp {name="fn1,fn2"}
[Benchmark]
public int fn1(){
    foreach (var i in props)i.Subscribe(_=> { });
    return props[0].Value;
}

[Benchmark]
public int fn2(){
    foreach (var i in props2)i.Subscribe(_=> { });
    return props[0].Value;
}

```
| Method | Mean     | Error    | StdDev  | Gen0   | Gen1   | Allocated |
|:---: |:---:|:---:|:---:|:---:|:---:|:---:|
| fn1   | 538.4 ns | 31.06 ns | 1.70 ns | 0.0572 | 0.0553 |     480 B |
| fn2   | 629.3 ns |  4.78 ns | 0.26 ns | 0.0610 | 0.0591 |     520 B |

Subscribe内の関数で全ての要素を参照する必要がないときはCombineLatestを使わず、個別でSubscribeした方がいいようだ。処理速度は早く、メモリ消費量も少なくて済む。