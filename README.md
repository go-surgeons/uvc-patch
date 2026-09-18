# uvc-patch

Linux カーネル `uvcvideo` ドライバの互換性パッチ。`maxpsize=0` を報告する UVC カメラを検出し、帯域幅チェックの回避・`GET_CUR` の応答補正・ストリーム断の自動復帰を行う。

検証済みのベースカーネルソース版は `.kernel-refs/` に記録している（Debian `linux-source-6.12`、Raspberry Pi `linux-source-6.18`）。

## 適用とビルド

```bash
# X.Y は uname -r のメジャー.マイナー(例: 6.12)
sudo apt-get install -y build-essential patch xz-utils linux-headers-$(uname -r) linux-source-X.Y

mkdir -p uvc
tar -xf /usr/src/linux-source-X.Y.tar.xz -C uvc --strip-components=5 --wildcards '*/drivers/media/usb/uvc/*'
patch -p1 < uvc-modifications.patch
make -C /lib/modules/$(uname -r)/build M=$PWD/uvc modules

sudo rmmod uvcvideo || true
sudo insmod uvc/uvcvideo.ko
dmesg | grep 'applying compatibility quirks'   # 適用が効いた目印
```

## ライセンス

GPL-2.0-only（全文は [LICENSE](LICENSE)）。本パッチは Linux カーネル `uvcvideo` ドライバの派生物であり、ビルドした `uvcvideo.ko` の完全な対応ソースは本リポジトリ（同じ `VERSION` のタグ）と `.kernel-refs/` に記録した版の `linux-source` パッケージである。
