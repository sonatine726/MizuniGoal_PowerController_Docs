
# 回生電力用ブレーキチョッパ

ブレーキチョッパ（Braking Chopper）の構成や回路例を確認したい場合は、英語検索を含めて以下のキーワードや技術フォーラムのスレッドが参考になります。

1. **All About Circuits - Braking Chopper Thread**
    
    - コミュニティ内でLM393を使用した過電圧クランプ（Braking Chopper）回路の設計とシュミレーションについて議論されているスレッドです。
        
    - 検索ワード: `All About Circuits Braking Chopper - overvoltage dumb load`
        
2. **Electrical Engineering Stack Exchange - Dynamic Braking Resistor Chopper Circuit**
    
    - ヒステリシスコンパレータを用いた回生ブレーキ制御の概念や、フライホイールダイオード等の注意点が詳しく解説されています。
        
    - 検索ワード: `Electrical Engineering Stack Exchange Dynamic Braking resistor chopper circuit`
        
3. **ODrive Open Source Hardware Repo (GitHub)**
    
    - オープンソースのモータコントローラ「ODrive」の周辺回路（Regen Clamp）の回路図（PDF）が公開されています。構成の参考として非常に優れています。
    - https://github.com/odriverobotics/ODriveHardware



# TO-263面実装パワー抵抗

https://www.digikey.jp/ja/products/detail/bourns-inc/PWR263S-35-1R00F/2620700
17


# v2.2の課題

**デフォルト値を一つ決める方針は、この回路構成では無理があります。** ばらつきの原因が 2 つ（EN しきい値、VBUS）あり、どちらも抵抗では吸収できないためです。


両端を見ると、**しきい値が低い個体ほど遮断が遅く、高い個体ほど早い**という関係です。

| EN しきい値      | 遮断点の範囲       | Q1 の最高温度 | 65℃ 環境での余裕 | 判定         |
| ------------ | ------------ | -------- | ---------- | ---------- |
| 0.8V         | 107.9〜124.4℃ | **153℃** | 43℃        | ✗ Tjmax 超過 |
| 1.15V        | 94〜110℃      | 139℃     | 29℃        | ✓          |
| 1.30V（今回の個体） | 89.9〜105.1℃  | 134℃     | 25℃        | ✓          |
| 1.50V        | 84〜99℃       | 128℃     | 19℃        | ✓          |
| 2.0V         | 74.6〜89.2℃   | 118℃     | **9.6℃**   | ✗ 誤遮断の余裕不足 |

**合格窓：EN 立ち下がりしきい値 1.15〜1.50V**（暫定）

今回の個体は 1.30V で窓の中央付近です。


9_ENしきい値選別 を新設しました

加熱不要の電気的選別です。手順 6 ステップを本文に書き、9-2 の立ち下がりしきい値を入力すると遮断点が自動計算されるようにしました。

試しに 1.30V を入れると、こう出ます。

	VBUS 19.5V	VBUS 29.4V
TH1 の遮断点	89.8℃	105.1℃
Q1 の推定温度	118.8℃	134.1℃
筐体内 65℃ での余裕	24.8℃	40.1℃