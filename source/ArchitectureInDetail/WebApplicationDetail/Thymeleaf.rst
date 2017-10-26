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
    * - | IElementTagProcessor
      - | 単一の開始タグまたは開始タグ内の属性に対して実行される処理を定義するためのインタフェース。
    * - | IElementModelProcessor
      - | 開始タグから閉じタグまでの要素全体に対して実行される処理を定義するためのインタフェース。
    * - | ITemplateBoundariesProcessor
      - | テンプレート処理操作の開始時または終了時に実行される処理を定義するためのインタフェース。
    * - | その他
      - | ITextProcessor, ICommentProcessorなど、それぞれのイベントに対応するインタフェースが提供されている。詳しくは\ `Tutorial: Extending Thymeleaf <http://www.thymeleaf.org/doc/tutorials/3.0/extendingthymeleaf.html#other-processors>`_\ を参照されたい。

IElementTagProcessorを実装し、独自属性を作成する例を以下に示す。

**実装例**

.. code-block:: java

    package com.example.sample.app.dialect;

    import javax.servlet.http.HttpServletRequest;

    import org.terasoluna.gfw.web.token.transaction.TransactionToken;
    import org.terasoluna.gfw.web.token.transaction.TransactionTokenInterceptor;
    import org.thymeleaf.context.ITemplateContext;
    import org.thymeleaf.context.IWebContext;
    import org.thymeleaf.engine.AttributeName;
    import org.thymeleaf.model.IProcessableElementTag;
    import org.thymeleaf.processor.element.AbstractAttributeTagProcessor;
    import org.thymeleaf.processor.element.IElementTagStructureHandler;
    import org.thymeleaf.templatemode.TemplateMode;

    // (1)
    public class MyProcessor extends AbstractAttributeTagProcessor {

        private static final String ATTR_NAME = "transaction";

        private static final int PRECEDENCE = 1000;

        public MyProcessor(final String dialectPrefix) {
            super(TemplateMode.HTML, // (2)
                    dialectPrefix, // (3)
                    null, // (4)
                    false, // (5)
                    ATTR_NAME, // (6)
                    true, // (7)
                    PRECEDENCE, // (8)
                    true); // (9)
        }

        // (10)
        @Override
        protected void doProcess(ITemplateContext context,
                IProcessableElementTag tag, AttributeName attributeName,
                String attributeValue,
                IElementTagStructureHandler structureHandler) {

            IWebContext webContext = (IWebContext) context;

            HttpServletRequest request = webContext.getRequest();

            TransactionToken nextToken = (TransactionToken) request.getAttribute(
                    TransactionTokenInterceptor.NEXT_TOKEN_REQUEST_ATTRIBUTE_NAME);

            structureHandler.setAttribute("type", "hidden");
            structureHandler.setAttribute("name",
                    TransactionTokenInterceptor.TOKEN_REQUEST_PARAMETER);
            structureHandler.setAttribute("value", nextToken.getTokenString());
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
      - | \ ``AbstractAttributeTagProcessor``\（\ ``IElementTagProcessor``\を実装した抽象クラス）を継承する。
    * - | (2)
      - | 適用対象となるテンプレートモードを指定する。
    * - | (3)
      - | タグや属性の名前に適用するprefixを指定する。
    * - | (4)
      - | 作成する独自属性の適用対象となるタグ名を指定する。nullの場合は任意のタグとなる。
    * - | (5)
      - | 適用対象となるタグの名前にprefixを適用させるか指定する。falseの場合はprefixを適用させない。
    * - | (6)
      - | 適用対象となる属性を指定する。
    * - | (7)
      - | 適用対象となる属性の名前にprefixを適用させるか指定する。trueの場合はprefixを適用させる。
    * - | (8)
      - | Processorの優先順位を指定する。値が低いほど優先度が高くなる。
    * - | (9)
      - | Processor適用後に適用対象となった属性の記述を削除するか指定する。trueの場合は削除する。
    * - | (10)
      - | トランザクショントークンをhidden項目として出力するための属性を追加する。


ExpressionObjectの実装
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

ExpressionObjectは独自のユーティリティメソッドを定義するオブジェクトである。

ExpressionObjectはインタフェース等を実装する必要がなく、POJOで定義できる。

**実装例**

.. code-block:: java

    package com.example.sample.app.dialect;

    import java.util.regex.Pattern;

    // (1)
    public class MyUtil {

        private static final Pattern URL_PATTERN = Pattern.compile(
                "(http|https)://[A-Za-z0-9\\._~/:\\-?&=%;]+");

        // (2)
        public static String link(String value) {
            if (value == null || value.isEmpty()) {
                return "";
            }
            return URL_PATTERN.matcher(value).replaceAll("<a href=\"$0\">$0</a>");
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
      - | 引数に指定されたURLにジャンプするためのハイパーリンク(\ ``<a>``\ タグ)を出力するユーティリティメソッドを定義する。


Dialectの実装
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

ProcessorやExpressionObjectで実装したロジックをテンプレート処理で実行するためにはDialectを実装してTemplateエンジンに追加する必要がある。

Dialectを実装するためにThymeleafから提供されているインタフェースを以下に示す。

.. tabularcolumns:: |p{0.20\linewidth}|p{0.80\linewidth}|
.. list-table::
    :header-rows: 1
    :widths: 20 80
    :class: longtable

    * - インタフェース名
      - 説明
    * - | IProcessorDialect
      - | Processorを登録するDialectを実装するためのインタフェース
    * - | IPreProcessorDialect
      - | Pre-Processorを登録するDialectを実装するためのインタフェース
    * - | IPostProcessorDialect
      - | Post-Processorを登録するDialectを実装するためのインタフェース
    * - | IExpressionObjectDialect
      - | Expression Objectを登録するDialectを実装するためのインタフェース
    * - | IExecutionAttributeDialect
      - | Execution Attributeを登録するDialectを実装するためのインタフェース

**実装例**

.. code-block:: java

    package com.example.sample.app.dialect;

    import java.util.HashSet;
    import java.util.Set;

    import org.thymeleaf.context.IExpressionContext;
    import org.thymeleaf.dialect.AbstractProcessorDialect;
    import org.thymeleaf.dialect.IExpressionObjectDialect;
    import org.thymeleaf.expression.IExpressionObjectFactory;
    import org.thymeleaf.processor.IProcessor;
    import org.thymeleaf.standard.processor.StandardXmlNsTagProcessor;
    import org.thymeleaf.templatemode.TemplateMode;

    // (1)
    public class MyDiarect extends AbstractProcessorDialect implements
                           IExpressionObjectDialect {

        final static String KEY = "myutil";

        final Set<String> names = new HashSet<String>() {
            {
                add(KEY);
            }
        };

        // (2)
        public MyDiarect() {
            super("My Custom Dialect", "mydialect", 1000);
        }

        // (3)
        public Set<IProcessor> getProcessors(final String dialectPrefix) {
            final Set<IProcessor> processors = new HashSet<IProcessor>();
            
            processors.add(new MyProcessor(dialectPrefix));
            processors.add(
                    new StandardXmlNsTagProcessor(TemplateMode.HTML, dialectPrefix));
            return processors;
        }

        // (4)
        @Override
        public IExpressionObjectFactory getExpressionObjectFactory() {
            return new IExpressionObjectFactory() {

                @Override
                public boolean isCacheable(String expressionObjectName) {
                    return true;
                }

                @Override
                public Set<String> getAllExpressionObjectNames() {
                    return names;
                }

                @Override
                public Object buildObject(IExpressionContext context,
                        String expressionObjectName) {

                    if (KEY.equals(expressionObjectName)) {
                        return MyExpressionObject.class;
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
      - | 作成したProcessorを登録するために\ ``AbstractProcessorDialect``\（\ ``IProcessorDialect``\ を実装した抽象クラス）を継承する。
        | また、作成したExpressionObjectを登録するために\ ``IExpressionObjectDialect``\を実装する。
    * - | (2)
      - | 引数はDialect名、登録するProcessorのprefix、優先順位である。
    * - | (3)
      - | 実装したProcessorを登録する。
    * - | (4)
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
      - | \ ``TemplateEngine``\ に作成したカスタムダイアレクトを追加する。

**view.html**

.. code-block:: html

    <!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>View</title>
    <link rel="stylesheet" th:href="@{/resources/app/css/styles.css}" type="text/css">
    </head>
    <body>

        <!-- omitted -->

        <input mydialect:transaction> <!-- (1) -->

        <!-- omitted -->

        <div th:utext="${#myutil.link('http://www.dummy.com/')}">dummy url</div> <!-- (2) -->

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

    <!-- omitted -->

    <input type="hidden" name="_TRANSACTION_TOKEN" value="todoTransactionTokenCheck~e599c943eccf81d689ac29c53300921a~450d85ddd30755842790c0e00a3ee00b">

    <!-- omitted -->

    <div><a href="http://www.dummy.com/">http://www.dummy.com/</a></div>

    <!-- omitted -->


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

