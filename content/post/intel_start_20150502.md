+++
categories = ["memo", "edison", "IoT"]
date = "2015-05-02T01:45:13+09:00"
description = "IntelのEdison始めてみた"
jtitle = "Edisonをセットアップするまで"
keywords = ["memo", "edison", "IoT"]
title = "intel_start_20150502"

+++
# 概要
Intel Edison Ardino Kit を購入したので、 
箱を開けてからセットアップするまでのメモ 
[Intelの出してるチュートリアル参照](https://software.intel.com/en-us/iot/library/edison-getting-started)

# 組み立て
Edisonさんをボードのピンにさして、ちっちゃなナットを締めるだけ。ナットは本当に小さいので無くさないように。ビスを締める際は奥側のビスを強めに締めるといいかも。対角線でしか固定されないので、チップが湾曲してボードと微妙な隙間ができて電源が付かなかった。。
![img](http://i.gyazo.com/17106a4f8fe140690535b19aad91776d.png)
あとは4つの足を付けて組み立て完成。

# セットアップ

## ログイン確認
J18(usb給電)とJ3(usb接続)にmicro USBをさすとPCと接続できる。チュートリル通りに

```
screen /dev/cu.usbserial-XXXXXXXX 115200 -L
```

とやってEnterを2回押してログインできる。 
login name は root　で初回のパスワードは無し！

## ファームウェアのアップデート
**Configure Username and Password**の最初に

>"Flash your Firmware to get the latest features and important updates."

とあるので最新イメージをEdisonに書き込む。最新イメージは[公式サイト](http://www.intel.com/support/edison/sb/CS-035180.html)で落とせるYocto complete image

既存のファイル、フォルダを削除してダウンロードしたファイルを全部Edisonディレクトリに移す。

```
rm -rf /Volumes/Edison/*
rm -rf /Volumes/Edison/\.*
cp ~/Downloads/edison-image-ww05-15/* /Volumes/Edison/.
```

`screen`でEdisonに入り

```
reboot ota
```

でアップデートが完了する。 
reboot中に電源が落ちないよう注意する。

## Edisonのセットアップ
Edisonに入ってから

```
configure_edison --setup 
```

で初期設定をしていく。
設定項目はrootのパスワードと, edisonの名前(host名), 
使用するwifi。
設定後にedisonの名前で\http://host.local`でEdisonのwebサーバにアクセスできる。sshも

```
ssh root@host.local
```

で利用可能になり、USBでつながなくてもEdisonを操作できるようになる。ただsshよりもシリアル接続の方がレスポンスがいいのでストレスは少ないかも。

# Lチカ
Hello world!的な存在らしいLチカをためしてみた。
![img](http://i.gyazo.com/d4ef680f9f68133d498d8a37dd93f93f.png)

```
int led = 13;

void setup() {
    // put your setup code here,  to run once:
    pinMode(led, OUTPUT);
}

void loop() {
    // put your main code here,  to run repeatedly:
    digitalWrite(led, HIGH);
    delay(1000);
    digitalWrite(led,  LOW);
    delay(1000);
}
```
