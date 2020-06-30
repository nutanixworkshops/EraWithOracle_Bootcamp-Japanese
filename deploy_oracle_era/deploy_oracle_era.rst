.. _deploy_oracle_era:

-------------------------
Deploying Oracle with Era
-------------------------

Oracleは四半期毎にPSUというパッチに相当するものをリリースします。
**このラボではOracleとOracle 19cデータベース用のGridソフトウェア両方の展開とパッチの適応をEraを用いて行います。**

Era を使ったOracle Serverの作成
+++++++++++++++++++++++++++++

このエクササイズでは各々の *Initials*\ **_ORACLE_19C** 1.0 Software Profileを使った最新のOracleデータベースを展開します。

#. ドロップダウンメニューから **Databases** を選び、左側のメニューから **Sources** を選択する

#. **+ Provision > Single Node Database** をクリックします。

#. **Provision a Database** ウィザードから、Detabase Server設定の以下の項目を埋めてください

   - **Engine** - Oracle
   - **Database Server** - Create New Server
   - **Database Server Name** - *Initials*\ _oracle_prod
   - **Description** - (Optional)
   - **Software Profile** - *Initials*\ _ORACLE_19C
   - **Compute Profile** - ORACLE_SMALL
   - **Network Profile** - Primary_ORACLE_NETWORK
   - Select **Enable High Availability**
   - **SYS ASM Password** - oracle
   - **SSH Public Key for Node Access** - Select **Text**

   ::

      ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAii7qFDhVadLx5lULAG/ooCUTA/ATSmXbArs+GdHxbUWd/bNGZCXnaQ2L1mSVVGDxfTbSaTJ3En3tVlMtD2RjZPdhqWESCaoj2kXLYSiNDS9qz3SK6h822je/f9O9CzCTrw2XGhnDVwmNraUvO5wmQObCDthTXc72PcBOd6oa4ENsnuY9HtiETg29TZXgCYPFXipLBHSZYkBmGgccAeY9dq5ywiywBJLuoSovXkkRJk3cd7GyhCRIwYzqfdgSmiAMYgJLrz/UuLxatPqXts2D8v1xqR9EPNZNzgd4QHK4of1lqsNRuz2SxkwqLcXSw0mGcAL8mIwVpzhPzwmENC5Orw==


   .. note::

   High Availability(高可用性)を有効にすると、Oracle Gridが展開の一部として設定され、Oracle Autmatic Storage Management(ASM)がボリュームマネジメントのために使われます。高可用性が無効ならば、Linux LVMとファイルシステムがデータベースストレージに使われます。GridとASMにはクラスタ化されたOracle RACの展開が要求されます。

   .. figure:: images/4.png

#. **Next** をクリックして、Database設定のために以下の項目を埋めてください

   -  **Database Name** - *Initials*\ _proddb
   -  **SID** - *Initials*\ prod
   -  **SYS and SYSTEM Password** - Nutanix/4u
   -  **Database Parameter Profile** - ORACLE_SMALL_PARAMS

   .. figure:: images/5.png

   .. note::
   Eraがサポートする各データベースエンジンについては、データベース作成の前後にスクリプトを実行する機会があります。一般的な用例は以下のものです:

      - Data masking scripts
      - Register the database with DB monitoring solution
      - Scripts to update DNS/IPAM
      - Scripts to automate application setup, such as app-level cloning for Oracle PeopleSoft

      **Encryption** は、コンプライアンスで求められる、脅威からのデータベースを介した攻撃やストレージの直接的な情報の盗聴を、データベースレイアでの保存データの暗号化によって阻止します。

#. **Next** をクリックしてデータベース用のTime Machineの設定のために以下の項目を埋めてください

   - **Name** - *Initials*\ _proddb_TM (Default)
   - **Description** - (Optional)
   - **SLA** - DEFAULT_OOB_GOLD_SLA
   - **Schedule** - (Defaults)

   .. figure:: images/6.png

#. **Next** をクリックしてデータベース用のTime Machineの設定のために以下の項目を埋めてください

#. ドロップダウンメニューから **Operarions** を選択し、進行状況を確認します。 この工程には60分ほど要します(クラスタの設定にもよります)

#. デプロイの完了を待たず、次のエクササイズに進んでください。
