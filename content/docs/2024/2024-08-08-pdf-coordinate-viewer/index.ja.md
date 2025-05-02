---
title: "ライブPDF座標ビューア"
description: "PDF編集をより簡単に"
date: "2024-08-08"
draft: false
author: "Kean Teng Blog"
tags: ["座標", "コーディング"]
weight: 5
summary: "行きたい場所の正確な位置がわからないとイライラしますよね。それと同じように、PDFフォームの正しい位置にテキストを挿入したり、請求書に画像を適切に配置したりするために、PDFの座標がわからずコードを何度も再実行しなければならないのも、非常に煩わしいものです。"
---

<center><img src="https://images.unsplash.com/photo-1642013352168-ccb46a0ac67f?q=80&w=1771&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"  class = "center"/></center>
<p style="text-align: center; color:grey;"><i>画像: Unsplashより</i></p>

特定の場所に行きたいときに、その場所がわからないと本当にイライラしますよね。同様に、PDFフォームの正しい場所にテキストを入力したり、請求書に画像を適切に配置したりするためだけに、コードを数回再実行しなければならない場合、PDFの座標がわからないのは非常に煩わしいことです。

[PDF-Lib](https://pdf-lib.js.org/) というツールを使って請求書生成アプリを作成していたとき、まさにそう感じました。というのも、PDFドキュメント上の目的の場所にテキストを適切に配置するためだけに、コードを何度も再実行する必要があったからです。

オンラインで情報を調べてみました。「PDF 座標 ビューア」、「PDF ドキュメント 座標 取得方法」など、多くのキーワードで検索しましたが、見つかった解決策は、単にPDFの座標を表示するためだけにソフトウェアのインストールや購入を求めるものばかりでした。2024年にもなって、この機能を提供するウェブサイトがないことに驚きました！とにかく、自分で解決策を見つけ出す必要がありました。

この問題を解決するには、PDFの座標系が少し異なる動作をすることを理解する必要があります。たとえば、画像をデカルト平面（直交座標系）に配置すると考えると、その画像の原点は左上隅になります。しかし、PDFの場合は左下隅が原点です。この情報は、正しいPDF座標を得るために変換を行う必要があり、画面上のカーソル座標をそのまま使用することはできないため、非常に重要です。

変換は次のように行うことができます：

```
pdf_x = (canvas_x / canvas_width) * page_width
pdf_y = (canvas_height - canvas_y) / canvas_height * page_height
```

基本的に、この式はキャンバス上の座標（たとえば画面上のカーソル座標）を正規化し、キャンバスの高さまたは幅に沿った小数での位置を表します。もちろん、この式はキャンバスとPDFドキュメントの原点の違いも考慮します。次に、これらの値を実際のPDFの高さと幅に合わせてスケーリングします。

## Pythonプログラム

この式を使えば、Pythonプログラムを作成できます。このプログラムは、画面上でカーソルを追跡できるようにするものです。画面には対象のPDFドキュメントを表示する必要があり、画面をクリックすると、変換されたカーソル座標がPDF座標として表示されます：

![Alt Text](https://github.com/keanteng/live-pdf-coordinate/blob/main/demo.gif?raw=true)

PDFドキュメントを扱うために `PyMuPDF` ライブラリを使用します。次のようにインストールする必要があります：

```py
py -m pip install PyMuPDF tk
```

プログラムは以下の通りです：

```py
import fitz  # PyMuPDF
import tkinter as tk
from PIL import Image, ImageTk


# キャンバス上のマウス位置を取得する関数
def on_click(event):
    canvas_x, canvas_y = event.x, event.y
    print(f"キャンバス上のカーソル位置: ({canvas_x}, {canvas_y})")

    # キャンバス座標をPDF座標にマッピング
    pdf_x = (canvas_x / canvas_width) * page_width
    pdf_y = (
        (canvas_height - canvas_y) / canvas_height * page_height
    )  # Y軸の反転を調整
    print(f"マッピングされたPDF座標: ({pdf_x}, {pdf_y})")


# PDFをロードして最初のページを取得、エラーがあれば表示
try:
    pdf_path = "sample.pdf"  # 対象のPDFファイルパス
    document = fitz.open(pdf_path)
    page = document.load_page(0)  # 最初のページを読み込む
except Exception as e:
    print(f"PDFの読み込みエラー: {e}")
    exit() # エラーがあれば終了するなどの処理を追加すると良い

#pdf_path = input("PDFファイルのパスを入力してください: ") # ユーザーに入力を促す場合

# 再度開く必要がある場合 (エラーハンドリングによる)
# document = fitz.open(pdf_path)
# page = document.load_page(0)

# ページを画像にレンダリング
pix = page.get_pixmap()
img = Image.frombytes("RGB", [pix.width, pix.height], pix.samples)

# tkinterウィンドウを作成
root = tk.Tk()
root.title("PDFビューア")

# 画像をtkinterが使用できる形式に変換
tk_img = ImageTk.PhotoImage(img)

# キャンバスを作成し、画像を追加
canvas = tk.Canvas(root, width=pix.width, height=pix.height)
canvas.pack()
canvas.create_image(0, 0, anchor=tk.NW, image=tk_img)

# マウスクリックイベントをon_click関数にバインド
canvas.bind("<Button-1>", on_click)

# キャンバスの寸法を取得（画像寸法と同じ）
canvas_width, canvas_height = pix.width, pix.height

# PDFページの寸法を取得
page_width = page.rect.width
page_height = page.rect.height
print(f"PDFページ寸法: {page_width}x{page_height}")

# tkinterメインループを開始
root.mainloop()

```

> [Github](https://github.com/keanteng/live-pdf-coordinate) へのリンク
```