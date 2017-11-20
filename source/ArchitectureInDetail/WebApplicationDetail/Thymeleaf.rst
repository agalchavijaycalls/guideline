テンプレートエンジン(Thymeleaf)
================================================================================

.. only:: html

 .. contents:: 目次
    :depth: 3
    :local:


.. note:: **【骨子レビュー用の補足説明】**
  
  当節の構成を以下に示す。
  なお、当節では、[How to extend]に記載する「カスタムダイアレクトの作成」を除き、NTTコムウェア社のレポートに明示的に記載の無い内容を扱っている。
  
  * Overview       : Thymeleafの特徴を説明し、Webアプリケーションに適用する例を示す。
  * How to use     : ガイドラインが推奨するThymeleaf + Springの設定とその機能について説明する。
  * How to extend  : カスタムダイアレクトの追加法について説明する。
  * Appendix       : テンプレートキャッシュ機能について説明する。

Overview
--------------------------------------------------------------------------------

Thymeleafとは
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  ここでは、テンプレートエンジンとしてのThymeleafの特徴を説明し、Webアプリケーションに適用する例を示す。


Thymeleafの特性
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

.. note:: 

  以下のような特性を紹介する。
  
  * Thymeleafのテンプレートは、ブラウザでの静的描画が可能
  * テンプレートの解釈がAPサーバの機能に依存しない
  * HTML5との親和性を持つ記法


JSPとの比較
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. note:: 

  JSP/Thymeleafの強み・弱み、動作方式の差について説明する。
  
  # Thymeleafの特性がJSPが備えていないものなので、動作方式の差についても簡単に説明する予定。


標準機能の説明
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. note:: 

  静的HTMLファイルにThymeleafの機能を適用し、動的描画が可能なテンプレートを実装していく事をとおし、
  Thymeleafの主な機能を説明する。

Thymeleafテンプレートの実装
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

.. note:: 

  入力フォームを持った静的HTMLをThymeleafのテンプレートに変換し、
  動的に値を渡す実装、及びサーバ側の処理のコード例を記述する。
  加えて、標準機能で利用頻度の多いダイアレクトを簡単に解説する。

  # 主に、ガイドラインの「3.アプリケーション開発」で記述するThymeleafテンプレートで利用するダイアレクトを説明対象とする。
  処理の詳細説明については、「Thymeleaf + Spring の機能」に記述し、ここでは動的にHTMLを変更できる程度とする。


コメント文
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
.. note:: 

  Thymeleafのテンプレートに記述可能な3種類のコメント文の各々の特性、及び利用場面を紹介する。


How to use
--------------------------------------------------------------------------------

アプリケーションの設定
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. note:: 

  ThymeleafをSpringMVCと連携して使用する為の設定の説明をする。


ブランクプロジェクトの設定
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

.. note:: 

  SpringMVCと組み合わせてThymeleafを使用する為のブランクプロジェクトの設定の説明をする。
  
  * pom.xml
  * web.xml
  * Bean定義ファイル（spring-mvc.xml, spring-security.xml）
  * Webコンテナが捕捉するエラーをハンドリングするControllerクラス


Thymeleaf + Spring  の機能
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. note:: 

  Thymeleaf + Spring  の機能の概要を説明する。
  アーキテクチャの概要図を記述し、「4.1.1.3.標準機能の説明」での実装例を通して、
  Springとの連携において、Springがカバーしている機能を説明する。
  

How to extend
--------------------------------------------------------------------------------

カスタムダイアレクトの追加
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Thymeleafでは開発者がカスタムダイアレクトを追加することで、独自に開発したタグや属性、式オブジェクトを使用することができる。

カスタムダイアレクトを追加するにはProcessorやExpressionObjectとDialectを実装する必要がある。

.. tabularcolumns:: |p{0.10\linewidth}|p{0.90\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 10 90
    :class: longtable

    * - 
      - 説明
    * - | Processor
      - | テンプレート内のイベントに対して実行する処理を定義するオブジェクト。
        | タグを定義する要素プロセッサとタグの属性を定義する属性プロセッサなどの種類がある。
    * - | ExpressionObject
      - | テンプレート内の式から呼び出されるオブジェクト。
        | テンプレート内で用いるためのメソッドなどを定義する。特に制約がなく、POJOで定義できる。
    * - | Dialect
      - | ProcessorやExpressionObjectをまとめたライブラリ。
        | テンプレートエンジンにDialectを登録することで、ProcessorやExpressionObjectで定義された文法をテンプレート内で用いることができるようになる。


Processorの実装
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Processorはテンプレート内のイベントに対して実行する処理を定義するオブジェクトである。

Processorを実装するためには、Thymeleafから提供されているインタフェースを実装すればよい。

Thymeleafから提供されている代表的なProcessorのインタフェースを以下に示す。

.. tabularcolumns:: |p{0.20\linewidth}|p{0.80\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 20 80
    :class: longtable

    * - processor
      - 説明
    * - | \ ``org.thymeleaf.processor.element.IElementTagProcessor``\
      - | 開始タグに対して実行される処理を定義するためのインタフェース。対象のタグの内容は参照可能だが、直接変更することはできない。structureHandlerを介してのみ対象のタグの属性やボディを変更することができる。
        | 通常は、\ ``IElementTagProcessor``\ を直接実装するのではなく、\ ``org.thymeleaf.processor.AbstractAttributeTagProcessor``\ などの\ ``IElementTagProcessor``\ を実装した抽象クラスを継承する。
    * - | \ ``org.thymeleaf.processor.elementIElementModelProcessor``\
      - | 開始タグから閉じタグまでの要素全体に対して実行される処理を定義するためのインタフェース。対象の要素全体をモデルとして処理するため、任意の要素を参照、直接変更することができる。また、閉じタグの後など、任意の箇所に要素を追加することもできる。
        | 通常は、\ ``IElementModelProcessor``\ を直接実装するのではなく、\ ``org.thymeleaf.processor.AbstractAttributeModelProcessor``\ などの\ ``IElementModelProcessor``\ を実装した抽象クラスを継承する。

.. note:: 

  上記のインタフェース以外にもイベントごとに対応するインタフェースが提供されている。詳しくは\ `Tutorial: Extending Thymeleaf(Processors) <http://www.thymeleaf.org/doc/tutorials/3.0/extendingthymeleaf.html#processors>`_\ を参照されたい。

Processorでの処理に用いる代表的なインタフェースを以下に示す。

.. tabularcolumns:: |p{0.20\linewidth}|p{0.80\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 20 80
    :class: longtable

    * - インタフェース
      - 説明
    * - | \ ``org.thymeleaf.model.IModel``\
      - | HTMLタグなどを抽象化したインタフェース。開始タグ、ボディ、終了タグなどのHTMLを構成する要素をリストのように保持する。
    * - | \ ``org.thymeleaf.model.IModelFactory``\
      - | \ ``IModel``\ の生成や組み立てをするインタフェース。
    * - | \ ``org.thymeleaf.context.ITemplateContext``\
      - | コンテキストの情報を保持するインタフェース。\ ``IModelFactory``\ などを取得することができる。
    * - | \ ``org.thymeleaf.model.IProcessableElementTag``\
      - | 属性を適用したタグ自体の情報を保持するインタフェース。タグの名前や付与された属性を取得することができる。
    * - | \ ``org.thymeleaf.processor.element.IElementTagStructureHandler``\
      - | 属性を適用したタグや、そのボディ部を編集するためのインタフェース。

ラベル、入力フィールド、エラーメッセージをまとめて出力する独自属性の実装例を以下に示す。

.. note:: 
  独自タグと独自属性どちらでも同じ機能を実装できる場合があるが、独自属性での実装を推奨する。
  
  理由は、静的表示する際、独自タグは\ ``<th:block>``\ と同様に解釈不能となってしまうが、独自属性はその属性のみが無視され、正しく表示できるためである。

**テンプレート記述例**

.. code-block:: html

    <form th:object="${userForm}">
        <div input:form-input="*{userName}"></div>
    </form>

**独自属性の処理結果**

.. code-block:: html

    <form th:object="${userForm}">
        <div class="form-input">
            <label for="userName">userName</label>
            <input th:field="*{userName}" />
            <span th:errors="*{userName}"></span>
        </div>
    </form>

.. note::

  上記の処理結果は実装する独自属性のみをテンプレートエンジンで評価した結果である。
  実際に出力されるHTMLは\ ``th:field``\ 属性などもテンプレートエンジンで評価した形となるため上記の処理結果とは異なる。
  実際のHTML出力については :ref:`custom_dialect_how_to_use` を参照されたい。

**実装例**

.. code-block:: java

    // (1)
    public class FormInputAttributeTagProcessor extends AbstractAttributeTagProcessor {

        public FormInputAttributeTagProcessor(final String dialectPrefix) {
            super(TemplateMode.HTML, // (2)
                    dialectPrefix, // (3)
                    null, false, // (4)
                    "form-input", true, // (5)
                    1000, // (6)
                    true // (7)
            );
        }

        @Override
        protected void doProcess(ITemplateContext context,
                IProcessableElementTag tag, AttributeName attributeName,
                String attributeValue, //(8)
                IElementTagStructureHandler structureHandler) {

            // (9)
            String classValue = tag.getAttributeValue("class");

            // (10)
            if (StringUtils.isEmpty(classValue)) {
                structureHandler.setAttribute("class", "form-input");
            } else {
                structureHandler.setAttribute("class", classValue + " form-input");
            }

            // (11)
            IModelFactory modelFactory = context.getModelFactory();
            IModel model = modelFactory.createModel();

            // (12)
            model.add(modelFactory.createOpenElementTag("label", "for", "userName"));
            model.add(modelFactory.createText(createLabel(attributeValue)));
            model.add(modelFactory.createCloseElementTag("label"));

            model.add(modelFactory.createStandaloneElementTag("input", "th:field",
                    attributeValue));

            model.add(modelFactory.createOpenElementTag("span", "th:errors",
                    attributeValue));
            model.add(modelFactory.createCloseElementTag("span"));

            // (13)
            structureHandler.setBody(model, true);

        }
    
        private String createLabel(String attributeValue){

            // omitted

        }

    }

.. tabularcolumns:: |p{0.10\linewidth}|p{0.90\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 10 90
    :class: longtable

    * - 項番
      - 説明
    * - | (1)
      - | \ ``AbstractAttributeTagProcessor``\（\ ``IElementTagProcessor``\ を実装した抽象クラス）を継承する。
    * - | (2)
      - | HTMLテンプレートに適用する場合は、\ ``TemplateMode.HTML``\ を指定する。
    * - | (3)
      - | 属性の名前に適用するプレフィックスを指定する。通常は、Dialectから引数で受け取った値を指定する。
    * - | (4)
      - | 独自タグを作成する場合、タグ名を設定する。この例では独自属性を作成するので\ ``null``\ を設定している。booleanはタグ名にプレフィックスを適用するかを指定する。
    * - | (5)
      - | 独自属性を作成する場合、属性名を設定する。booleanは属性名にプレフィックスを適用するかを指定する。
    * - | (6)
      - | Dialect内におけるProcessorの優先順位を指定する。値が低いほど優先度が高くなる。
    * - | (7)
      - | Processor適用後に適用対象の属性の記述を削除するか指定する。基本的に適用対象の属性は出力するHTMLには不要となるので\ ``true``\ を指定する。
    * - | (8)
      - | 適用対象の属性が持つ値が渡される。渡される値は式の処理をしていない状態で、上記のテンプレート記述例の場合は\ ``*{userName}``\ が渡される。
    * - | (9)
      - | 適用対象の属性を持つタグから\ ``class``\ 属性の値を取得する。\ ``class``\ 属性が存在しない場合は\ ``null``\ になる。
    * - | (10)
      - | 適用対象の属性を持つタグの\ ``class``\ 属性の値に\ ``form-input``\ を追加する。
    * - | (11)
      - | \ ``IModelFactory``\ を取得し、\ ``IModel``\ を生成する。
    * - | (12)
      - | \ ``IModel``\ にラベル、入力フィールド、エラーメッセージを出力させるための要素を追加する。
    * - | (13)
      - | 渡した\ ``IModel``\適用対象の属性を持つタグのボディを置き換える。booleanは置き換えたボディをテンプレートエンジンで再評価するかを指定する。
        | 上記の例では\ ``th:field``\ 属性と\ ``th:errors``\ 属性を再評価する必要があるため\ ``true``\ を指定している。

.. note:: 

  \ ``AbstractAttributeTagProcessor``\を継承した抽象クラスがいくつか提供されており、より簡単にProcessorを実装することができる場合がある。詳しくは\ `AbstractAttributeTagProcessor <http://www.thymeleaf.org/apidocs/thymeleaf/3.0.8.RELEASE/org/thymeleaf/processor/element/AbstractAttributeTagProcessor.html>`_\ を参照されたい。


ExpressionObjectの実装
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

ExpressionObjectはテンプレート内の式から呼び出すメソッドなどを定義するオブジェクトである。

ExpressionObjectはインタフェース等を実装する必要がなく、POJOで定義できる。

日付(\ ``java.util.Date``\ )をyyyy/MM/dd形式でフォーマットして出力するメソッドを持つ式オブジェクトの実装例を以下に示す。

.. note:: 
  日付を引数で渡した形式でフォーマットして出力する機能はthymeleafから提供されている。

**実装例**

.. code-block:: java

    // (1)
    public class CustomDateFormat {

        // (2)
        public String formatYYYYMMDD(Date date) {
            DateFormat dateFormat = new SimpleDateFormat("yyyy/MM/dd");
            return dateFormat.format(date);
        }

    }

.. tabularcolumns:: |p{0.10\linewidth}|p{0.90\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 10 90
    :class: longtable

    * - 項番
      - 説明
    * - | (1)
      - | POJOとして作成する。
    * - | (2)
      - | 引数に指定された日付をyyyy/MM/dd形式でフォーマットした文字列を返す。


Dialectの実装
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

ProcessorやExpressionObjectで実装した処理をテンプレートに適用するためにはDialectを実装してテンプレートエンジンに追加する必要がある。

Dialectを実装するためにThymeleafから提供されている代表的なインタフェースを以下に示す。

.. tabularcolumns:: |p{0.20\linewidth}|p{0.80\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 20 80
    :class: longtable

    * - インタフェース名
      - 説明
    * - | \ ``org.thymeleaf.dialect.IProcessorDialect``\ 
      - | Processorを登録するDialectを実装するためのインタフェース
        | 通常は、\ ``IProcessorDialect``\ を直接実装するのではなく、\ ``IProcessorDialect``\ を実装した抽象クラス\ ``org.thymeleaf.dialect.AbstractProcessorDialect``\ を継承する。
    * - | \ ``org.thymeleaf.dialect.IExpressionObjectDialect``\ 
      - | ExpressionObjectを登録するDialectを実装するためのインタフェース

.. note:: 

  上記のインタフェース以外にも登録内容ごとに対応するインタフェースが提供されている。詳しくは\ `Tutorial: Extending Thymeleaf(Dialects) <http://www.thymeleaf.org/doc/tutorials/3.0/extendingthymeleaf.html#dialects>`_\ を参照されたい。

ProcessorとExpressionObjectを登録するDialectの実装例を以下に示す。

**実装例（Processorの登録）**

.. code-block:: java

    // (1)
    public class InputFormDialect extends AbstractProcessorDialect {

        // (2)
        public InputFormDialect() {
            super("Input Form Dialect", "input", 1000);
        }

        @Override
        public Set<IProcessor> getProcessors(String dialectPrefix) {

            final Set<IProcessor> processors = new HashSet<IProcessor>();

            // (3)
            processors.add(new FormInputAttributeTagProcessor(dialectPrefix));

            // (4)
            processors.add(
                    new StandardXmlNsTagProcessor(TemplateMode.HTML, dialectPrefix));

            return processors;

        }

    }

.. tabularcolumns:: |p{0.10\linewidth}|p{0.90\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 10 90
    :class: longtable

    * - 項番
      - 説明
    * - | (1)
      - | Processorを登録する場合は、\ ``AbstractProcessorDialect``\ （\ ``IProcessorDialect``\ を実装した抽象クラス）を継承する。
    * - | (2)
      - | 引数はDialect名、登録するProcessorのプレフィックス、Dialectの優先順位である。
        | Processorの適用順序はDialectの優先順位、Processorの優先順位の順番で比較して決められる。
    * - | (3)
      - | 実装したProcessorを登録する。
    * - | (4)
      - | HTMLの最初につける\ ``xmlns:th="http://www.thymeleaf.org"``\ のようなネームスペース表記を削除するために\ ``org.thymeleaf.standard.processor.StandardXmlNsTagProcessor``\ を登録する。

**実装例（ExpressionObjectの登録）**

.. code-block:: java

    // (1)
    public class CustomFormatDialect implements IExpressionObjectDialect {
    
        private Set<String> names = new HashSet<String>() {
            {
                add("customdateformat");
            }
        };

        @Override
        public IExpressionObjectFactory getExpressionObjectFactory() {
            return new IExpressionObjectFactory() {

                // (2)
                @Override
                public Set<String> getAllExpressionObjectNames() {
                    return names;
                }

                // (3)
                @Override
                public Object buildObject(IExpressionContext context,
                        String expressionObjectName) {
                    if ("customdateformat".equals(expressionObjectName)) {
                        return new CustomDateFormat();
                    }
                    return null;
                }

                // (4)
                @Override
                public boolean isCacheable(String expressionObjectName) {
                    return true;
                }

            };
        }

        @Override
        public String getName() {
            return "Date Format(yyyy/MM/dd) Dialect";
        }

    }

.. tabularcolumns:: |p{0.10\linewidth}|p{0.90\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 10 90
    :class: longtable

    * - 項番
      - 説明
    * - | (1)
      - | ExpressionObjectを登録する場合は、\ ``IExpressionObjectDialect``\を実装する。
    * - | (2)
      - | ExpressionObjectの名前を登録する。
    * - | (3)
      - | 実装したExpressionObjectを登録する。引数の\ ``expressionObjectName``\に入る値が(2)で登録した名前に存在する場合、このメソッドが呼ばれる。
    * - | (4)
      - | ExpressionObjectをキャッシュするか指定する。ExpressionObjectが状態によって異なる値を返す場合は\ ``false``\ 、状態にかかわらず返す値が一定である場合は\ ``true``\ を指定する。

.. note:: 

  上記の例ではProcessorとExpressionObjectを別のDialectで登録する例を示しているが、意味的にまとめられる機能であれば一つのDialectで登録することも可能である。


.. _custom_dialect_how_to_use:

カスタムダイアレクトの使用方法
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

作成したカスタムダイアレクトを使用するために必要なアプリケーション設定と出力画面の実装を以下に示す。

**spring-mvc.xml**

.. code-block:: xml

    <bean id="templateEngine" class="org.thymeleaf.spring4.SpringTemplateEngine">

        <!-- omitted -->

        <!-- (1) -->
        <property name="additionalDialects">
            <set>
                <bean class="org.thymeleaf.extras.springsecurity4.dialect.SpringSecurityDialect" />
                <bean class="com.example.sample.dialect.InputFormDialect" />
                <bean class="com.example.sample.dialect.CustomFormatDialect" />
            </set>
        </property>
    </bean>

.. tabularcolumns:: |p{0.10\linewidth}|p{0.90\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 10 90
    :class: longtable

    * - 項番
      - 説明
    * - | (1)
      - | テンプレートエンジンに作成したカスタムダイアレクトを\ ``java.util.Set<IDialect>``\ で追加する。

**view.html**

.. code-block:: html

    <!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org" xmlns:input="http://inputform.sample.example.com"> <!-- (1) -->
    <head>

        <!-- omitted -->

    </head>
    <body>

        <!-- omitted -->

        <!-- (2) -->
        <form th:object="${userForm}">
            <div input:form-input="*{userName}"></div>
        </form>

        <!-- omitted -->

        <span th:text="${#customdateformat.formatYYYYMMDD(date)}">yyyy/MM/dd</span> <!-- (3) -->

        <!-- omitted -->

    </body>
    </html>

.. tabularcolumns:: |p{0.10\linewidth}|p{0.90\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 10 90
    :class: longtable

    * - 項番
      - 説明
    * - | (1)
      - | 作成したDialectの名前空間を定義する。
    * - | (2)
      - | 作成した\ ``input:form-input``\ 属性を指定する。
    * - | (3)
      - | 作成した式オブジェクト\ ``customdateformat``\ を呼び出す。

**出力結果**

.. code-block:: html

    <!DOCTYPE html>
    <html>
    <head>

        <!-- omitted -->

    </head>
    <body>

        <!-- omitted -->

        <form>
            <!-- (1) -->
            <div class="form-input">
                <label for="userName">userName</label>
                <input id="userName" name="userName" value=""/>
            </div>
        </form>


        <!-- omitted -->

        <span>2017/10/30</span>

        <!-- omitted -->

    </body>
    </html>

.. tabularcolumns:: |p{0.10\linewidth}|p{0.90\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 10 90
    :class: longtable

    * - 項番
      - 説明
    * - | (1)
      - | 見やすくするために改行とインデントを入れてあるが、実際には開始タグから閉じタグまで1行で出力される。


Appendix
--------------------------------------------------------------------------------

テンプレートキャッシュの適用
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. note:: 

  テンプレートキャッシュ機能について述べる。

テンプレートキャッシュ機能の説明
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

.. note:: 

  テンプレートキャッシュの機構・方式について説明する。

アプリケーションの設定
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

.. note:: 

  テンプレートキャッシュを有効にする為の設定、各種パラメータの設定について記述する。
  
  # 今後キャッシュ機能を検証し、設定値による挙動の傾向が明らかに表れる場合は、APの特性に応じた推奨値を補足する予定。

