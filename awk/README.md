# awkのメモ

普通はパイプから連続したフィールドを受取、それを処理することが多いが、bashスクリプトの中で電卓的に使いたい場合等に標準入力からの
入力がいらない場合では`BEGIN`が必要

```bash
#!/usr/bin/bash

awk 'BEGIN{print sqrt(2)}'

```

- 例: 二次方程式の解

```bash
#!/usr/bin/bash

# 二次方程式 $x^2 + 6x + 2 = 0$ の解は

alpha=$(awk 'BEGIN{print -3 + sqrt(3^2 - 2)}')
beta=$(awk 'BEGIN{print -3 - sqrt(3^2 - 2)}')

echo "二次方程式 x^2 + 6x + 2 = 0の２個の解 alpha, betaは"
printf "alpha = %f, beta = %f\n" ${alpha} ${beta}
echo "とawkで計算できる"
```

## printf

書式はCのprintfを全く同じである

```
printf "a = %d, b = %d\n",a, b 
```

bashのprintfでは以下のように間をスペース区切りでカンマを入れないがawkの場合は必要であることに注意せよ

```
printf "a = %d, b = %d\n" 12 21
```

## 自動変数

- $1 ~ $n : n列目
- $0      : 行全体


### 応用

今以下のような複数列のデータarrayがある

```
3 30
5 50
6 60
1 10
2 21
```

- このデータに対して一列目、二列目の和をそれぞれ求めよ

```
cat array | awk '{sum1+=$1;sum2+=$2}END{print sum1,sum2}' 
```

もじ1行目に以下のようにヘッダが入っていてこれを避けたい場合は以下の変数`NR`に条件を加える

```
data_1 data_2
3 30
5 50
6 60
1 10
2 21
```
```
cat array | awk 'NR>1{sum1+=$1;sum2+=$2}END{print sum1,sum2}' 
```

## 正規表現を行に対して適用して抽出する

```
cat <file> | awk '{if($<number of row> ~ /<regular expression>/) print $0}'
```

### 応用(1)

今以下のようなファイルdataがある

data
```
xyz 10
abc 11
xz 12
yy 23
cs 2
ds 3
xx 3
```

1. dataのうち一列目がxから始まる行を抽出せよ

```
cat data | awk '{if($1~/^x/) print $0}'
cat data |grep '^x' # grepでもできる 
```

2. dataのうち一列目がxから始まる行の二列目の値の和を求めよ

```
cat data | awk '{if($1~/^x/) sum+=$2}END{print sum}'
cat data |grep '^x'|awk '{sum+=$2}END{print sum}'
```

今以下のようなファイルabcがある

```
a
a
b
a
c
a
b
c
b
a
b
```

1. このファイルの中にa,b,cはそれぞれ何個含まれているか、またその全個数は何個であるか示せ

```
cat a | sort | uniq -c | awk '{print $2, $1; sum+=$1}END{print "合計:" ,sum }'
```

### 応用(2)

以下は分子分光学のおけるスペクトルシミュレーション・データ解析に用いられる`PGOPHER`のタブ区切りのフォーマットで出力した`LineList`ファイルの一部分である

```tsv
AsymmetricTop	Excited	60	O-u	10	Ground	59	O+g	9	32240.8461296648	4.76437202935324E-10	32295.1055821715	54.2594525067844	0.00531189023650838	1384.22426268295	0.0020000000949949	sR17,42(59)	Excited 1Bu 60 19 41 - Ground 1Ag 59 17 42
AsymmetricTop	Excited	67	E-u	10	Ground	66	E+g	9	32240.8651680862	5.25504455676921E-10	32299.7417424442	58.8765743580071	0.0101915066112442	2380.39005246675	0.0020000000949949	sR16,50(66)	Excited 1Bu 67 18 49 - Ground 1Ag 66 16 50
AsymmetricTop	Excited	67	O-u	9	Ground	66	O+g	8	32240.8651680862	5.25504455676877E-10	32299.7417424442	58.8765743580071	0.0101915066112433	2380.39005246654	0.0020000000949949	sR16,51(66)	Excited 1Bu 67 18 50 - Ground 1Ag 66 16 51
```

このフォーマットにおいて重要なのは17列目の内容で例えば

```text
qP17,4(20)
```

となっている場合は`P`枝領域の中の`q`枝で量子数`J,K`
がそれぞれ`52 -> 51`で`17 -> 17`と遷移する回転線を意味する。


この回転遷移を取り出したいならば、17行目が正規表現`^qP17`にマッチするものを取り出すようにすれば良い

```bash
cat *.tsv | awk '{if($17 ~/^qP17/) print $0}'
```


