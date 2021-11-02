# C++ CMake template  Gitpod
1. 環境構築 | Web IDEである　[Gitpod](https://www.gitpod.io/docs/getting-started)　の導入

2. Gitpod 導入
   - 環境構築を簡略化
   - Mac でもC, C++ 環境を整えられるかどうか

This is a [C++ CMake](https://clang.llvm.org/) template configured for ephemeral development environments on [Gitpod](https://www.gitpod.io/).

### 使用しないフレームワークを削除

オプションのGUI /グラフィックフレームワーク（fltk、gtkmm、imguiなど）を1つ以上使用しないことがわかっている場合は、次のコマンドでそれらを削除できます `git rm`:

    $ git rm -r src/<unnecessary_framework>

#### GUI libraries

このプロジェクトは、いくつかのオプションのGUIフレームワークで動作するように作成できます。
必要に応じて、ここにリンクされているドキュメントの指示に従って、次のオプションの依存関係をインストールする必要がある

- [FLTK](https://www.fltk.org/doc-1.4/index.html)
- [GTKMM](https://www.gtkmm.org/en/documentation.html)
- [QT](https://doc.qt.io/)

以下の依存関係は、CMakeとConanによって自動的にダウンロードできます。それらをインストールするために必要なのは、構成中にCMakeフラグをオンにすることだけです。それらの使用に問題が発生した場合は、ここにリンクされているドキュメントを参照

- [NANA](http://nanapro.org/en-us/documentation/)
- [SDL](http://wiki.libsdl.org/FrontPage)
- [IMGUI](https://github.com/ocornut/imgui/tree/master/docs):
このフレームワークはSFMLに依存しており、Linuxを使用している場合は、パッケージマネージャーを使用してSFMLの依存関係のいくつかをインストールする必要がある場合があります。詳細について[utorial](https://www.sfml-dev.org/tutorials/2.5/compile-with-cmake.php) を参照

## Build 手順

### Build directory を作成
```
mkdir build
```
### 環境変数を使用してコンパイラを指定します

デフォルトでは（環境変数 `CC` とを設定しない場合 `CXX`）、システムのデフォルトコンパイラが使用される

ConanとCMakeは、環境変数CCとCXXを使用して、使用するコンパイラーを決定します。したがって、競合の問題を回避するには、これらの変数を使用してコンパイラーのみを指定

CMakeは、各Conanターゲットのビルドに使用されたコンパイラを検出します。1つのコンパイラですべてのConanターゲットをビルドしてから、別のコンパイラでCMakeターゲットをビルドすると、プロジェクトのビルドに失敗する場合がある

環境変数はで事前定義されている `.gitpod.Dockerfile`.

### ビルドを構成

プロジェクトを構成してmakefileを書き込むに `cmake` は、一連のコマンドラインオプションを使用できます。より簡単なオプションは、cmakeをインタラクティブに実行すること

#### **cmake-guiを介して構成**:

1) プロジェクトディレクトリからcmake-guiを開く:
```
cmake-gui .
```
2) ビルドディレクトリを設定:

![build_dir](https://user-images.githubusercontent.com/16418197/82524586-fa48e380-9af4-11ea-8514-4e18a063d8eb.jpg)

3) ジェネレーターを構成:

cmake-guiで、上部のメニューからを選択 `Tools/Configure`.

**Warning**: 警告：オプションを設定 `CC` して `CXX` 常に選択している `use default native compilers` 場合。これはピック `CC` と `CXX`. この段階ではコンパイラを変更しない!

<details>
<summary>Windows - MinGW Makefiles</summary>

ジェネレーターとしてMinGWMakefilesを選択します。:

<img src="https://user-images.githubusercontent.com/16418197/82769479-616ade80-9dfa-11ea-899e-3a8c31d43032.png" alt="mingw">

</details>

<details>
<summary>Cmakeオプションを選択して、以下を生成しますr</summary>

You should have already set `C` and `CXX` to `cl.exe`.

ジェネレーターとして「VisualStudio162019」を選択します。:

<img src="https://user-images.githubusercontent.com/16418197/82524696-32502680-9af5-11ea-9697-a42000e900a6.jpg" alt="default_vs">

</details>

<details>

<summary>Windows-VisualStudioジェネレーターとClangコンパイラー</summary>

 `C` と `CXX` を設定しておく必要が `clang.exe` あり `clang++.exe`ます

ジェネレーターとして「VisualStudio162019」を選択します。Visual Studioに使用するように指示するには `clang-cl.exe`:
- Visual Studioに付属のLLVMを使用する場合 `ClangCl` 「使用するオプションのツールセット」の下に書き込みます。

<img src="https://user-images.githubusercontent.com/16418197/82781142-ae60ac00-9e1e-11ea-8c77-222b005a8f7e.png" alt="visual_studio">

- 外部LLVMを使用する場合 [`LLVM_v142`](https://github.com/zufuliu/llvm-utils#llvm-for-visual-studio-2017-and-2019)
 使用するオプションのツールセット」の下に書き込みます.

<img src="https://user-images.githubusercontent.com/16418197/82769558-b3136900-9dfa-11ea-9f73-02ab8f9b0ca4.png" alt="visual_studio">

</details>
<br/>

4) Cmakeオプションを選択して、以下を生成:

![generate](https://user-images.githubusercontent.com/16418197/82781591-c97feb80-9e1f-11ea-86c8-f2748b96f516.png)

#### **ccmakeを介して構成**:
Cmake Cursesダイアログコマンドラインツールを使用すると、次のようになります:

    ccmake -S . -B ./build

`ccmake` セットアップが完了したら、「c」を押してプロジェクトを構成し、「g」を押して生成し、「q」を押して終了します

### Build
使用するすべてのオプションを選択したら、プロジェクト（すべてのターゲット）をビルドできます:

    cmake --build ./build

Visual Studioの場合、次のようなビルド構成（Release、RelWithDeb、Debugなど）を指定します:

    cmake --build ./build -- /p:configuration=Release

## トラブルシューティング

### Update Conan
Conanを更新することで、ユーザーが抱える多くの問題を解決できるため、このプロジェクトで問題が発生した場合は、まずそれを実行する必要があります。

コナンを更新するには:

    $ pip install --user --upgrade conan

プラットフォームによって `pip3` または `pip` iこのコマンドの代わりにを使用する必要がある

### Conan　キャッシュをクリアする
Conanの依存関係で引き続き問題が発生する場合は、Conanキャッシュをクリアしてみてください

    $ conan remove -f '*'

次回 `cmake` またはを実行する `cmake --build` と、Conanの依存関係が再構築されます。システムのデフォルトコンパイラを使用していない場合は、上記の「代替コンパイラを使用したビルド」セクションで説明されているように、CC、CXX、CMAKE_C_COMPILER、およびCMAKE_CXX_COMPILER変数を設定することを忘れないでください。

### コナンの依存関係の設定ミスの特定

別の依存関係「B」の特定のバージョンを必要とする依存関係「A」があり、プロジェクトが誤ったバージョンの依存関係「B」を使用しようとしている場合、CMakeを実行すると、Conanはこの構成エラーに関する警告を生成します。これらの警告は、プロジェクトのサイズに応じて、数百行または数千行の出力の間で簡単に失われる可能性があります。

プロジェクトにコナン構成エラーがある場合は、それを使用 `conan info` して見つける `conan info` プロジェクトの依存関係グラフに関する情報を、一部の端末で色付きの出力とともに表示:

    $ cd build
    $ conan info .

`conan info` の出力の最初の数行に、プロジェクトのすべての構成警告が明るい黄色のフォントで表示されます。

たとえば、パッケージ `spdlog/1.5.0` はパッケージに依存します `fmt/6.1.2`.
などの `cmake/Conan.cmake` 以前のバージョンが必要になるようにファイルを変更して `fmt`, `fmt/6.0.0`,から、次のコマンドを実行する場合:

    $ conan remove -f '*'       # clear Conan cache
    $ rm -rf build              # clear previous CMake build
    $ mkdir build && cd build
    $ cmake ..                  # rebuild Conan dependencies
    $ conan info .

...出力の最初の行は `spdlog` より新しいバージョンのを必要とする警告になります `fmt`.

### テスト
参照 : [Catch2 tutorial](https://github.com/catchorg/Catch2/blob/master/docs/tutorial.md)

### Fuzz testing

参照 : [libFuzzer Tutorial](https://github.com/google/fuzzing/blob/master/tutorial/libFuzzerTutorial.md)
