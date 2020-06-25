.. _deploy_oracle:

-----------------
Deploying Oracle
-----------------

従来のデータベースVMの展開は下の図のようになっていて、最初はデータベースのITチケット(開発、試験、QA、解析など)から始まります。次にいくつかのチームが必要とするストレージリソースを展開する必要があります。基盤の準備が整ったら、DBA(データベースアドミニストレーター)はデータベースソフトウェアの設定と供給をします。データベースソフトの供給が済んだら、トレーニングやデータ保護/バックアップポリシーの制定を行う必要があります。そしてようやくエンドユーザーにデータベースを引き渡すことができるのです。これには多くのハンドオフ(引き渡し、引き継ぎ)があり、摩擦や情報の欠落を生む可能性があります。

.. figure:: images/0.png

一方、Nutanixクラスタ及びEraを用いれば、データベースの供給と保護をこのイントロを読むより早く行えるはずです。

** このラボでは、OracleVMの展開をOracle 19c Source VMを素にしたクローンから行います。このVMはマスターイメージとして振る舞い、Eraを使った追加のOracleVMの展開のためのプロファイルを作成します。 **

Clone Source Oracle VM
++++++++++++++++++++++

このVMはApril PSUパッチ適応済みのOracle 19cが動作しています。

#. **Prism Central** で、 :fa:`bars` **> Virtual Infrastructure > VMs** と選択します。

   .. figure:: images/1.png

#. **Oracle19cSource** のチェックボックスを選択し、**Actions > Clone** とクリックします。

   .. figure:: images/1b.png

#. 以下の項目を埋めてください

   - **Number Of Clones** - 1
   - **Name** - *Initials*\ _oracle_base
   - **Description** - (Optional) Description for your VM.
   - **vCPU(s)** - 2
   - **Number of Cores per vCPU** - 1
   - **Memory** - 8 GiB

**Save** をクリックし、VMを作成する

      これでこのVMのコピーが出来て、のちのOctober PSUパッチのインストールに使われます。

#. VMが作成できたら、もう一度 **Actions > Clone** をクリックします。

#. 名前を *Initials*\ **_oracle_patched** と変更し、 **Save** をクリックします。

#. 両方のVMを選択し、**Actions > Power On** をクリックします。

Era Resourcesを見つける
+++++++++++++++++++++++

EraはAHVやESXiに導入できる仮想アプライアンスとして提供されており、共有Eraサーバはメモリリソースの節約のために既にあなたのクラスタ上にあります。

.. note::

  Eraアプライアンスのインストール方法については `こちら <https://portal.nutanix.com/#/page/docs/details?targetId=Nutanix-Era-User- Guide-v12:era-era-installing-on-ahv-t.html>`_.

#. **EraServer-\ **VM に割り当てられたIPアドレスを、**Prism Central > VMs > List** の **IP Addresses** の列から確認します。

#. \https://*ERA-VM-IP:8443*/ を新しいタブで開きます。

#. 以下の認証情報に従ってログインします

   - **Username** - admin
   - **Password** - nutanix/4u

#. **Dashboard** のドロップダウンメニューから **Administration** を選択します。

#.Eraが指定したクラスタに設定されていることを、**Cluster Details** から確認してください。

   .. figure:: images/6.png

#. **Era Resources** を左側のメニューから選択してください。

#. 設定したネットワークを確認します。 もし **VLANs Available for Network Profiles** でネットワークが見つからないなら、 **Add** をクリックします。 **Secondary** VLANを選んで、**Add** をクリックします。

   .. note::

   クラスタのIPAMを利用してアドレスを管理するため、** IPアドレスプールの管理** をオフのままにします。

   .. figure:: images/era_networks_001.png

#. ドロップダウンメニューから **SLAs** を選択します。

   .. figure:: images/7a.png

   Eraには5つのSLAが組み込まれています(Gold, Silver, Bronze, Zero, and Brass)。SLAはデータベースサーバのバックアップ方法を制御し、継続的な保護や日毎、週毎、月毎、四半期毎の保護間隔を組み合わせることが出来ます。

#. ドロップダウンメニューから**Profiles** を選択します。

   プロファイルはリソースや設定を予め定義して環境の供給を一貫してシンプルにし、設定が煩雑になることを抑制します。例えば、Compute ProfilesはデータベースサーバのサイズをvCPUsやそのコア、メモリなどの詳細を考慮して決定できます。

#. **Network** 内に定義されたネットワークが無い場合、**+ Create** をクリックします。

   .. figure:: images/8.png

#. 以下の項目を埋めて、 **Create** をクリックします。

   - **Engine** - ORACLE
   - **Type** - Single Instance
   - **Name** - Primary_ORACLE_NETWORK
   - **Public Service VLAN** - Secondary

   .. figure:: images/9.png

Eraを用いたOracle serverの登録
+++++++++++++++++++++++++++++++

このエクササイズでは、April PSU VMの登録と、それをあなたのOracle 19c Software Profileのバージョン1.0として登録します。

#. **Era** 内のドロップダウンメニューから **Database Servers** を選択します。し、左側のメニューの **List** を選択します。

#. **+ Register** をクリックし、以下の **Darabase Server** の項目を埋めてください。

   - **Engine** - Oracle
   - **IP Address or Name of VM** - *Initials*\ _oracle_base
   - **Database Version** - 19.0.0.0
   - **Era Drive User** - oracle
   - **Oracle Database Home** - /u02/app/oracle/product/19.0.0/dbhome_1
   - **Grid Infrastructure Home** - /u01/app/19.0.0/grid
   - **Provide Credentials Through** - Password
   - **Password** - Nutanix/4u

   .. note::

      Era Drive User はノンパスでsudoアクセス可能な任意のユーザーです、Eraはスナップショットの取得など様々な操作のためにこの資格情報を使用します。

      Era Drive User はノンパスでsudoアクセス可能な任意のユーザーです、Eraはスナップショットの取得など様々な操作のためにこの資格情報を使用します。

      Grid Infrastructure HomeはOracle Infrastructureソフトウェアがインストールされたディレクトリで、Oracle RACかSHIMAデータベースにのみ適用されます。

   .. figure:: images/2.png

#. **Register** をクリックします。

#.ドロップダウンメニューから **Operarions** を選択し、進行状況を確認します。 この処理には5分ほどかかります。 次に進む前にこの登録操作が完了するのを待ちます

    *Initials*\ **_oracle_base**の登録が完了したら、追加のOracleVMの展開のためにソフトウェプロファイルを作成する必要があります。

ドロップダウンメニューから **Profiles** を選択します。し、左側のメニューから **Software** を選択します。

#. **+ Create** をクリックし、以下の項目を埋めます。

   - **Engine** - Oracle
   - **Type** - Single Instance
   - **Name** - *Initials*\ _ORACLE_19C
   - **Description** - (Optional)
   - **Database Server** - Select your registered *Initials*\ _oracle_base VM

   .. figure:: images/3.png

#. **Create** をクリックします。

#.ドロップダウンメニューから **Operarions** を選択し、進行状況を確認します。 この処理には5分ほどかかります。

データベースの登録
++++++++++++++++++++++

#. **Era** のドロップダウンメニューから **Databases** を選択します。し、左側のメニューの **Sources** を選択します。

   .. figure:: images/11.png

#. **+ Register** をクリックし、以下の項目を埋めます。

   - **Engine** - ORACLE
   - **Database is on a Server that is:** - Registered
   - **Registered Database Servers** - Select your registered *Initials*\ _oracle_base VM

   .. figure:: images/12.png

#. **Next** をクリックします。

   - **Database Name in Era** - *Initials*\ -orcl
   - **SID** - orcl19c

   .. note::

  ID(SID)はシステム上の特定のデータベースを一意に識別するために使われます。そのため、一つのコンピュータシステム上に同じSIDのデータベースは持てません。RACを使う場合、同じデータベースに属する全てが一意のSIDを保つ必要があります。

   .. figure:: images/13.png

**Next** をクリックします。

   - **Name** - *Initials*\ -orcl_TM
   - **SLA** - DEFAULT_OOB_BRASS_SLA (no continuous replay)

   .. figure:: images/14.png

**Register** をクリックします。

#.ドロップダウンメニューから**Operarions** を選択し、進行状況を確認します。 この処理には5分ほどかかります。
