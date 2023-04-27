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

### 応用

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
