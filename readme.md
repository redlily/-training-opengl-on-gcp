# Server 3D rendering with EGL and OpenGL.

<img src='image/rendering-10-40--10.png' />

EGLとOpenGLを使用してX Window Systemを使用しないサーバ・レンダリング。

## 概要

GPU搭載のLinuxサーバ上で3Dレンダリングを実現するための最小のシステム構成例と最小の3D描画のための実装を提供します。

## ディレクトリ構成

- main.cpp  
サーバ・レンダリングを実際に行うためのC++用のソースコード。
- math_utils.hpp  
3Dのための数学系ユーティリティの処理をまとめたC++用のヘッダファイル。
- model.h  
3Dモデルの情報を焼き込んだC++用のヘッダファイル。
- PC01_Kohaku.png  
3Dモデル用のテクスチャイメージ。
- shader.vs, shader.fs  
描画用の頂点シェーダとフラグメントシェーダ (ピクセルシェーダ)。
- image  
レンダリングされた画像を保存するためのディレクトリ、apache, nginx等でレンダリングサーバを作る場合は `chmod 0777` 等で権限を与えておいてください。
- ExportMesh.cc  
3Dモデルデータを出力するためのUnity用のソースファイル
- index.php  
レンダリングプログラムをWeb APIとして動作させるためのPHP用のサンプルプログラム。

## 3Dモデル・データ

ローポリ ユニティちゃん

http://unity-chan.com/download/releaseNote.php?id=LowPolyUnityChan

これをExportMesh.ccを使用してmodel.hを生成しています。

## インストール・パッケージ

- NVIDIAのGPUドライバ  
EGLを動作させるためのパッケージ、インストール手順はOSやハードウェア構成によって違うのでNVIDIAの公式ページを参照。
- EGL  
ハードウェアとOpenGLをつなげるためのAPI。
- OpenGL ES 2.0  
3D描画を行うためのAPI、その中でも組み込み系に特化したパッケージ。
- g++  
C/C++コンパイラ。
- png++  
PNGの読み書き用のライブラリ。

## 環境構築例

### Ubuntu 16.04

#### 環境

GCPインスタンス

- CPU vCPU x 1
- メモリ 3.75GB
- GPU NVIDIA tesla K80 x 1
- OS Ubuntu 16.04 Minimal or CentOS 7

#### インストール

```bash
# NVIDIAのGPUドライバ用のリポジトリ追加

$ wget http://us.download.nvidia.com/tesla/396.44/nvidia-diag-driver-local-repo-ubuntu1604-396.44_1.0-1_amd64.deb
$ dpkg -i nvidia-diag-driver-local-repo-ubuntu1604-396.44_1.0-1_amd64.deb
$ apt-key add /var/nvidia-diag-driver-local-repo-396.44/7fa2af80.pub
$ apt-get update

# GPUドライバーインストール

$ apt-get install cuda-drivers

# 再起動

$ reboot

# 各種必要なパッケージのインストール

$ apt-get install g++
$ apt-get install libpng++-dev
$ apt-get install libgles2-mesa-dev libegl1-mesa-dev
$ apt-get install apache2
```

### CentOS 7

```bash
# NVIDIAのGPUドライバ用のリポジトリ追加

$ curl -O http://us.download.nvidia.com/tesla/384.145/nvidia-diag-driver-local-repo-rhel7-384.145-1.0-1.x86_64.rpm
$ rpm -i nvidia-diag-driver-local-repo-rhel7-384.145-1.0-1.x86_64.rpm
$ yum clean all
$ yum install epel-release -y
$ yum update -y

# GPUドライバーインストール

$ yum install cuda-drivers

# 再起動

$ reboot

# 各種必要なパッケージのインストール

$ yum install gcc-c++
$ yum install libpng-devel
$ yum install mesa-libGLES-devel mesa-libEGL-devel
$ yum install httpd

# PNG++のインストール

$ curl -L -O http://download.savannah.nongnu.org/releases/pngpp/png++-0.2.9.tar.gz
$ tar -zxf png++-0.2.9.tar.gz
$ cd png++-0.2.9
$ make
$ make install
```

追加予定

## レンダラーのコンパイルと実行

### コンパイル

```
$ g++ -o main main.cpp -lEGL -lGLESv2 -lpng
```

### 実行

```
$ ./main ${x軸の回転角} ${y軸の回転角} ${z軸の回転角}
```

実際にレンダリングされた画像はimageディレクトリに `image/rendering-${x}-${y}-${z}.png` の形式で保存されます。

## 参考文献

https://devblogs.nvidia.com/egl-eye-opengl-visualization-without-x-server/

X Serverを使用しないEGLによるOpenGLの初期化と、その他にここで触れていないGPUクラスタでのEGLの初期化例が書かれています。

## ライセンス

MIT

### 3Dモデルデータ、及びテクスチャデータ

http://unity-chan.com/contents/guideline/
