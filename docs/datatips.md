# データ下処理メモ

## CSVファイルを空行ごとに分割したい
EPMAやXRFのデータを試料ごとに空行を挿入した1枚のCSVファイルにまとめておいたものを，再び試料ごとに分割したいというケースがよくある．
```python
import pandas as pd

def main():
    df = pd.read_csv("filename.csv")
    
    start_num = 0
    
    for i in range(len(df)): # DataFrame形式はlen(df)で行数を取得できる．
        if df.iloc[i,1] == df.iloc[i,1]: # 空行のNaNは自分自身と比較するとFalseを返す．
            pass
        else:
            df_save = df[start_num:i]
            csv_name = df.iloc[0,1] # 先頭行からCSVのファイル名を取得している．インデックスが振り直されていることに注意．
            df_save.to_csv(csv_name+".csv") # to_csv(filename) でCSVに保存できる．
            start_num = i+1 # 空行の次の行からまたデータが始まるので振り直し．


if __name__ == "__main__":
    main()
```

[Return to the home page](../index.md)
