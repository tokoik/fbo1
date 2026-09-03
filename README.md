# fbo1 - シャドウマッピングで FBO を使ってみる サンプルプログラム

## 1. 概要

このプログラムは、OpenGL / GLSL における「シャドウマッピング (Shadow Mapping)」に「Framebuffer Object (FBO)」を適用し、デプステクスチャへの直接レンダリングを行う学生向けのサンプルプログラムです。本プログラムは、以下のブログ記事の解説に沿って学習を進めるための雛形として提供されています。

- [シャドウマッピングで FBO を使ってみる](https://tokoik.github.io/blog/2006-07-18.html)

従来の `glCopyTexSubImage2D()` によるデプスバッファからテクスチャへのコピーを省き、FBO のデプスアタッチメントにデプステクスチャを直接割り当てて光源視点からの深度値を描画します。これにより、ウィンドウサイズに依存しない高解像度のシャドウマップを効率よく生成できます。

## 2. ビルド方法

このプログラムは [CMake](https://cmake.org/) を用いてビルド環境を整備します。各OSとも、ソースコードが置かれているディレクトリにターミナル（またはコマンドプロンプト）で移動してから、以下の手順を実行してください。なお、プログラムをビルドするためのバイナリディレクトリは、バージョン管理ファイル（.gitignore）の設定に合わせて **build** という名前にします。

### 2.1 Windows (Visual Studio 2022 の場合)

1. コマンドプロンプトまたは PowerShell を開き、このプロジェクトのディレクトリに移動します。
2. 以下のコマンドを実行してビルドディレクトリを作成し、CMake で構成を行います。

   ```bat
   mkdir build
   cd build
   cmake .. -G "Visual Studio 17 2022"
   ```

3. 生成された build フォルダ内の fbo1.sln を Visual Studio で開きます。
4. ソリューションエクスプローラーで **fbo1** プロジェクトを右クリックし、「スタートアップ プロジェクトに設定」を選択します。
5. 「ローカル Windows デバッガー」をクリックするか、F5 キーを押してビルドおよび実行します。

### 2.2 macOS (Xcode の場合)

1. ターミナルを開き、このプロジェクトのディレクトリに移動します。
2. 以下のコマンドを実行してビルドディレクトリを作成し、Xcode 用のプロジェクトを生成します。

   ```sh
   mkdir build
   cd build
   cmake .. -G Xcode
   ```

3. 生成された build/fbo1.xcodeproj を Xcode で開きます。
4. 左上のスキーム選択（再生ボタンの横）が **fbo1** になっていることを確認します。
5. 「Run」ボタン（再生ボタン）をクリックするか、Command + R を押してビルドおよび実行します。

### 2.3 Ubuntu Linux

1. ターミナルを開き、このプロジェクトのディレクトリに移動します。
2. 必要なパッケージ（freeglut3-dev など）がインストールされていることを確認し、以下のコマンドでビルドします。

   ```sh
   mkdir build
   cd build
   cmake ..
   make
   ```

## 3. 使い方

### 3.1 プログラムの起動方法

- **Windows**: `build\Debug\fbo1.exe`
- **macOS**: `open build/Debug/fbo1.app` または Xcode 上で Run
- **Ubuntu Linux**: `cd build && ./fbo1`

### 3.2 操作方法

- **マウスの左ボタンでドラッグ**: 視点を 3 次元的に回転
- **キーボードの q, Q または ESC キー**: プログラムを終了

## 4. 解説

### 4.1 FBO によるデプステクスチャ直接レンダリング

```cpp
// FBO にデプステクスチャをアタッチ
glFramebufferTexture2DEXT(GL_FRAMEBUFFER_EXT, GL_DEPTH_ATTACHMENT_EXT,
                          GL_TEXTURE_2D, tex, 0);

// カラーバッファの読み書きを無効化
glDrawBuffer(GL_NONE);
glReadBuffer(GL_NONE);
```

カラーバッファを割り当てずにデプステクスチャのみをアタッチすることで、不要なカラー描画処理を省きながらシャドウマップを高速に生成します。
