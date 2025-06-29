# NTUB Thesis LaTex Template v1 Chinese Version (2025/07)

- [ ] **2025/07 確認格式可以被接受後發佈模板 [Overleaf-NTUB-Template]()**

本範本是由 NTUST Thesis template 2.0.1 (Chinese Version) latex 範本改編而來

## 📌 免責聲明與使用須知

本 LaTeX 模板依據 **國立臺北商業大學資訊管理系人工智慧與商業應用碩士班論文格式**需求，參考並改寫自 NTUST 與 NCKU 之公開模板版本，**並非官方提供範本**。本範本已盡力符合撰寫格式要求，惟各系所或指導教授對格式可能仍有個別調整要求，請使用者**務必自行確認所屬學院與系所之最新論文格式規範**，如有不符請自行修改。

## 修改內容

1. 封面格式

2. 刪除無用的頁面

3. 章節格式

4. 參考文獻分成中英部分

5. 版面配置更新(使用 Thesis NCKU latex 的 yzu_report.cls 2008/12/16 v0.3)

6. 更改第 5 點 cls(更改目錄空白間隔)

```tex
692 % \newcommand*{\l@chapter}{\@dottedtocline{0}{0em}{\tocChNumberWidth}}
693 \newcommand*{\l@chapter}{\@dottedtocline{0}{0em}{4em}}%第二個 em 是中間的間隔
694 % \newcommand*\l@section{\@dottedtocline{1}{1.5em}{2.3em}}
695 \newcommand*\l@section{\@dottedtocline{1}{0.9em}{4em}}%改成中文節前面有空格要消除，第二個 em 是中間的間隔
712 \newcommand\*\l@figure{\@dottedtocline{1}{0em}{2.3em}}% 圖跟表目錄不用空格
```

7. 移除一些 Logs 提示

8. 內文為新細明體，標題為標楷體(字型用字型檔案匯入，不然不準確)

9. ctex 設定 fontset=none 因本地端字型有 Bug，且也不需用到 ctex 字體，主要用在修改成章跟節(目錄以及標題)

10. \newcommand{\mybaselinestretch}{2} %行距 1.5 倍 --> 設定 2 看起來比較像 1.5 倍，以此類推

```tex
\usepackage[fontset=none, UTF8, heading=true]{ctex}
```

[ctex-kit issue #514](https://github.com/CTeX-org/ctex-kit/issues/514)

11. 修改圖跟表的標題格式

```tex
\long\def\@makecaption#1#2{%
  \vskip\abovecaptionskip
  \sbox\@tempboxa{#1\space\space\space\space#2}%
  \ifdim \wd\@tempboxa >\hsize
    #1\space\space\space\space#2\par
  \else
    \global \@minipagefalse
    \hb@xt@\hsize{\hfil\box\@tempboxa\hfil}%
  \fi
  \vskip\belowcaptionskip}
```

12. 頁碼格式調整

```tex
\pagestyle{plain}  % 前幾頁要顯示 小寫羅馬數字 故不套用 fancyhdr
```

```tex
\usepackage{fancyhdr} % 引入 fancyhdr 套件
\usepackage{lastpage} % 引入 lastpage 套件來獲取最後一頁的頁碼

\pagestyle{fancy}
\fancyhf{}
\fancyfoot[C]{\ifnum\value{page}>0 第 \thepage 頁，共 \pageref{LastPage} 頁\fi}

% 確保 plain 頁碼格式（如章節首頁）也適用 fancyhdr
\fancypagestyle{plain}{%
    \fancyhf{}
    \fancyfoot[C]{第 \thepage 頁，共 \pageref{LastPage} 頁}
}
```

13. 新增 biblatex 格式客製化（中英分開、符合社會科學論叢格式）

為了符合《社會科學論叢》要求，新增並調整 `biblatex` 的格式如下：

- 載入 `biblatex` 套件並使用 `authoryear` 樣式（符合作者-年份格式），文獻依作者排序（`nyt = name, year, title`），保留 URL 欄位：

  ```tex
  \usepackage[style=authoryear,sorting=nyt,url=true]{biblatex}
  \addbibresource{my_bib.bib}
  ```

- 隱藏 `type` 欄位（例如 Doctoral dissertation 不顯示）：

  ```tex
  \DeclareFieldFormat{type}{}
  ```

- 中文標點定義：

  ```tex
  \newcommand{\zhcomma}{，}
  \newcommand{\zhperiod}{。}
  ```

- `@miscc` 條目只處理 `keywords=chinese` 的文獻（避免英文文獻誤套格式）：

  ```tex
  \DeclareBibliographyDriver{miscc}{%
    \usebibmacro{bibindex}%
    \usebibmacro{begentry}%
    \ifkeyword{chinese}{%
      \printnames{author}\zhcomma
      \printfield{year}\zhcomma
      「\printfield{title}」\zhcomma
      \printfield{note}%
      \zhperiod
    }%
  }
  ```

- 調整英文引用格式中 `title` 欄位不使用斜體、以直引號 “ ” 包住：

  ```tex
  \DeclareFieldFormat[article,inproceedings,online]{title}{“#1”}
  ```

- 設定年份加上 a/b/c 識別（同年多文獻情形）

  ```tex
  \DeclareLabeldate{%
    \field{year}
    \field{extradate}  % ❗extrayear 已棄用，改用 extradate
  }
  \DeclareFieldFormat{labelyear}{#1\printfield{extradate}}
  ```

- 調整 `@article` 格式：

  ```tex
  \DeclareBibliographyDriver{article}{%
    \usebibmacro{bibindex}%
    \usebibmacro{begentry}%
    \printnames{author}\adddot\addspace%
    \printfield{labelyear}\adddot\addspace
    \printfield{title}\adddot\addspace%
    \printfield{journaltitle}\addcomma\addspace%
    \printfield{volume}%
    \iffieldundef{number}{}{%
      \addcomma\addspace%
      no.~\printfield{number}%
    }%
    \addcomma\addspace%
    pp.~\printfield{pages}%
    \finentry%
  }
  ```

- 調整 `@inproceedings` 格式：

  ```tex
  \DeclareBibliographyDriver{inproceedings}{%
    \usebibmacro{bibindex}%
    \usebibmacro{begentry}%
    \printnames{author}\adddot\addspace%
    \printfield{labelyear}\adddot\addspace
    \printfield{title}\addcomma\addspace%
    \printfield{booktitle}%
    \iffieldundef{publisher}{}{%
      \addcomma\addspace%
      \printlist{publisher}%
    }%
    \addcomma\addspace%
    \printfield{pages}%
    \finentry%
  }
  ```

- 調整 `@online` 格式：

  ```tex
  \DeclareBibliographyDriver{online}{%
    \usebibmacro{bibindex}%
    \usebibmacro{begentry}%
    \printnames{author}\adddot\addspace%
    \printfield{labelyear}\adddot\addspace
    \printfield{title}\adddot\addspace%
    original source: \url{\thefield{url}}\adddot\addspace%
    \printfield{note}%
    \finentry%
  }
  ```

- 英文欄位用語微調，去除 "in"：

  ```tex
  \DefineBibliographyStrings{english}{
    in = {}
  }
  ```

## 使用方法

在進行論文寫作時，只需改變所有檔名為 `my_XXXXXXX.XXX` 的文件。

- 主結構檔：`my_ntub_thesis.tex`
- 需修改部分：`frontpages`（封面分出來為 `cover`）及 `backpages` 的文件
- 論文主結構及內容：請分類放置於 `sections` 目錄中
- 參考文獻的 bibtex 檔：請放置於目錄第一層的 `my_bib.bib`

### 浮水印設定

- 位於 `common_env` 內第 89 行
- 等待學校審核完論文後才能下載浮水印檔案

  ```tex
  % \input{watermark/ntub_watermark.tex}
  ```

## 版本資訊

- **版本**：v1 (2025/07)
- **狀態**：可以成功執行於 Overleaf Online 以及 VSCode + TeXLive 2024（Windows 和 macOS）

## VSCode 設定

[latex-in-vscode 相關教學](https://hackmd.io/@zxcj04/latex-in-vscode)

```json
"latex-workshop.latex.recipes": [
  {
    "name": "xelatex",
    "tools": ["xelatexmk"]
  },
  {
    "name": "xelatex -> bibtex -> xelatex * 2",
    "tools": ["xelatexmk", "bibtex", "xelatexmk", "xelatexmk"]
  },
  {
    "name": "bibtex",
    "tools": ["bibtex"]
  },
  {
    "name": "pdflatex",
    "tools": ["pdflatex"]
  }
],

"latex-workshop.latex.clean.fileTypes": [
  "*.aux",
  "*.bbl",
  "*.blg",
  "*.idx",
  "*.ind",
  "*.lof",
  "*.lot",
  "*.out",
  "*.toc",
  "*.acn",
  "*.acr",
  "*.alg",
  "*.glg",
  "*.glo",
  "*.gls",
  "*.ist",
  "*.fls",
  "*.log",
  "*.fdb_latexmk"
]
```
