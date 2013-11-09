# `Schema.yaml` Instructions
========

### 開始之前
---
	
	Rime schema
	encoding: utf-8

### 描述檔
---

1. `name:` 此處爲方案的顯示名偁〔既出現於方案選單中以示人的，通常爲中文〕
2. `schema_id:` 此處爲方案內部名，在代碼中引用此方案時以此名爲正，通常由英文、數字、下劃線組成
3. `author:` 這裏爲發明人、撰寫者。如果您對方案做出了修改，請保留原作者名，並將自己的名字加在後面。
4. `description:` 請簡要描述方案歷史、碼表來源、該方案規則等
5. `dependencies:` 如果本方案依賴於其它方案〔通常來說會依頼其它方案做爲反查，抑或是兩種或多種方案混用時〕
6. `version:` 版本號，在發佈新版前請確保已陞版本號

   #### 示例

	```
schema:
  name: "蒼頡檢字法"
  schema_id: cangjie6
  author:
    - "發明人 朱邦復先生、沈紅蓮女士"
  dependencies:
    - luna_pinyin
    - jyutping
    - zyenpheng
  description: "第六代倉頡輸入法\x0a碼表由雪齋、惜緣和crazy4u整理\x0a"
  version: 0.19
```

### 開關
---

通常包含以下四個

1. `ascii_mode` 是中英文轉換開關。預設`0`爲英文，`1`爲中文
2. `full_shape` 是全角符號／半角符號開關。注意，開啓全角時英文字母亦爲全角。預設`0`爲半角，`1`爲全角
3. `extended_charset` 是字符集開關。`0`爲小字符集，`1`爲不限制字符集
4. `simplification` 是簡化字開關。一般情況下與上同，`0`爲不開啓簡化，`1`爲簡化。
  - 此選項名偁可自定義，亦可添加多套替換用字方案：
   
	```
  - name: zh_cn
    states: ["漢字", "汉字"]
    reset: 0
```
或
```
  - options: [ zh_trad, zh_cn, zh_mars ]
    states:
      - 字型 → 漢字
      - 字型 → 汉字
      - 字型 → 灘茡
    reset: 0
```
  - `states:` 可不寫，如不寫則此開關存在但不可見，可由快捷鍵操作
  - `reset:` 設定默認狀態，`0`爲英文，`1`爲中文。〔`reset`可不寫，此時切換窗口時不會重置到默認狀

   ####示例

	```
switches:
  - name: ascii_mode
    states: ["中文", "西文"]
  - name: full_shape
    states: ["半角", "全角"]
  - name: extended_charset
    states: ["通用", "增廣"]
  - name: simplification
    states: ["漢字", "汉字"]
```

### 引擎
---

引擎分四組：

#### 一、`processors` 
  - 這批組件處理各類按鍵消息

1. `ascii_composer` 處理西文模式及中西文切
2. **`recognizer`** 與`matcher`搭配，處理符合特定規則的輸入碼，如網址、反查等`tags`
3. **`key_binder`** 在特定條件下將按鍵綁定到其他按鍵，如重定義逗號、句號爲候選翻頁、開關快捷鍵等
4. **`speller`** 拼寫處理器，接受字符按鍵，編輯輸入
5. **`punctuator`** 句讀處理器，將單個字符按鍵直接映射爲標點符號或文字
6. `selector` 選字處理器，處理數字選字鍵〔可以換成別的哦〕、上、下候選定位、換頁
7. `navigator` 處理輸入欄內的光標移動
8. `express_editor` 編輯器，處理空格、回車上屏、回退鍵
9. *`fluency_editor`* 句式編輯器，用於以空格斷詞、回車上屏的【注音】、【語句流】等輸入方案，替換` express_editor`
10. *`chord_composer`* 和絃作曲家或曰並擊處理器，用於【宮保拼音】等多鍵並擊的輸入方案

#### 二、`segmentors`
  - 這批組件識別不同內容類型，將輸入碼分段並加上`tag`

1. `ascii_segmentor` 標識西文段落〔譬如在西文模式下〕字母直接上屛
2. `matcher` 配合`recognizer`標識符合特定規則的段落，如網址、反查等，加上特定`tag`
3. **`abc_segmentor`** 標識常規的文字段落，加上`abc`這個`tag`
4. **`punct_segmentor`** 標識句讀段落〔鍵入標點符號用〕加上`punct`這個`tag`
5. `fallback_segmentor` 標識其他未標識段落
6. **`affix_segmentor`** 用戶自定義`tag`
  - 此項可加載多個實例，後接`@`+`tag`名
#### 三、`translators`
  - 這批組件翻譯特定類型的編碼段爲一組候選文字

1. `echo_translator` 沒有其他候選字時，回顯輸入碼〔輸入碼可以`Shift`+`Enter`上屛〕
2. `punct_translator` 配合**`punct_segmentor`**轉換標點符號
3. **`table_translator`** 碼表翻譯器，用於倉頡、五筆等基於碼表的輸入方案
  - 此項可加載多個實例，後接`@`+翻譯器名〔如：`cangjie`、`wubi`等〕
4. **`script_translator`** 腳本翻譯器，用於拼音、粵拼等基於音節表的輸入方案
  - 此項可加載多個實例，後接`@`+翻譯器名〔如：`pinyin`、`jyutping`等〕
5. *`reverse_lookup_translator`* 反查翻譯器，用另一種編碼方案查碼

#### 四、`filters`
  - 這批組件過濾翻譯的結果

1. **`simplifier`** 用字轉換
2. `uniquifier` 過濾重複的候選字，有可能來自**`simplifier`**
3. **`reverse_lookup_filter`** 反查濾鏡，以更靈活的方式反查，Rime1.0後替代*`reverse_lookup_translator`*
  - 此項可加載多個實例，後接`@`+濾鏡名〔如：`pinyin_lookup`、`jyutping_lookup`等〕
