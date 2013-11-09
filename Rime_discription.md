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
    states: ["字型 → 漢字", "字型 → 汉字", "字型 → 灘茡"]
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
