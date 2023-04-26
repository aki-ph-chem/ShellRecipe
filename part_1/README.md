# part1

とりあえずパート1

## ファイルの存在を確認

ファイルがあることを確認
```bash
if [ -e "$file_name" ];then 
    echo "file is exist"
fi
```

ないことを確認 `!`と`-e`の間は開けること
```bash
if [ ! -e "file_name" ]; then
    echo "file is not exist"
fi
```

## forループのワンライナー

```bash
for i in {1..10};do echo "i = ${i}"; done
```

## awkを使ってdockerのrm, rmiを一括実行

以下はイメージを一括削除

```bash
docker images | awk 'NR>1{print "docker rmi " $3}' |sh
```
