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

Processorを実装するためには、Thymeleafから提供されているインタフェースを実装しなければならない。

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

条件に合わせて\ ``class``\ 属性を変更したうえでtodoを出力する独自属性の実装例を以下に示す。実装する独自属性のテンプレート記述とHTML出力の例については :ref:`custom_dialect_how_to_use` を参照されたい。

.. note:: 
  独自タグと独自属性どちらでも同じ機能を実装できる場合があるが、独自属性での実装を推奨する。
  
  理由は、静的表示する際、独自タグは\ ``<th:block>``\ と同様に解釈不能となってしまうが、独自属性はその属性のみが無視され、正しく表示できるためである。

**実装例**

.. code-block:: java

    // (1)
    public class TodoTitleTagProcessor extends AbstractAttributeTagProcessor {

        public TodoTitleTagProcessor(final String dialectPrefix) {
            super(TemplateMode.HTML, // (2)
                    dialectPrefix, // (3)
                    null, false // (4)
                    "title", true,  // (5)
                    1000, // (6)
                    true // (7)
            );
        }

        @Override
        protected void doProcess(ITemplateContext context,
                IProcessableElementTag tag, AttributeName attributeName,
                String attributeValue,
                IElementTagStructureHandler structureHandler) {

            Object expressionResult = null;

            // (8)
            if (attributeValue != null) {
                final IStandardExpression expression = EngineEventUtils
                        .computeAttributeExpression(context, tag, attributeName,
                                attributeValue);
                expressionResult = expression.execute(context);
            }

            if (expressionResult instanceof Todo) {
                Todo todo = (Todo) expressionResult;

                if (todo.isFinished()) {

                    // (9)
                    structureHandler.setAttribute("class", "strike");
                }

                // (10)
                structureHandler.setBody(HtmlEscape.escapeHtml5Xml(todo
                        .getTodoTitle()),false);

            }
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
      - | 適用対象の属性の値（\ ``attributeValue``\ ）の式を処理する。
    * - | (9)
      - | \ ``class="strike"``\ を適用対象の属性を持つタグに付与する。
    * - | (10)
      - | \ ``structureHandler#setBody``\ の第一引数の文字列でボディを置き換える。HTMLのソースコードとして値がそのまま出力されるので、HTMLエスケープを行う。第二引数のbooleanは置き換えたボディにテンプレートエンジンで再評価を行うか指定する。

.. note:: 

  \ ``AbstractAttributeTagProcessor``\を継承した抽象クラスがいくつか提供されており、より簡単にProcessorを実装することができる場合がある。詳しくは\ `AbstractAttributeTagProcessor <http://www.thymeleaf.org/apidocs/thymeleaf/3.0.8.RELEASE/org/thymeleaf/processor/element/AbstractAttributeTagProcessor.html>`_\ を参照されたい。
  \ ``org.thymeleaf.standard.processor.AbstractStandardExpressionAttributeTagProcessor``\ を継承して実装した例を以下に示す。
  
  **実装例**

    .. code-block:: java

        // (1)
        public class TodoTitleTagProcessor extends
                                 AbstractStandardExpressionAttributeTagProcessor {

            public TodoTitleTagProcessor(final String dialectPrefix) {
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

                if (expressionResult instanceof Todo) {

                    // (7)
                    Todo todo = (Todo) expressionResult;

                    
                    if (todo.isFinished()) {

                        structureHandler.setAttribute("class", "strike");
                    }

                    structureHandler.setBody(HtmlEscape.escapeHtml5Xml(todo
                            .getTodoTitle()),false);

                }
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
          - | \ ``AbstractStandardExpressionAttributeTagProcessor``\（\ ``AbstractAttributeTagProcessor``\ を継承した抽象クラス）を継承する。
        * - | (2)
          - | HTMLテンプレートに適用する場合は、\ ``TemplateMode.HTML``\ を指定する。
        * - | (3)
          - | 属性の名前に適用するプレフィックスを指定する。Dialectから引数として受け取った値を指定する。
        * - | (4)
          - | Processorの処理対象となる属性名を指定する。
        * - | (5)
          - | Dialect内におけるProcessorの優先順位を指定する。値が低いほど優先度が高くなる。
        * - | (6)
          - | Processor適用後に適用対象の属性の記述を削除するか指定する。基本的に適用対象の属性は出力するHTMLには不要となるので\ ``true``\ を指定する。
        * - | (7)
          - | \ ``expressionResult``\ には適用対象の属性の値が式を処理した形式で格納されている。


上記の例で行った処理以外にも、適用対象の属性をもつタグの名前や他の属性を取得をすることができる\ ``tag``\ や、
タグやテキストなどのテンプレートを構成する要素をリストのように扱い、要素の参照、追加、変更などを行うことができるモデルを用いた処理を行うことができる。

\ ``tag``\ とモデルを用いた処理の例を以下に示す。

.. code-block:: java

        // (1)
        String elementName = tag.getElementCompleteName();

        // (2)
        String url = "http://sample.com"
        final IModelFactory modelFactory = context.getModelFactory();
        final IModel model = modelFactory.createModel();
        model.add(modelFactory.createOpenElementTag("a", "href", url));
        model.add(modelFactory.createText(url));
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
      - | \ ``model``\ を作成し、ボディを置き換える。この例ではボディは\ ``<a href="http://sample.com">http://sample.com</a>``\ に置き換えられる。

ExpressionObjectの実装
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

ExpressionObjectはテンプレート内の式から呼び出すメソッドなどを定義するオブジェクトである。

ExpressionObjectはインタフェース等を実装する必要がなく、POJOで定義できる。

日付(Joda Time)をyyyy/MM/dd形式でフォーマットして出力するメソッドを持つ式オブジェクトの実装例を以下に示す。

.. note:: 
  日付(Joda Time)をフォーマットして出力する機能は\ `thymeleaf-joda-dialect  <https://github.com/ultraq/thymeleaf-joda-dialect>`_\が提供する式オブジェクトで行うことができる。

**実装例**

.. code-block:: java

    // (1)
    public class JodaDatetimeFormat {

        // (2)
        public String format(DateTime date) {
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

ProcessorとExpressionObjectを両方登録するDialectの実装例を以下に示す。

**実装例**

.. code-block:: java

    // (1)
    public class TodoDiarect extends AbstractProcessorDialect implements
                           IExpressionObjectDialect {

        private static final String KEY = "myjoda";

        private static final Set<String> names = new HashSet<String>() {
            {
                add(KEY);
            }
        };

        // (2)
        public TodoDiarect() {
            super("My Custom Dialect", "todo", 1000);
        }

        public Set<IProcessor> getProcessors(final String dialectPrefix) {
            final Set<IProcessor> processors = new HashSet<IProcessor>();
            
            // (3)
            processors.add(new TodoTitleTagProcessor(dialectPrefix));
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
                public Set<String> getAllExpressionObjectNames() {
                    return names;
                }

                // (6)
                @Override
                public Object buildObject(IExpressionContext context,
                        String expressionObjectName) {

                    if (KEY.equals(expressionObjectName)) {
                        return new JodaDatetimeFormat();
                    }
                    return null;
                }

                // (7)
                @Override
                public boolean isCacheable(String expressionObjectName) {
                    return true;
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
      - | Processorを登録する場合は、\ ``AbstractProcessorDialect``\ （\ ``IProcessorDialect``\ を実装した抽象クラス）を継承する。
        | また、ExpressionObjectを登録する場合は、\ ``IExpressionObjectDialect``\を実装する。
    * - | (2)
      - | 引数はDialect名、登録するProcessorのプレフィックス、Dialectの優先順位である。
        | Processorの適用順序はDialectの優先順位、Processorの優先順位の順番で比較して決められる。
    * - | (3)
      - | 実装したProcessorを登録する。
    * - | (4)
      - | HTMLの最初につける\ ``xmlns:th="http://www.thymeleaf.org"``\ のようなネームスペース表記を削除するために\ ``org.thymeleaf.standard.processor.StandardXmlNsTagProcessor``\ を登録する。
    * - | (5)
      - | ExpressionObjectの名前を登録する。
    * - | (6)
      - | 実装したExpressionObjectを登録する。引数の\ ``expressionObjectName``\に入る値が(5)で登録した名前に存在する場合、このメソッドが呼ばれる。
    * - | (7)
      - | ExpressionObjectをキャッシュするか指定する。ExpressionObjectが状態によって異なる値を返す場合は\ ``false``\ 、状態にかかわらず返す値が一定である場合は\ ``true``\ を指定する。

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
      - | テンプレートエンジンに作成したカスタムダイアレクトを\ ``java.util.Set<IDialect>``\ で追加する。

**view.html**

.. code-block:: html

    <!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org" xmlns:todo="http://todosample"> <!-- (1) -->
    <head>

        <!-- omitted -->

    </head>
    <body>

        <!-- omitted -->

        <!-- (2) -->
        <span todo:title="${todo}">
            todo title
        </span>

        <!-- omitted -->

        <span th:text="${#myjoda.format(date)}">yyyy/MM/dd</span> <!-- (3) -->

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
      - | 作成した\ ``todo:title``\ 属性を指定する。
    * - | (3)
      - | 作成した式オブジェクト\ ``myjoda``\ を呼び出す。

**出力結果**

.. code-block:: html

    <!DOCTYPE html>
    <html>
    <head>

        <!-- omitted -->

    </head>
    <body>

        <!-- omitted -->

        <span class="strike">sample TODO</span>

        <!-- omitted -->

        <span>2017/10/30</span>

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

