.. _rest_api:

----------------------
Era REST API Explorer
----------------------

Overview
++++++++

.. note::

  所要時間15分の想定です。

このラボではEra REST API Explorerを確認します。

Era REST API Explorerの使用
+++++++++++++++++++++++++++++++

Eraは **”APIファースト”** アーキテクチャを採用しており、外部ツールによる自動化や組織化を可能にするための完全にドキュメント化されたREST APIを提供します。Prismと同様に、EraはAPI機能を選定したり試験するのを容易にするためのRest API Explorerを提供します。

#. メニューバーから、上部右側の **Admin > REST API Explorer** を選択します。

   .. figure:: images/29.png

#. 異なるカテゴリを拡張して使用可能なオペレーションを表示します。
また、Nutanixクラスタを含むデータベースの登録と供給及び複製と更新、プロファイルとSLAの更新、そしてオペレーションやアラートを取得します。

#. 単純なテストとして、**Databases > GET /databases** を展開します

      この機能は詳細を監視する全ての登録されたあるいは提供されたデータベースろ含むJSONを返し、追加のパラメータを要求することはありません。

#. **Try it out > Execute** をクリックします。

   .. figure:: images/30.png

   以下の画像のようなJSONの応答を受け取る必要があります。

   .. figure:: images/32.png

   このAPIはServiceNow,やAnsibleなど、Nutanix Calmのようなツールを使って強固なワークフローを構築することが出来、例えばデータベース自体を複製するための要求をEraCalm eScriptを用い、アプリケーションのWEBレベルのCalm設計図で提供することができるとか、Calm用に新しく提供されたIPを返したり出来ます。

まとめ
+++++++++

- Eraは他の仕組みや自動化されたツールとREST APIとの統合を提供する。
