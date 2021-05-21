# UNVT WSL2インストール手順
Windows10 2004 (x64) 以降のWSL2 Ubuntu 20.04上に United Nations Vector Tile Toolkit (UNVT) 環境を構築する。


WSL2の準備
---
* WSL2利用環境が整っているものとする。
* WSL2はUbuntu 20.04 を使用する。
* Microsoft Store からUbuntu 20.04をインストールする。
* Ubunntu20.04が起動したら、ユーザーアカウントとパスワードを設定する。

#### aptのリポジトリにgisライブラリの参照を追加する

```
sudo add-apt-repository ppa:ubuntugis/ppa
```
_＊既に追加してある場合は不要_

#### yarn系のリポジトリを設定する

```
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -    
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
```

リポジトリデータベースを更新してモジュールをインストールする

```
sudo apt update
sudo apt -y upgrade
```

### UNVTに必要なライブラリのインストール（１）

```
sudo apt install -y autoconf automake cmake gdal-bin git libsqlite3-dev libtool nginx nodejs osmium-tool parallel ruby sqlite3 tmux vim xrdp yarn zip 
```

### UNVTに必要なライブラリのインストール（２）

#### VSCodeのインストール
https://code.visualstudio.com/ からvscode をダウンロード

```
sudo apt install ./公式サイトからダウンロード.debファイル
sudo apt install -y apt-transport-https
sudo apt update
sudo apt -y upgrade  ＊以前にダウンロードされたインストールパッケージを使用した場合
```

.profileに追加

```
export DONT_PROMPT_WSL_INSTALL=1
```

#### Visual Studio Codeの日本語設定

```
$ code 
```

メニューから［View］-［Command Palette］を選択
［Configure Display Language］を選択
［Install Additional Laugage］を選択
サイドバーに拡張言語パックが表示される。
サイドバーから［Japanese Language Pack for Visual Studio Code］を探し、［Install］ボタンをクリック
右下にポップアップウィザードが表示されるので、［Restart Now］をクリック
VS Codeが再起動され、日本語表示になる。


### npmのモジュールアップデート

```
sudo npm i -g npm
```

＊ WSL2ではxrdpは不要かもしれない

```
sudo service xrdp restart
```

tippecanoeのインストール
---
```
git clone https://github.com/mapbox/tippecanoe
cd tippecanoe
make -j3
sudo make install
cd ..
rm -rf tippecanoe
```

vt-optimizerのインストール
---

```
sudo yarn global add browserify budo hjson pm2 rollup @mapbox/mapbox-gl-style-spec @pushcorn/hocon-parser

git clone https://github.com/ibesora/vt-optimizer
cd vt-optimizer
yarn  install
cd ..
```

fgdのインストール
---
tippecanoeのテスト用

```
git clone https://github.com/unvt/fgd

cd fgd
yarn
sudo gem install rubyzip
```

基盤地図情報からテストデータをダウンロードする

https://fgd.gsi.go.jp/download

ダウンロードしたファイルを展開する。

_＊場所はR/Wできるところであればどこでもよい。_

~/Downloads/PackDLMap　に展開、fgdディレクトリで作業を行う場合

fgd/mbtilesディレクトリを作成する

fgd/Rakefileを編集する

```:Rakefile
SRC_DIR = "#{Dir.home}/Downloads/PackDLMap"
MBTILES_PATH = "tiles.mbtiles"
MBTILES_DIR = "mbtiles"
LAN_URL = 'http://localhost:9966'
```

タイルを作成する

```
rake produce
```

正常に終了すると、fgd/docs/にベクトルタイルフォルダができる。

以下は動作未確認
テストサイトをホスティングする
```
rake lan
rake host
rake pages
cp docs ~/your-pages-repo
```
