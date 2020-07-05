.. _patching_oracle:

------------------------
Patching Oracle with Era
------------------------

従来の環境ではデータベース間で一律のパッチレベルを維持することは非常に大変な作業でした。
Eraはバージョン管理されたソフトウェプロファイルを介してデータベースエンジンにパッチを適応させる方法によってそれを容易にします。
データベースサーバグループはEraのWEBインターフェースやCLI、APIを通じてパッチやロールバック出来ます。

OracleはPSUというパッチセットを四半期毎にリリースします。**このラボではOracleとEraを使ったOracle 19c用のGridソフトウェアの展開とパッチ作業を行います。**

Base Oracle VMのパッチ作業
+++++++++++++++++++++++

このエクササイズでは、手動で複製したVMにOctorber PSUパッチを適用し、それを使って新しいバージョンの *Initials*\ **_ORACLE_19C** ソフトウェアプロファイルを作成します。

#. *Initials*\ **_oracle_patched** VMのIPアドレスを **Prism Central** から確認してください

#. 以下の資格情報を使って *Initials*\ **_oracle_patched** VMにSSH接続してください。

   - **User Name** - root
   - **Password** - Nutanix/4u

#. 以下のスクリプトを実行して、OracleとGrid Octorber PSUパッチをダウンロード＆インストールしてください

    .. code-block:: bash

      cd Downloads
      ./applypsu.sh

#. スクリプトが最初に表示するVMの現在のパッチレベルを確認し、リリース日がAprilで有ることを確認してください。

   .. figure:: images/7.png

もしプロンプトが出たら **A** と入力して既存のファイルを上書きし、プロンプトに従ってパッチの展開を続行してください。 スクリプトの実行時間は20分ほどです。

   .. figure:: images/8.png

#. スクリプトの実行が完了したら **Era > Database Servers > List** に戻ります

#. **+ Register** をクリックして **Database Server** の項目を埋めてください

   - **Engine** - Oracle
   - **IP Address or Name of VM** - *Initials*\ _oracle_patched
   -  **Database Version** - 19.0.0.0
   - **Era Drive User** - oracle
   - **Oracle Database Home** - /u02/app/oracle/product/19.0.0/dbhome_1
   -  **Grid Infrastructure Home** - /u01/app/19.0.0/grid
   - **Provide Credentials Through** - Password
   - **Password** - Nutanix/4u

   .. figure:: images/9.png

#. **Register** をクリックします。

#. **Operations** ページで進行状況をモニターしてください。 この処理には5分程かかります。

#. 登録が完了したら、**Era > Profiles > Software** を選択し、自分の*Initials*\ **_ORACLE_19C** Software Profileをクリックします。
Eraは **Database Software** や **Grid Software** を含めたOSへのパッケージインストールの完璧な自己診断を提供することを観察してください。
**Database Software** 内の **Patches Found** を確認してください。

   .. figure:: images/10.png

#. *Initials*\ **_ORACLE_19C** Software Profileをクリックしてから、**+ Create** をクリックして前のステップで登録した
*Initials*\ **_oracle_patched** VMを基にした新しいバージョンを作成します

   .. figure:: images/10b.png

#. 以下の項目を埋めてから **Create** をクリックします。

   - **Name** - *Initials*\ _ORACLE_19C (Oct19 PSU)
   - Select *Initials*\ **_oracle_patched**

   .. figure:: images/11.png

#. **Operations** ページで進行状況をモニターしてください。 この処理には5分程かかります。

#. **Era > Profiles > Software** に戻って自分の *Initials*\ **_ORACLE_19C** Software Profileをクリックします。
**Database Software** と **Grid Infrastructure Software** に追加のパッチが見つかり、バージョン2.0と表示されることを確認してください。

   .. figure:: images/12.png
   *Initials*\ **_oracle_prod** VMにパッチ適応済みのSoftware Profileを適応させる前に、Software Profileは直ちに公開されなければなりません、
   そうでなければEraは利用可能なバージョンや推奨アップデートを表示できません。

#. **2.0** プロファイルを選択して **Update** をクリックします。

#. **Status** 内で、**Published** を選択して **Next** をクリックします。

   .. figure:: images/13.png

#. オプションで、Operating SystemやOracle、Gridソフトウェアに適用されるパッチに関する注意事項を払い出すことができます

   .. figure:: images/14.png

#. **Era > Database Servers > List** に戻って自分の *Initials*\ **_oracle_prod** データベースサーバをクリックします。

#. **Profiles** 内で公開された新しいソフトウェアプロファイルがデータベースサーバで利用可能なアップデートとして推奨されていることを確認してください。 **Update** をクリックします。

   .. figure:: images/15.png

#. ドロップダウンメニューから望むパッチプロファイル(実環境ではいくつかのオプションを公開する可能性があります)を選択して **Patch 1 Database** をクリックしてアップデートを開始します。

   .. note::

      Eraは予め設定されたメンテナンスウィンドウを選択することで、パッチの適用をスケジュールする機能を提供しています。
      クラスタ化されたデータベースへの展開には、Eraはローリングアップデートをサポートし、
      アップデートプロセスを通じてデータベースへのアクセス性を保証しています。

      .. figure:: images/17.png

#. **Operations** ページで処理の進行状況を確認してください。 この処理には25分ほどかかります。

   パッチ処理の間、EraはデータベースやGridサービスを適宜停止、VMをシャットダウンさせ、関連する仮想ディスクを2.0 Software Profileからシンクローンでリプレイス、データベースサーバを起動し、オンラインに戻します。

   .. figure:: images/18.png

#. パッチ処理が完了したら、VMが正常に動作しているかどうかEraの外から簡単に確認できます。自分の *Initials*\ **_oracle_prod** VMに以下の承認情報でSSH接続します、

   - **User Name** - oracle
   - **Password** - Nutanix/4u

#. インストールしたパッチのバージョンを表示するために以下のコマンドを実行してください

   ::

      $ORACLE_HOME/OPatch/opatch lsinventory | egrep 'appl|desc'

   .. figure:: images/19.png

まとめ
+++++++++

- Software Profilesはバージョン管理され、既存のデータベースサーバへの一貫したアップデートの展開のために使用されます。
- Software Profilesはパッチ処理を簡略化し、必要な手動でのパッチ処理の量を軽減します。
- アップデートのスケジューリングはウィンドウの切り替えやSLAアップタイムウィンドウに使います。
