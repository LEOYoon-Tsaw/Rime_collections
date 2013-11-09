# `Schema.yaml` 詳解
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
4. `punct_segmentor` 標識句讀段落〔鍵入標點符號用〕加上`punct`這個`tag`
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

   ####示例
   
   ```
   engine:
     processors:
       - ascii_composer
       - recognizer
       - key_binder
       - speller
       - punctuator
       - selector
       - navigator
       - express_editor
     segmentors:
       - ascii_segmentor
       - matcher
       - affix_segmentor@pinyin
       - affix_segmentor@jyutping
       - affix_segmentor@pinyin_lookup
       - affix_segmentor@jyutping_lookup
       - affix_segmentor@reverse_lookup
       - abc_segmentor
       - punct_segmentor
       - fallback_segmentor
     translators:
       - punct_translator
       - table_translator
       - script_translator@pinyin
       - script_translator@jyutping
       - script_translator@pinyin_lookup
       - script_translator@jyutping_lookup
     filters:
       - simplifier@zh_simp
       - uniquifier
       - reverse_lookup_filter@middle_chinese
       - reverse_lookup_filter@pinyin_reverse_lookup
       - reverse_lookup_filter@jyutping_reverse_lookup
```

### 細項配置
---

引擎中所舉之加粗者均可在下方詳細描述，格式爲：

```
name:
  branches: configurations
```
或
```
name:
  branches:
    - configurations
```
#### 一、`speller`

1. `alphabet:` 定義本方案輸入鍵
2. `delimiter:` 上屛時的音節間分音符
3. `algebra:` 拼寫運算規則，由之算出的拼寫匯入`prism`中
4. `max_code_length:` 形碼最大碼長，超過則頂字上屛
5. `auto_select:` 自動上屛
6. `auto_select_unique_candidate:` 和`auto_select:`配合使用，形碼無重碼自動上屛
7. `use_space:` 以空格作輸入碼

    ```
    speller的演算包含：
       xform --改寫〔不保留原形〕
       derive --衍生〔保留原形〕
       abbrev --簡拼〔出字優先級較上兩組更低〕
       fuzz --畧拼〔此種簡拼僅組詞，不出單字〕
       xlit --變換〔適合大量一對一變換〕
       erase --刪除
```

   ####示例
   ```
   speller:
     alphabet: zyxwvutsrqponmlkjihgfedcba
     delimiter: " '"
     algebra:
       - erase/^xx$/
       - abbrev/^([a-z]).+$/$1/
       - abbrev/^([zcs]h).+$/$1/
       - derive/^([nl])ve$/$1ue/
       - derive/^([jqxy])u/$1v/
       - derive/un$/uen/
       - derive/ui$/uei/
       - derive/iu$/iou/
       - derive/([aeiou])ng$/$1gn/
       - derive/([dtngkhrzcs])o(u|ng)$/$1o/
       - derive/ong$/on/
       - derive/ao$/oa/
       - derive/([iu])a(o|ng?)$/a$1$2/
```

#### 二、`segmentor`
  * `segmentor`配合`recognizer`標記出`tag`。這裏會用到`affix_segmentor`和`abc_translator`。`tag`用在`translator`、`reverse_lookup_filter`、`simplifier`中用以標定各自作用範圍。如果不需要用到`extra_tags`則不需要單獨配置`segmentor`

1. `tag:` 設定其`tag`
2. `prefix:` 設定其前綴標識，可不塡，不塡則無前綴
3. `suffix:` 設定其尾綴標識，可不塡，不塡則無尾綴
4. `tips:` 設定其輸入前提示符，可不塡，不塡則無提示符
5. `closing_tips:` 設定其結束輸入提示符，可不塡，不塡則無提示符
6. `extra_tags:` 爲此`segmentor`所標記的段落插上其它`tag`

   *當`affix_segmentor`和`translator`重名時，兩者可併在一處配置，此處1-5條對應下面16-20條。`abc_segmentor`僅可設`extra_tags`*

   ####示例
   
   ```
   reverse_lookup:
     tag: reverse_lookup
     prefix: "`"
     suffix: ";"
     tips: "【反查】"
     closing_tips: "【蒼頡】"
     extra_tags: 
       - pinyin_lookup
       - jyutping_lookup
```

#### 三、`translator`
  - 每個方案有一個主`translator`，在引擎列表中不以`@`+翻譯器名定義，在細項配置時直接以`translator:`命名。以下加粗項爲可在主`translator`中定義之項，其它可在副〔以`@`+翻譯器名命名〕`translator`中定義

1. **`enable_charset_filter:`** 是否開啓字符集過濾
2. **`enable_sentence:`** 是否開啓自動造句
3. **`enable_encoder:`** 是否開啓自動造詞〔對`table`translator`有效〕
4. **`encode_commit_history:`** 是否對已上屛詞自動成詞〔對`table`translator`有效〕
5. **`max_phrase_length:`** 最大自動成詞詞長〔對`table`translator`有效〕
6. **`enable_user_dict:`** 是否開啓用戶詞典〔用戶詞典記錄動態字詞頻、用戶詞〕
7. **`disable_user_dict_for_patterns:`** 禁止某些編碼錄入用戶詞典
  - 以上選塡`true`或`false`
8. **`dictionary:`** 翻譯器將調取此字典文件
9. **`prism:`** 設定由此主翻譯器的`speller`生成的棱鏡文件名，或此副編譯器調用的棱鏡名
10. **`user_dict:`** 設定用戶詞典名 
11. **`db_class:`** 設定用戶詞典類型，有`stabledb`和`text`
12. **`preedit_format:`** 上屛碼自定義
13. **`comment_format:`** 提示碼自定義
14. **`spelling_hints:`** 設定多少字以內候選標註完整帶調拼音〔對`script_translator`有效〕
15. **`initial_quality:`** 設定此翻譯器出字優先級
16. `tag:` 設定此翻譯器針對的`tag`。可不塡，不塡則僅針對`abc`
17. `prefix:` 設定此翻譯器的前綴標識，可不塡，不塡則無前綴
18. `suffix:` 設定此翻譯器的尾綴標識，可不塡，不塡則無尾綴
19. `tips:` 設定此翻譯器的輸入前提示符，可不塡，不塡則無提示符
20. `closing_tips:` 設定此翻譯器的結束輸入提示符，可不塡，不塡則無提示符

   ####示例
   
   蒼頡主翻譯器
   ```
   translator:
     dictionary: cangjie6
     enable_charset_filter: true
     enable_sentence: true
     enable_encoder: true
     encode_commit_history: true
     max_phrase_length: 5
     preedit_format:
       - xform/^([a-z ]*)$/$1｜\U$1\E/
       - xform/(?<=[a-z])\s(?=[a-z])//
       - "xlit|ABCDEFGHIJKLMNOPQRSTUVWXYZ|日月金木水火土竹戈十大中一弓人心手口尸廿山女田止卜片|"
     comment_format:
       - "xlit|abcdefghijklmnopqrstuvwxyz~|日月金木水火土竹戈十大中一弓人心手口尸廿山女田止卜片・|"
     disable_user_dict_for_patterns:
       - "^z.*$"
     initial_quality: 0.75
```

   拼音副翻譯器
   ```
   pinyin:
     tag: pinyin
     dictionary: luna_pinyin
     enable_charset_filter: true
     prefix: 'P' #須配合recognizer
     suffix: ';' #須配合recognizer
     preedit_format:
       - "xform/([nl])v/$1ü/"
       - "xform/([nl])ue/$1üe/"
       - "xform/([jqxy])v/$1u/"
     tips: "【漢拼】"
     closing_tips: "【蒼頡】"
```

   拼音・簡化字主翻譯器
   ```
   translator:
     dictionary: luna_pinyin
     prism: luna_pinyin_simp
     preedit_format:
       - xform/([nl])v/$1ü/
       - xform/([nl])ue/$1üe/
       - xform/([jqxy])v/$1u/
```

#### 四、`reverse_lookup_filter`
  * 此濾鏡須掛在`translator`上，不影響該`translator`工作
  
1. `tags:` 設定其作用���範圍
2. `overwrite_comment:` 是否覆蓋其他提示
3. `dictionary:` 反查所得提示碼之碼表
4. `comment_format:` 自定義提示碼格式

   ####示例
   ```
   pinyin_reverse_lookup: #該反查濾鏡名
     tags: [ pinyin_lookup ] #掛在這個tag所對應的翻譯器上
     overwrite_comment: true
     dictionary: cangjie6 #反查所得爲蒼頡碼
     comment_format:
       - "xform/$/〕/"
       - "xform/^/〔/"
       - "xlit|abcdefghijklmnopqrstuvwxyz |日月金木水火土竹戈十大中一弓人心手口尸廿山女田止卜片、|"
```

#### 五、`simplifier`

1. `option_name:` 對應`swiches`中設定的切換項名
2. `opencc_config:` 用字轉換定義文件
3. `tags:` 設定轉換���範圍
4. `tips:` 設定是否提示轉換前的字，可塡`none`〔或不塡〕、`char`〔僅對單字有效〕、`all`

   ####示例
   ```
   zh_tw:
     option_name: zh_tw
     opencc_config: zht2zhtw_p.ini
     tags: [ abc ] #abc對應abc_segmentor
     tips: none
```


#### 六、*`chord_composer`*
  * 並擊把鍵盤分兩半，相當於兩塊鍵盤。兩邊同時擊鍵，系統默認在其中一半上按的鍵先於另一半，由此得出上屛碼

1. `alphabet:` 字母表，包含用於並擊的按鍵。擊鍵雖有先後，形成並擊時，一律以字母表順序排列
2. `algebra:` 拼寫運算規則，將一組並擊編碼轉換爲拼音音節
3. `output_format:` 並擊完成後套用的式樣，追加隔音符號
4. `prompt_format:` 並擊過程中套用的式樣，加方括弧

   ###示例
   ```
   chord_composer:
     # 字母表，包含用於並擊的按鍵
     # 擊鍵雖有先後，形成並擊時，一律以字母表順序排列
     alphabet: "swxdecfrvgtbnjum ki,lo."
     # 拼寫運算規則，將一組並擊編碼轉換爲拼音音節
     algebra:
       # 先將物理按鍵字符對應到宮保拼音鍵位中的拼音字母
       - 'xlit|swxdecfrvgtbnjum ki,lo.|sczhlfgdbktpRiuVaNIUeoE|'
       # 以下根據宮保拼音的鍵位分別變換聲母、韻母部分
       # 組合聲母
       - xform/^zf/zh/
       - xform/^cl/ch/
       - xform/^fb/m/
       - xform/^ld/n/
       - xform/^hg/r/
       # g,k,h 接 i/ü 時作 ji/ju, qi/qu, xi/xu
       - xform/^[gz]([iV])/j$1/
       - xform/^[kc]([iV])/q$1/
       - xform/^[hs]([iV])/x$1/
       # 空格鍵單擊時產生空白
       - 'xform/^a$/ /'
       # 特例：以組合鍵[ae]輸入拼音‹a›
       - xform/ae$/a/
       # 單擊時產生字符 , .
       - xform/^U$/,/
       - xform/^E$/./
       # 上排三鍵並擊 ong, uang
       - xform/(ua?)Io$/$1Ne/
       - xform/aI$/ai/
       - xform/I[oe]$/ei/
       - xform/uI$/uei/
       # I 鍵亦可用作韻母 ‹i›
       - xform/^gI$/ji/
       - xform/^kI$/qi/
       - xform/^hI$/xi/
       - xform/I$/i/
       # 下排三鍵並擊 iong
       - xform/VUE$/VNe/
       # [ü] 活用爲介音 ‹i-› 以利於並擊 iao, iu
       - xform/V(a?)U$/i$1U/
       - xform/aU$/ao/
       - xform/UE?$/ou/
       - xform/([aiuV])Ne$/$1ng/
       # ‹eng› 省略 ‹e›
       - xform/Ne$/eng/
       - xform/^ung$/weng/
       - xform/ung$/ong/
       - xform/Vng$/iong/
       - xform/([aiuV])N$/$1n/
       # ‹en› 省略 ‹e›
       - xform/N$/en/
       - xform/^un$/wen/
       - xform/R$/er/
       # 漢語拼音方案的拼寫規則
       - xform/^i(ng?)$/yi$1/
       - xform/^i$/yi/
       - xform/^i/y/
       - xform/^u$/wu/
       - xform/^u/w/
       - xform/^V/yu/
       - xform/^([jqx])V/$1u/
       # 一些容錯
       - xform/^([zcsr]h?)i([aoe])/$1$2/
       - xform/^([zcsr]h?)i(ng?)$/$1e$2/
       # 拼寫規則
       - xform/iou$/iu/
       - xform/uei$/ui/
       - xlit/VE/ve/
       # 聲母獨用時補足隠含的韻母
       - xform/^([bpf])$/$1u/
       - xform/^([mdtnlgkh])$/$1e/
       - xform/^([mdtnlgkh])$/$1e/
       - xform/^([zcsr]h?)$/$1i/
     # 並擊完成後套用的式樣，追加隔音符號
     output_format:
       - "xform/^([a-z]+)$/$1'/"
     # 並擊過程中套用的式樣，加方括弧
     prompt_format:
       - "xform/^(.*)$/[$1]/"
```

#### 七、其它
  * 包括`recognizer`、`key_binder`、`punctuator`

1. **`import_preset:` 由外部統一文件導入**
2. `recognizer:`下設`patterns:` 配合`segmentor`的`prefix`和`suffix`完成段落劃分、`tag`分配
3. `key_binder:`下設`bindings:` 設置功能性快捷鍵
4. `punctuator:`下設`full_shape:`和`half_shape:` 分别控制全角模式下的符號和半角模式下的符號，另有`use_space:`空格頂字

   ####示例
   ```
   key_binder:
     import_preset: default
     bindings:
       - {accept: semicolon, send: 2, when: has_menu} #分號選第二重碼
       - {accept: apostrophe, send: 3, when: has_menu} #引號選第三重碼
       - {accept: "Control+1", select: .next, when: always}
       - {accept: "Control+2", toggle: full_shape, when: always}
       - {accept: "Control+3", toggle: simplification, when: always}
       - {accept: "Control+4", toggle: extended_charset, when: always}

   punctuator:
     import_preset: symbols
     half_shape:
       "'": {pair: ["「", "」"]} #第一次按是「，第二次是」
       "(": ["〔", "［"] #彈出選單
       .: {commit: "。"} #無選單，直接上屛。優先級最高

   recognizer:
     import_preset: default
     patterns:
       email: "^[a-z][-_.0-9a-z]*@.*$"
       url: "^(www[.]|https?:|ftp:|mailto:).*$"
       reverse_lookup: "`[a-z]*;?$"
       pinyin_lookup: "`P[a-z]*;?$"
       jyutping_lookup: "`J[a-z]*;?$"
       pinyin: "(?<!`)P[a-z']*;?$"
       jyutping: "(?<!`)J[a-z']*;?$"
```

###其它
---

```
menu:
  alternative_select_keys: ASDFGHJKL #如編碼字符佔用數字鍵則須另設選字鍵
  page_size: 5 #選單每䈎顯示個數

style:
  `font_face:` "HanaMinA, HanaMinB" #字體
  `font_point:` 15 #字號
  `horizontal:` false #橫／直排
  `line_spacing:` 1 #行距
```

# `Dict.yaml` 詳解
========

### 開始之前
---
	
	Rime dict
	encoding: utf-8
	〔你可以在這���裏註釋字典來源、變動記䤸等〕

### 描述檔
---

1. `name:` 內部字典名，也即`schema`所引用的字典名，確保與文年的名相一致
2. `version:` 如果發佈，請確保每次改動升版本號

   ####示例
   ```
   name: "cangjie6.extended"
   version: "0.1"
```

### 配置
---

1. `sort:` 字典**初始**排序，可選`original`或`by_weight`
2. `use_preset_vocabulary:` 是否引入���「八股文」〔含字詞頻、詞庫〕
3. `max_phrase_length:` 配合`use_preset_vocabulary:`，設定導入詞條最大詞長
4. `min_phrase_weight:` 配合`use_preset_vocabulary:`，設定導入詞條最小詞頻
5. `columns:` 定義碼表以`Tab`分隔出的各列
6. `import_tables:` 加載其它外部碼表
7. `encoder:` 形碼造詞規則
   1. `exclude_patterns:`
   2. `rules:` 可用`length_equal:`和`length_in_range:`定義。大寫字母表示字序，小寫字母表示其所跟隨的大寫字母所以表的字中的編碼序
   3. `tail_anchor:` 造詞碼包含結構分割符〔僅用於倉頡〕
   4. `exclude_patterns` 取消某編碼的造詞資格


   ####示例
   ```
   sort: by_weight
   use_preset_vocabulary: false
   import_tables:
     - cangjie6
   columns:
     - text
     - weight
   encoder:
     exclude_patterns:
       - '^z.*$'
     rules:
       - length_equal: 2
         formula: "AaAzBaBbBz"
       - length_in_range: [3, 3]
         formula: "AaAzBaYzZz"
       - length_in_range: [4, 8]
         formula: "AaBzCaYzZz"
     tail_anchor: "'"
```

### 碼表
---
  * 以`Tab`分隔各列，各列依`columns:`定義排列。

   ####示例
   蒼頡
   ```
   columns:
     - text #第一列字／詞
     - code #第二列碼
     - weight #第三列字／詞頻
     - stem #第四列造詞碼
```   
   ```
   個	owjr	246268	ow'jr
   看	hqbu	245668
   中	l	243881
   呢	rsp	242970
   來	doo	235101
   嗎	rsqf	221092
   爲	bhnf	211340
   會	owfa	209844
   她	vpd	204725
   與	xyc	203975
   給	vfor	193007
   等	hgdi	183340
   這	yymr	181787
   用	bq	168934	b'q
```

===
