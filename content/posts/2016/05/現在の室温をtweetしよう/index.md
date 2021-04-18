+++
date = "2016-05-06T14:56:01+09:00"
draft = false
title = "現在の室温をtweetしよう"
categories = ["gadget"]
cover = "raspberrypi.jpg"
+++

連休に自宅サーバをこれまで愛用していたHP社製microserverからRaspberry Pi3へリプレイスしました。microserverでは地味にRAID1+0環境にしてたりして安心確実ストレージとなっているのですが、日常からHDDを4本も回してなくていいんじゃ？と言う事が事はじめです。とは言えストレージサーバとしてはとても信頼おける状態となっていますので、時折WakeOnLanで起こしてはバックアップ先として活躍して頂く予定で、普段使いはRaspberry Pi3につないだシングルのHDDでも問題ないよね、という感じです。

さて普通にLinux環境が動くRaspberry Piですのでサーバ構築はそれこそ鼻歌交じりで完了します。何も躓くところはありません。そしてコンパクトになり各種センサーを接続するGPIOが接続しやすく、となると色々センサー繋ぎたくなりますよね。

我が家ではCITIZENの電波時計をリビングや寝室に置いてあり、これには温湿度計が付いています。脱衣所や窓辺にはそれぞれ脱衣所の室温や外気温（屋根裏にも設置してあります）を測るための温度計を設置してありますが、それぞれ独立した温度計でその時々の温度しか知る事が出来ませんしその場に行かねばわかりません。

じゃあRaspberry Piを設置するリビング位Raspberry Piに計測させて遠隔でも見れるようにしたらいいよね。と言う事でこの連休に色々パーツを買いそろえて表題の通り室温をTweetするまで作りました。


さて、こんな事は実はすでに先人が多く通った道なのでGoogleで検索するといっぱい記事が見つかります。「Raspberry Pi BME280」あたりで検索してみてください。BME280は何かというと今回用いた温湿度センサーで1000円ちょっとする部品ですが、1チップで温度も湿度も気圧も測れてお得でおススメです。

今回Raspberry Pi以外に用意したものとしては

- [AE-BME280](http://akizukidenshi.com/catalog/g/gK-09421/) （秋月電子製温湿度気圧センサー）
- ブレッドボード（試作に大活躍）
- ジャンパーピン（オス-メス）
- ジャンパーピン（オス-オス)
- 十字配線ユニバーサル基板



こんな感じです。その他にもLIS3DHと言う3軸加速度センサーも一緒に買って回路に組んでますが、今回の室温をTweetするのには関係ない部分なので割愛。

さて、回路を組んで試験する処までは [こちらのページ](http://deviceplus.jp/hobby/raspberrypi_entry_039/) がとても参考になります。むしろこちらのページだけで十分でこんなエントリー要らないくらい！

## Raspberry Piでの準備
Raspberry Piに入れているOSにより準備が異なってきます。Raspbianであれば

{{< highlight bash >}}

$ sudo raspi-config

{{< / highlight >}}


これで設定画面が開きますのでAdvanced Optionを選びます。

{{< fancybox "." "01_advanced-options-1.png" "BIOS Advanced Option" "gallery" >}}

Advanced Optionの中にI2Cの設定がありますので、選択し有効化。これでI2Cが使えるようになります。

{{< fancybox "." "02_I2C-1.png" "I2C 設定" "gallery" >}}

またその他としてI2Cを扱うツールをインストールする必要があります。

{{< highlight bash >}}

$ sudo apt-get -y install i2c-tools python-smbus

{{< / highlight >}}

これでまずは温度を取得するのに必要なツールがインストールされました。ここで一度再起動しておきましょう。

<h2>I2C通信の仕組みを知る</h2>

アナログセンサーを接続してアナログデータを取ったりした事はありますが、I2Cを触るのは初めてでした。センサー単体で動かしてよしよし、って言うページはとても多く見つかるんですが欲張りにセンサー2つ以上繋ぎたい場合は？となるとなかなかページ見つかりません。仕方ないので [I2Cについて調べたら](http://www.mcc-us.com/I2CBusTechnicalOverview.pdf) なんの事はない、バス接続で良いと知りました。という訳で2つのセンサーを繋いでみよう、とこんな回路にして接続してみました。AE-BME280をI2C通信として用いるためにはJ3と書かれたジャンパを半田でショートさせる必要があります。ちなみにLIS3DHの場合は裏面のABC3つのジャンパをショートさせます。これらはそれぞれ添付のマニュアルにちゃんと記載がありますのでちゃんと確認しましょう。

なお今回両方のセンサーを繋ぐため以下のようになってますが、今回のお題である室温をtweetするだけであれば右側のセンサーは不要です。

{{< fancybox "." "BME280-kairo-1.png" "I2C通信の回路図" "gallery" >}}

センサー側の接続が済んだらRaspberry Piに接続しますがI2Cはホットプラグ可能ですので電源入れたままで繋いで問題ありません。接続端子間違えないようにしましょうね。例えば今回のBME280もLIS3DHも電源電圧は3.3Vですのでうっかり5Vの端子に繋いじゃったりすると壊れてしまう可能性もゼロではありません。

接続したらi2cdetectコマンドを用いてraspberry piで接続を確認してみると

{{< highlight bash >}}

$ sudo i2cdetect -y 1
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- 18 -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- 76 --

{{< / highlight >}}

ばっちり認識されました。0x76の方が今回用いる温度センサーで0x18の方が今回用いない加速度センサーです。と言う事でI2Cに対応しているセンサーであれば測りたいセンサーをこんな感じでどんどん繋いで行ける！って事ですね。便利！

## 温度データを取得する

さて、I2Cデバイスとして認識するところまで来ました。いよいよここからデータを取りだします。本当はデータシートを読んで頑張らなくては本来いけない所なんでしょうけれども、 [SWITCH SCIENCEさんが用意して下さっているスクリプト](https://github.com/SWITCHSCIENCE/BME280) をまずは使って動作確認します。

{{< highlight bash >}}

$ wget https://raw.githubusercontent.com/SWITCHSCIENCE/BME280/master/Python27/bme280_sample.py
$ sudo python bme280_sample.py
temp : 28.97  ℃
pressure : 1017.31 hPa
hum :  34.95 ％

{{< / highlight >}}

このままツイートさせてもいいのですが加工したいのでスクリプトを修正

{{< highlight python >}}

 #!/usr/bin/python
#coding: utf-8

import smbus
import time

import datetime
import locale

d = datetime.datetime.today()

bus_number  = 1
i2c_address = 0x76

bus = smbus.SMBus(bus_number)

digT = []
digP = []
digH = []

t_fine = 0.0
yymmdd = 0
hhmm = 0
temperature = 0
humidity = 0
pressure = 0

def writeReg(reg_address, data):
        bus.write_byte_data(i2c_address,reg_address,data)

def get_calib_param():
        calib = []

        for i in range (0x88,0x88+24):
                calib.append(bus.read_byte_data(i2c_address,i))
        calib.append(bus.read_byte_data(i2c_address,0xA1))
        for i in range (0xE1,0xE1+7):
                calib.append(bus.read_byte_data(i2c_address,i))

        digT.append((calib[1] &lt;&lt; 8) | calib[0])
        digT.append((calib[3] &lt;&lt; 8) | calib[2])
        digT.append((calib[5] &lt;&lt; 8) | calib[4])
        digP.append((calib[7] &lt;&lt; 8) | calib[6])
        digP.append((calib[9] &lt;&lt; 8) | calib[8])
        digP.append((calib[11]&lt;&lt; 8) | calib[10])
        digP.append((calib[13]&lt;&lt; 8) | calib[12])
        digP.append((calib[15]&lt;&lt; 8) | calib[14])
        digP.append((calib[17]&lt;&lt; 8) | calib[16])
        digP.append((calib[19]&lt;&lt; 8) | calib[18])
        digP.append((calib[21]&lt;&lt; 8) | calib[20])
        digP.append((calib[23]&lt;&lt; 8) | calib[22])
        digH.append( calib[24] )
        digH.append((calib[26]&lt;&lt; 8) | calib[25])
        digH.append( calib[27] )
        digH.append((calib[28]&lt;&lt; 4) | (0x0F &amp; calib[29]))
        digH.append((calib[30]&lt;&lt; 4) | ((calib[29] &gt;&gt; 4) &amp; 0x0F))
        digH.append( calib[31] )

        for i in range(1,2):
                if digT[i] &amp; 0x8000:
                        digT[i] = (-digT[i] ^ 0xFFFF) + 1

        for i in range(1,8):
                if digP[i] &amp; 0x8000:
                        digP[i] = (-digP[i] ^ 0xFFFF) + 1

        for i in range(0,6):
                if digH[i] &amp; 0x8000:
                        digH[i] = (-digH[i] ^ 0xFFFF) + 1

def readData():
        data = []
        for i in range (0xF7, 0xF7+8):
                data.append(bus.read_byte_data(i2c_address,i))
        pres_raw = (data[0] &lt;&lt; 12) | (data[1] &lt;&lt; 4) | (data[2] &gt;&gt; 4)
        temp_raw = (data[3] &lt;&lt; 12) | (data[4] &lt;&lt; 4) | (data[5] &gt;&gt; 4)
        hum_raw  = (data[6] &lt;&lt; 8)  |  data[7]

        compensate_T(temp_raw)
        compensate_P(pres_raw)
        compensate_H(hum_raw)

def compensate_P(adc_P):
        global  t_fine
        pressure = 0.0

        v1 = (t_fine / 2.0) - 64000.0
        v2 = (((v1 / 4.0) * (v1 / 4.0)) / 2048) * digP[5]
        v2 = v2 + ((v1 * digP[4]) * 2.0)
        v2 = (v2 / 4.0) + (digP[3] * 65536.0)
        v1 = (((digP[2] * (((v1 / 4.0) * (v1 / 4.0)) / 8192)) / 8)  + ((digP[1] * v1) / 2.0)) / 262144
        v1 = ((32768 + v1) * digP[0]) / 32768

        if v1 == 0:
                return 0
        pressure = ((1048576 - adc_P) - (v2 / 4096)) * 3125
        if pressure &lt; 0x80000000:
                pressure = (pressure * 2.0) / v1
        else:
                pressure = (pressure / v1) * 2
        v1 = (digP[8] * (((pressure / 8.0) * (pressure / 8.0)) / 8192.0)) / 4096
        v2 = ((pressure / 4.0) * digP[7]) / 8192.0
        pressure = pressure + ((v1 + v2 + digP[6]) / 16.0)
        global press
        press = pressure/100

def compensate_T(adc_T):
        global t_fine
        v1 = (adc_T / 16384.0 - digT[0] / 1024.0) * digT[1]
        v2 = (adc_T / 131072.0 - digT[0] / 8192.0) * (adc_T / 131072.0 - digT[0] / 8192.0) * digT[2]
        t_fine = v1 + v2
        temperature = t_fine / 5120.0
        global temp
        temp = temperature -4

def compensate_H(adc_H):
        global t_fine
        var_h = t_fine - 76800.0
        if var_h != 0:
                var_h = (adc_H - (digH[3] * 64.0 + digH[4]/16384.0 * var_h)) * (digH[1] / 65536.0 * (1.0 + digH[5] / 67108864.0 * var_h * (1.0 + digH[2] / 67108864.0 * var_h)))
        else:
                return 0
        var_h = var_h * (1.0 - digH[0] * var_h / 524288.0)
        if var_h &gt; 100.0:
                var_h = 100.0
        elif var_h &lt; 0.0:
                var_h = 0.0
        global hum
        hum = var_h

def setup():
        osrs_t = 1                      #Temperature oversampling x 1
        osrs_p = 1                      #Pressure oversampling x 1
        osrs_h = 1                      #Humidity oversampling x 1
        mode   = 3                      #Normal mode
        t_sb   = 5                      #Tstandby 1000ms
        filter = 0                      #Filter off
        spi3w_en = 0                    #3-wire SPI Disable

        ctrl_meas_reg = (osrs_t &lt;&lt; 5) | (osrs_p &lt;&lt; 2) | mode
        config_reg    = (t_sb &lt;&lt; 5) | (filter &lt;&lt; 2) | spi3w_en
        ctrl_hum_reg  = osrs_h

        writeReg(0xF2,ctrl_hum_reg)
        writeReg(0xF4,ctrl_meas_reg)
        writeReg(0xF5,config_reg)

setup()
get_calib_param()
if __name__ == '__main__':
        try:
                readData()
        except KeyboardInterrupt:
                pass

        print d.strftime("%Y%m%d,%H:%M"),",%2.2f,%4.2f,%6.2f" % (temp, hum,press)

{{< / highlight >}}


これで扱いやすいデータになりました。
所で実はここで校正データを入れてあります。114行目に"temp = temperature -4"とあり、ここの最後で値から4度引いています。これは何かというと組んだ回路で測定して誤差も少ない素晴らしいセンサーを使っているのですが、例えば近くにRaspberry Piと言う熱源がありますよね。ですのでここで表示される値が必ずしも我々の体感する室温か？というとたぶん高めに表示されると思います。ですのでいくつか温度計を用意して校正すると良いかと思います。

{{< highlight bash >}}

$ sudo python room_tweet.py
20160506,14:03 ,25.12,36.98,1017.93

{{< / highlight >}}

## いよいよtweetする部分

Raspberry Piからscriptでtweetするのには [ttytter](http://www.floodgap.com/software/ttytter/) と言うのが流行っぽいです。でもリンク先を見に行くとTTYtter is dead.とか書いてますね(汗)

ttytterの使い方は [こちらのページが詳しい](http://masatolan.com/raspberry-pi/raspberry-pi-ttytter/) です。

インストールしてまずは認証画面を開きます。 途中"Press RETURN/ENTER to continue "と言われるのでEnterを押しましょう。

{{< highlight bash >}}

$ sudo apt-get -y install ttytter
$ ttytter -ssl

{{< / highlight >}}

すると認証用のURLが出てきますのでブラウザでその認証URLを開き、アプリケーション認証。出てくるPIN番号をまたTerminalに返してあげてください。

tweetするには

{{< highlight bash >}}

$ ttytter -ssl -status="tweet内容"

{{< / highlight >}}

となりますので先ほどの温度取得pythonスクリプトで生成してあげればいいのですが他用途でもこの温度取得scriptは使っていますので(グラフを作成している)、別途簡単なshellscriptで先ほどのpythonスクリプトを叩いてtweetしています。

{{< highlight bash >}}

#!/bin/sh
export LANG=ja_JP.utf8
export LANGUAGE=japanese

 text=`sudo /path/room_tweet.py`
 time_value=`echo $text | awk -F, '{print $2}'`
 temp_value=`echo $text | awk -F, '{print $3}'`
 humidity_value=`echo $text | awk -F, '{print $4}'`
 hpa_value=`echo $text | awk -F, '{print $5}'`
 tweet="${time_value} 現在の室温は ${temp_value} 度、湿度は ${humidity_value} ％、気圧は ${hpa_value} hPaです。"
 ttytter -ssl -status="${tweet}"

{{< / highlight >}}

上記の通りsudoをscriptより実行していますのでroot_tweet.pyの実行はsudoersファイルを編集してNOPASSで実行できないと定期的に実行してくれません。あとはこれをcronで回すだけ！
気温測定のScriptとTweet部を別にしておく事でttytterが使えなくなったときの修正も楽ですし、特定の室温を超えたときだけtweetなんて条件文もちょちょいと書けていいかな。

こんな感じで定期的に室温をtweetしています。フォロワーさんの迷惑にならないように頻度は考えましょう。
