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

Thymeleafでは開発者がカスタムダイアレクトを追加することで、独自に開発したタグや属性、ユーティリティメソッドを使用することができる。

カスタムダイアレクトを追加するにはProcessorやExpressionObjectとDialectを実装する必要がある。

Processorの実装
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Processorはテンプレート内のイベントに対して実行する処理を定義するオブジェクトである。

Thymeleafから提供されているProcessorのインタフェースを以下に示す。

.. tabularcolumns:: |p{0.20\linewidth}|p{0.80\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 20 80
    :class: longtable

    * - processor
      - 説明
    * - | \ ``org.thymeleaf.processor.element.IElementTagProcessor``\
      - | 開始タグに対して実行される処理を定義するためのインタフェース。対象のタグの内容は参照可能だが、直接変更することはできない。structureHandlerを介してのみ対象のタグの属性やボディを変更することができる。
    * - | \ ``org.thymeleaf.processor.elementIElementModelProcessor``\
      - | 開始タグから閉じタグまでの要素全体に対して実行される処理を定義するためのインタフェース。対象の要素全体を参照、直接変更することができる。閉じタグの後に要素を追加することもできる。
    * - | その他
      - | イベントごとに対応するインタフェースが提供されている。詳しくは\ `Tutorial: Extending Thymeleaf(Processors) <http://www.thymeleaf.org/doc/tutorials/3.0/extendingthymeleaf.html#processors>`_\ を参照されたい。

終了フラグに合わせて\ ``class``\ 属性を変更したうえでtodoを出力する独自属性の例を以下に示す。

**実装例**

.. code-block:: java

    // (1)
    public class TodoTagProcessor extends
                             AbstractStandardExpressionAttributeTagProcessor {

        public TodoTagProcessor(final String dialectPrefix) {
            super(TemplateMode.HTML, // (2)
                    dialectPrefix, // (3)
                    "title", // (4)
                    1000, // (5)
                    true // (6)
            );
        }

        @Override
        protected void doProcess(ITemplateContext context,
                IProcessableElementTag tag, AttributeName attributeName,
                String attributeValue, Object expressionResult,
                IElementTagStructureHandler structureHandler) {

            // (7)
            Todo todo = (Todo) expressionResult;

            // (8)
            if (todo.isFinished()) {
                structureHandler.setAttribute("class", "strike");
            }

            // (9)
            structureHandler.setBody(HtmlEscape.escapeHtml5Xml(todo.getTodoTitle()),
                false);

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
      - | \ ``org.thymeleaf.standard.processor.AbstractAttributeTagProcessor``\（\ ``IElementTagProcessor``\ を実装した抽象クラス）を継承する。
    * - | (2)
      - | 適用対象となるテンプレートモードを指定する。
    * - | (3)
      - | タグや属性の名前に適用するプレフィックスを指定する。
    * - | (4)
      - | 適用対象となる属性を指定する。
    * - | (5)
      - | Dialect内におけるProcessorの優先順位を指定する。値が低いほど優先度が高くなる。
    * - | (6)
      - | Processor適用後に適用対象の属性の記述を削除するか指定する。基本的に適用対象の属性は出力するHTMLには不要となるので\ ``true``\ を指定する。
    * - | (7)
      - | \ ``expressionResult``\ には適用対象の属性の値が式を処理した形式で格納されている。
    * - | (8)
      - | 終了フラグが\ ``true``\ の場合、\ ``class="strike"``\ 属性を適用対象の属性を持つタグに付与する。
    * - | (9)
      - | \ ``structureHandler#setBody``\ の第一引数の文字列でボディを置き換える。第二引数のbooleanは置き換えたボディにテンプレート処理を行うかを指定する。

その他の代表的な処理の使用例を以下に示す。

.. code-block:: java

        // (1)
        String elementName = tag.getElementCompleteName();

        // (2)
        final IModelFactory modelFactory = context.getModelFactory();
        final IModel model = modelFactory.createModel();
        model.add(modelFactory.createOpenElementTag("a"));
        model.add(modelFactory.createText("http://sample.com"));
        model.add(modelFactory.createCloseElementTag("a"));
        structureHandler.setBody(model, false);

.. tabularcolumns:: |p{0.10\linewidth}|p{0.90\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 10 90
    :class: longtable

    * - 項番
      - 説明
    * - | (1)
      - | 適用対象の属性を持つタグの名前を取得する。
    * - | (2)
      - | \ ``model``\ を作成し、ボディを置き換える。この例ではボディは\ ``<a>http://sample.com</a>``\ に置き換えられる。

ExpressionObjectの実装
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

ExpressionObjectは独自のユーティリティメソッドを定義するオブジェクトである。

ExpressionObjectはインタフェース等を実装する必要がなく、POJOで定義できる。

日付(Joda Time)をyyyy/MM/dd形式でフォーマットして出力する例を以下に示す。

**実装例**

.. code-block:: java

    // (1)
    public class MyExpressionObject {

        // (2)
        public static String format(DateTime date) {
            return date.toString("yyyy/MM/dd");
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
      - | 引数に指定された日付(Joda Time)をyyyy/MM/dd形式でフォーマットした文字列を返す。


Dialectの実装
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

ProcessorやExpressionObjectで実装したロジックをテンプレート処理で実行するためにはDialectを実装してTemplateEngineに追加する必要がある。

Dialectを実装するためにThymeleafから提供されているインタフェースを以下に示す。

.. tabularcolumns:: |p{0.20\linewidth}|p{0.80\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 20 80
    :class: longtable

    * - インタフェース名
      - 説明
    * - | \ ``org.thymeleaf.dialect.IProcessorDialect``\ 
      - | Processorを登録するDialectを実装するためのインタフェース
    * - | \ ``org.thymeleaf.dialect.IExpressionObjectDialect``\ 
      - | Expression Objectを登録するDialectを実装するためのインタフェース
    * - | その他
      - | 登録内容ごとに対応するインタフェースが提供されている。詳しくは\ `Tutorial: Extending Thymeleaf(Dialects) <http://www.thymeleaf.org/doc/tutorials/3.0/extendingthymeleaf.html#dialects>`_\ を参照されたい。

**実装例**

.. code-block:: java

    // (1)
    public class MyDiarect extends AbstractProcessorDialect implements
                           IExpressionObjectDialect {

        final static String KEY = "myjoda";

        final Set<String> names = new HashSet<String>() {
            {
                add(KEY);
            }
        };

        // (2)
        public MyDiarect() {
            super("My Custom Dialect", "todo", 1000);
        }

        public Set<IProcessor> getProcessors(final String dialectPrefix) {
            final Set<IProcessor> processors = new HashSet<IProcessor>();
            
            // (3)
            processors.add(new MyProcessor(dialectPrefix));
            // (4)
            processors.add(
                    new StandardXmlNsTagProcessor(TemplateMode.HTML, dialectPrefix));
            return processors;
        }

        @Override
        public IExpressionObjectFactory getExpressionObjectFactory() {
            return new IExpressionObjectFactory() {

                // (5)
                @Override
                public boolean isCacheable(String expressionObjectName) {
                    return false;
                }

                // (6)
                @Override
                public Set<String> getAllExpressionObjectNames() {
                    return names;
                }

                // (7)
                @Override
                public Object buildObject(IExpressionContext context,
                        String expressionObjectName) {

                    if (KEY.equals(expressionObjectName)) {
                        return new MyExpressionObject();
                    }
                    return null;
                }

            };
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
      - | Processorを登録する場合は、\ ``org.thymeleaf.dialect.AbstractProcessorDialect``\ （\ ``IProcessorDialect``\ を実装した抽象クラス）を継承する。
        | また、ExpressionObjectを登録する場合は、\ ``IExpressionObjectDialect``\を実装する。
    * - | (2)
      - | 引数はDialect名、登録するProcessorのプレフィックス、優先順位である。
    * - | (3)
      - | 実装したProcessorを登録する。
    * - | (4)
      - | HTMLの最初につける\ ``xmlns:th="http://www.thymeleaf.org"``\ のようなネームスペース表記を削除するために\ ``org.thymeleaf.standard.processor.StandardXmlNsTagProcessor``\ を登録する。
    * - | (5)
      - | ExpressionObjectをキャッシュするか指定する。ExpressionObjectが状態によって異なる値を返す場合は\ ``false``\ を指定する。
    * - | (6)
      - | ExpressionObjectの名前を登録する。
    * - | (7)
      - | 実装したExpressionObjectを登録する。

カスタムダイアレクトの使用方法
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

作成したカスタムダイアレクトを使用するために必要なアプリケーション設定と出力画面の実装を以下に示す。

**spring-mvc.xml**

.. code-block:: xml

    <bean id="templateEngine" class="org.thymeleaf.spring4.SpringTemplateEngine">
    
        <property name="templateResolver" ref="templateResolver" />
        <property name="enableSpringELCompiler" value="true" />
        <!-- (1) -->
        <property name="additionalDialects">
            <set>
                <bean class="com.example.sample.app.dialect.MyDialect" />
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
      - | TemplateEngine に作成したカスタムダイアレクトを追加する。

**view.html**

.. code-block:: html

    <!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org" xmlns:todo="http://todosample">
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>View</title>
    <link rel="stylesheet" th:href="@{/resources/app/css/styles.css}" type="text/css">
    </head>
    <body>

        <!-- omitted -->

        <!-- (1) -->
        <span todo:title="${todo}">
            todo title
        </span>

        <!-- omitted -->

        <div th:text="${#myjoda.format(date)}">yyyy/MM/dd</div> <!-- (2) -->

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
      - | 作成したタグまたは属性を指定する。
    * - | (2)
      - | 作成したユーティリティメソッドを呼び出す。

**出力結果**

.. code-block:: html

    <!DOCTYPE html>
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>View</title>
    <link rel="stylesheet"
        href="/todo/resources/app/css/styles.css" type="text/css">
    </head>
    <body>

        <!-- omitted -->

        <span class="strike">sample TODO</span>

        <!-- omitted -->

        <div>2017/10/30</div>

        <!-- omitted -->

    </body>
    </html>


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

