.. _admin_oracle:

--------------------------
DB Administration with Era
--------------------------

ここでは、Eraを使って通常のデータベース管理タスクを実行する方法を見ていきます。

**このラボでは、あなたのORACLE DBを管理します**

あなたのデータベースを探す
++++++++++++++++++++++

#. **Era** のドロップダウンメニューから **Databases** を選び、左側のメニューから **Sources** を選択します。

   .. figure:: images/1.png

#. *Initials*\ **-proddb** をクリックします、するとDatabase Summary ページに戻ります。このページでは、データベース、データベースサーバーアクセス、タイムマシンスケジュール、プロビジョニングに使用されるコンピュート/ネットワーク/ソフトウェアプロファイルの詳細を提供します。

    - **Database Summary:**

    .. figure:: images/2.png

    - **Database Server:**

    .. figure:: images/3.png

    - **Time Machine:**

    .. figure:: images/4.png

    - **Profiles:**

    .. figure:: images/5.png

データベースのスナップショット
++++++++++++++++++++++

データベースのマニュアルスナップショットを取る前に、新しいテーブルをProdDBに書きます。

データベースに新しいテーブルを書く
.............................

#. SSH (Terminal/Putty) で *Initials*\ -proddb VM に接続します。

   - **User Name** - oracle
   - **Password** - Nutanix/4u

   .. code-block:: Bash

     ssh oracle@PRODDB IP

#. **sqlplus** を起動します。

     .. code-block:: Bash

       sqlplus / as sysdba

#. テーブル作成のために以下を実行します。

     .. code-block:: Bash

       CREATE TABLE testlabtable
       (
       column1 VARCHAR(30),
       column2 DATE
       );

#. 以下を実行してテーブル一覧を表示することで、新しいテーブルが存在することを確認します。


     .. code-block:: Bash

       select owner as schema_name,
       table_name
       from sys.all_tables
       where table_name like 'TEST%';

データベースのマニュアルスナップショットを取る
................................

#. **Era** のドロップダウンメニューから **Databases** を選び、左側のメニューから**Sources**を選択します。

#. データベースのタイムマシン（*Initials*\ -proddb_TM）をクリックする。

   .. figure:: images/6.png

#. **Actions > Log Catch Up** をクリックします。

   .. figure:: images/12.png

#. **Yes** をクリックします。

#. 完了後、 **Actions > Snapshot** をクリックします。

   .. Figure:: images/7.png

   - **Snapshot Name** - *Initials*\ -proddb-1st-Snapshot

   .. Figure:: images/8.png

#. **Create** をクリックします。

#. ドロップダウンメニューから **Operations** を選択し、保存の進行状況を確認します。この処理は2-5分くらいかかります。

データベース及びデータベースサーバのクローン
+++++++++++++++++++++++++++++++++++++

#. **Era** 上で **Time Machines** をドロップダウンメニューから選択し、*Initials*\ -proddb_TM を選びます。

#. **Actions > Clone Database** をクリックします。

   - **Snapshot** - *Initials*\ -proddb-1st-Snapshot (Date Time)

   .. figure:: images/9.png

#. **Next** をクリック

   - **Database Server** - Create New Server
   - **Database Server Name** - *Initials*\ _oracle_prod_Clone1
   - **Compute Profile** - ORACLE_SMALL
   - **Network Profile** - Primary-ORACLE-Network
   - **SSH Public Key Through** - Select **Text**

   ::

      ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAii7qFDhVadLx5lULAG/ooCUTA/ATSmXbArs+GdHxbUWd/bNGZCXnaQ2L1mSVVGDxfTbSaTJ3En3tVlMtD2RjZPdhqWESCaoj2kXLYSiNDS9qz3SK6h822je/f9O9CzCTrw2XGhnDVwmNraUvO5wmQObCDthTXc72PcBOd6oa4ENsnuY9HtiETg29TZXgCYPFXipLBHSZYkBmGgccAeY9dq5ywiywBJLuoSovXkkRJk3cd7GyhCRIwYzqfdgSmiAMYgJLrz/UuLxatPqXts2D8v1xqR9EPNZNzgd4QHK4of1lqsNRuz2SxkwqLcXSw0mGcAL8mIwVpzhPzwmENC5Orw==

   .. figure:: images/10.png

#. **Next** をクリックします。

   - **Clone Name** - *Initials*\ _proddb_Clone1
   -  **SID** - *Initials*\ prod
   -  **SYS and SYSTEM Password** - Nutanix/4u
   -  **Database Parameter Profile** - ORACLE_SMALL_PARAMS

   .. figure:: images/11.png

#. **Clone** をクリックします。

#. ドロップダウンメニューから **Operations** を選択し、保存の進行状況を確認します。この処理は30-50分くらいかかります。

テーブルの削除とクローンの更新
++++++++++++++++++++++++++++++

テーブルやその他のデータが不慮に消えてしまったなら、それをもとに戻したいはずです。ここでは、テーブルを削除して最後のスナップショットのEra Clone Refreshアクションからデータを復旧します。

テーブルの削除
............

#. SSH (Terminal/Putty) で *Initials*\ -proddb VM に接続します。

   - **User Name** - oracle
   - **Password** - Nutanix/4u

   .. code-block:: Bash

     ssh oracle@PRODDB_Clone1 IP

#. **sqlplus** を起動します。

     .. code-block:: Bash

       sqlplus / as sysdba

#. 以下に従ってテーブルをドロップします。

     .. code-block:: Bash

       DROP TABLE testlabtable;

#. テーブルの一覧を表示し、テーブルがなくなっていることを確認する。

     .. code-block:: Bash

       select owner as schema_name,
       table_name
       from sys.all_tables
       where table_name like 'TEST%';

クローンの更新
.............

#. **Era** 上でドロップダウンメニューから **Databases** を選択し、左側のメニューから **Clones** を選択します。

#. データベースの*Initials*\ _proddbを選択して、**Clones**をクリックします。

   - **Snapshot** - *Initials*\ _proddb-1st-Snapshot (Date Time)

#. **Refresh** をクリックします。

#. ドロップダウンメニューから **Operations** を選択し、保存の進行状況を確認します。この処理は2-5分くらいかかります。

テーブルが復元出来たことを確認する
....................

#. SSH (Terminal/Putty) で *Initials*\ -proddb VM に接続します。

   - **User Name** - oracle
   - **Password** - Nutanix/4u

   .. code-block:: Bash

     ssh oracle@PRODDB_Clone1 IP

#. **sqlplus** を起動します。

     .. code-block:: Bash

       sqlplus / as sysdba

#. テーブルの一覧を表示し、テーブルが復元されていることを確認します。

     .. code-block:: Bash

       select owner as schema_name,
       table_name
       from sys.all_tables
       where table_name like 'TEST%';

Takeaways
+++++++++
