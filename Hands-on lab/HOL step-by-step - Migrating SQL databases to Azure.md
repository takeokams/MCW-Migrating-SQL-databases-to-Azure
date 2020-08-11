![マイクロソフト クラウド ワークショップ](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "マイクロソフト クラウド ワークショップ")

<div class="MCWHeader1">
Migrating SQL databases to Azure
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step guide
</div>

<div class="MCWHeader3">
June 2020
</div>

このドキュメントに記載されている情報 (URL 等のインターネット Web サイトに関する情報を含む) は、将来予告なしに変更されることがあります。特に断りがない限り、ここで使用している会社、組織、製品、ドメイン名、電子メール アドレス、ロゴ、人物、場所、イベントの例は、架空のものであり、実在する会社、組織、製品、ドメイン名、電子メール アドレス、ロゴ、人物、場所、イベントなどとは一切関係ありません。お客様ご自身の責任において、適用されるすべての著作権関連法規に従ったご使用を願います。このドキュメントのいかなる部分も、米国 Microsoft Corporation の書面による許諾を受けることなく、その目的を問わず、どのような形態であっても、複製または譲渡することは禁じられています。ここでいう形態とは、複写や記録など、電子的な、または物理的なすべての手段を含みます。ただしこれは、著作権法上のお客様の権利を制限するものではありません。

マイクロソフトは、このドキュメントに記載されている内容に関し、特許、特許申請、商標、著作権、またはその他の無体財産権を有する場合があります。別途マイクロソフトのライセンス契約上に明示の規定のない限り、このドキュメントはこれらの特許、商標、著作権、またはその他の無体財産権に関する権利をお客様に許諾するものではありません。

製造元や製品の名前、URL は情報の提供のみを目的としており、マイクロソフトは、これらの製造元、またはマイクロソフトの技術での製品の使用について、明示的、黙示的、または法的にいかなる表示または保証も行いません。製造元または製品の使用は、マイクロソフトによるその製造元または製品の推奨を意味するものではありません。サード パーティのサイトへのリンクが提供されている場合があります。このようなサイトはマイクロソフトの管理下にはなく、マイクロソフトは、リンクされたサイトの内容またはリンクされたサイトに含まれるリンク、あるいはこのようなサイトの変更または更新について責任を負いません。マイクロソフトは、リンクされたサイトから受信された Web キャストまたは他のいかなる形態の転送にも責任を負いません。マイクロソフトは、これらのリンクを便宜のみを目的として提供しており、いかなるリンクの使用も、マイクロソフトによるサイトまたはそこに含まれる製品の推奨を意味するものではありません。

© 2020 Microsoft Corporation.All rights reserved.

Microsoft および <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> に記載されている商標は、Microsoft グループの商標です。その他すべての商標は、該当する各社が所有しています。

**目次**

- [Azure への SQL データベースの移行 ハンズオン ラボ ステップバイステップ](#azure-への-sql-データベースの移行-ハンズオン-ラボ-ステップバイステップ)
    - [要約と学習目的](#要約と学習目的)
    - [概要](#概要)
    - [ソリューションのアーキテクチャ](#ソリューションのアーキテクチャ)
    - [必要条件](#必要条件)
    - [演習 1: データベースの評価を実行する](#演習-1-データベースの評価を実行する)
        - [タスク 1: SqlServer2008 VM 上で WWI TailspinToys データベースを設定する](#タスク-1-sqlserver2008-vm-上で-wwi-tailspintoys-データベースを設定する)
        - [タスク 2: Azure SQL Database への移行に向けた評価を実行する](#タスク-2-azure-sql-database-への移行に向けた評価を実行する)
        - [タスク 3: Azure SQL Managed Instance への移行に向けた評価を実行する](#タスク-3-azure-sql-managed-instance-への移行に向けた評価を実行する)
    - [演習 2: データベースを SQL MI に移行する](#演習-2-データベースを-sql-mi-に移行する)
        - [タスク 1: SMB ネットワーク共有を SqlServer2008 VM 上で作成する](#タスク-1-smb-ネットワーク共有を-sqlserver2008-vm-上で作成する)
        - [タスク 2: MSSQLSERVER サービスを変更して sqlmiuser アカウントで実行されるようにする](#タスク-2-mssqlserver-サービスを変更して-sqlmiuser-アカウントで実行されるようにする)
        - [タスク 3: WWI TailspinToys データベースのバックアップを作成する](#タスク-3-wwi-tailspintoys-データベースのバックアップを作成する)
        - [タスク 4: SQL MI と SQL Server 2008 VM の接続情報を取得する](#タスク-4-sql-mi-と-sql-server-2008-vm-の接続情報を取得する)
        - [タスク 5: サービス プリンシパルを作成する](#タスク-5-サービス-プリンシパルを作成する)
        - [タスク 6: オンライン データ移行プロジェクトを作成し実行する](#タスク-6-オンライン-データ移行プロジェクトを作成し実行する)
        - [タスク 7: 移行のカットオーバーを実施する](#タスク-7-移行のカットオーバーを実施する)
        - [タスク 8: データベースとトランザクション ログの移行を検証する](#タスク-8-データベースとトランザクション-ログの移行を検証する)
    - [演習 3: Web アプリケーションを更新して新しい SQL MI データベースを使用させる](#演習-3-web-アプリケーションを更新して新しい-sql-mi-データベースを使用させる)
        - [タスク 1: Web アプリを Azure に展開する](#タスク-1-web-アプリを-azure-に展開する)
        - [タスク 2: App Service の設定を更新する](#タスク-2-app-service-の設定を更新する)
    - [演習 4: App Service と仮想ネットワークを統合する](#演習-4-app-service-と仮想ネットワークを統合する)
        - [タスク 1: ポイント対サイト アドレスを設定する](#タスク-1-ポイント対サイト-アドレスを設定する)
        - [タスク 2: VNet と App Services の統合を設定する](#タスク-2-vnet-と-app-services-の統合を設定する)
        - [タスク 3: Web アプリケーションを開く](#タスク-3-web-アプリケーションを開く)
    - [演習 5: Advanced Data Security でデータベースのセキュリティ体制を強化する](#演習-5-advanced-data-security-でデータベースのセキュリティ体制を強化する)
        - [タスク 1: Advanced Data Security を有効にする](#タスク-1-advanced-data-security-を有効にする)
        - [タスク 2: SQL データの検出と分類を設定する](#タスク-2-sql-データの検出と分類を設定する)
        - [タスク 3: Advanced Data Security の脆弱性評価をレビューする](#タスク-3-advanced-data-security-の脆弱性評価をレビューする)
    - [演習 6: 動的データ マスクを有効にする](#演習-6-動的データ-マスクを有効にする)
        - [タスク 1: クレジット カード 番号に対して DDM を有効にする](#タスク-1-クレジット-カード-番号に対して-ddm-を有効にする)
        - [タスク 2: メール アドレスに DDM を適用する](#タスク-2-メール-アドレスに-ddm-を適用する)
    - [演習 7: 読み取り専用クエリのためのオンライン セカンダリを使用する](#演習-7-読み取り専用クエリのためのオンライン-セカンダリを使用する)
        - [タスク 1: WWI Tailspin Toys の Web アプリケーションのランキング レポートを表示する](#タスク-1-wwi-tailspin-toys-の-web-アプリケーションのランキング-レポートを表示する)
        - [タスク 2:読み取り専用接続文字列を更新する](#タスク-2読み取り専用接続文字列を更新する)
        - [タスク 3: Tailspin Toys の Web アプリのランキング レポートを再読み込みする](#タスク-3-tailspin-toys-の-web-アプリのランキング-レポートを再読み込みする)
    - [ハンズオン ラボの後に](#ハンズオン-ラボの後に)
        - [タスク 1: Azure リソース グループを削除する](#タスク-1-azure-リソース-グループを削除する)
        - [タスク 2:wide-world-importers のサービス プリンシパルを削除する](#タスク-2wide-world-importers-のサービス-プリンシパルを削除する)

# Azure への SQL データベースの移行 ハンズオン ラボ ステップバイステップ

## 要約と学習目的

このハンズオン ラボでは、オンプレミスの SQL Server 2008 R2 データベースを Azure SQL Managed Instance (SQL MI) に移行するための概念実証 (PoC) を実装します。オンプレミスの SQL Server 2008 R2 データベースと Azure のマネージド データベース オファリングとの間で、機能パリティや互換性の問題がないかを明らかにするために、評価を実施します。続いて、ダウンタイムを最小限またはゼロに抑えながら、お客様のオンプレミスのプレイヤー情報 Web アプリケーションとデータベースを Azure に移行します。最後に、SQL MI で利用できる高度な SQL 機能の一部を有効にして、お客様のアプリケーションのセキュリティとパフォーマンスを向上させます。

このハンズオン ラボを完了すると、ビジネスクリティカルなアプリケーションおよびデータベースのクラウド移行ソリューションをより効果的に実装できるようになります。

## 概要

Wide World Importers (WWI) の子会社である Tailspin Toys は、いくつかの人気のあるオンライン ゲームを開発した企業です。2010 年に設立された同社は、オンラインの多人数ゲームを含んだ、非常に人気のあるゲーム シリーズの第一弾リリース以降、急激な成長を遂げました。以来、同社はゲーム ポートフォリオの大部分にオンライン機能を追加することで、その成功を積み重ねてきました。

オンライン ゲームの追加によって、同社のゲームの人気は劇的に上昇しましたが、サービスへの需要が急速に増大したため、現在の構成のサポートに問題が生じるようになりました。オンライン ゲームの提供を円滑に行うために、同社はレンタルのハードウェアを使って、オンプレミスでゲーム サービスをホストしています。各ゲームに対して、同社のゲーム サービスの構成は、ゲーム ソフトウェアを実行する 3 台の仮想マシンと、単一の SQL Server 2008 R2 インスタンスでホストされる 5 つのゲーム データベースから成っています。専用のゲーム VM とデータベースに加え、認証およびゲートウェイ用に共有の VM とデータベースもホストしています。Tailspin Toys は基本的にゲーム開発企業であり、主にソフトウェア開発者によって構成されています。データベースおよびインフラストラクチャ専門のリソースは少数で、拡大を続けるワークロードへの対応に苦戦しています。

WWI は、サービスをオンプレミスからクラウドに移行することによって、インフラストラクチャ管理の問題をいくらか軽減できると同時に、新しい改良されたゲームをリリースすることでビジネス価値を提供するという同社の取り組みに再度集中できるようになることを期待しています。現在は、プレイヤー情報 Web アプリケーションとデータベースをクラウドに移行するための概念実証 (PoC) を実施しようとしています。同社では、プレイヤー情報データベースである `TailspinToys` をオンプレミスの SQL Server 2008 R2 データベース上で維持しています。このシステムは、プレイヤーによるプロフィール更新、ランキング表示、ゲームのアドオン購入などのために使用されています。このシステムは収益の促進に寄与しているため、ビジネスクリティカルなアプリケーションであり、高可用性が必要であると考えられます。同社では SQL Server 2008 R2 のサポート終了が近づいていることを認識しており、このデータベースを Azure に移行させるための選択肢に目を向けています。Azure でしか利用できない、セキュリティとパフォーマンスに関する高度なチューニング オプションについて書かれた情報を見たことがあり、可能であれば、データベースを "サービスとしてのプラットフォーム (PaaS)" オファリングへ移行したいと考えています。WWI では WWI `TailspinToys` データベース内のメッセージングのために、SQL Server の Service Broker 機能を利用しています。この機能によっていくつかのクリティカルなプロセスが実現されているため、オペレーション データベースをクラウドに移行する際に、これを失うわけにはいきません。また、現時点では、代替となるメッセージ ブローカーを使用できるようにソリューションを再設計するためのリソースがないと語っています。

## ソリューションのアーキテクチャ

以下の図は、このラボで実装するソリューションのアーキテクチャを示しています。これについて入念に学び、さまざまなコンポーネントに取り組む際に、ソリューション全体を把握できるようにしてください。

![このソリューションの図にある仮想ネットワークには、分離したサブネット内に SQL MI が、管理サブネット内に JumpBox VM および Database Migration Service が含まれています。MI サブネット内には、プライマリのマネージド インスタンスと、読み取り専用のレプリカの両方が示されています。レプリカは、Web アプリのレポートによるアクセスを受けます。Web アプリは、サブネット ゲートウェイとポイント対サイト VPN を介して SQL MI に接続します。この Web アプリは、Visual Studio 2019 を使って App Service に発行します。オンプレミスの SQL Server から SQL MI へのオンライン データ移行は、Azure Database Migration Service を使って実施します。Azure Database Migration Service は、SMB ネットワーク共有からバックアップ ファイルを読み取ります。](./media/preferred-solution-architecture.png "望ましいソリューションの図")

このソリューションでは、まず始めに Microsoft Data Migration Assistant (DMA) を使用して、オンプレミスの SQL Server 2008 R2 データベースの機能パリティと互換性に関する評価を実施します。評価は Azure SQL Database (Azure SQL DB) と Azure SQL Managed Instance (SQL MI) の両方に対して実施します。目標は、変更を最小限またはゼロに抑えながら、`TailspinToys` データベースを Azure PaaS オファリングに移行することです。評価を完了し、結果をレビューした後、Azure Database Migration Service のオンライン データ移行オプションを利用して、SQL Server 2008 R2 データベースを SQL MI に移行します。オンライン データ移行を利用すると、SMB ネットワーク共有に保存されたバックアップとトランザクション ログを使うことで、ダウンタイムを短時間またはゼロに抑えながらデータベースを移行できます。

Web アプリは、Visual Studio 2019 を使って Azure App Service Web App に展開します。データベースの移行とカットオーバーが済んだら、`TailspinToysWeb` アプリケーションを設定して、このアプリケーションがポイント対サイト VPN を利用して、仮想ネットワーク ゲートウェイを通じて SQL MI VNet と通信できるようにします。また、接続文字列を、新しい SQL MI を指すように更新します。

SQL MI では、Azure SQL Database のいくつかの機能を検証します。Advanced Data Security (ADS) を有効にします。また "データの検出と分類" を使用して、データについての理解、そしてデータベース内のデータに関する潜在的なコンプライアンス問題についての理解を深めます。ADS の脆弱性評価を使用してデータベース内の潜在的なセキュリティ脆弱性と問題を特定し、その結果を利用して、Transparent Data Encryption をデータベース内で有効にすることで 1 つの評価結果を緩和します。動的データ マスク (DDM) を利用して、データベースへのクエリ実行の際に機密データが表示されることを防ぎます。最後に、読み取りスケールアウトを使用して、WWI の Tailspin Toys の Web アプリ上のレポートを読み取り専用のセカンダリに振り向け、プライマリ データベースのパフォーマンスに影響を与えることなくレポーティングを実行できるようにします。

## 必要条件

- Microsoft Azure サブスクリプションが従量課金制または MSDN であること。
  - 無料評価版サブスクリプションでは動作しません。
- Visual Studio Community 2019 またはそれ以上を組み込んだ仮想マシンを用意すること ("ハンズオン ラボの前に" の演習でセットアップする)。
- **重要**: このハンズオン ラボを完了するためには、Azure Active Directory アプリケーションやサービス プリンシパルを作成したり、サブスクリプションのロールを割り当てたりするための十分な権限を、Azure AD テナント内に持っている必要があります。

## 演習 1: データベースの評価を実行する

所要時間: 20 分

この演習では、Microsoft Data Migration Assistant (DMA) を使用して、`TailspinToys` データベースの評価を実行します。作成する評価は 2 つです。1 つは Azure SQL Database への移行用で、もう 1 つは SQL MI への移行用です。これらの評価は、オンプレミスのデータベースと Azure のマネージド SQL データベース サービス オプションとの間で発生しうる、機能パリティや互換性の問題についてのレポートを提供します。

> DMA は、新しいバージョンの SQL Server や Azure SQL Database でのデータベース機能に影響を与えうる互換性の問題を検出することで、最新のデータ プラットフォームへのアップグレードを支援します。DMA はターゲット環境のパフォーマンスと信頼性に関わる改善点を提案し、スキーマ、データ、および非包含オブジェクトをソース サーバーからターゲット サーバーに移動できるようにします。詳しくは、[Data Migration Assistant のドキュメント](https://docs.microsoft.com/sql/dma/dma-overview?view=azuresqldb-mi-current)を参照してください。

### タスク 1: SqlServer2008 VM 上で WWI TailspinToys データベースを設定する

このタスクでは、SQL Server 2008 R2 インスタンス上の WWI `TailspinToys` データベースに対して、移行に向けて準備するためのいくつかの設定を行います。

1. [Azure portal](https://portal.azure.com) に移動し、\[Azure services\] リストから \[**Resource groups**\] を選択します。
   
   ![\[Azure services\] リストで \[Resource groups\] が強調表示されている。](media/azure-services-resource-groups.png "[Azure services]")

2. リストから hands-on-lab-SUFFIX リソース グループを選択します。
   
   ![Azure のナビゲーション ペインで \[Resource groups\] が選択され、"hands-on-lab-SUFFIX" リソース グループが強調表示されている。](./media/resource-groups.png "リソース グループのリスト")

3. リソース グループのリソース リストで、SqlServer2008 VM を選択します。
   
   ![リソース リストで SqlServer2008 VM が強調表示されている。](media/resource-list-sqlserver2008.png "リソース リスト")

4. Azure portal の \[SqlServer2008 Virtual machine\] ブレードで、前にも行ったように、左側のメニューから \[**Overview**\] を選択した後、上部のメニューで \[**Connect**\] と \[**RDP**\] を選択します。
   
   ![\[SqlServer2008 Virtual machine\] ブレードが表示され、上部のメニューの \[Connect\] ボタンが強調表示されている。](./media/connect-vm-rdp.png "SqlServer2008 VM への接続")

5. \[Connect with RDP\] ブレードで \[**Download RDP File**\] を選択した後、ダウンロードされた RDP ファイルを開きます。

6. \[Remote Desktop Connection\] ダイアログで \[**Connect**\] を選択します。
   
   ![\[Remote Desktop Connection\] ダイアログ ボックスの \[Connect\] ボタンが強調表示されている。](./media/remote-desktop-connection-sql-2008.png "[Remote Desktop Connection] ダイアログ")

7. プロンプトが表示されたら、以下の資格情報を入力し、\[**OK**\] を選択します。
   
   - **ユーザー名:** `sqlmiuser`
   - **パスワード**: `Password.1234567890`
   
   ![\[Enter your credentials\]　ダイアログに上記の資格情報が入力されている。\[Enter your credentials\]](media/rdc-credentials-sql-2008.png "[Enter your credentials]")

8. \[The identity of the remot  computer  annot be  erified\] というプロンプトが表示された場合、\[**Yes**\] を選択して接続します。
   
   ![\[Remote Desktop Connection\] ダイアログ ボックスで、このリモート コンピューターの ID が確認できないことを示し、このまま続行するか尋ねる警告メッセージが表示されている。下部の \[Yes\] ボタンが赤線で囲まれている。](./media/remote-desktop-connection-identity-verification-sqlserver2008.png "[Remote Desktop Connection] ダイアログ")

9. ログインしたら、Windows の \[スタート\] メニューの検索バーに「sql server」と入力し、検索結果から \[**Microsoft SQL Server Management Studio 17**\] を選択して、**Microsoft SQL Server Management Studio 17** (SSMS) を開きます。
   
   ![Windows の \[スタート\] メニューの検索ボックスに「sql server」と入力され、検索結果内の \[Microsoft SQL Server Management Studio 17\] が強調表示されている。](media/start-menu-ssms-17.png "Windows の [スタート] メニューの検索")

10. SSMS の \[**サーバーに接続**\] ダイアログで、\[サーバー名\] ボックスに「**SQLSERVER2008**」と入力し、\[**Windows 認証**\] が選択されていることを確認した後、\[**接続**\] を選択します。
    
    ![\[サーバー名\] に「SQLSERVER2008」と入力され、\[Windows認証\] が選択さた、SQL Server の \[サーバーに接続\] ダイアログが表示されている。](media/sql-server-connect-to-server.png "[サーバーに接続]")

11. 接続したら、データベース下で `TailspinToys` データベースが表示されていることを確認します。表示されていない場合、VM のセットアップ時に設定スクリプトが適切に実行されなかった可能性があります。[手動によるリソースのセットアップ ガイド](./Manual-resource-setup.md)のタスク 12 の手順に従って、手動でデータベースを追加および復元できます。

12. SSMS ツールバーで \[**新しいクエリ**\] を選択します。
    
    ![SSMS ツールバーの \[新しいクエリ\] ボタンが強調表示されている。](media/ssms-new-query.png "SSMS のツールバー")

13. 次に、以下の SQL スクリプトを新しいクエリのウィンドウにコピーして貼り付けます。このスクリプトは Service Broker を有効にし、データベースの復旧モデルを FULL に変更します。
    
    ```sql
    USE master;
    GO
    
    -- Update the recovery model of the database to FULL and enable Service Broker
    ALTER DATABASE TailspinToys SET
    RECOVERY FULL,
    ENABLE_BROKER WITH ROLLBACK IMMEDIATE;
    GO
    ```

14. スクリプトを実行するには、SSMS ツールバーから **\[実行\]** を選択します。
    
    ![SSMS ツールバーの \[実行\] ボタンが強調表示されている。](media/ssms-execute.png "SSMS ツールバー")

### タスク 2: Azure SQL Database への移行に向けた評価を実行する

このタスクでは、Microsoft Data Migration Assistant (DMA) を使用して、WWI の `TailspinToys` データベースを Azure SQL Database (Azure SQL DB) に対して評価します。この評価によって、オンプレミスのデータベースと Azure の SQL DB サービスとの間で発生しうる、機能パリティや互換性の問題についてのレポートが得られます。

1. SqlSever2008 VM で、Windows の \[スタート\] メニューの検索バーに「data migration」と入力し、検索結果から \[**Microsoft Data Migration Assistant**\] を選択して、DMA を起動します。
   
   ![Windows の \[スタート\] メニューの検索バーに「data migration」と入力され、Windows の \[スタート\] メニューの検索結果で \[Microsoft Data Migration Assistant\] が強調表示されている。](media/windows-start-menu-dma.png "Data Migration Assistant")

2. DMA のダイアログで、左側メニューから \[**+**\] を選択し、新しいプロジェクトを作成します。
   
   ![DMA の新しいプロジェクトのアイコンが強調表示されている。](media/dma-new.png "新しい DMA プロジェクト")

3. \[New\] プロジェクト ペインで、次の項目を設定します。
   
   - **Project type**: \[**Assessment**\] を選択します。
   - **Project name**: 「`ToAzureSqlDb`」と入力します。
   - **Assessment type**: \[**Database Engine**\] を選択します。
   - **Source server type**: \[**SQL Server**\] を選択します。
   - **Target server type**: \[**Azure SQL Database**\] を選択します。
   
   ![SQL Server から Azure SQL Database への移行の評価を目的とした新しいプロジェクトの設定がダイアログに入力されている。](media/dma-new-project-to-azure-sql-db.png "新しいプロジェクトの設定")

4. \[**Create**\] を選択します。

5. \[**Options**\] 画面で \[**Check database compatibility**\] と \[**Check feature parity**\] の両方がオンになっていることを確認し、続いて \[**Next**\] を選択します。
   
   ![\[Option\] 画面で \[Check database compatibility\] と \[Check feature parity\] がオンになっている。](media/dma-options.png "DMA のオプション")

6. \[**Sources**\] 画面で、右側に表示される \[**Connect to a server**\] ダイアログに以下の事項を入力します。
   
   - **Server name**: \[**SQLSERVER2008**\] を選択します。
   - **Authentication type**: \[**SQL Server Authentication**\] を選択します。
   - **Username**: 「`WorkshopUser`」と入力します。
   - **Password**: 「`Password.1234567890`」と入力します。
   - **Encrypt connection**: このボックスをオンにします。
   - **Trust server certificate**: このボックスをオンにします。
   
   ![\[Connect to a server\] ダイアログで、上記の値が適切なフィールドに入力されている。](media/dma-connect-to-a-server.png "[Connect to a server]")

7. \[**Connect**\] を選択します。

8. 続いて表示される \[**Add sources**\] ダイアログで\[**TailspinToys**\] ボックスをオンにし、\[**Add**\] を選択します。
   
   ![\[Add sources\] ダイアログで \[TailspinToys\] ボックスがオンになっている。](media/dma-add-sources.png "[Add sources]")

9. \[**Start Assessment**\] を選択します。
   
   ![評価の開始](media/dma-start-assessment-to-azure-sql-db.png "評価の開始")

10. Azure SQL DB への移行が可能か判断するため、移行評価を確認します。
    
    ![ターゲット プラットフォームである Azure SQL DB について、機能パリティによって、Azure SQL DB でサポートされていない 2 つの機能が示されている。左側で Service Broker 機能が選択され、右側の "Service Broker feature is not supported in Azure SQL Database" が強調表示されている。](media/dma-feature-parity-service-broker-not-supported.png "データベースの機能パリティ")
    
    > ターゲット プラットフォームである Azure SQL DB に WWI の `TailspinToys` データベースを移行するための DMA 評価では、サポートされていない使用中の機能が示されます。Service Broker を含むこれらの機能が妨げとなっているため、WWI はまず自社のデータベースに変更を加えなければ、Azure SQL DB PaaS オファリングへの移行を行えません。

### タスク 3: Azure SQL Managed Instance への移行に向けた評価を実行する

1 つの PaaS オファリングが機能パリティによって除外されたので、Azure SQL Managed Instance (SQL MI) に対して、2 回目の DMA 評価を実行します。この評価によって、オンプレミスのデータベースと SQL MI サービスとの間で発生しうる、機能パリティや互換性の問題についてのレポートが得られます。

1. 開始するには、DMA の左側メニューから \[**+**\] を選択し、別の新しいプロジェクトを作成します。
   
   ![DMA の新しいプロジェクトのアイコンが強調表示されている。](media/dma-new.png "新しい DMA プロジェクト")

2. \[New\] プロジェクト ペインで、次の項目を設定します。
   
   - **Project type**: \[**Assessment**\] を選択します。
   - **Project name**: 「`ToSqlMi`」と入力します。
   - **Assessment type**: \[**Database Engine**\] を選択します。
   - **Source server type**: \[**SQL Server**\] を選択します。
   - **Target server type**: \[**Azure SQL Database Managed Instance**\] を選択します。
   
   ![SQL Server から Azure SQL Managed Instance への移行の評価を目的とした新しいプロジェクトの設定がダイアログに入力されている。](media/dma-new-project-to-sql-mi.png "新しいプロジェクトの設定")

3. \[**Create**\] を選択します。

4. \[**Options**\] 画面で \[**Check database compatibility**\] と \[**Check feature parity**\] の両方がオンになっていることを確認し、続いて \[**Next**\] を選択します。
   
   ![\[Option\] 画面で \[Check database compatibility\] と \[Check feature parity\] がオンになっている。](media/dma-options.png "DMA のオプション")

5. \[**Sources**\] 画面で、右側に表示される \[**Connect to a server**\] ダイアログに以下の事項を入力します。
   
   - **Server name**: \[**SQLSERVER2008**\] を選択します。
   - **Authentication type**: \[**SQL Server Authentication**\] を選択します。
   - **Username**: 「`WorkshopUser`」と入力します。
   - **Password**: 「`Password.1234567890`」と入力します。
   - **Encrypt connection**: このボックスをオンにします。
   - **Trust server certificate**: このボックスをオンにします。
   
   ![\[Connect to a server\] ダイアログで、上記の値が適切なフィールドに入力されている。](media/dma-connect-to-a-server.png "[Connect to a server]")

6. \[**Connect**\] を選択します。

7. 続いて表示される \[**Add sources**\] ダイアログで\[**TailspinToys**\] ボックスをオンにし、\[**Add**\] を選択します。
   
   ![\[Add sources\] ダイアログで \[TailspinToys\] ボックスがオンになっている。](media/dma-add-sources.png "[Add sources]")

8. \[**Start Assessment**\] を選択します。
   
   ![評価の開始](media/dma-start-assessment-to-sql-mi.png "評価の開始")

9. Azure SQL Managed Instance への移行が可能か判断するため、移行評価の \[SQL Server feature parity\] オプションと \[Compatibility issues\] オプションを確認します。
   
   ![ターゲット プラットフォームである Azure SQL Managed Instance について、問題は挙げられていない。](media/dma-feature-parity-sql-mi.png "データベースの機能パリティ")
   
   ![ターゲット プラットフォームである Azure SQL Managed Instance について、フルテキスト検索が変更されているというメッセージと、影響を受けるオブジェクトのリストが表示されている。](media/dma-compatibility-issues-sql-mi.png "互換性の問題")
   
   > **注**: ターゲット プラットフォームである Azure SQL Managed Instance に WWI の `TailspinToys` データベースを移行するための評価レポートでは、機能パリティは表示されておらず、フルテキスト検索機能について確認せよという注記が示されています。フルテキスト検索の変更は `TailspinToys` データベースから SQL MI への移行に影響を与えません。

10. このデータベースは、Service Broker 機能を含め、現状のまま移行させることが可能であり、WWI は Azure 上でフルマネージドの PaaS データベースを実行できます。従来、Azure SQL Database との互換性がない Service Broker のような機能を使用しているデータベースを移行させる唯一の選択肢は、Azure 上で動作している仮想マシン (IaaS) にデータベースを展開する、あるいはサポートされていない機能を使わないようにデータベースと関連アプリケーションを修正することでした。しかし Azure SQL MI の登場によって、TailspinToys による Azure SQL Database の使用を妨げていた機能を含め、"ほぼ 100% の互換性" を確保しながら、マネージドな Azure SQL データベース サービスへのデータベースの移行を行えるようになりました。

## 演習 2: データベースを SQL MI に移行する

所要時間: 60 分

この演習では、[Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) (DMS) を使用して、オンプレミスの SQL Server 2008 R2 データベースから SQL MI に WWI の `TailspinToys` データベースを移行します。WWI は自社のプレイヤー情報 Web アプリケーションが収益促進のために重要であると述べています。そのため、今回の移行ではダウンタイムの短縮を目的として、オンライン移行オプションを採用します。[Business Critical サービス レベル](https://docs.microsoft.com/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview#managed-instance-service-tiers)は、お客様の高可用性要件への適合を狙いとしています。

> Business Critical サービス レベルは、最高度のパフォーマンスおよび高可用性 (HA) の要件を持つビジネス アプリケーションに向けて設計されています。詳しくは、[Managed Instance のサービス レベルのドキュメント](https://docs.microsoft.com/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview#service-tiers)を参照してください。

### タスク 1: SMB ネットワーク共有を SqlServer2008 VM 上で作成する

このタスクでは、SqlServer2008 VM で新しい SMB ネットワーク共有を作成します。これは、データベース移行プロセス中に DMS が WWI `TailspinToys` データベースのバックアップを取得するために使用するフォルダーです。

1. SqlServer2008 VM で、Windows タスクバー上のアイコンを選択して**エクスプローラー**を開きます。
   
   ![Windows タスクバーの \[エクスプローラー\] アイコンが強調表示されている。](media/windows-task-bar.png "Windows タスクバー")

2. \[エクスプローラー\] ウィンドウ内で、ツリー ビューの \[**コンピューター**\] を展開し、\[**Windows (C:)**\] を選択します。続いて上部メニューの \[**新しいフォルダー**\] を選択します。
   
   ![エクスプローラーの左側ツリー ビューで \[コンピューター\] 以下の \[Windows (C:)\] が選択され、上部メニューの \[新しいフォルダー\] が強調表示されている。](media/windows-explorer-new-folder.png "エクスプローラー")

3. 新しいフォルダーに **dms-backups** という名前を付け、そのフォルダーを右クリックして、コンテキスト メニューの \[**共有**\] と \[**特定のユーザー**\] を選択します。
   
   ![エクスプローラーで dms-backups フォルダーのコンテキスト メニューが表示され、\[共有\] と \[特定のユーザー\] が強調表示されている。](media/windows-explorer-folder-share-with.png "エクスプローラー")

4. \[ファイル共有\] ダイアログで **sqlmiuser** がリストされ、アクセス許可レベルが**読み取り/書き込み**になっていることを確認してから、\[**共有**\] を選択します。
   
   ![\[ファイル共有\] ダイアログで sqlmiuser が強調表示され、読み取り/書き込みのアクセス許可レベルが割り当てられている。](media/file-sharing.png "[ファイル共有]")

5. \[**ネットワークの探索とファイル共有**\] ダイアログで、既定値である "**いいえ、接続しているネットワークをプライベート ネットワークにします**" を選択します。
   
   ![\[ネットワークの探索とファイル共有\] ダイアログの "いいえ、接続しているネットワークをプライベート ネットワークにします" が強調表示されている。](media/network-discovery-and-file-sharing.png "ネットワークの探索とファイル共有]")

6. \[ファイル共有\] ダイアログに戻ったら、共有フォルダーのパスである `\\SQLSERVER2008\dms-backups` を確認し、\[**完了**\] を選択して共有プロセスを完了します。
   
   ![\[ファイル共有\] ダイアログの \[完了\] ボタンが強調表示されている。](media/file-sharing-done.png "[ファイル共有]")

### タスク 2: MSSQLSERVER サービスを変更して sqlmiuser アカウントで実行されるようにする

このタスクでは、SQL Server Configuration Manager を使用して、SQL Server (MSSQLSERVER) が使用するサービス アカウントを `sqlmiuser` アカウントに更新します。これを実行するのは、共有フォルダーにバックアップを書き込むための適切な権限を、SQL Server サービスに確保させるためです。

1. SqlServer2008 VM で **\[スタート\] メニュー**を選択し、検索バーに「sql configuration」と入力します。続いて検索結果から \[**SQL Server Configuration Manager**\] を選択します。
   
   ![Windows の \[スタート\] メニューの検索ボックスに「sql configuration」と入力され、検索結果で \[SQL ServerSQL Server Configuration Manager\] が強調表示されている。](media/windows-start-sql-configuration-manager.png "Windows Search")
   
   > **注**: 必ず \[**SQL Server Configuration Manager**\] を選んでください。**SQL Server 2017 Configuration Manager**は、インストールされている SQL Server 2008 R2 データベースに対応していないため、選択しないでください。

2. \[SQL Server Configuration Manager\] ダイアログで、左側ツリー ビューの \[**SQL Server サービス**\] を選択します。続いてサービスのリストにある \[**SQL Server (MSSQLSERVER)**\] を右クリックし、コンテキスト メニューの \[**プロパティ**\] を選択します。
   
   ![SQL Server 設定マネージャーのツリー ビューで SQL Server サービスが選択され、強調表示されている。サービス ペインで SQL Server (MSSQLSERVER) が選択され、強調表示されている。コンテキスト メニューの \[プロパティ\] が強調表示されている。](media/sql-server-configuration-manager-services.png "SQL Server 設定マネージャー")

3. \[SQL Server (MSSQLSERVER) プロパティ\] ダイアログで、\[ログオン\] 下の \[**このアカウント**\] を選択し、以下の項目を入力します。
   
   - **アカウント名** `sqlmiuser`
   - **パスワード**: `Password.1234567890`
   
   ![\[SQL Server (MSSQLSERVER) プロパティ\] ダイアログで、\[ログオン\] 下の \[このアカウント\] が選択され、sqlmiuser のアカウント名とパスワードが入力されている。](media/sql-server-service-properties.png "[SQL Server (MSSQLSERVER) プロパティ]")

4. \[**OK**\] を選択します。

5. \[アカウント変更の確認\] ダイアログで \[**はい**\] を選択します。
   
   ![\[アカウント変更の確認\] ダイアログの \[はい\] ボタンが強調表示されている。](media/confirm-account-change.png "[アカウント変更の確認]")

6. SQL Server (MSSQLSERVER) サービスの \[**ログオン**\] の値が `./sqlmiuser` に変更されたことを確認してください。
   
   ![SQL Server サービスのリストで、SQL Server (MSSQLSERVER) サービスが強調表示されている。](media/sql-server-service.png "SQL Server サービス")

7. SQL Server Configurat ion Manager を閉じます。

### タスク 3: WWI TailspinToys データベースのバックアップを作成する

オンライン データ移行を実行するために、DMS はソース データベース サーバーの SMB 共有バックアップ フォルダーにあるバックアップとログを探します。このタスクでは、SSMS を使って WWI `TailspinToys` データベースのバックアップを作成し、それを前のタスクで作成した SMB ネットワーク共有 `\\SQLSERVER2008\dms-backups` に書き込みます。このバックアップ ファイルにはチェックサムが含まれている必要があるので、それをバックアップ手順の間に付加します。

1. SqlServer2008 VM で、Windows の \[スタート\] メニューの検索バーに「sql server」と入力し、**Microsoft SQL Server Management Studio 17** を起動します。
   
   ![Windows の \[スタート\] メニューの検索ボックスに「SQL Server」と入力され、検索結果内で \[Microsoft SQL Server Management Studio 17\] が強調表示されている。](media/start-menu-ssms-17.png "Windows の [スタート] メニューの検索")

2. SSMS の \[**サーバーに接続**\] ダイアログで、\[サーバー名\] ボックスに「**SQLSERVER2008**」と入力し、\[**Windows 認証**\] が選択されていることを確認した後、\[**接続**\] を選択します。
   
   ![\[サーバー名\] に に「SQLSERVER2008」と入力され、\[Windows 認証\] が選択さた、SQL Server の \[サーバーに接続\] ダイアログが表示されている。](media/sql-server-connect-to-server.png "[サーバーに接続]")

3. 接続が済んだら、Object Explorer で \[SQLSERVER2008\] 以下の \[**データベース**\] を展開してから、\[**TailspinToys**\] データベースを右クリックします。コンテキスト メニューから \[**タスク**\] を選び、続いて \[**バックアップ**\] を選択します。
   
   ![SSMS の Object Explorer で、TailspinToys データベースのコンテキスト メニューが表示され、\[タスク\] と \[バックアップ\] が強調表示されている。](media/ssms-backup.png "SSMS の [バックアップ]")

4. \[データベースのバックアップ\] ダイアログの \[出力先\] ボックスに、`C:\TailspinToys.bak` がリストされているはずです。これはもう不要なので、選択してから、\[**削除**\] を選んでください。
   
   ![\[データベースのバックアップ\] ダイアログの \[全般\] タブで、C:\\TailspinToys.bak が選択され、\[出力先\] 下の \[削除\] ボタンが強調表示されている。](media/ssms-back-up-database-general-remove.png)

5. 続いて、SMB ネットワーク共有をバックアップの保存先として追加するために、\[**追加**\] を選択します。
   
   ![\[データベースのバックアップ\] ダイアログの \[全般\] タブで、\[出力先\] 下の \[追加\] ボタンが強調表示されている。](media/ssms-back-up-database-general.png "[データベースのバックアップ]")

6. \[バックアップ先の選択\] ダイアログで、\[参照\] (`...`) ボタンを選択します。
   
   ![\[バックアップ先の選択\] ダイアログの \[参照\] ボタンが強調表示されている。](media/ssms-select-backup-destination.png "[バックアップ先の選択]")

7. \[データベース ファイルの場所\] ダイアログで、`C:\dms-backups` フォルダーを選択し、\[ファイル名\] フィールドに「`TailspinToys.bak`」と入力してから、\[**OK**\] を選択します。
   
   ![\[ファイルの選択\] ペインで、C:\\dms-backups フォルダーが選択および強調表示され、\[ファイル名\] フィールドに「TailspinToys.bak」と入力されている。](media/ssms-locate-database-files.png "[データベース ファイルの場所]")

8. \[**OK**\] を選択して、\[バックアップ先の選択\] ダイアログを閉じます。

9. \[データベースのバックアップ\] ダイアログで、\[ページの選択\] ペイン の \[**メディア オプション**\] を選択してから、以下の項目を設定します。
   
   - \[**既存のメディア セットにバックアップ**\] を選んでから、\[**既存のすべてのバックアップ セットを上書き**\] を選択します。
   - \[信頼性\] 下の \[**メディアへの書き込みの前にチェックサムを実行**\] ボックスをオンにします。これはバックアップを使用してデータベースを SQL MI へ復元する際、DMS にとって必要となります。
   
   ![\[データベースのバックアップ\] ダイアログで、\[メディア オプション\] ページが選択され、\[既存のすべてのバックアップ セットを上書き\] と \[メディアへの書き込みの前にチェックサムを実行\] が選択および強調表示されている。](media/ssms-back-up-database-media-options.png "[データベースのバックアップ]")

10. \[**OK**\] を選択してバックアップを実行します。

11. バックアップが完了したら、メッセージが表示されます。\[**OK**\] を選択します。
    
    ![データベースのバックアップが正常に終了したというメッセージを表示するダイアログ](media/ssms-backup-complete.png "バックアップの完了")

### タスク 4: SQL MI と SQL Server 2008 VM の接続情報を取得する

このタスクでは、Azure Cloud Shell を使用して、DMS から SqlServer2008 VM に接続するのに必要な情報を取得します。

1. [Azure portal](https://portal.azure.com) のトップメニューから \[Azure Cloud Shell\] アイコンを選択します。
   
   ![Azure portal のトップメニューで \[Azure Cloud Shell\] アイコンが強調表示されている。](media/cloud-shell-icon.png "Azure Cloud Shell")

2. ブラウザー ウィンドウの下部に開かれる \[Cloud Shell\] ウィンドウで、\[**PowerShell**\] を選択します。
   
   ![\[Welcome to Azure Cloud Shell\] ウィンドウで、\[PowerShell\] が強調表示されている。](media/cloud-shell-select-powershell.png "Azure Cloud Shell")

3. "You have no storage mounted (ストレージがマウントされていません)" というメッセージが表示された場合は、このハンズオン ラボのために使用しているサブスクリプションを選び、\[**Create storage**\] を選択します。
   
   ![\[You have no storage mounted\] ダイアログで、サブスクリプションが選択され、\[Create Storage\] ボタンが強調表示されている。](media/cloud-shell-create-storage.png "Azure Cloud Shell")
   
   > **注**: 作成が失敗した場合は、\[**Advanced settings**\] を選択して、新しいストレージ アカウントのためのサブスクリプション、リージョン、リソース グループを指定する必要があるかもしれません。

4. しばらくすると、Cloud Shell の要求に成功したというメッセージが \[PS Azure\] プロンプトと一緒に表示されます。
   
   ![\[Azure Cloud Shell\] ダイアログで、Cloud Shell の要求に成功したというメッセージが表示され、\[PS Azure\] プロンプトが表示されている。](media/cloud-shell-ps-azure-prompt.png "Azure Cloud Shell")

5. プロンプトで、SqlSerer2008 VM のパブリック IP アドレスを取得します。このアドレスは、そのサーバーのデータベースへの接続に使用します。以下の PowerShell コマンドを入力します。その際、リソース グループ名変数内の **`<your-resource-group-name>`** を自分のリソース グループ名に**置き換え**てください。
   
   ```powershell
   $resourceGroup = "<your-resource-group-name>"
   az vm list-ip-addresses -g $resourceGroup -n SqlServer2008 --output table
   ```
   
   > **注**: 複数の Azure サブスクリプションを保有していて、このハンズオン ラボに使用しているアカウントが既定のアカウントではない場合は、Azure Cloud Shell プロンプトで `az account list --output table` を実行し、サブスクリプションのリストを出力する必要があるかもしれません。その場合、このラボに使用しているアカウントのサブスクリプション ID をコピーした後、`az account set --subscription <your-subscription-id>` を実行して Azure CLI コマンド用の適切なアカウントを設定してください。

6. 出力の中から `PublicIpAddresses` フィールド以下の `ipAddress` プロパティの値を探し、それをコピーします。後で参照するために、Notepad.exe (メモ帳) などのテキスト エディターにその値を貼り付けます。
   
   ![az vm list-ip-addresses コマンドの出力が Cloud Shell に表示され、SqlServer2008 VM の publicIpAddress が強調表示されている。](media/cloud-shell-az-vm-list-ip-addresses.png "Azure Cloud Shell")

7. 次のタスクのために、Azure Cloud Shell は開いたままにしておきます。

### タスク 5: サービス プリンシパルを作成する

このタスクでは Azure Cloud Shell を使用して、Azure SQL MI へのアクセスを DMS に提供する Azure Active Directory (Azure AD) アプリケーションとサービス プリンシパル (SP) を作成します。この SP には、hands-on-lab-SUFFIX リソース グループやサブスクリプションに対するアクセス許可を与えます。

> **重要**: このタスクを完了するためには、Azure Active Directory アプリケーションやサービス プリンシパルを作成したり、サブスクリプションのロールを割り当てたりするための十分な権限を、Azure AD テナント内に持っている必要があります。

1. 次に、Cloud Shell プロンプトで、**wide-world-importers** と名付けたサービス プリンシパルを作成するコマンドを発行し、それに **hands-on-lab-SUFFIX** リソース グループに対する共同作成者権限を割り当てます。

2. まずは、サブスクリプション ID を取得する必要があります。Cloud Shell プロンプトに以下を入力してください。
   
   ```powershell
   az account list --output table
   ```

3. 出力テーブルの中から、このハンズオン ラボで使用しているサブスクリプションを見つけます。SubscriptionId の値をコピーし、それを使って以下のコマンドの `<your-subscription-id>` を置き換えます。完成したコマンドを CLI コマンド プロンプトで実行します。
   
   ```powershell
   $subscriptionId = "<your-subscription-id>"
   ```

4. 次に、以下の `az ad sp create-for-rbac` コマンドを Cloud Shell プロンプトに入力し、`Enter` を押してコマンドを実行します。
   
   ```powershell
   az ad sp create-for-rbac -n "https://wide-world-importers" --role owner --scopes subscriptions/$subscriptionId/resourceGroups/$resourceGroup
   ```

5. コマンドの出力をテキスト エディターにコピーします。これは、次のタスクで `appId` と `password` が必要になるためです。出力は以下に示すものと類似しているはずです。
   
   ```json
   {
     "appId": "aeab3b83-9080-426c-94a3-4828db8532e9",
     "displayName": "wide-world-importers",
     "name": "https://wide-world-importers",
     "password": "hd_42~OwuFk.mp1BQ8l6nKkzOLzg5vrQpC",
     "tenant": "d280491c-b27a-XXXX-XXXX-XXXXXXXXXXXX"
   }
   ```

6. ロールの割り当てを確認するために、**hands-on-lab-SUFFIX** リソース グループ ブレードの左側メニューから \[**Access control (IAM)**\] を選択し、続いて \[**Role assignments**\] タブを選び、OWNER ロールの下の **wide-world-importers** を見つけます。
   
   ![\[Role assignments\] タブが表示され、リスト内の OWNER の下の wide-world-importers が強調表示されている。](media/rg-hands-on-lab-role-assignments.png "[Role assignments]")

7. 続いて別のコマンドを発行して、新しく作成したサービス プリンシパルに、サブスクリプション レベルでの CONTRIBUTOR (共同作成者) ロールを与えます。Cloud Shell プロンプトで、以下のコマンドを実行します。
   
   ```powershell
   az role assignment create --assignee https://wide-world-importers --role contributor
   ```

### タスク 6: オンライン データ移行プロジェクトを作成し実行する

このタスクでは、DMS で新しいオンライン データ移行プロジェクトを WWI の `TailspinToys` データベース用に作成します。

1. [Azure portal](https://portal.azure.com) で、左側のナビゲーション メニューから \[**Resource groups**\] を選んで **hands-on-lab-SUFFIX** リソース グループを選択し、続いてリソースのリストから **wwi-dms** Azure Database Migration Service を選択して、Azure Database Migration Service に移動します。
   
   ![hands-on-lab-SUFFIX リソース グループのリソース リストで tailspin-dms Azure Database Migration Service が強調表示されている。](media/resource-group-dms-resource.png "リソース")

2. \[Azure Database Migration Service\] ブレードで、\[**+New Migration Project**\] を選択します。
   
   ![\[Azure Database Migration Service\] ブレードで、ツールバーの \[+New Migration Project\] が強調表示されている。](media/dms-add-new-migration-project.png "Azure Database Migration Service の新しいプロジェクト")

3. \[New migration project\] ブレードで、以下を入力します。
   
   - **Project name**: 「`OnPremToSqlMi`」と入力します。
   - **Source server type**: \[**SQL Server**\] を選択します。
   - **Target server type**: \[**Azure SQL Database Managed Instance**\] を選択します。
   - **Choose type of activity**: \[**Online data migration**\] を選び、\[**Save**\] を選択します。
   
   ![\[New migration project\] ブレードが表示され、該当するフィールドに、上記の指定された値が入力されている。](media/dms-new-migration-project-blade.png "[New migration project]")

4. \[**Create and run activity**\] を選択します。

5. Migration Wizard の \[**Select source**\] ブレードで、以下を入力します。
   
   - **Source SQL Server instance name**: 前のタスクでテキスト エディターにコピーした、SqlServer2008 VM の IP アドレスを入力します。例えば `13.66.228.107` など。
   - **Authentication type**: \[SQL Authentication\] を選択します。
   - **Username**: 「`WorkshopUser`」と入力します。
   - **Password**: 「`Password.1234567890`」と入力します。
   - **Connection properties**: \[Encrypt connection\] と \[Trust server certificate\] の両方をオンにします。
   
   ![Migration Wizard の \[Select source\] ブレードが表示され、該当するフィールドに、上記の指定された値が入力されている。](media/dms-migration-wizard-select-source.png "Migration Wizard の [Select source]")

6. \[**Save**\] を選択します。

7. Migration Wizard の \[**Select target**\] ブレードで、以下を入力します。
   
   - **Application ID**: 1 つ前のタスクで実行した　\`az ad sp create-for-rbac' コマンドの出力から、`appId` の値を入力します。
   - **Key**: 1 つ前のタスクで実行した　az ad sp create-for-rbac コマンドの出力から、`password` の値を入力します。
   - **Skip the Application ID Contributor level access check on the subscription**: オフの状態のままにします。
   - **Subscription**: このハンズオン ラボで使用しているサブスクリプションを選択します。
   - **Target Azure SQL Managed Instance**: sqlmi-UNIQUEID インスタンスを選択します。
   - **SQL Username**: 「`sqlmiuser`」と入力します。
   - **Password**: 「`Password.1234567890`」と入力します。
   
   ![Migration Wizard の \[Select target\] ブレードが表示され、該当するフィールドに、上記の指定された値が入力されている。](media/dms-migration-wizard-select-target.png "Migration Wizard の [Select target]")

8. \[**Save**\] を選択します。

9. Migration Wizard の \[**Select databases**\] ブレードで、`TailspinToys` を選択します。
   
   ![Migration Wizard の \[Select databases\] ブレードが表示され、TailspinToys が選択されている。](media/dms-migration-wizard-select-databases.png "Migration Wizard の [Select databases]")

10. \[**Save**\] を選択します。

11. Migration Wizard の \[**Configure migration settings**\] ブレードで、以下を入力します。
    
    - **Network share location**: 「`\\SQLSERVER2008\dms-backups`」と入力します。これは前に作成した SMB ネットワーク共有のパスです。
    - **Windows User Azure Database Migration Service impersonates to upload files to Azure Storage**: 「`SQLSERVER2008\sqlmiuser`」と入力します。
    - **Password**: 「`Password.1234567890`」と入力します。
    - **Subscription containing storage account**: このハンズオン ラボで使用しているサブスクリプションを選択します。
    - **Storage account**: ストレージ アカウント \[sqlmistoreUNIQUEID\] を選択します。
    
    ![Migration Wizard の \[Configure migration settings\] ブレードが表示され、該当するフィールドに、上記の指定された値が入力されている。](media/dms-migration-wizard-configure-migration-settings.png "Migration Wizard の [Configure migration settings]")

12. \[**Configure migration setting**\] ブレードで、\[**Save**\] を選択します。

13. Migration Wizard の \[**Summary**\] ブレードで、以下を入力します。
    
    - **Activity name**: 「WwiMigration」と入力します。
    
    ![Migration Wizard の \[Summary\] ブレードが表示され、名前フィールドに「WwiMigration」と入力され、\[Choose validation option\] ブレードの \[Validate my database(s)\] が選ばれ、3 つの検証オプションすべてが選択されている。](media/dms-migration-wizard-migration-summary.png "Migration Wizard の [Summary]")

14. \[**Run migration**\] を選択します。

15. 表示される状態画面で移行を監視します。ツールバーにある \[Refresh\] アイコンを選択し、最新の状態を取得します。
    
    ![\[Migration job\] ブレードで、\[Refresh \] ボタンが強調表示され、"Full backup uploading" という状態が表示および強調表示されている。](media/dms-migration-wizard-status-running.png "移行状況")

16. 状態が "**Log shipping in progress**" に変化するまで、**\[Refresh\]** を 5 ～ 10 秒ごとに選択し続けます。その状態が表示されたら、次のタスクに進みます。
    
    ![移行監視ウィンドウで、"Log shipping in progress" という状態が強調表示されている。](media/dms-migration-wizard-status-log-files-uploading.png "移行状況")

### タスク 7: 移行のカットオーバーを実施する

ここで実行したのは "オンライン データ移行" であるため、移行ウィザードは、新しく追加されるログ バックアップ ファイルのための SMB ネットワーク共有を監視し続けます。これによって、SQL MI データベースへのカットオーバーを行うまでにソース データベースに生じた更新を、すべて記録できるようになっています。このタスクでは、データベース テーブルの 1 つにレコードを追加してから、ログをバックアップし、SQL MI データベースへのカットオーバーを行って、WWI `TailspinToys` データベースの移行を完了します。

1. Azure portal の移行状況ウィンドウで、\[DATABASE NAME\] の下の \[**TailspinToys**\] を選択し、データベース移行に関するより詳しい情報を確認します。
   
   ![移行状況ウィンドウでデータベース名 \[TailspinToys\] が強調表示されている。](media/dms-migration-wizard-database-name.png "移行状況")

2. \[TailspinToys\] の画面で、`TailspinToys.bak` ファイルの **Restored** という状態に注目してください。
   
   ![\[TailspinToys\] ブレードのアクティブなバックアップ ファイルのリストで、TailspinToys.bak ファイルの横にある Restored という状態が強調表示されている。](media/dms-migration-wizard-database-restored.png "Migration Wizard")

3. ログ配布を実演し、移行プロセス中にソース データベースで実行されるトランザクションがどのようにターゲットの SQL MI データベースに追加されるのかを示すため、データベース テーブルの 1 つにレコードを追加することにします。

4. SqlServer2008 VM の SSMS に戻り、ツールバーから \[**新しいクエリ**\] を選択します。
   
   ![SSMS ツールバーの \[新しいクエリ\] ボタンが強調表示されている。](media/ssms-new-query.png "SSMS ツールバー")

5. 以下の SQL スクリプトを新しいクエリのウィンドウに貼り付けます。このスクリプトは、`Game` テーブルにレコードを挿入します。
   
   ```sql
   USE TailspinToys;
   GO
   
   INSERT [dbo].[Game] (Title, Description, Rating, IsOnlineMultiplayer)
   VALUES ('Space Adventure', 'Explore the universe with are newest online multiplayer gaming experience. Build custom  rocket ships, and take off for the stars in an infinite open-world adventure.', 'T', 1)
   ```

6. SSMS ツールバーの \[**実行**\] を選択して、クエリを実行します。
   
   ![SSMS ツールバーの \[実行\] ボタンが強調表示されている。](media/ssms-execute.png "SSMS ツールバー")

7. 新しいレコードを `Games` テーブルに追加したら、トランザクション ログをバックアップします。DMS はすべての新しいバックアップを検出し、それを移行サービスへ配布します。ツールバーの \[**新しいクエリ**\] をもう一度選択し、新しいクエリのウィンドウに次のスクリプトを貼り付けます。
   
   ```sql
   USE master;
   GO
   
   BACKUP LOG TailspinToys
   TO DISK = 'c:\dms-backups\TailspinToysLog.trn'
   WITH CHECKSUM
   GO
   ```

8. SSMS ツールバーの \[**実行**\] を選択して、クエリを実行します。

9. Azure portal の移行状況ページに戻ります。\[TailspinToys\] の画面で \[**Resfresh**\] を選択します。すると、**TailspinToysLog.trn** ファイルが表示され、状態が **"Uploaded"** になっているのが確認できるはずです。
   
   ![\[TailspinToys\] ブレードの \[Refresh\] ボタンが強調表示されている。アクティブなバックアップ ファイルのリストで、TailspinToysLog.trn ファイルの横にある "Uploaded" という状態が強調表示されている。](media/dms-migration-wizard-transaction-log-uploaded.png "Migration Wizard")
   
   > **注**: Transaction log (トランザクション ログ) のエントリが表示されない場合は、表示されるまで数秒ごとに \[Refresh\] を選択し続けてください。

10. トランザクション ログは、アップロードが済んだらデータベースに復元されます。状態が "**Restored**" に変わったことを確認するまで、10 ～ 15 秒ごとに **\[Refresh\]** を選択してください。これには 1 ～ 2 分かかることがあります。
    
    ![アクティブなバックアップ ファイルのリストで、TailspinToysLog.trn ファイルの横にある "Restored" という状態が強調表示されている。](media/dms-migration-wizard-transaction-log-restored.png "Migration Wizard")

11. トランザクション ログの状態が "**Restored**" になっていることを確認してから、\[**Start Cutover**\] を選択します。
    
    ![\[Start Cutover\] ボタンが表示されている。](media/dms-migration-wizard-start-cutover.png "DMS Migration Wizard")

12. \[Complete cutover\] ダイアログで、Pending log backups が `0` であることを確認し、\[**Confirm**\] をオンにしてから、\[**Apply**\] を選択します。
    
    ![\[Complete cutover\] ダイアログで、Pending log backups の隣の 0 という値が強調表示され、\[Confirm\] チェックボックスがオンになっている。](media/dms-migration-wizard-complete-cutover-apply.png "Migration Wizard")

13. \[Complete cutover\] ダイアログの \[Apply\] ボタンの下にある進行状況バーは、カットオーバーの状況の最新情報を提供します。移行が完了すると、状況が "**Completed**" に変わります。
    
    ![\[Complete cutover\] ダイアログで "Completed" という状態が表示されている。](media/dms-migration-wizard-complete-cutover-completed.png "Migration Wizard")
    
    > **注**: 数分経っても進行状況バーが動かない場合は、次の手順に進み、\[WwiMigration\] ブレードの \[Refresh\] を選択することでカットオーバーの進行状況を監視しても構いません。

14. ダイアログの右上隅の "X" を選択して \[Complete cutover\] ダイアログを閉じ、\[TailspinToys\] ブレードについても同様にします。こうすることで \[WwiMigration\] ブレードに戻ります。\[**Refresh**\] を選択します。すると、TailspinToys データベースの "**Completed**" という状態を確認できるはずです。
    
    ![\[Migration job\] ブレードで、"Completed" という状態が強調表示されている。](media/dms-migration-wizard-status-complete.png "移行の状態が \"Completed\" になっている")

15. WWI の `TailspinToys` データベースを Azure SQL Managed Instance に正常に移行できました。

### タスク 8: データベースとトランザクション ログの移行を検証する

このタスクでは、SSMS を使用して SQL MI データベースに接続し、移行をすばやく検証します。

1. 始めに、Azure Cloud Shell を使用して、自分の SQL MI データベースの完全修飾ドメイン名を取得します。[Azure portal](https://portal.azure.com) のトップメニューから \[Azure Cloud Shell\] アイコンを選択します。
   
   ![Azure portal のトップメニューで \[Azure Cloud Shell\] アイコンが強調表示されている。](media/cloud-shell-icon.png "Azure Cloud Shell")

2. ブラウザー ウィンドウの下部に開かれる \[Cloud Shell\] ウィンドウで、\[**PowerShell**\] を選択します。
   
   ![\[Welcome to Azure Cloud Shell\] ウィンドウで、\[PowerShell\] が強調表示されている。](media/cloud-shell-select-powershell.png "Azure Cloud Shell")

3. しばらくすると、Cloud Shell の要求に成功したというメッセージが \[PS Azure\] プロンプトと一緒に表示されます。
   
   ![\[Azure Cloud Shell\] ダイアログで、Cloud Shell の要求に成功したというメッセージが表示され、\[PS Azure\] プロンプトが表示されている。](media/cloud-shell-ps-azure-prompt.png "Azure Cloud Shell")

4. プロンプトで以下の PowerShell コマンドを入力し、hands-on-lab-SUFFIX リソース グループ内の SQL MI についての情報を取得します。その際、リソース グループ名変数内の **`<your-resource-group-name>`** を自分のリソース グループ名に**置き換え**てください。
   
   ```powershell
   $resourceGroup = "<your-resource-group-name>"
   az sql mi list --resource-group $resourceGroup
   ```
   
   > **注**: 複数の Azure サブスクリプションを保有していて、このハンズオン ラボに使用しているアカウントが既定のアカウントではない場合は、Azure Cloud Shell プロンプトで `az account list --output table` を実行し、サブスクリプションのリストを出力する必要があるかもしれません。このラボのために使用しているアカウントのサブスクリプション ID をコピーし、`az account set --subscription <your-subscription-id>` を実行して Azure CLI コマンド用の適切なアカウントを設定してください。

5. 上のコマンドの出力から、`fullyQualifiedDomainName` プロパティの値を見つけてコピーします。以降の手順で参照するために、Notepad.exe (メモ帳) などのテキスト エディターにその値を貼り付けます。
   
   ![az sql mi list コマンドの出力が Cloud Shell に表示され、fullyQualifiedDomainName プロパティとその値が強調表示されている。](media/cloud-shell-az-sql-mi-list-output.png "Azure Cloud Shell")

6. SqlServer2008 VM の　SSMS に戻り、Object Explorer のメニューから \[**接続**\] と \[**データベース エンジン**\] を選択します。
   
   ![SSMS の Object Explore で、メニューの \[接続\] が強調表示され、\[接続\] のコンテキスト メニューの \[データベース エンジン\] が強調表示されている。](media/ssms-object-explorer-connect.png "SSMS の [接続]")

7. \[サーバーに接続\] ダイアログで、以下を入力します。
   
   - **サーバー名:** SQL マネージド インスタンスの完全修飾ドメイン名 (前の手順で Azure Cloud Shell からコピーしたもの) を入力します。
   - **認証:** **\[**SQL Server 認証**\]** を選択します。
   - **ログイン:** 「`sqlmiuser`」と入力します。
   - **パスワード**: 「`Password.1234567890`」と入力します。
   - **\[パスワードを記憶する\]** ボックスをオンにします。
   
   ![上で指定された SQL マネージド インスタンスの詳細情報が \[サーバーに接続\] ダイアログに入力されている。](media/ssms-connect-to-server-sql-mi.png "[サーバーに接続]")

8. \[**接続**\] を選択します。

9. SQLSERVER2008 接続の下に SQL MI 接続が表示されます。SQL MI 接続の \[データベース\] を展開し、`TailspinToys` データベースを選択します。
   
   ![SSMS の Object Explorer で、SQL MI 接続が展開され、TailspinToys データベースが強調表示および選択されている。](media/ssms-sql-mi-tailspintoys-database.png "SSMS の Object Explorer")

10. `TailspinToys` データベースを選択したまま、SSMS ツールバーの \[**新しいクエリ**\] を選択して新しいクエリのウィンドウを開きます。

11. 新しいクエリのウィンドウに、次の SQL スクリプトを入力します。
    
    ```sql
    USE TailspinToys;
    GO
    
    SELECT * FROM Game
    ```

12. SSMS ツールバーにある \[**実行**\] を選択し、クエリを実行します。`Game` テーブルに格納されているレコードを確認します。これには、移行プロセスの開始後に追加した新しいゲーム `Space Adventure` も含まれています。
    
    ![新しいクエリのウィンドウに上記のクエリが入力され、新しいゲーム Space Adventure が \[結果\] ペインで強調表示されている。](media/ssms-query-game-table.png "SSMS のクエリ")

13. 以上で SqlServer2008 VM の使用は終了です。開いたウィンドウをすべて閉じ、VM からログオフします。このハンズオン ラボの残りのタスクでは、JumpBox VM を使用します。

## 演習 3: Web アプリケーションを更新して新しい SQL MI データベースを使用させる

所要時間: 30 分

WWI `TailspinToys` データベースは、Azure 上の SQL MI で動作するようになりました。そこで次は、WWI TailspinToys のプレイヤー情報 Web アプリケーションに、必要な修正を加える手順に進みます。

> **注**: Azure SQL Managed Instance は専用 VNet 内のプライベート IP アドレスを持っています。そのため、アプリケーションを接続するには、Managed Instance を展開した VNet へのアクセスを設定する必要があります。詳しくは、[「Azure SQL Managed Instance にアプリケーションを接続する」](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance)を参照してください。

### タスク 1: Web アプリを Azure に展開する

このタスクでは JumpBox VM への RDP 接続を作成してから、JumpBox で Visual Studio を使用して、`TailspinToysWeb` アプリケーションを Azure の App Service に展開します。

1. [Azure portal](https://portal.azure.com) の Azure のナビゲーション ペインで \[**Resource groups**\] を選択し、リストから **hands-on-lab-SUFFIX** リソースグループを選択します。
   
   ![Azure のナビゲーション ペインで \[Resource groups\] が選択され、"hands-on-lab-SUFFIX" リソース グループが強調表示されている。](./media/resource-groups.png "リソース グループのリスト")

2. リソース グループのリソースのリストで、\[JumpBox\] VM を選択します。
   
   ![hands-on-lab-SUFFIX リソース グループのリスースのリストが表示され、\[JumpBox\] が強調表示されている。](./media/resource-group-resources-jumpbox.png "リソース グループのリストの [JumpBox]")

3. \[JumpBox VM\] ブレードで、上部のメニューから \[**Connect**\] と \[**RDP**\] を選択します。
   
   ![\[JumpBox VM\] ブレードが表示され、上部のメニューの \[Connect\] ボタンが強調表示されている。](./media/connect-vm-rdp.png "JumpBox VM への接続")

4. \[Connect with RDP\] ブレードで \[**Download RDP File**\] を選択した後、ダウンロードされた RDP ファイルを開きます。

5. \[Remote Desktop Connection\] ダイアログで \[**Connect**\] を選択します。
   
   ![\[Remote Desktop Connection\] ダイアログ ボックスの \[Connect\] ボタンが強調表示されている。](./media/remote-desktop-connection.png "[Remote Desktop Connection] ダイアログ")

6. プロンプトが表示されたら、以下の資格情報を入力し、\[**OK**\] を選択します。
   
   - **Username:** `sqlmiuser`
   - **Password**: `Password.1234567890`
   
   ![\[Enter your credentials\]　ダイアログに上記の資格情報が入力されている。\[Enter your credentials\]](media/rdc-credentials.png "[Enter your credentials]")

7. \[The identity of the remot  computer  annot be  erified\] というプロンプトが表示された場合、\[**Yes**\] を選択して接続しま す。
   
   ![\[Remote Desktop Connection\] ダイアログ ボックスで、このリモート コンピューターの ID が確認できないことを示し、このまま続行するか尋ねる警告メッセージが表示されている。下部の \[Yes\] ボタンが赤線で囲まれている。](./media/remote-desktop-connection-identity-verification-jumpbox.png "[Remote Desktop Connection] ダイアログ")

8. ログインしたら、エクスプローラーを Windows の \[スタート\] バーから選択して開きます。
   
   ![Windows の \[スタート\] バーの \[エクスプローラー\] アイコンが強調表示されている。](media/windows-2019-start-bar-file-explorer.png "Windows の [スタート] バー")

9. \[エクスプローラー\] ダイアログで、`C:\hands-on-lab` フォルダーに移動し、`Migrating-SQL-databases-to-Azure-master\Hands-on lab\lab-files` まで階層を下ります。`lab-files` フォルダーの `TailspinToysWeb.sln` をダブルクリックし、このソリューションを Visual Studio で開きます。
   
   ![上で指定されたパスのフォルダーが表示され、TailspinToys.sln が強調表示されている。](media/windows-explorer-tailspintoysweb.png "エクスプローラー")

10. ファイルの開き方を尋ねるメッセージが表示されたら、\[**Visual Studio 2019**\] を選び、\[**OK**\] を選択します。
    
    ![Visual Studio のバージョン セレクターで Visual Studio 2019 が選択され強調表示されている。](media/visual-studio-version-selector.png "Visual Studio")

11. \[**サインイン**\] を選択し、要求に応じて Azure アカウントの資格情報を入力します。
    
    ![Visual Studio のようこそ画面で、\[サインイン\] ボタンが強調表示されている。](media/visual-studio-sign-in.png "Visual Studio")

12. セキュリティ警告ダイアログで、\[**ソリューション内のすべてのプロジェクトに対して確認メッセージを表示する**\] をオフにしてから \[**OK**\] を選択します。
    
    ![Visual Studio のセキュリティ警告が表示され、\[ソリューション内のすべてのプロジェクトに対して確認メッセージを表示する\] チェックボックスがオフの状態で強調表示されている。](media/visual-studio-security-warning.png "Visual Studio")

13. Visual Studio へのログインしたら、Solution Explorerで \[`TailspinToysWeb`\] プロジェクトを右クリックし、\[\]**公開** を選択します。
    
    ![Solution Explorerで、TailspinToysWeb プロジェクトのコンテキスト メニューが表示され、\[公開\] が強調表示されている。](media/visual-studio-project-publish.png "Visual Studio")

14. \[**公開**\] ダイアログの \[Target\] ボックスで \[**Azure**\] を選択し、\[**次へ**\] を選択します。
    
    ![\[公開\] ダイアログの  \[ターゲット\] ボックスで \[Azure\] が選択および強調表示されている。\[次へ\] ボタンが強調表示されている。](media/vs-publish-to-azure.png "Azure への API アプリの発行")

15. 次に、\[**特定のターゲット**\] ボックスで、\[**Azure App Service (Windows)**\] を選択します。
    
    ![\[公開\] ダイアログの \[特定のターゲット\] ボックスで \[Azure App Service (Windows)\] が選択および強調表示されている。\[次へ\] ボタンが強調表示されている。](media/vs-publish-specific-target.png "Azure への API アプリの発行")

16. 最後に、\[**App Service**\] ボックスで、自身のサブスクリプションを選択し、hands-on-lab-SUFFIX リソース グループを展開して、Web アプリを選択します。
    
    ![\[公開\] ダイアログの hands-on-lab-SUFFIX リソース グループ下で Tailspin Toys の Web アプリが選択および強調表示されている。](media/vs-publish-web-app-service.png "Azure への API アプリの発行")

17. \[**完了**\] を選択します。

18. `TailspinToysWeb` プロジェクトの Visual Studio の \[公開\] ページに戻り、\[**公開**\] を選択し、Web API を Azure API App に発行するプロセスを開始します。
    
    ![Visual Studio の \[公開\] ページで \[公開\] ボタンが強調表示されている。](media/visual-studio-publish-web-app.png "[公開]")

19. 発行が完了すると、Visual Studio の \[出力\] ページに、発行に成功したというメッセージが表示されます。
    
    !["発行に成功しました" というメッセージが Visual Studio の \[出力\] ペインに表示されている。](media/visual-studio-output-publish-succeeded.png "Visual Studio")

20. 発行した Web アプリへのリンクを Visual Studio の出力ウィンドウから選択すると、エラー ページが返されます。これは、データベース接続文字列が SQL MI データベースを指すように更新されていないためです。これについては、次のタスクで対処します。
    
    ![Web アプリの設定でデータベース接続文字列が SQL MI データベースを指すように更新されていないため、エラー画面が表示されている。](media/web-app-error-screen.png "Web アプリのエラー")

### タスク 2: App Service の設定を更新する

このタスクでは、WWI の Tailspin Toys のプレイヤー情報 Web アプリケーションに更新を加えて、SQL MI データベースへの接続とその利用を行えるようにします。

1. [Azure portal](https://portal.azure.com) で、\[Azure services\] リストから \[**Resource groups**\] を選択します。
   
   ![\[Azure services\] リストで \[Resource groups\] が強調表示されている。](media/azure-services-resource-groups.png "[Azure services]")

2. リストから hands-on-lab-SUFFIX リソース グループを選択します。
   
   ![Azure のナビゲーション ペインで \[Resource groups\] が選択され、"hands-on-lab-SUFFIX" リソース グループが強調表示されている。](./media/resource-groups.png "リソース グループのリスト")

3. リソース グループのリソース リストで、**hands-on-lab-SUFFIX** リソース グループを選び、続いてリソースのリストから **tailspintoysUNIQUEID** という App Service を選択します。
   
   ![リソース グループのリソースのリストで tailspintoys という App Service が強調表示されている。](media/rg-app-service.png "リソース グループ")

4. \[App Service\] ブレードで、左側の \[Settings\] の下の \[**Configuration**\] を選択します。
   
   ![\[Settings\] の下の \[Configuration\] アイテムが選択されている。](media/app-service-configuration-menu.png "[Configuration]")

5. \[Configuration \] ブレードで \[**Connection strings**\] セクションを見つけ、接続文字列 `TailspinToysContext` の右にある鉛筆 (編集) アイコンを選択します。
   
   ![\[Connection strings\] セクションで、接続文字列 TailspinToysContext の右にある鉛筆アイコンが強調表示されている。](media/app-service-configuration-connection-strings.png "[Connection strings]")

6. 接続文字列の値は以下のようになっているはずです。
   
   ```sql
   Server=tcp:your-sqlmi-host-fqdn-value,1433;Database=TailspinToys;User ID=sqlmiuser;Password=Password.1234567890;Trusted_Connection=False;Encrypt=True;TrustServerCertificate=True;
   ```

7. \[Add/Edit connection string\] ダイアログで、`your-sqlmi-host-fqdn-value` を自分の SQL MI の完全修飾ドメイン名に置き換えます。このドメイン名は以前の手順で、Azure Cloud Shell からテキスト エディターにコピーしたものです。
   
   ![接続文字列内の文字列 your-sqlmi-host-fqdn-value が強調表示されている。](media/app-service-configuration-edit-conn-string.png "接続文字列の編集")

8. 更新された値は、以下のスクリーンショットに似たものになります。
   
   ![更新された接続文字列が表示され、文字列内で SQL MI の完全修飾ドメイン名が強調表示されている。](media/app-service-configuration-edit-conn-string-value.png "接続文字列の値")

9. **\[OK\]** を選択します。

10. 今度は接続文字列 `TailspinToysReadOnlyContext` について、手順 3 ～ 7 を再度実行します。

11. \[Configuration\] ブレード上部の \[**Save**\] を選択します。
    
    ![\[Configuration\] ブレードの保存ボタンが強調表示されている。](media/app-service-configuration-save.png "[Save]")

12. アプリケーション設定と接続文字列を変更するとアプリケーションが再起動されるというメッセージが表示されたら、\[**Continue**\] を選択します。
    
    ![アプリケーションが再起動されるという警告メッセージが表示され、\[Continue \] ボタンが強調表示されている。](media/app-service-restart.png "再起動メッセージ")

13. \[Configuration\] ブレードの左にある \[**Overview**\] を選択し、App Service の概要ブレードに戻ります。
    
    ![App Service の左側メニューで \[Overview\] が強調表示されている。](media/app-service-overview-menu-item.png "メニュー項目 [Overview ]")

14. この時点で、\[Overview\] ブレードにおいて App Service の **URL** を選択しても、まだエラーが返されます。これは SQL Managed Instance が VNet 内のプライベート IP アドレスを持っているためです。アプリケーションに接続するには、Managed Instance が展開されている VNet へのアクセスを設定する必要があります。これについては次の演習で扱います。
    
    ![プライベート仮想ネットワーク内の SQL MI にアプリケーションが接続できないため、エラー画面が表示されている。](media/web-app-error-screen.png "Web アプリのエラー")

## 演習 4: App Service と仮想ネットワークを統合する

所要時間: 15 分

この演習では、"ハンズオン ラボの前に" の演習で作成した仮想ネットワークと、App Service を統合します。ARM テンプレートによって、VNet 上のゲートウェイ サブネットと、仮想ネットワーク ゲートウェイが作成されました。この 2 つのリソースはどちらも、App Service を統合して、SQL MI に接続するために必要となります。

### タスク 1: ポイント対サイト アドレスを設定する

このタスクでは、クライアント アドレス プールを設定します。これは、プライベート IP アドレスの範囲であり、以下で指定します。ポイント対サイト VPN を介して接続するクライアントは、この範囲から IP アドレスを動的に受け取ります。使用するプライベート IP アドレスの範囲は、VNet と重複しないものにします。

1. [Azure portal](https://portal.azure.com) で、リソース グループ hands-on-lab-SUFFIX のリソース リストから**hands-on-lab-SUFFIX-vnet-gateway** 仮想ネットワーク ゲートウェイを選択し、そこに移動します。
   
   ![リソースのリストで仮想ネットワーク ゲートウェイ リソースが強調表示されている。](media/resource-group-vnet-gateway.png "リソース")

2. \[Virtual network gateway\] ブレードで、左側メニューの \[Settings\] 下の \[**User VPN configuration**\] を選択し、続いて \[**Configure now**\] を選択します。
   
   ![左側メニューの \[User VPN configuration\] が強調表示および選択されている。\[User VPN configuration\] ブレードの \[Configure now\] が強調表示されている。](media/virtual-network-gateway-configure-point-to-site.png "[Virtual network gateway] の [User VPN configuration]")

3. \[**Point-to-site configuration**\] ページで、以下の設定を指定します。
   
   - **Address pool**: 使用したいプライベート IP アドレスの範囲を追加します。このアドレス空間は以下のアドレス ブロックの 1 つである必要がありますが、VNet が使用するアドレス空間と重複してはいけません。
     - `10.0.0.0/8` - これは 10.0.0.0 ～ 10.255.255.255 という IP アドレスの範囲を意味します。
     - `172.16.0.0/12` - これは 172.16.0.0 ～ 172.31.255.255 という IP アドレスの範囲を意味します。
     - `192.168.0.0/16` - これは 192.168.0.0 ～ 192.168.255.255 という IP アドレスの範囲を意味します。
   - **Tunnel type**: \[**SSTP (SSL)**\] を選択します。
   - **Authentication type**: \[**Azure certificate**\] を選択します。
   
   ![上で指定された値が Point-to-site configuration フォームに入力されている。](media/virtual-network-gateway-point-to-site-configuration.png "仮想ネットワーク ゲートウェイ")

4. \[**Save**\] を選択して確定し、設定を保存します。保存の完了には数分かかります。

### タスク 2: VNet と App Services の統合を設定する

このタスクでは、App Service にネットワーキング設定を追加して、VNet 内のリソースとの通信を可能にします。

1. [Azure portal](https://portal.azure.com) で、左側メニューから \[**Resource groups**\] を選んで**hands-on-lab-SUFFIX** リソース グループを選択し、続いてリソースのリストから **tailspintoysUNIQUEID** という App Service を選択します。
   
   ![リソース グループのリソースのリストで tailspintoys という App Service が強調表示されている。](media/rg-app-service.png "リソース グループ")

2. \[App Service\] ブレードで左側メニューから \[**Networking**\] を選び、続いて \[**VNet Integration**\] の下の \[**Click here to configure**\] を選択します。
   
   ![\[App Service\] ブレードで左側メニューの \[Networking \] が選択され、\[VNet Integration\] の下の \[Click here to configure\] が強調表示されている。](media/app-service-networking.png "App Service")

3. \[VNet Configuration\] ブレードで \[**Add VNet**\] を選択します。
   
   ![\[VNet Configuration\] ブレードの \[Add VNet\] が強調表示されている。](media/app-service-vnet-configuration.png "App Service")

4. \[Network Feature Status\] ダイアログで、以下を入力します。
   
   - **Virtual Network**: \[hands-on-lab-SUFFIX-vnet\] を選択します。
   - **Subnet**: \[Create New Subnet\] を選択します。
   - **Subnet Name**: 「WebAppSubnet」と入力します。
   - **Virtual Network Address Block**: このドロップダウンの唯一のオプションである \[10.x.0.0/16\] を選択します。
   - **Subnet Address Block**: /24 ブロックを使用してサブネットを入力します (例: 10.x.3.0/24)。
   
   ![上記の値が \[Network Feature Status\] ダイアログに入力されている。](media/app-service-vnet-network-feature-status.png "[Network Feature Status] の設定")

5. 数分以内に VNet が追加され、変更を適用するために App Service が再起動します。\[**Refresh**\] を選択して詳細を確認します。CERTIFICATE STATUS (証明書の状態) が Certificates in sync になっているのを確認できるはずです。**注**: CERTIFICATE STATUS が Certificates in sync になっていない場合は、\[Refresh\] (更新) を押してみてください。証明書の状態が反映されるまでには、少し時間がかかる場合があります。
   
   ![VNet Configuration (VNet 設定) の詳細が表示されている。"Certificates in sync" という CERTIFICATE STATUS が強調表示されている。](media/app-service-vnet-details.png "App Service")
   
   > **注**: Web アプリへの仮想ネットワークの追加が失敗したというメッセージが表示されたら、\[VNet Configuration\] ブレードで \[**Disconnect**\] を選択し、上の手順 3 ～ 5 を再度実行してください。

### タスク 3: Web アプリケーションを開く

このタスクでは、Web アプリケーションがロードされ、Web アプリのホームページが表示されるかを確認します。

1. App Service の左側メニューの \[**Overview**\] を選び、App service の **URL** を選択して Web サイトを立ち上げます。これにより、ブラウザーのウィンドウでその URL が開かれます。
   
   ![App service の URL が強調表示されている。](media/app-service-url.png "App Service の URL")

2. Web サイトとデータが正しくロードされたことを確認します。ページは以下と同様の表示になっているはずです。
   
   ![TailspinToys のオペレーション Web アプリのスクリーンショット](media/tailspin-toys-web-app.png "TailspinToys の Web")
   
   > **注**: エラー画面が表示されたら、ブラウザーのウィンドウで \[更新\] を選択してみてください。

3. これで完了です。アプリケーションを新しい SQL MI データベースに接続させることができました。

## 演習 5: Advanced Data Security でデータベースのセキュリティ体制を強化する

所要時間: 30 分

この演習では、SQL MI データベースで Advanced Data Security (ADS) を有効にして、Azure でデータベースを実行する際の セキュリティ上のメリットをいくつか見ていきます。[SQL Database 向け Advance Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) (ADS) は、機密データの検出と分類、潜在的なデータベース脆弱性の発見と緩和、データベースへの脅威の兆候かもしれない異常なアクティビティの検出といった、高度な SQL セキュリティ機能を提供します。

### タスク 1: Advanced Data Security を有効にする

このタスクでは、Managed Instance 上のすべてのデータベースに対して ADS を有効にします。

1. [Azure portal](https://portal.azure.com) で、\[Azure services\] リストから \[**Resource groups**\] を選択します。
   
   ![\[Azure services\] リストで \[Resource groups\] が強調表示されている。](media/azure-services-resource-groups.png "[Azure services]")

2. リストから hands-on-lab-SUFFIX リソース グループを選択します。
   
   ![Azure のナビゲーション ペインで \[Resource groups\] が選択され、"hands-on-lab-SUFFIX" リソース グループが強調表示されている。](./media/resource-groups.png "リソース グループのリスト")

3. リストから **TailspinToys** という Managed database (マネージド データベース) リソースを選択します
   
   ![リソース リストで TailspinToys という Managed database が強調表示されている。](media/resources-sql-mi-database.png "リソース")

4. TailspinToys マネージド データベース のブレードで、左側メニューから \[Security\] の下の \[**Advanced Data Security**\] を選び、続いて \[**Enable Advanced Data Security on the managed instance**\] を選択します。
   
   ![マネージド データベースのブレードの左側メニューで \[Advanced Data Security\] が選択および強調表示され、\[Enable Advanced Data Security on the managed instance\] ボタンが強調表示されている。](media/sql-mi-managed-database-advanced-data-security-enable.png "Advanced Data Security")

5. 数分以内に、Managed Instance 上のすべてのデータベースに対して ADS が有効になります。有効化が完了すると、\[Advanced Data Security\] ブレードの 3 つのタイルが有効になり、データが読み込まれているのが確認できるはずです。
   
   ![\[Advanced Data Security\] ブレードで、有効になったタイルが表示されている。](media/ads-panels.png "Advanced Data Security")

### タスク 2: SQL データの検出と分類を設定する

このタスクでは、Advanced Data Security の ["SQL データの検出と分類"](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification?view=sql-server-2017) 機能をレビューします。"データの検出と分類" には、データベース内の機密データの検出、分類、ラベル付け、およびレポーティングのための新しいツールが導入されました。高度な一連のサービスが導入され、データベースだけでなくデータベース内のデータも保護することを目的とした、新しい SQL Information Protection パラダイムが形成されています。最も機密性の高いデータ (ビジネス、財務、医療など) の検出と分類は、組織の情報保護の達成において極めて重要な役割を果たすことができます。

1. \[Advanced Data Security\] ブレードで、\[**Data Discovery \& Classification**\] タイルを選択します。
   
   ![\[Data Discovery \& Classification\] タイルが表示されている。](media/ads-data-discovery-and-classification-pane.png "Advanced Data Security")

2. \[**Data Discovery \& Classification**\] ブレードで、"**We have found 35 columns with classification recommendations**" というメッセージの付いた情報リンクを選択します。
   
   ![\[Data Discovery \& Classification\] で、推奨事項へのリンクが強調表示されている。](media/ads-data-discovery-and-classification-recommendations-link.png "[Data Discovery & Classification]")

3. 推奨事項のリストに目を通し、データの種類と、組み込みの分類設定に基づいて割り当てられる分類について理解を深めてください。分類に関する推奨事項のリストで、**Sales - CreditCard - CardNumber** フィールドに対する推奨事項を選択します。
   
   ![推奨事項のリストで、クレジット カード番号に関する推奨事項が強調表示されている。](media/ads-data-discovery-and-classification-recommendations-credit-card.png "[Data Discovery & Classification]")

4. クレジット カード情報の流出リスクを考慮して、WWI はこの情報を推奨事項でも提案されているように、この情報を単に "**Confidential**" (機密) ではなく、極秘として分類する方式を希望しています。これを修正するために、\[Data Discovery \& Classification\] ブレード上部の \[**+ Add classification**\] を選択します。
   
   ![ツールバーの \[+Add classification\] ボタンが強調表示されている。](media/ads-data-discovery-and-classification-add-classification-button.png "[Data Discovery & Classification]")

5. \[**Sensitivity label**\] フィールドを展開し、選択が可能な、さまざまな組み込みのラベルを簡単に確認します。必要があれば、カスタム ラベルを追加することもできます。
   
   ![組み込みの機密ラベルのリストが表示されている。](media/ads-data-discovery-and-classification-sensitivity-labels.png "[Data Discovery & Classification]")

6. \[Add classification\] ダイアログで、以下を入力します。
   
   - **Schema name**: \[**Sales**\] を選択します。
   - **Table name**: \[**CreditCard**\] を選択します。
   - **Column name**: \[**CardNumber (nvarchar)**\] を選択します。
   - **Information type**: \[**Credit Card**\] を選択します。
   - **Sensitivity level**: \[**Highly Confidential**\] を選択します。
   
   ![上記の値が \[Add classification\] ダイアログに入力されている。](media/ads-data-discovery-and-classification-add-classification.png "[Add classification]")

7. \[**Add classification**\] を選択します。

8. 推奨事項リストから **Sales - CreditCard - CardNumber** フィールドが消え、推奨事項の数が 1 つ減っていることに注目してください。

9. \[Data Classification\] ウィンドウのツールバーにある \[**Save**\] を選択します。保存が完了するまでに数分かかる場合があります。
   
   ![分類された項目のリストに対する更新を保存する](media/ads-data-discovery-and-classification-save.png "[Save]")

10. ほかにレビューできる推奨事項として、**NationIDNumber** と **BirthDate** に対応する **HumanResources - Employee** フィールドがあります。推奨サービスがこれらのフィールドに **Confidential - GDPR** というフラグを設定していることに注目してください。WWI はヨーロッパを含む世界中のプレイヤーに関するデータを維持しています。そのため、GDPR へのコンプライアンスに関係する可能性があるデータを検出できるツールがあれば、大変役立ちます。
    
    ![推奨事項のリストで GDPR 情報が強調表示されている。](media/ads-data-discovery-and-classification-recommendations-gdpr.png "[Data Discovery & Classification]")

11. リスト上部の \[**Select all**\] チェックボックスをオンにして、推奨されている分類の残りすべてを選択してから、\[**Accept selected recommendations**\] を選択します。
    
    ![推奨されている分類のすべてがオンになり、\[Accept selected recommendations\] ボタンが強調表示されている。](media/ads-data-discovery-and-classification-accept-recommendations.png "[Data Discovery & Classification]")

12. \[Data Classification\] ウィンドウのツールバーにある \[**Save**\] を選択します。保存が完了するまでに数分かかる場合があります。
    
    ![分類された項目のリストに対する更新を保存する](media/ads-data-discovery-and-classification-save.png "[Save]")
    
    > **注**: 保存中にエラーが表示されたら、\[Advanced Data Security\] ブレードに戻り、\[Data Discovery \& Classification\] タイルをもう一度選択して、結果を確認してみてください。

13. 保存が完了したら、\[Data Discovery \& Classification\] ブレードの \[**Overview**\] タブを選択して、データベースの分類状態の全体概要を提供するレポートを表示します。
    
    ![ツールバーの \[View Report\] ボタンが強調表示されている。](media/ads-data-discovery-and-classification-overview-report.png "[View Report]")

### タスク 3: Advanced Data Security の脆弱性評価をレビューする

このタスクでは、ADS により生成された `TailspinToys` データベースのために生成した評価レポートをレビューし、`TailspinToys` データベースにおいて、結果中の 1 項目に対する修復処置を取ります。[SQL 脆弱性評価サービス](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)は、セキュリティ状態に対する可視性を提供します。このサービスには、セキュリティ問題を解決してデータベース セキュリティを強化するために実践できる手順が含まれています。

1. `TailspinToys` マネージド データベースの \[**Advanced Data Security**\] ブレードに戻ってから、\[**Vulnerability Assessment**\] タイルを選択します。
   
   ![\[Vulnerability \] タイルが表示されている。](media/ads-vulnerability-assessment-tile.png "Advanced Data Security")

2. \[Vulnerability Assessment\] ブレードのツールバーにある \[**Scan**\] を選択します。
   
   ![ツールバーで Vulnerability assessment の \[Scan\] ボタンが選択されている。](media/vulnerability-assessment-scan.png "[Scan]")

3. スキャンが完了すると、チェックの不合格数と合格数を表示するダッシュボードが現れ、重大度別に分けられたリスク概要も表示されます。
   
   ![\[Vulnerability Assessment\] ダッシュボードが表示されている。](media/sql-mi-vulnerability-assessment-dashboard.png "[Vulnerability Assessment] ダッシュボード")

4. スキャン結果画面で、数分かけて Failed (不合格) のチェックと Passed (合格) のチェックの両方に目を通し、実行されたチェックの種類を確認します。\[**Failed**\] のリストから、**Transparent data encryption** に関するセキュリティ チェックを見つけてください。このチェックは **VA1219** という ID を持っています。
   
   ![Transparent data encryption に関する VA1219 の結果が強調表示されている。](media/sql-mi-vulnerability-assessment-failed-va1219.png "[Vulnerability assessment]")

5. **VA1219** の結果を選択して、詳しい説明を確認します。
   
   ![\[VA1219 - Transparent data encryption should be enabled\] の結果の詳細が表示され、説明、影響、修復のフィールドが強調表示されている。](media/sql-mi-vulnerability-assessment-failed-va1219-details.png "[Vulnerability assessment]")
   
   > それぞれの結果の詳細からは、その結果がもたらされた理由に関する、さらなる洞察が得られます。注目していただきたいのは、結果を説明するフィールド、推奨設定がもたらす影響についてのフィールド、結果の修復に関する詳細についてのフィールドです。

6. それでは今から、結果に対して推奨されている修復手順に従って、`TailspinToys` データベースに対する [Transparent Data Encryption](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal) を有効化することにします。これを達成するために、JumpBox VM 上の SSMS に切り替え、次のいくつかの手順ではこれを使用します。
   
   > **注**: Azure SQL Managed Instance に対して、Transparent data encryption (TDE) は手作業で有効にする必要があります。TDE は悪意のあるアクティビティの脅威から Azure SQL Database、Azure SQL Managed Instance、および Azure Data Warehouse を保護するために役立ちます。TDE はデータベース、関連するバックアップ、保存されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行します。また、そのためにアプリケーションの変更は必要とされません。

7. JumpBox VM の \[スタート\] メニューから Microsoft SQL Server Management Studio 18 を開き、以下の情報を \[**サーバーに接続**\] ダイアログに入力します。
   
   - **サーバー名:** SQL マネージド インスタンスの完全修飾ドメイン名 (前のタスクで Azure Cloud Shell からコピーしたもの) を入力します。
   - **認証:** \[**SQL Server 認証**\] を選択します。
   - **ログイン:** 「`sqlmiuser`」と入力します。
   - **パスワード**: 「`Password.1234567890`」と入力します。
   - \[**パスワードを記憶する**\] ボックスをオンにします。
   
   ![上で指定された SQL マネージド インスタンスの詳細情報が \[サーバーに接続\] ダイアログに入力されている。](media/ssms-18-connect-to-server.png "[サーバーに接続]")

8. SSMS のツールバーの \[**新しいクエリ**\] を選択し、新しいクエリのウィンドウに次の SQL スクリプトを貼り付けます。
   
   ```sql
   USE TailspinToys;
   GO
   
   ALTER DATABASE [TailspinToys] SET ENCRYPTION ON
   ```
   
   > Transparent Data Encryption のオンとオフはデータベース レベルで行います。Azure SQL Managed Instance 内のデータベースで Transparent Data Encryption を有効にするには、T-SQL を使用する必要があります。

9. SSMS ツールバーから \[**実行**\] を選択します。数秒後に、"Commands completed successfully" というメッセージが表示されます。

10. [sys.dm\_database\_encryption\_keys view](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) を使用すると、暗号化の状態と、それに関連付けられている暗号化キーの情報を確認できます。SSMS のツールバーの \[**新しいクエリ**\] をもう一度選択し、新しいクエリのウィンドウに次のクエリを貼り付けます。
    
    ```sql
    SELECT * FROM sys.dm_database_encryption_keys
    ```
    
    ![SSMS の新しいクエリのウィンドウに、上のクエリが貼り付けられている。](media/ssms-sql-mi-database-encryption-keys.png "新しいクエリ")

11. SSMS ツールバーから \[**実行**\] を選択します。\[結果\] ウィンドウに 2 件のレコードが表示されるはずです。これらは暗号化の状態、および暗号化に使用されているキーについての情報を提供しています。
    
    ![SSMS ツールバーの \[実行\] ボタンが強調表示されている。さらに \[結果\] ウィンドウで、TailspinToys データベースの暗号化の状態とキーに関する 2 件のレコードが強調表示されている。](media/ssms-sql-mi-database-encryption-keys-results.png "[結果]")
    
    > 既定では、サービスマネージドの透過的なデータ暗号化 (Transparent Data Encryption) が使用されます。透過的なデータ暗号化のための証明書は、このデータベースが含まれているサーバーに対して自動的に生成されます。

12. Azure portal を再度開き、`TailspinToys` マネージド データベースの \[Advanced Data Security\] - \[Vulnerability Assessment\] ブレードに戻ります。ツールバーにある \[**Scan**\] を選択して、データベースの評価を新たに開始します。
    
    ![ツールバーで Vulnerability assessment の \[Scan\] ボタンが選択されている。](media/vulnerability-assessment-scan.png "[Scan]")

13. スキャンが完了したら、\[**Failed**\] タブを選択し、検索フィルター ボックスに「**VA1219**」と入力して、以前の不合格項目が \[Failed\] のリストからなくなっていることを確認します。
    
    ![\[Failed\] タブが強調表示され、検索フィルターに「VA1219」と入力されている。リストには結果なしと表示されている。](media/sql-mi-vulnerability-assessment-failed-filter-va1219.png "[Failed]")

14. 今度は \[**Passed**\] タブを選択し、**VA1219** チェックが "**PASS**" という状態とともにリストされていることを確認します。
    
    ![\[Passed\] タブが強調表示され、検索フィルターに「VA1219」と入力されている。検索結果で、VA1219 が "PASS" という状態とともに強調表示されている。](media/sql-mi-vulnerability-assessment-passed-va1219.png "[Passed]")
    
    > SQL 脆弱性評価を使用すると、潜在的なデータベース脆弱性を簡単に特定および修復し、データベース セキュリティを予防的に強化することができます。

## 演習 6: 動的データ マスクを有効にする

所要時間: 15 分

この演習では、`TailspinToys` データベース内のクレジット カード番号に対して、[動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (DDM) を有効にします。DDM は、権限を持たないユーザーに対して機密データをマスクすることにより、そのデータの公開を制限します。この機能を使用すると、アプリケーション レイヤーに対する影響を最小限に抑えながら、表示する機密データの量を指定して、機密データに対する未承認のアクセスを防ぐことができます。これはポリシーベースのセキュリティ機能であり、指定されたデータベース フィールドに対するクエリの結果セット内の機密データが表示されないようにします。その際、データベース内のデータが変更されることはありません。

> 例えば、コール センターのサポート担当者は、クレジット カード番号の一部の数字から電話相手を特定できる場合がありますが、このようなデータ項目をサポート担当者にすべて公開すべきではありません。マスク ルールの設定によって、クエリの結果セットに含まれるクレジット カード番号の末尾 4 桁を除くすべての番号をマスクすることができます。もう 1 つの例として、開発者は適切なデータ マスクを定義して、個人情報 (PII) データを保護することにより、コンプライアンス規定に違反することなくトラブルシューティング目的で、運用環境に対するクエリを実行できます。

### タスク 1: クレジット カード 番号に対して DDM を有効にする

ADS Data Discovery \& Classification ツールを使用して `TailspinToys` データベース内のデータを検査した際、クレジット カード番号に対する機密ラベルを "Highly Confidential (極秘)" に設定しました。このタスクでは、また別の手順を取って、この情報を保護することにし、そのために `CreditCard` テーブル内の `CardNumber` フィールドに対して DDM を有効にします。DDM は、このテーブルに対するクエリによって完全なクレジット カード番号が返されることを防ぎます。

1. JumpBox VM で、前に開いた SQL Server Management Studio (SSMS) のウィンドウに戻ります。

2. **TailspinToys** データベース下の \[**テーブル**\] を展開し、`Sales.CreditCard` テーブルを見つけます。テーブルの列を展開し、`CardNumber` という名前の列があることを確認します。テーブルを右クリックして、コンテキスト メニューから \[**上位1000行を選択**\] を選びます。
   
   ![Sales.CreditCard テーブルのコンテキスト メニューで \[上位1000行を選択\]　という項目が強調表示されている。](media/ssms-sql-mi-credit-card-table-select.png "[上位1000行を選択]")

3. 開かれたクエリ ウィンドウで、`CardNumber` フィールドを含んだ \[結果\] を確認します。このデータがプレーン テキストで表示されていることから、データベースへのクエリのためのアクセス権を持つユーザーなら誰でもこのデータを利用できてしまう点に注目してください。
   
   ![プレーン テキストのクレジット カード番号がクエリ結果で強調表示されている。](media/ssms-sql-mi-credit-card-table-select-results.png "[結果]")

4. `CardNumber` フィールドに適用されているマスクをテストするには、まずマスクされたフィールドのテストに使用する、データベース内のユーザーを作成します。SSMS の \[**新しいクエリ**\] を選択し、新しいクエリのウィンドウに次の SQL スクリプトを貼り付けます。
   
   ```sql
   USE [TailspinToys];
   GO
   
   CREATE USER DDMUser WITHOUT LOGIN;
   GRANT SELECT ON [Sales].[CreditCard] TO DDMUser;
   ```
   
   > 上の SQL スクリプトはデータベース内に `DDMUser` という名前の新しいユーザーを作成し、そのユーザーに `Sales.CreditCard` テーブルに対する `SELECT` 権を付与します。

5. SSMS のツールバーから \[**実行**\] を選択し、クエリを実行します。\[メッセージ\] ペインに、コマンドが正常に完了したというメッセージが表示されます。

6. 新しいユーザーが作成されたので、簡単なクエリを実行して結果を確認しましょう。\[**新しいクエリ**\] をもう一度選択し、新しいクエリのウィンドウに以下を貼り付けます。
   
   ```sql
   USE [TailspinToys];
   GO
   
   EXECUTE AS USER = 'DDMUser';
   SELECT TOP 10 * FROM [Sales].[CreditCard];
   REVERT;
   ```

7. ツールバーから \[**実行**\] を選択し、\[結果\] ペインを検証します。クレジット カード番号が、上の場合と同様、プレーン テキストで表示されることに注目してください。
   
   ![クエリ結果内のクレジット カード番号がマスクされていない](media/ssms-sql-mi-ddm-results-unmasked.png "クエリ結果")

8. それでは、`CardNumber` フィールドに DDM を適用して、クエリ結果に表示されないようにしましょう。SSMS のツールバーから \[**新しいクエリ**\] を選択し、`CardNumber` フィールドにマスクを適用する次のクエリをクエリ ウィンドウに貼り付けて、\[**実行**\] を選択します。
   
   ```sql
   USE [TailspinToys];
   GO
   
   ALTER TABLE [Sales].[CreditCard]
   ALTER COLUMN [CardNumber] NVARCHAR(25) MASKED WITH (FUNCTION = 'partial(0,"xxx-xxx-xxx-",4)')
   ```

9. 手順 6 で開いた `SELECT` クエリをもう一度実行し、結果を確かめます。特に、`CardNumber` フィールドの出力を詳しく確認します。参考のために、このクエリを下に示します。
   
   ```sql
   USE [TailspinToys];
   GO
   
   EXECUTE AS USER = 'DDMUser';
   SELECT TOP 10 * FROM [Sales].[CreditCard];
   REVERT;
   ```
   
   ![クエリ結果内のクレジット カード番号がマスクされている](media/ssms-sql-mi-ddm-results-masked.png "クエリ結果")
   
   > `CardNumber` が、適用済みのマスクを使用して表示されています。そのため、カード番号の末尾 4 桁のみが表示されています。動的データ マスクは、未承認ユーザーによる機密情報や制限付きの情報の表示を防止できる、強力な機能です。これはポリシーベースのセキュリティ機能であり、指定されたデータベース フィールドに対するクエリの結果セットに含まれる機密データが表示されないようにします。その際、データベース内のデータが変更されることはありません。

### タスク 2: メール アドレスに DDM を適用する

ADS の Data Discovery \& Classification レポートの結果では、メール アドレスが "Confidential" (機密) としてラベル付けされていました。このタスクでは、組み込み関数の 1 つを使ってメール アドレスに DDM を適用し、この情報の保護に役立てます。

1. そのためには、`[dbo].[Gamer]` テーブルの `LoginEmail` フィールドをターゲットにします。新しいクエリのウィンドウを開き、次のスクリプトを実行します。
   
   ```sql
   USE [TailspinToys];
   GO
   
   SELECT TOP 10 * FROM [dbo].[Gamer]
   ```
   
   ![クエリ結果で完全なメール アドレスが表示されている](media/ddm-select-gamer-results.png "クエリ結果")

2. それでは上で行ったのと同じように、\[dbo\].\[Gamer\] に対する `SELECT` 権を `DDMUser` に付与します。新しいクエリのウィンドウに次のスクリプトを入力してから、\[**実行**\] を選択します。
   
   ```sql
   USE [TailspinToys];
   GO
   
   GRANT SELECT ON [dbo].[Gamer] to DDMUser;
   ```

3. 次は、`LoginEmail` フィールドに DDM を適用して、クエリ結果に完全には表示されないようにします。SSMS のツールバーから \[**新しいクエリ**\] を選択し、`LoginEmail` フィールドにマスクを適用する次のクエリをクエリ ウィンドウに貼り付けて、\[**実行**\] を選択します。
   
   ```sql
   USE [TailspinToys];
   GO
   
   ALTER TABLE [dbo].[Gamer]
   ALTER COLUMN [LoginEmail] NVARCHAR(250) MASKED WITH (FUNCTION = 'Email()');
   ```
   
   > **注**: 組み込みの `Email()` マスク関数が上でどのように使われているかを確認してください。これは SQL Server データベースで利用できる、各種の事前定義済マスクの 1 つです。

4. 以下の `SELECT` クエリを実行し、結果を確かめます。特に、`LoginEmail` フィールドの出力を詳しく確認します。参考のために、このクエリを下に示します。
   
   ```sql
   USE [TailspinToys];
   GO
   
   EXECUTE AS USER = 'DDMUser';
   SELECT TOP 10 * FROM [dbo].[Gamer];
   REVERT;
   ```
   
   ![クエリ結果内のメール アドレスがマスクされている](media/ddm-select-gamer-results-masked.png "クエリ結果")

## 演習 7: 読み取り専用クエリのためのオンライン セカンダリを使用する

所要時間: 15 分

この演習では、プライマリ データベースに対する大きなトランザクション負荷によって影響を生じさせることなしに、自動的に作成されたオンライン セカンダリを使用してレポーティングを行う方法について検証します。Business Critical サービス レベルの SQL MI に含まれる各データベースは、可用性 SLA を支えるため、自動的にいくつかの AlwaysON レプリカとともにプロビジョニングされています。[[読み取りスケールアウト](https://docs.microsoft.com/azure/sql-database/sql-database-read-scale-out)]を用いると、1 台の読み取り専用レプリカのキャパシティを利用して、Azure SQL Database の読み取り専用ワークロードの負荷分散を行えます。

### タスク 1: WWI Tailspin Toys の Web アプリケーションのランキング レポートを表示する

このタスクでは、App Service に展開した Web アプリケーションを使用して、Web レポートを開きます。

1. [Azure portal](https://portal.azure.com) で、\[Azure services\] リストから \[**Resource groups**\] を選択します。
   
   ![\[Azure services\] リストで \[Resource groups\] が強調表示されている。](media/azure-services-resource-groups.png "[Azure services]")

2. リストから hands-on-lab-SUFFIX リソース グループを選択します。
   
   ![Azure のナビゲーション ペインで \[Resource groups\] が選択され、"hands-on-lab-SUFFIX" リソース グループが強調表示されている。](./media/resource-groups.png "リソース グループのリスト")

3. hands-on-lab-SUFFIX リソース グループで、リソースのリストから **tailspintoysUNIQUEID** という App Service を選択します。
   
   ![hands-on-lab-SUFFIX のリソース リストで App Service リソースが選択されている。](media/rg-app-service.png "hands-on-lab-SUFFIX リソース グループ")

4. App Service の概要ブレードで **URL** を選択し、ブラウザーのウィンドウで Web アプリケーションを開きます。
   
   ![App service の URL が強調表示されている。](media/app-service-url.png "App Service の URL")

5. WWI Tailspin Toys の Web アプリで、メニューから \[**Leaderboard**\] を選択します。
   
   ![Leaderboard ページで READ\_WRITE が強調表示されている。](media/tailspin-toys-leaderboard-read-write.png "Tailspin Toys の Web アプリ")
   
   > ページ上の文字列 `READ_WRITE` に注目してください。これは、ターゲット データベースの `ApplicationIntent` オプションに関連付けられている `Updateability` プロパティを読み込んだ結果の出力であり、`SELECT DATABASEPROPERTYEX(DB_NAME(), "Updateability")` という SQL クエリを使って取得できます。

### タスク 2:読み取り専用接続文字列を更新する

このタスクでは、接続文字列内の `ApplicationIntent` オプションを使用して、`TailspinToys` データベースに対する読み取りスケールアウトを有効にします。このオプションは、接続が書き込みレプリカと読み取り専用レプリカのどちらにルーティングされるのかを規定します。具体的には、`ApplicationIntent` の値が `ReadWrite` (既定値) の場合は、接続がデータベースの読み取り/書き込みレプリカに向けられます。`ApplicationIntent` の値が `ReadOnly` の場合は、接続が読み取り専用レプリカにルーティングされます。

1. Azure portal の \[App Service\] ブレードに戻り、左側の \[Settings\] 以下の \[**Configuration**\] を選択します。
   
   ![\[Settings\] 以下の \[Configuration\] アイテムが選択されている。](media/app-service-configuration-menu.png "[Configuration]")

2. \[Configuration\] ブレードで下にスクロールし、\[**Connection strings**\] セクション内で `TailspinToysReadOnlyContext` という名前の接続文字列を見つけてから、その右にある鉛筆 (編集) アイコンを選択します。
   
   ![読み取り専用接続文字列の横にある編集アイコンが強調表示されている。](media/app-service-configuration-connection-strings-read-only.png "[Connection strings]")

3. \[Add/Edit connection string\] ダイアログで、`TailspinToysReadOnlyContext` の \[**Value**\] 列の値を選択し、接続文字列の末尾に次のパラメーターを貼り付けます。
   
   ```sql
   ApplicationIntent=ReadOnly;
   ```

4. 接続文字列 `TailspinToysReadOnlyContext` は、以下と類似したものになっているはずです。
   
   ```sql
   Server=tcp:sqlmi-abcmxwzksiqoo.15b8611394c5.database.windows.net,1433;Database=TailspinToys;User ID=sqlmiuser;Password=Password.1234567890;Trusted_Connection=False;Encrypt=True;TrustServerCertificate=True;ApplicationIntent=ReadOnly;
   ```

5. \[**OK**\] を選択します。

6. \[Configuration\] ブレード上部の \[**Save**\] を選択します。アプリケーションが再起動されるというメッセージが表示されたら、\[**Continue**\] を選択します。
   
   ![\[Application settings\] ブレードの保存ボタンが強調表示されている。](media/app-service-configuration-save.png "[Save]")

### タスク 3: Tailspin Toys の Web アプリのランキング レポートを再読み込みする

このタスクでは、WWI の Tailspin Toys の Web アプリのランキング レポートを最新の情報に更新し、結果を確認します。

1. 先ほど開いた Tailspin Toys のプレイヤー情報 Web サイトに戻り、\[**Leaderboard**\] ページを最新の情報に更新します。ページは以下と同様の表示になっているはずです。
   
   ![\[Reports\] ページで READ\_ONLY が強調表示されている。](media/tailspin-toys-leaderboard-read-only.png "Tailspin Toys の Web アプリ")
   
   > `updateability` オプションが `READ_ONLY` として表示されていることに注目してください。データベース接続文字列への簡単な追加を行うことで、読み取り専用クエリを Business-Critical の SQL MI データベースのオンライン セカンダリに送信できるようになります。これにより、1 台の読み取り専用レプリカのキャパシティを利用して、読み取り専用ワークロードの負荷分散を行うことが可能になります。SQL MI の Business Critical クラスターは、組み込みの読み取りスケールアウト機能を通じて、無償の読み取り専用ノードを提供します。このノードは、プライマリ ワークロードのパフォーマンスに影響を与えないことが求められる、読み取り専用クエリの実行に利用できます。

## ハンズオン ラボの後に

所要時間: 10 分

この演習では、今回のハンズオン ラボで利用するために作成したすべての Azure リソースのプロビジョニング解除を実行します。

### タスク 1: Azure リソース グループを削除する

1. Azure portal の左側メニューから \[**Resource groups**\] を選択し、**hands-on-lab-SUFFIX** リソース グループを見つけて削除します。
   
   > **注**: SQL MI を含んだリソース グループの削除は、必ずしも 1 回目にうまくいくとは限りません。最初に削除を試みた後に、SQL MI インスタンスとともに、いくつかのネットワーキング コンポーネント (ルート テーブル、SQL MI NSG、VNet) がリソース グループに残ることがあります。こうしたケースでは、最初のプロセスが完了するのを待ってから、もう一度リソース グループの削除を試みてください。場合によっては、数時間以上の間隔を置いてから削除を試みる必要があります。

### タスク 2:wide-world-importers のサービス プリンシパルを削除する

1. Azure portal で \[**Azure Active Directory**\] を選んでから、\[**App registrations**\] を選択します。

2. アプリケーション **wide-world-importers** を選び、アプリケーション ブレードの \[**Delete**\] を選択します。

ここで挙げられた手順のすべては、ハンズオン ラボの "参加後" に行う必要があります。