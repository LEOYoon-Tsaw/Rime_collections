<html>
<body>
<h1 align="center"><code>Schema.yaml</code> 詳解</h1>
<h2></h2>
<hr>

<h2>開始之前</h2>

<pre><code># Rime schema
# encoding: utf-8
</code></pre>

<h2>描述檔</h2>

<ol><li><code>name:</code> 方案的顯示名偁〔即出現於方案選單中以示人的，通常爲中文〕</li>
<li><code>schema_id:</code> 方案內部名，在代碼中引用此方案時以此名爲正，通常由英文、數字、下劃線組成</li>
<li><code>author:</code> 發明人、撰寫者。如果您對方案做出了修改，請保留原作者名，並將自己的名字加在後面</li>
<li><code>description:</code> 請簡要描述方案歷史、碼表來源、該方案規則等</li>
<li><code>dependencies:</code> 如果本方案依賴於其它方案〔通常來說會依頼其它方案做爲反查，抑或是兩種或多種方案混用時〕</li>
<li><code>version:</code> 版本號，在發佈新版前請確保已陞版本號</li>
</ol>

<ul><h4><strong>示例</strong></h4>
<pre><code>schema:
  name: "蒼頡檢字法"
  schema_id: cangjie6
  author:
    - "發明人 朱邦復先生、沈紅蓮女士"
  dependencies:
    - luna_pinyin
    - jyutping
    - zyenpheng
  description: |
    第六代倉頡輸入法
    碼表由雪齋、惜緣和crazy4u整理
  version: 0.19
</code></pre></ul>

<h2>開關</h2>
<p>通常包含以下數個：</p>

<ol><li><code>ascii_mode</code> 是中英文轉換開關。預設<code>0</code>爲中文，<code>1</code>爲英文</li>
<li><code>full_shape</code> 是全角符號／半角符號開關。注意，開啓全角時英文字母亦爲全角。<code>0</code>爲半角，<code>1</code>爲全角</li>
<li><code>extended_charset</code> 是字符集開關。<code>0</code>爲CJK基本字符集，<code>1</code>爲CJK全字符集</li>
<ul><li>僅<code>table_translator</code>可用</li></ul>
<li><code>ascii_punct</code> 是中西文標點轉換開關，<code>0</code>爲中文句讀，<code>1</code>爲西文標點。</li>
<li><code>simplification</code> 是轉化字開關。一般情況下與上同，<code>0</code>爲不開啓轉化，<code>1</code>爲轉化。</li>

<ul><li><code>simplification</code>選項名偁可自定義，亦可添加多套替換用字方案：</li></ul>
<ul><pre><code>- name: zh_cn
  states: ["漢字", "汉字"]
  reset: 0
</code></pre>
<p>或</p>
<pre><code>- options: [ zh_trad, zh_cn, zh_mars ]
  states:
    - 字型 → 漢字
    - 字型 → 汉字
    - 字型 → 䕼茡
  reset: 0
</code></pre>
<ul>
<li><code>name</code>/<code>options</code>名：須與<code>simplifier</code>中<code>option_name</code>相同</li>
<li><code>states</code>：可不寫，如不寫則此開關存在但不可見，可由快捷鍵操作</li>
<li><code>reset</code>：設定默認狀態〔<code>reset</code>可不寫，此時切換窗口時不會重置到默認狀態〕</li>
</ul>
</ul>
<li>字符集過濾。此選項沒有默認名偁，須配合<code>charset_filter</code>使用。可單用，亦可添加多套字符集：</li>
<pre><code>- name: gbk
  states: [ 增廣, 常用 ]
  reset: 0
</code></pre>
或
<pre><code>- options: [ utf-8, big5hkscs, big5, gbk, gb2312 ]
  states:
    - 字集 → 全
    - 字集 → 港臺
    - 字集 → 臺
    - 字集 → 大陸
    - 字集 → 简体
  reset: 0
</code></pre>
<ul>
<li><code>name</code>/<code>options</code>名：須與<code>charset_filter</code><code>@</code>後的tag相同</li>
<li>避免同時使用字符集過濾和<code>extended_charset</code></li>
</ul>
</ol>

<ul>
<h4><strong>示例</strong></h4>
<pre><code>switches:
  - name: ascii_mode
    reset: 0
    states: ["中文", "西文"]
  - name: full_shape
    states: ["半角", "全角"]
  - name: extended_charset
    states: ["通用", "增廣"]
  - name: simplification
    states: ["漢字", "汉字"]
  - name: ascii_punct
    states: ["句讀", "符號"]
</code></pre></ul>

<h2>引擎</h2>
<ul><li>以下<b>加粗</b>項爲可細配者，<i>斜體</i>者爲不常用者</li>
</ul>
<p>引擎分四組：</p>

<h3>一、<code>processors</code></h3>
<ul><li>這批組件處理各類按鍵消息</li></ul>

<ol><li><code>ascii_composer</code> 處理西文模式及中西文切</li>
<li><b><code>recognizer</code></b> 與<code>matcher</code>搭配，處理符合特定規則的輸入碼，如網址、反查等<code>tags</code></li>
<li><b><code>key_binder</code></b> 在特定條件下將按鍵綁定到其他按鍵，如重定義逗號、句號爲候選翻頁、開關快捷鍵等</li>
<li><b><code>speller</code></b> 拼寫處理器，接受字符按鍵，編輯輸入</li>
<li><b><code>punctuator</code></b> 句讀處理器，將單個字符按鍵直接映射爲標點符號或文字</li>
<li><code>selector</code> 選字處理器，處理數字選字鍵〔可以換成別的哦〕、上、下候選定位、換頁</li>
<li><code>navigator</code> 處理輸入欄內的光標移動</li>
<li><code>express_editor</code> 編輯器，處理空格、回車上屏、回退鍵</li>
<li><i><code>fluid_editor</code></i> 句式編輯器，用於以空格斷詞、回車上屏的【注音】、【語句流】等輸入方案，替換<code>express_editor</code></li>
<li><i><code>chord_composer</code></i> 和絃作曲家或曰並擊處理器，用於【宮保拼音】等多鍵並擊的輸入方案</li>
<li><code>lua_processor</code> 使用<code>lua</code>自定義按鍵，後接<code>@</code>+<code>lua</code>函數名</li>
<ul><li><code>lua</code>函數名即用戶文件夾內<code>rime.lua</code>中函數名，參數爲<code>(key, env)</code></li></ul>
</ol>

<h3>二、<code>segmentors</code></h3>
<ul><li>這批組件識別不同內容類型，將輸入碼分段並加上<code>tag</code></li></ul>

<ol><li><code>ascii_segmentor</code> 標識西文段落〔譬如在西文模式下〕字母直接上屛</li>
<li><code>matcher</code> 配合<code>recognizer</code>標識符合特定規則的段落，如網址、反查等，加上特定<code>tag</code></li>
<li><b><code>abc_segmentor</code></b> 標識常規的文字段落，加上<code>abc</code>這個<code>tag</code></li>
<li><code>punct_segmentor</code> 標識句讀段落〔鍵入標點符號用〕加上<code>punct</code>這個<code>tag</code></li>
<li><code>fallback_segmentor</code> 標識其他未標識段落</li>
<li><b><code>affix_segmentor</code></b> 用戶自定義<code>tag</code></li>
<ul><li>此項可加載多個實例，後接<code>@</code>+<code>tag</code>名</li></ul>
<li><i><code>lua_segmentor</code></i> 使用<code>lua</code>自定義切分，後接<code>@</code>+<code>lua</code>函數名</li>
</ol>

<h3>三、<code>translators</code></h3>
<ul><li>這批組件翻譯特定類型的編碼段爲一組候選文字</li></ul>

<ol><li><code>echo_translator</code> 沒有其他候選字時，回顯輸入碼〔輸入碼可以<code>Shift</code>+<code>Enter</code>上屛〕</li>
<li><code>punct_translator</code> 配合<code>punct_segmentor</code>轉換標點符號</li>
<li><b><code>table_translator</code></b> 碼表翻譯器，用於倉頡、五筆等基於碼表的輸入方案</li>
  - 此項可加載多個實例，後接<code>@</code>+翻譯器名〔如：<code>cangjie</code>、<code>wubi</code>等〕</li>
<li><b><code>script_translator</code></b> 腳本翻譯器，用於拼音、粵拼等基於音節表的輸入方案</li>
  - 此項可加載多個實例，後接<code>@</code>+翻譯器名〔如：<code>pinyin</code>、<code>jyutping</code>等〕</li>
<li><i><code>reverse_lookup_translator</code></i> 反查翻譯器，用另一種編碼方案查碼</li>
<li><b><code>lua_translator</code></b> 使用<code>lua</code>自定義輸入，例如動態輸入當前日期、時間，後接<code>@</code>+<code>lua</code>函數名</li>
<ul><li><code>lua</code>函數名即用戶文件夾內<code>rime.lua</code>中函數名，參數爲<code>(input, seg, env)</code></li>
<li>可以<code>env.engine.context:get_option("option_name")</code>方式綁定到<code>switch</code>開關／<code>key_binder</code>快捷鍵</li></ul>
</ol>

<h3>四、<code>filters</code></h3>
<ul><li>這批組件過濾翻譯的結果</li></ul>
<ol>
<li><code>uniquifier</code> 過濾重複的候選字，有可能來自<b><code>simplifier</code></b></li>
<li><code>cjk_minifier</code> 字符集過濾〔僅用於<code>script_translator</code>，使之支援<code>extended_charset</code>開關〕</li>
<li><b><code>single_char_filter</code></b> 單字過濾器，如加載此組件，則屛敝詞典中的詞組〔僅<code>table_translator</code>有效〕</li>
<li><b><code>simplifier</code></b> 用字轉換</li>
<li><b><code>reverse_lookup_filter</code></b> 反查濾鏡，以更靈活的方式反查，Rime1.0後替代<i><code>reverse_lookup_translator</code></i></li>
<ul><li>此項可加載多個實例，後接<code>@</code>+濾鏡名〔如：<code>pinyin_lookup</code>、<code>jyutping_lookup</code>等〕</li></ul>
<li><b><code>charset_filter</code></b> 字符集過濾</li>
<ul><li>後接<code>@</code>+字符集名〔如：<code>utf-8</code>(無過濾)、<code>big5</code>、<code>big5hkscs</code>、<code>gbk</code>、<code>gb2312</code>〕</li></ul>
<li><b><code>lua_filter</code></b> 使用<code>lua</code>自定義過濾，例如過濾字符集、調整排序，後接<code>@</code>+<code>lua</code>函數名</li>
<ul><li><code>lua</code>函數名即用戶文件夾內<code>rime.lua</code>中函數名，參數爲<code>(input, env)</code></li>
<li>可以<code>env.engine.context:get_option("option_name")</code>方式綁定到<code>switch</code>開關／<code>key_binder</code>快捷鍵</li></ul>
</ol>

<ul><h4><strong>示例</strong></h4>
<p><small>cangjie6.schema.yaml</small></p>
<pre><code>engine:
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
    - lua_translator@get_date
  filters:
    - simplifier@zh_simp
    - uniquifier
    - cjk_minifier
    - charset_filter@gbk
    - reverse_lookup_filter@middle_chinese
    - reverse_lookup_filter@pinyin_reverse_lookup
    - reverse_lookup_filter@jyutping_reverse_lookup
    - lua_filter@single_char_first
</code></pre></ul>

<h2>細項配置</h2>
<ul><li>凡<code>comment_format</code>、<code>preedit_format</code>、<code>speller/algebra</code>所用之正則表達式，請參閱<a href="http://www.boost.org/doc/libs/1_49_0/libs/regex/doc/html/boost_regex/syntax/perl_syntax.html">「Perl正則表達式」</a></li>
</ul>
<p><strong>引擎中所舉之加粗者均可在下方詳細描述，格式爲：</strong></p>

<pre><code>name:
  branches: configurations
</code></pre>
<p>或</p>
<pre><code>name:
  branches:
    - configurations
</code></pre>

<h3>一、<code>speller</code></h3>

<ol><li><code>alphabet:</code> 定義本方案輸入鍵</li>
<li><code>initials:</code> 定義僅作始碼之鍵</li>
<li><code>finals:</code> 定義僅作末碼之鍵</li>
<li><code>delimiter:</code> 上屛時的音節間分音符</li>
<li><code>algebra:</code> 拼寫運算規則，由之算出的拼寫匯入<code>prism</code>中</li>
<li><code>max_code_length:</code> 形碼最大碼長，超過則頂字上屛〔<code>number</code>〕</li>
<li><code>auto_select:</code> 自動上屛〔<code>true</code>或<code>false</code>〕</li>
<li><code>auto_select_pattern:</code> 自動上屏規則，以正則表達式描述，當輸入串可以被匹配時自動頂字上屏。
<li><code>use_space:</code> 以空格作輸入碼〔<code>true</code>或<code>false</code>〕</li>
</ol>

<ul><ul><li><code>speller</code>的演算包含：</li>
<pre><code>xform --改寫〔不保留原形〕
derive --衍生〔保留原形〕
abbrev --簡拼〔出字優先級較上兩組更低〕
fuzz --畧拼〔此種簡拼僅組詞，不出單字〕
xlit --變換〔適合大量一對一變換〕
erase --刪除
</code></pre></ul></ul>

<ul><h4><strong>示例</strong></h4>
<p><small>luna_pinyin.schema.yaml</small></p>
<pre><code>speller:
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
</code></pre></ul>

<h3>二、<code>segmentor</code></h3>
<ul><li><code>segmentor</code>配合<code>recognizer</code>標記出<code>tag</code>。這裏會用到<code>affix_segmentor</code>和<code>abc_translator</code></li>
<li><code>tag</code>用在<code>translator</code>、<code>reverse_lookup_filter</code>、<code>simplifier</code>中用以標定各自作用範圍</li>
<li>如果不需要用到<code>extra_tags</code>則不需要單獨配置<code>segmentor</code></li>
</ul>

<ol><li><code>tag:</code> 設定其<code>tag</code></li>
<li><code>prefix:</code> 設定其前綴標識，可不塡，不塡則無前綴</li>
<li><code>suffix:</code> 設定其尾綴標識，可不塡，不塡則無尾綴</li>
<li><code>tips:</code> 設定其輸入前提示符，可不塡，不塡則無提示符</li>
<li><code>closing_tips:</code> 設定其結束輸入提示符，可不塡，不塡則無提示符</li>
<li><code>extra_tags:</code> 爲此<code>segmentor</code>所標記的段落插上其它<code>tag</code></li>
</ol>

<ul><strong>當<code>affix_segmentor</code>和<code>translator</code>重名時，兩者可併在一處配置，此處1-5條對應下面19-23條。<code>abc_segmentor</code>僅可設<code>extra_tags</code></strong></ul>

<ul><h4><strong>示例</strong></h4>
<p><small>cangjie6.schema.yaml</small></p>
<pre><code>reverse_lookup:
  tag: reverse_lookup
  prefix: "`"
  suffix: ";"
  tips: "【反查】"
  closing_tips: "【蒼頡】"
  extra_tags:
    - pinyin_lookup
    - jyutping_lookup
</code></pre></ul>

<h3>三、<code>translator</code></h3>
<ul><li>每個方案有一個主<code>translator</code>，在引擎列表中不以<code>@</code>+翻譯器名定義，在細項配置時直接以<code>translator:</code>命名。以下加粗項爲可在主<code>translator</code>中定義之項，其它可在副〔以<code>@</code>+翻譯器名命名〕<code>translator</code>中定義</li>
</ul>

<ol><li><b><code>enable_charset_filter:</code></b> 是否開啓字符集過濾〔僅<code>table_translator</code>有效。啓用<code>cjk_minifier</code>後可適用於<code>script_translator</code>〕</li>
<li><b><code>enable_encoder:</code></b> 是否開啓自動造詞〔僅<code>table_translator</code>有效〕</li>
<li><b><code>encode_commit_history:</code></b> 是否對已上屛詞自動成詞〔僅<code>table_translator</code>有效〕</li>
<li><b><code>max_phrase_length:</code></b> 最大自動成詞詞長〔僅<code>table_translator</code>有效〕</li>
<li><b><code>enable_completion:</code></b> 提前顯示尚未輸入完整碼的字〔僅<code>table_translator</code>有效〕</li>
<li><b><code>sentence_over_completion:</code></b> 在無全碼對應字而僅有逐鍵提示時也開啓智能組句〔僅<code>table_translator</code>有效〕</li>
<li><b><code>strict_spelling:</code></b> 配合<code>speller</code>中的<code>fuzz</code>規則，僅以畧拼碼組詞〔僅<code>table_translator</code>有效〕</li>
<li><b><code>disable_user_dict_for_patterns:</code></b> 禁止某些編碼錄入用戶詞典</li>
<li><b><code>enable_sentence:</code></b> 是否開啓自動造句</li>
<li><b><code>enable_user_dict:</code></b> 是否開啓用戶詞典〔用戶詞典記錄動態字詞頻、用戶詞〕</li>
<ul><li>以上選塡<code>true</code>或<code>false</code></li></ul>
<li><b><code>dictionary:</code></b> 翻譯器將調取此字典文件</li>
<li><b><code>prism:</code></b> 設定由此主翻譯器的<code>speller</code>生成的棱鏡文件名，或此副編譯器調用的棱鏡名</li>
<li><b><code>user_dict:</code></b> 設定用戶詞典名</li>
<li><b><code>db_class:</code></b> 設定用戶詞典類型，可設<code>tabledb</code>〔文本〕或<code>userdb</code>〔二進制〕</li>
<li><b><code>preedit_format:</code></b> 上屛碼自定義</li>
<li><b><code>comment_format:</code></b> 提示碼自定義</li>
<li><b><code>spelling_hints:</code></b> 設定多少字以內候選標註完整帶調拼音〔僅<code>script_translator</code>有效〕</li>
<li><b><code>initial_quality:</code></b> 設定此翻譯器出字優先級</li>
<li><code>tag:</code> 設定此翻譯器針對的<code>tag</code>。可不塡，不塡則僅針對<code>abc</code></li>
<li><code>prefix:</code> 設定此翻譯器的前綴標識，可不塡，不塡則無前綴</li>
<li><code>suffix:</code> 設定此翻譯器的尾綴標識，可不塡，不塡則無尾綴</li>
<li><code>tips:</code> 設定此翻譯器的輸入前提示符，可不塡，不塡則無提示符</li>
<li><code>closing_tips:</code> 設定此翻譯器的結束輸入提示符，可不塡，不塡則無提示符</li>
<li><code>contextual_suggestions:</code> 是否使用語言模型優化輸出結果〔需配合<code>grammar</code>使用〕</li>
<li><code>max_homophones:</code> 最大同音簇長度〔需配合<code>grammar</code>使用〕</li>
<li><code>max_homographs:</code> 最大同形簇長度〔需配合<code>grammar</code>使用〕</li>
</ol>

<ul><h4><strong>示例</strong></h4>
<p><small>cangjie6.schema.yaml  蒼頡主翻譯器</small></p>
<pre><code>translator:
  dictionary: cangjie6
  enable_charset_filter: true
  enable_sentence: true
  enable_encoder: true
  encode_commit_history: true
  max_phrase_length: 5
  preedit_format:
    - xform/^([a-z ]<em>)$/$1｜\U$1\E/
    - xform/(?&lt;=[a-z])\s(?=[a-z])//
    - "xlit|ABCDEFGHIJKLMNOPQRSTUVWXYZ|日月金木水火土竹戈十大中一弓人心手口尸廿山女田止卜片|"
  comment_format:
    - "xlit|abcdefghijklmnopqrstuvwxyz~|日月金木水火土竹戈十大中一弓人心手口尸廿山女田止卜片・|"
  disable_user_dict_for_patterns:
    - "^z.</em>$"
  initial_quality: 0.75
</code></pre>

<p><small>cangjie6.schema.yaml  拼音副翻譯器</small></p>
<pre><code>pinyin:
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
</code></pre>

<p><small>pinyin_simp.schema.yaml  拼音・簡化字主翻譯器</small></p>
<pre><code>translator:
  dictionary: luna_pinyin
  prism: luna_pinyin_simp
  preedit_format:
    - xform/([nl])v/$1ü/
    - xform/([nl])ue/$1üe/
    - xform/([jqxy])v/$1u/
</code></pre>

<p><small>luna_pinyin.schema.yaml 朙月拼音用戶短語</small></p>
<pre><code>custom_phrase: #這是一個table_translator
  dictionary: ""
  user_dict: custom_phrase
  db_class: tabledb
  enable_sentence: false
  enable_completion: false
  initial_quality: 1
</code></pre></ul>

<h3>四、<code>reverse_lookup_filter</code></h3>
<ul><li>此濾鏡須掛在<code>translator</code>上，不影響該<code>translator</code>工作</li>
</ul>

<ol><li><code>tags:</code> 設定其作用範圍
<li><code>overwrite_comment:</code> 是否覆蓋其他提示
<li><code>dictionary:</code> 反查所得提示碼之碼表
<li><code>comment_format:</code> 自定義提示碼格式
</ol>

<ul><h4><strong>示例</strong></h4>
<p><small>cangjie6.schema.yaml</small></p>
<pre><code>pinyin_reverse_lookup: #該反查濾鏡名
  tags: [ pinyin_lookup ] #掛在這個tag所對應的翻譯器上
  overwrite_comment: true
  dictionary: cangjie6 #反查所得爲蒼頡碼
  comment_format:
    - "xform/$/〕/"
    - "xform/^/〔/"
    - "xlit|abcdefghijklmnopqrstuvwxyz |日月金木水火土竹戈十大中一弓人心手口尸廿山女田止卜片、|"
</code></pre></ul>

<h3>五、<code>simplifier</code></h3>

<ol><li><code>option_name:</code> 對應<code>switches</code>中設定的切換項名</li>
<li><code>opencc_config:</code> 用字轉換配置文件</li>
<ul><li>位於：<code>rime_dir/opencc/</code>，自帶之配置文件含：</li>
<ol><li>繁轉簡〔默認〕：<code>t2s.json</code></li>
<li>繁轉臺灣：<code>t2tw.json</code></li>
<li>繁轉香港：<code>t2hk.json</code></li>
<li>簡轉繁：<code>s2t.json</code></li>
</ol></ul>
<li><code>tags:</code> 設定轉換範圍</li>
<li><code>tips:</code> 設定是否提示轉換前的字，可塡<code>none</code>〔或不塡〕、<code>char</code>〔僅對單字有效〕、<code>all</code></li>
<li><code>show_in_comment:</code> 設定是否僅將轉換結果顯示在備注中
<li><i><code>excluded_types:</code></i> 取消特定範圍〔一般爲<i><code>reverse_lookup_translator</code></i>〕轉化用字</li>
</ol>

<ul><h4><strong>示例</strong></h4>
<p><small>修改自 luna_pinyin_kunki.schema</small></p>
<pre><code>zh_tw:
  option_name: zh_tw
  opencc_config: t2tw.json
  tags: [ abc ] #abc對應abc_segmentor
  tips: none
</code></pre></ul>

<h3><i>六、<code>chord_composer</code></i></h3>
<ul><li>並擊把鍵盤分兩半，相當於兩塊鍵盤。兩邊同時擊鍵，系統默認在其中一半上按的鍵先於另一半，由此得出上屛碼</li>
</ul>

<ol><li><code>alphabet:</code> 字母表，包含用於並擊的按鍵。擊鍵雖有先後，形成並擊時，一律以字母表順序排列
<li><code>algebra:</code> 拼寫運算規則，將一組並擊編碼轉換爲拼音音節
<li><code>output_format:</code> 並擊完成後套用的式樣，追加隔音符號
<li><code>prompt_format:</code> 並擊過程中套用的式樣，加方括弧
</ol>

<ul><h4><strong>示例</strong></h4>
<p><small>combo_pinyin.schema.yaml</small></p>
<pre><code>chord_composer:
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
    ……
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
</code></pre></ul>

<h3>七、<code>lua</code></h3>
<ul><li>請參攷<a href="https://github.com/hchunhui/librime-lua">hchunhui/librime-lua</a> 以尋求更多靈感。</li></ul>
<ol>
<li><code>lua_translator</code></li>
<li><code>lua_filter</code></li>
<li><code>lua_processor</code></li>
<li><code>lua_segmentor</code></li>
</ol>
<ul><h4><strong>示例</strong></h4>
<p><small>rime.lua</small></p>
<pre><code>function get_date(input, seg, env)
  --- 以 show_date 爲開關名或 key_binder 中 toggle 的對象
  on = env.engine.context:get_option("show_date")
  if (on and input == "date") then
    --- Candidate(type, start, end, text, comment)
    yield(Candidate("date", seg.start, seg._end, os.date("%Y年%m月%d日"), " 日期"))
  end
end

function single_char_first(input, env)
  --- 以 single_char 爲開關名或 key_binder 中 toggle 的對象
  on = env.engine.context:get_option("single_char")
  local cache = {}
  for cand in input:iter() do
    if (not on or utf8.len(cand.text) == 1) then
      yield(cand)
    else
      table.insert(cache, cand)
    end
  end
  for i, cand in ipairs(cache) do
    yield(cand)
  end
end
</code></pre></ul>

<h3>八、其它</h3>
<ul><li>包括<code>recognizer</code>、<code>key_binder</code>、<code>punctuator</code>。<b>標點</b>、<b>快捷鍵</b>、<b>二三選重</b>、<b>特殊字符</b>等均於此設置</li>
</ul>

<ol><li><b><code>import_preset:</code></b> 由外部統一文件導入
<li><code>grammar:</code> 下設：
<ul><li><code>language:</code> 取值<code>zh-han[ts]-t-essay-bg[wc]</code></li>
<li><code>collocation_max_length:</code> 最大搭配長度（整句輸入可忽畧此項）</li>
<li><code>collocation_min_length:</code> 最小搭配長度（整句輸入可忽畧此項）</li>
</ul></li>
<li><code>recognizer:</code> 下設<code>patterns:</code> 配合<code>segmentor</code>的<code>prefix</code>和<code>suffix</code>完成段落劃分、<code>tag</code>分配
<ul><li>前字段可以爲以<code>affix_segmentor@someTag</code>定義的<code>Tag</code>名，或者<code>punct</code>、<code>reverse_lookup</code>兩個內設的字段。其它字段不調用輸入法引擎，輸入即輸出〔如<code>url</code>等字段〕</li></ul></li>
<li><code>key_binder:</code> 下設<code>bindings:</code> 設置功能性快捷鍵
<ul><li>每一條<code>binding</code>可能包含：<code>accept</code>實際所按之鍵、<code>send</code>輸出效果、<code>toggle</code>切換開關和<code>when</code>作用範圍〔<code>send</code>和<code>toggle</code>二選一〕</li>
<ul>
<li><code>toggle</code>可用字段包含各開關名</li>
<li><code>when</code>可用字段包含：</li>
<ul><pre><code>paging	翻䈎用
has_menu	操作候選項用
composing	操作輸入碼用
always	全域
</code></pre></ul>
<li><code>accept</code>和<code>send</code>可用字段除A-Za-z0-9外，還包含以下鍵板上實際有的鍵：</li>
<ul><pre><code>BackSpace	退格
Tab	水平定位符
Linefeed	换行
Clear	清除
Return	回車
Pause	暫停
Sys_Req	印屏
Escape	退出
Delete	刪除
Home	原位
Left	左箭頭
Up	上箭頭
Right	右箭頭
Down	下箭頭
Prior、Page_Up	上翻
Next、Page_Down	下翻
End	末位
Begin	始位
Shift_L	左Shift
Shift_R	右Shift
Control_L	左Ctrl
Control_R	右Ctrl
Meta_L	左Meta
Meta_R	右Meta
Alt_L	左Alt
Alt_R	右Alt
Super_L	左Super
Super_R	右Super
Hyper_L	左Hyper
Hyper_R	右Hyper
Caps_Lock	大寫鎖
Shift_Lock	上檔鎖
Scroll_Lock	滾動鎖
Num_Lock	小鍵板鎖
Select	選定
Print	列印
Execute	執行
Insert	插入
Undo	還原
Redo	重做
Menu	菜單
Find	蒐尋
Cancel	取消
Help	幫助
Break	中斷

space	 
exclam	!
quotedbl	"
numbersign	#
dollar	$
percent	%
ampersand	&
apostrophe	'
parenleft	(
parenright	)
asterisk	*
plus	+
comma	,
minus	-
period	.
slash	/
colon	:
semicolon	;
less	<
equal	=
greater	>
question	?
at	@
bracketleft	[
backslash	\
bracketright	]
asciicircum	^
underscore	_
grave	`
braceleft	{
bar	|
braceright	}
asciitilde	~

KP_Space	小鍵板空格
KP_Tab	小鍵板水平定位符
KP_Enter	小鍵板回車
KP_Delete	小鍵板刪除
KP_Home	小鍵板原位
KP_Left	小鍵板左箭頭
KP_Up	小鍵板上箭頭
KP_Right	小鍵板右箭頭
KP_Down	小鍵板下箭頭
KP_Prior、KP_Page_Up	小鍵板上翻
KP_Next、KP_Page_Down	小鍵板下翻
KP_End	小鍵板末位
KP_Begin	小鍵板始位
KP_Insert	小鍵板插入
KP_Equal	小鍵板等於
KP_Multiply	小鍵板乘號
KP_Add	小鍵板加號
KP_Subtract	小鍵板減號
KP_Divide	小鍵板除號
KP_Decimal	小鍵板小數點
KP_0	小鍵板0
KP_1	小鍵板1
KP_2	小鍵板2
KP_3	小鍵板3
KP_4	小鍵板4
KP_5	小鍵板5
KP_6	小鍵板6
KP_7	小鍵板7
KP_8	小鍵板8
KP_9	小鍵板9
</ul></pre></code>
</ul></ul></ul>
<li><code>editor</code>用以訂製操作鍵〔不支持<code>import_preset:</code>〕，鍵板鍵名同<code>key_binder/bindings</code>中的<code>accept</code>和<code>send</code>，效果定義如下：</li>
<ul><pre><code>confirm	上屏候選項
commit_comment	上屏候選項備注
commit_raw_input	上屏原始輸入
commit_script_text	上屏變換後輸入
commit_composition	語句流單字上屏
revert	撤消上次輸入
back	按字符回退
back_syllable	按音節回退
delete_candidate	刪除候選項
delete	向後刪除
cancel	取消輸入
noop	空
</code></pre></ul>
<li><code>punctuator:</code> 下設<code>full_shape:</code>和<code>half_shape:</code>分别控制全角模式下的符號和半角模式下的符號，另有<code>use_space:</code>空格頂字〔<code>true</code>或<code>false</code>〕
<ul><li>每條標點項可加<code>commit</code>直接上屏和<code>pair</code>交替上屏兩種模式，默認爲選單模式</li></ul>
</ol>

<ul><h4><strong>示例</strong></h4>
<p><small>修改自 cangjie6.schema.yaml</small></p>
<pre><code>key_binder:
  import_preset: default
  bindings:
    - {accept: semicolon, send: 2, when: has_menu} #分號選第二重碼
    - {accept: apostrophe, send: 3, when: has_menu} #引號選第三重碼
    - {accept: "Control+1", select: .next, when: always}
    - {accept: "Control+2", toggle: full_shape, when: always}
    - {accept: "Control+3", toggle: simplification, when: always}
    - {accept: "Control+4", toggle: extended_charset, when: always}

editor:
  bindings:
    Return: commit_comment

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
    pinyin: "(?&lt!`)P[a-z']*;?$"
    jyutping: "(?&lt!`)J[a-z']*;?$"
    punct: "/[a-z]*$" #配合symbols.yaml中的特殊字符輸入
</code></pre></ul>

<h2>其它</h2>
<ul><li>Rime還爲每個方案提供選單和一定的外觀訂製能力</li>
<li>通常情況下<code>menu</code>在<code>default.yaml</code>中定義〔或用戶修改檔<code>default.custom.yaml</code>〕，<code>style</code>在<code>squirrel.yaml</code>或<code>weasel.yaml</code>〔或用戶修改檔<code>squirrel.custom.yaml</code>或<code>weasel.custom.yaml</code>〕</li>

<h4><strong>示例</strong></h4>
<pre><code>menu:
  alternative_select_labels: [ ①, ②, ③, ④, ⑤, ⑥, ⑦, ⑧, ⑨ ]  # 修改候選標籤
  alternative_select_keys: ASDFGHJKL #如編碼字符佔用數字鍵則須另設選字鍵
  page_size: 5 #選單每䈎顯示個數

style:
  font_face: "HanaMinA, HanaMinB" #字體〔小狼毫得且僅得設一個字體；鼠鬚管得設多個字體，後面的字體自動補前面字體不含的字〕
  font_point: 15 #字號
  label_format: '%s'  # 候選標籤格式
  horizontal: false #橫／直排
  line_spacing: 1 #行距
  inline_preedit: true #輸入碼內嵌
</code></pre></ul>

<br>

<h1 align="center"><code>Dict.yaml</code> 詳解</h1>
<h2></h2>
<hr>

<h2>開始之前</h2>

<pre><code># Rime dict
# encoding: utf-8
〔你還可以在這註釋字典來源、變動記錄等〕
</code></pre>

<h2>描述檔</h2>

<ol><li><code>name:</code> 內部字典名，也即<code>schema</code>所引用的字典名，確保與文件名相一致</li>
<li><code>version:</code> 如果發佈，請確保每次改動陞版本號</li>
</ol>

<ul><h4><strong>示例</strong></h4>
<pre><code>name: "cangjie6.extended"
version: "0.1"
</code></pre></ul>

<h2>配置</h2>

<ol><li><code>sort:</code> 字典<b>初始</b>排序，可選<code>original</code>或<code>by_weight</code></li>
<li><code>use_preset_vocabulary:</code> 是否引入「八股文」〔含字詞頻、詞庫〕</li>
<li><code>max_phrase_length:</code> 配合<code>use_preset_vocabulary:</code>，設定導入詞條最大詞長</li>
<li><code>min_phrase_weight:</code> 配合<code>use_preset_vocabulary:</code>，設定導入詞條最小詞頻</li>
<li><code>columns:</code> 定義碼表以<code>Tab</code>分隔出的各列，可設<code>text</code>【文本】、<code>code</code>【碼】、<code>weight</code>【權重】、<code>stem</code>【造詞碼】</li>
<li><code>import_tables:</code> 加載其它外部碼表</li>
<li><code>encoder:</code> 形碼造詞規則</li>
<ol type="a">
<li><code>exclude_patterns:</code></li>
<li><code>rules:</code> 可用<code>length_equal:</code>和<code>length_in_range:</code>定義。大寫字母表示字序，小寫字母表示其所跟隨的大寫字母所以表的字中的編碼序</li>
<li><code>tail_anchor:</code> 造詞碼包含結構分割符〔僅用於倉頡〕</li>
<li><code>exclude_patterns</code> 取消某編碼的造詞資格</li>
</ol></ol>

<ul><h4><strong>示例</strong></h4>
<p><small>cangjie6.extended.dict.yaml</small></p>
<pre><code>sort: by_weight
use_preset_vocabulary: false
import_tables:
  - cangjie6 #單字碼表由cangjie6.dict.yaml導入
columns: #此字典爲純詞典，無單字編碼，僅有字和詞頻
  - text #字／詞
  - weight #字／詞頻
encoder:
  exclude_patterns:
    - '^z.*$'
  rules:
    - length_equal: 2 #對於二字詞
      formula: "AaAzBaBbBz" #取第一字首尾碼、第二字首次尾碼
    - length_equal: 3 #對於三字詞
      formula: "AaAzBaYzZz" #取第一字首尾碼、第二字首尾碼、第三字尾碼
    - length_in_range: [4, 5] #對於四至五字詞
      formula: "AaBzCaYzZz" #取第一字首碼，第二字尾碼、第三字首碼、倒數第二字尾碼、最後一字尾碼
  tail_anchor: "'"
</code></pre></ul>

<h2>碼表</h2>
<ul><li>以<code>Tab</code>分隔各列，各列依<code>columns:</code>定義排列。</li></ul>

<ul><h4><strong>示例</strong></h4>
<p><small>cangjie6.dict.yaml</small></p>
<pre><code>columns:
  - text #第一列字／詞
  - code #第二列碼
  - weight #第三列字／詞頻
  - stem #第四列造詞碼
</code></pre>
<p><small>cangjie6.dict.yaml</small></p>
<pre><code>個	owjr	246268	ow'jr
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
</code></pre>
</ul>

<hr>

<p align="right">雪齋<br>
09-Nov-2013</p>

</body>
</html>
