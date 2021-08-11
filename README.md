# はじめに

この記事では AWS Cloud Tech を通して Amazon Sagemaker を
実際に使ってみるハンズオン記事です。

主な内容としては実践したときのメモを中心に書きます。（忘れやすいことなど）
誤りなどがあれば書き直していく予定です。

## 対象読者

- クラウドを活用してデータサイエンスをやりたい人
- Python 書けるしデータサイエンスもなんとなくできるけどクラウドを使ったデータサイエンスはまだな人
- Amazon Sagemaker のことがチョット気になっている人

※利用サービスの説明から入ります。

## 環境

- 初代 Surface Book

  - 実装 RAM 8GB

- エディション Windows 10 Pro
- バージョン 20H2
- OS ビルド 19042.867

- Chrome

  - 89.0.4389.90（Official Build） （64 ビット）

## 今回扱うサービス

- Git Hub
- Amazon SageMaker
- IAM

## Git Hub とは

プログラムの元となるソースコードを共有、バージョン管理できるプラットフォーム

https://github.co.jp/about

## Amazon SageMaker とは

簡単に説明すると AWS の機械学習(ML)サービス

> すべてのデータサイエンティストとデベロッパーのための機械学習
> AWS の歴史の中で最も急速に成長しているサービスの 1 つ

https://aws.amazon.com/jp/sagemaker/

## ハンズオン開始

### AWS マネジメントコンソールの操作

サービス検索で「SageMaker」と検索してサービス名をクリック
![SageMaker検索](./sagemaker/sagemaker_search.JPG)

### 既存の Git リポジトリを AWS に設定

SageMaker のトップ画面にある左ペインから Git リポジトリの追加
ノートブック=>Git リポジトリの順にクリック

![左ペイン](./sagemaker/left_pain.JPG)

リポジトリの追加画面が表示される。以下のパラメータを各項目にセット

| 項目                          | パラメータ                                        |
| :---------------------------- | :------------------------------------------------ |
| リポジトリタイプ              | GitHub または 他の Git ベースのリポジトリ         |
| Amazon SageMaker リポジトリ名 | sagemaker-handson                                 |
| Git リポジトリの URL          | https://github.com/ymd65536/sagemaker_handson.git |
| リポジトリのブランチ名        | main                                              |
| Git 認証情報                  | シークレットがありません                          |

![SageMaker設定画面](./sagemaker/sagemaker_git.JPG)

### ノートブックインスタンスの作成

SageMaker のトップ画面にある左ペインからノートブックインスタンスを作成する。
ノートブック=>ノートブックインスタンスの順にクリック

![左ペイン](./sagemaker/left_pain.JPG)

ノートブックインスタンスの作成をクリック後、以下のようにパラメータを設定

| 項目                           | パラメータ        |
| :----------------------------- | :---------------- |
| ノートブックインスタンス名     | sagemaker-handson |
| ノートブックインスタンスタイプ | ml.t2.medium      |
| Elastic Inference              | なし              |

アクセス許可と暗号化

| 項目           | パラメータ         |
| :------------- | :----------------- |
| IAM ロール     | 新しいロールの作成 |
| ルートアクセス | 無効化             |
| 暗号化キー     | カスタム暗号化なし |

「新しいロールを作成」を選択するとロールを作成する画面に遷移
今回は S3 をは利用しない為、なしにチェックを入れる。

![ロールの作成](./sagemaker/sagemaker_iam_role.JPG)

ネットワーク
|項目|パラメータ|
|:---|:---|
|VPC オプション|非 VPC|

Git リポジトリは先ほど指定したリポジトリを選択
![リポジトリ](./sagemaker/sagemaker_git_repo.JPG)

| キー      | バリュー |
| :-------- | :------- |
| sagemaker | handson  |

![キーバリュー](./sagemaker/key_value.JPG)

最後にノートブックインスタンスの作成をクリック
これで GitHub にあるリポジトリを保存したノートブックインスタンスが作成されます。

ノートブックインスタンスの作成には時間がかかります。
Pending から InService になるまでしばらく待ちましょう。
![pending.JPG](./sagemaker/pending.JPG)

![inservice.JPG](./sagemaker/inservice.JPG)

## ノートブックインスタンスを使ってみる(Jupyter を利用)

### ノートブックを開く

sagemaker-handson をクリック

![name.JPG](./sagemaker/name.JPG)

「Jupyter を開く 」をクリック

![action_jupyter.JPG](./sagemaker/action_jupyter.JPG)

Jupyter Notebook が起動します。
sagemaker_handson をクリック

![sagemaker_on_jupyter_notebook.JPG](./sagemaker/sagemaker_on_jupyter_notebook.JPG)

main.ipynb をクリック

### ノートブックを起動

起動時に Kernel not found と表示された場合は
「conda_python3」 を選択して Set Kernel をクリック

| 項目        | 値            |
| :---------- | :------------ |
| 利用 Kernel | conda_python3 |

![kernel_not_found.JPG](./sagemaker/kernel_not_found.JPG)

### ノートブックを実行

基本的な使い方

jupyter notebook はセルという単位でソースコードを管理します。

具体的には
In セルにはコードを入力
Out セルには In セルに入力したコードの実行結果が出力されます。

先頭行で 1 度宣言すると 2 行目以降からは宣言が不要になります。
ただし、宣言内容を変更した場合は再度実行する必要があります。

また、jupyter notebook は宣言済みの変数名を書いて実行すると
変数の中身が表示されます。

## いくつか実行してみる

### import と配列を宣言

今回は numpy を利用して配列 fish_data を計算します。

```python:cell1
## numpyをインポート
import numpy as np
fish_data = np.array([2, 3, 3, 4, 4, 4, 4, 5, 5, 6,7])
```

### 合計値を計算

numpy の sum メソッドを利用して合計値を計算

```python:cell2
# 合計値(sum_value)を出す
sum_value = np.sum(fish_data)
sum_value
## Out[2] 47
```

### 配列の長さを計算

```python:cell2
# 標本の数 N を数える
N = len(fish_data)
N
## Out[3] 11
```

### 平均値を計算

```python:cell3
# 平均を出す sum_value / N
# mean メソッドを利用
avg_m = np.mean(fish_data)

avg_m
## Out[4] 4.2727272727272725
```

または

```python:cell4
# 平均を出す sum_value / N
# average を利用
a_avg = np.average(fish_data)
a_avg

## Out[5] 4.2727272727272725
```

### 最小値を計算

```python:cell6
np.amin(fish_data)
## Out[6] 4.2727272727272725
```

## ノートブックインスタンスを使ってみる(Jupyter Lab を利用)

### ノートブックを開く

「JupyterLab を開く 」をクリック

![action_jupyter.JPG](./sagemaker/action_jupyter.JPG)

フォルダマークをクリック
sagemaker-handson をクリック

![jupyterLab_left_pain.JPG](./sagemaker/jupyterLab_left_pain.JPG)

main.ipynb をクリック
すると notebook の画面が表示されます。

### 統計量を計算(numpy pandas を利用)

### pickle を使って図を書き出す

## おわり

## その他

```shell
python -m venv sagemaker
python -m pip install --upgrade pip
python -m pip install -r data_science.txt
python -m pip freeze > jupyter.txt
```

> scipy.sum is deprecated and will be removed in SciPy 2.0.0, use numpy.sum instead

scipy.sum は非推奨で、SciPy 2.0.0 で削除されます。
