# **Carla**
Carla 0.9.6の導入とinterp_e2e_drivingを動かすまでの手順
# black listの作成

```
 cat < /etc/modprobe.d/blacklist-nouveau.conf
 blacklist nouveau
 options nouveau modeset=0
 EOF
```
再起動
```
reboot
```

# __Cuda,cudnnの導入__
**バージョン情報**
 
Cuda：11.2
<br>
cudnn：8.1.1
<br>
nvidia-driver：520.61.05
<br>
<br>
## **Cudaインストール手順**
<br>
以下サイトから自分の環境を選択し、
<br>
https://developer.nvidia.com/cuda-11.2.2-download-archive

```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
```
まで行う。その後、指定したバージョンのCUDAをインストールする。
```
 sudo apt-get -y install cuda-11-1
```
<br>

### Pathの記載
.bashrcファイルに以下を記載し更新する

```
export CUDA_PATH=/usr/local/cuda
export PATH="/usr/local/cuda/bin:$PATH"
export LD_LIBRARY_PATH="/usr/local/cuda/lib64:$LD_LIBRARY_PATH"
```


## __注意1__

```
 sudo apt-get -y install cuda
```
でインストールを行うと指定したバージョンではなく最新バージョンのCUDAがインストールされるので注意

<br>

## __注意2__

nvidia-driverが同時にインストールされているかを確認する

```
nvidia-smi
```

<br>

## __注意3__
インストール中に"公開を利用できないため、以下の署名は検証できませんでした"と表示された場合以下サイトを参考に解決する。
<br>
https://www.ninton.co.jp/archives/5315
<br>
<br>


## **cudnnインストール手順**
以下サイトの"Download cuDNN v8.1.1 (Feburary 26th, 2021), for CUDA 11.0,11.1 and 11.2"以下の"cuDNN Runtime Library for Ubuntu18.04 aarch64sbsa (Deb)"と"
cuDNN Developer Library for Ubuntu18.04 aarch64sbsa (Deb)"をインストールする。
<br>
https://developer.nvidia.com/rdp/cudnn-archive
<br>

### __ファイルを解凍__

<br>
ファイルのあるディレクトリへ移動し以下コマンドで解凍する
```
 sudo dpkg -i ファイル名１ ファイル名２
```


## __注意__

ダウンロード時にhttps://developer.nvidia.com/rdp/cudnn-downloadでのログインが必要

<br>

## **Pythonのインストール**
```
sudo apt install python3.7
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 2
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.7 1
sudo update-alternatives --config python3  (python3.6と3.7が入っているか確認して3.7選択)
cd /usr/lib/python3/dist-packages
sudo ln -s apt_pkg.cpython-{36m,37m}-x86_64-linux-gnu.so
cd /usr/lib/python3/dist-packages/gi
sudo ln -s _gi.cpython-{36m,37m}-x86_64-linux-gnu.so
sudo apt-get install python3-dev
sudo apt install libpython3.7-dev
```

## **Gitのインストール**
```
cd ~
sudo apt install git 
```

## **Unreal Engineのインストール**
インストールするバージョンによってかなり変化があるので参考サイトを見て行ったほうが良い

以下サイト参考
<br>
https://carla.readthedocs.io/en/0.9.6/how_to_build_on_linux/
<br>

```
sudo apt-get update
sudo apt-get install wget software-properties-common
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key|sudo apt-key add -
sudo apt-add-repository "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-7 main"
sudo apt-get update
sudo apt-get install build-essential clang-7 lld-7 g++-7 cmake ninja-build libvulkan1 python python-pip python-dev python3-dev python3-pip libpng-dev libtiff5-dev libjpeg-dev tzdata sed curl unzip autoconf libtool rsync
sudo apt update

pip install --user setuptools
pip3 install --user setuptools

sudo update-alternatives --install /usr/bin/clang++ clang++ /usr/lib/llvm-7/bin/clang++ 170
sudo update-alternatives --install /usr/bin/clang clang /usr/lib/llvm-7/bin/clang 170

git clone --depth=1 -b 4.22 https://github.com/EpicGames/UnrealEngine.git ~/UnrealEngine_4.22
cd ~/UnrealEngine_4.22
./Setup.sh && ./GenerateProjectFiles.sh && make
cd ~/UnrealEngine_4.22/Engine/Binaries/Linux
./UE4Editor
```
開けたら閉じて良し

## **注意**

Unreal EnginのインストールするためにはUnreal Enginのページにログインしている必要がある

git cloneのためにsshで鍵を生成し登録する必要がある

参考サイト

https://sonnaka.com/git-clone-by-ssh/

## **Carlaのインストール**
<br>

```
cd ~
git clone https://github.com/carla-simulator/carla
cd ~/carla

git checkout e2c4dc1 (errorが出るけど無視して良い)

sudo apt-get install aria2

./Update.sh

export UE4_ROOT=~/UnrealEngine_4.22

make launch
make PythonAPI
make package
```
(carla/Dist/内のファイルを確認. ok:CARLA_0.9.6.tar.gz not:CARLA_0.9.9.tar.gz)
<br>
<br>
carlaディレクトリを名前を変えて新たに作成
以後、既存のcarlaディレクトリをcarla1、新たに作成したディレクトリをcarla2と表記
<br>
CARLA_0.9.6.tar.gzを展開してcarla1ディレクトリに展開
<br>
CARLA_0.9.6.tar.gzをダウンロードしてcarla2ディレクトリに展開
```
wget http://carla-assets-internal.s3.amazonaws.com/Releases/Linux/CARLA_0.9.6.tar.gz
```
<br>

carla1内のPythonAPI/carla/dist/以下の###-py3.6-eggと###-py3.7-eggの名前をメモし削除
<br>
carla2内のPythonAPI/carla/dist/###-py3.5-eggをcarla1内のPythonAPI/carla/dist/にメモした２つの名前に変更してコピー

```
sudo rm -fdr /usr/local/lib/python3.7/dist-package/*
sudo rm -fdr /usr/local/lib/python3.6/dist-package/*

sudo apt install pkg-config libopenblas-dev liblapack-dev libhdf5-serial-dev graphviz
sudo python3 -m pip install --upgrade pip
pip install pygame numpy opencv-python
pip3 install pygame numpy opencv-python tqdm
```

## **注意１**
git cloneを行う際は自分のgithubアカウントのIDとトークンが必要


## **注意２**
make launchで出るエラーが出た場合以下のサイトを参考にしてください
<br>
https://github.com/carla-simulator/carla/issues/2664

## **Carla動作確認**
```
./CarlaUE4.sh -opengl
```

```
DISPLAY= ./CarlaUE4.sh -opengl -carla-port=2000
```

```
./CarlaUE4.sh -windowed -carla-port=2000
```
(重いときは "-quality-level=Low" のオプションをつける)
<br>
Carlaが開ければ作業完了
<br>
<br>
<br>
<br>
# interp-e2eの導入
https://github.com/cjy1992/interp-e2e-driving を参考に導入を行う
<br>
Cuda, cudnnを変更したことによって起こる、他の変更点をいかに記す
<br>
- tensorflow-gpuのバージョンを2.5.0に変更変更
- tensorflow-probabilityのバージョンを0.12.1に変更
- tf-agentsのバージョンを0.7.1に変更

<br>

## エラー対応
- carlaを認識しない場合
    - egg ファイルへのパスが通っていない










