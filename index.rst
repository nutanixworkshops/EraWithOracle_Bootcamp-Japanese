.. title:: Databases: Era with Oracle Bootcamp

.. toctree::
   :maxdepth: 2
   :caption: Era with Oracle
   :name: _dbs
   :hidden:

   labsetup/labsetup
   deploy_oracle/deploy_oracle
   deploy_oracle_era/deploy_oracle_era
   patching_oracle/patching_oracle
   admin_oracle/admin_oracle

.. toctree::
  :maxdepth: 2
  :caption: Optional Labs
  :name: _optional_labs
  :hidden:

  era_rest_api/era_rest_api

.. toctree::
  :maxdepth: 2
  :caption: Appendix
  :name: _appendix
  :hidden:

  tools_vms/windows_tools_vm
  tools_vms/linux_tools_vm
  appendix/glossary

.. _getting_started:

---------------
Getting Started
---------------

Welcome to the Databases: Era with Oracle Bootcamp! This workbook accompanies an instructor-led session that introduces Nutanix technologies and many common management tasks.


What's New
++++++++++

- Workshop updated for the following software versions:
    - AOS 5.11.x / 5.15.x / 5.16.x
    - PC 5.16.x

- Optional Lab Updates:

Agenda
++++++

- Introductions
- Lab Setup
- Deploy Oracle
- Deploy Oracle with Era
- Patching Oracle with Era
- Admin Oracle with Era

Optional labs:

- Era API Explorer

Introductions
+++++++++++++

- Name
- Familiarity with Nutanix

Initial Setup
+++++++++++++

- Take note of the *Passwords* being used.
- Log into your virtual desktops (connection info below)

Environment Details
+++++++++++++++++++

Nutanix Workshops are intended to be run in the Nutanix Hosted POC environment. Your cluster will be provisioned with all necessary images, networks, and VMs required to complete the exercises.

環境の詳細
+++++++++++++++++++

Nutanixワークショップは、Nutanix Hosted POC環境を利用します。演習を完了するために必要なイメージ、ネットワーク、VMは事前に構築されています。

ネットワーク
..........

Hosted POCクラスターは、標準の命名規則に従います。

- **クラスター名** - POC\ *XYZ*
- **サブネット** - 10.**21**.\ *XYZ*\ .0
- **クラスタIP** - 10.**21**.\ *XYZ*\ .37

例:

- **クラスター名** - POC055
- **サブネット** - 10.21.55.0
- **クラスターIP** - 10.21.55.37

環境内には、* XYZ *をサブネットの正しいオクテットに置き換える必要があるVMが存在しています。たとえば：

.. list-table::
   :widths: 25 75
   :header-rows: 1

   * - IP Address
     - Description
   * - 10.21.\ *XYZ*\ .37
     - Nutanix Cluster Virtual IP
   * - 10.21.\ *XYZ*\ .39
     - **PC** VM IP, Prism Central
   * - 10.21.\ *XYZ*\ .40
     - **DC** VM IP, NTNXLAB.local Domain Controller

各クラスターは、VMに使用できる2つのVLANで構成されています。

.. list-table::
  :widths: 25 25 10 40
  :header-rows: 1

  * - Network Name
    - Address
    - VLAN
    - DHCP Scope
  * - Primary
    - 10.21.\ *XYZ*\ .1/25
    - 0
    - 10.21.\ *XYZ*\ .50-10.21.\ *XYZ*\ .124
  * - Secondary
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .132-10.21.\ *XYZ*\ .253

認証情報
...........

.. note::

  <クラスタ・パスワード>は各クラスタに固有であり、講師によって提供されます。

.. list-table::
   :widths: 25 35 40
   :header-rows: 1

   * - ノード
     - ユーザ名
     - パスワード
   * - Prism Element
     - admin
     - *<Cluster Password>*
   * - Prism Central
     - admin
     - *<Cluster Password>*
   * - Controller VM
     - nutanix
     - *<Cluster Password>*
   * - Prism Central VM
     - nutanix
     - *<Cluster Password>*

各クラスターには専用のドメインコントローラーVM、DCが存在し、ntnxlab.localドメインにActive Directoryサービスを提供します。ドメインには、次のユーザーとグループが格納されています。

.. list-table::
   :widths: 25 35 40
   :header-rows: 1

   * - グループ
     - ユーザ名
     - パスワード
   * - Administrators
     - Administrator
     - nutanix/4u
   * - SSP Admins
     - adminuser01-adminuser25
     - nutanix/4u
   * - SSP Developers
     - devuser01-devuser25
     - nutanix/4u
   * - SSP Consumers
     - consumer01-consumer25
     - nutanix/4u
   * - SSP Operators
     - operator01-operator25
     - nutanix/4u
   * - SSP Custom
     - custom01-custom25
     - nutanix/4u
   * - Bootcamp Users
     - user01-user25
     - nutanix/4u

アクセス手順
+++++++++++++++++++

Nutanix Hosted POC 環境には以下の方法で接続できます。:

ユーザ名、パスワード
...........................

PHX クラスタ:
**Username:** PHX-POCxxx-User01 〜 PHX-POCxxx-User20, **Password:** *<講師から提供>*

RTP クラスタ:
**Username:** RTP-POCxxx-User01 〜 RTP-POCxxx-User20, **Password:** *<講師から提供>*

Frame VDI
.........

ログイン: https://frame.nutanix.com/x/labs

上記 **Username**, **Password** を入力

Parallels VDI
.................

PHX クラスタ: https://xld-uswest1.nutanix.com

RTP クラスタ: https://xld-useast1.nutanix.com

上記 **Username**, **Password** を入力

Nutanix Version Info
++++++++++++++++++++

- **AHV Version** - AHV 20170830.337
- **AOS Version** - 5.11.2.3
- **PC Version** - 5.11.2.1
