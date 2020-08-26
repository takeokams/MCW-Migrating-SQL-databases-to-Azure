![マイクロソフト クラウド ワークショップ](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "マイクロソフト クラウド ワークショップ")

<div class="MCWHeader1">
Migrating SQL databases to Azure
</div>
<div class="MCWHeader2">
Before the hands-on lab setup guide
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

<!-- TOC -->

- [ハンズオン ラボ前の Azure への SQL データベースの移行の準備ガイド](#ハンズオン-ラボ前の-azure-への-sql-データベースの移行の準備ガイド)
    - [必要条件](#必要条件)
    - [ハンズオン ラボの前に](#ハンズオン-ラボの前に)
        - [タスク 1: リソース グループを作成する](#タスク-1-リソース-グループを作成する)
        - [タスク 2: Microsoft DataMigration リソース プロバイダーを登録する](#タスク-2-microsoft-datamigration-リソース-プロバイダーを登録する)
        - [タスク 3: ARM テンプレートを実行してラボ リソースをプロビジョニングする](#タスク-3-arm-テンプレートを実行してラボ-リソースをプロビジョニングする)

<!-- /TOC -->
# ハンズオン ラボ前の Azure への SQL データベースの移行の準備ガイド

## 必要条件

- Microsoft Azure サブスクリプションが従量課金制または MSDN であること。
  - 無料評価版サブスクリプションでは動作しません。

> **重要**: このハンズオン ラボを完了するためには、Azure Active Directory アプリケーションやサービス プリンシパルを作成したり、サブスクリプションのロールを割り当てたりするための十分な権限を、Azure AD テナント内に持っている必要があります。

## ハンズオン ラボの前に

所要時間: 15 分

この演習では、ハンズオン ラボの以降の部分で使用する環境をセットアップします。ここで挙げられた手順のすべては、ハンズオン ラボの "参加前" に行う必要があります。

> **重要**: 多くの Azure リソースにおいて、グローバルに一意の名前を付ける必要があります。これらのステップ全体を通して、リソース名の一部として "SUFFIX" という単語が使用されています。リソースに一意の名前を付けるには、これをマイクロソフトのエイリアス、イニシャル、または別の値に置き換える必要があります。

### タスク 1: リソース グループを作成する

1. [Azure portal](https://portal.azure.com) で、\[Azure services\] リストから \[**Resource groups**\] を選択します。
   
   ![\[Azure services\] リストで \[Resource groups\] が強調表示されている。](media/azure-services-resource-groups.png "[Azure services]")

2. \[Resource groups\] ブレードで、\[**+Add**\] を選択します。
   
   ![\[Resource groups\] ブレードのツールバーの \[+Add\] が強調表示されている。](media/resource-groups-add.png "[Resource groups]")

3. \[Create a resource group\] の \[**Basics**\] タブで、以下を入力します。
   
   - **Subscription**: このハンズオン ラボで使用するサブスクリプションを選択します。
   - **Resource group**: 新しいリソース グループの名前として「`hands-on-lab-SUFFIX`」と入力します。
   - **Region**: このハンズオン ラボで使用するリージョンを選択します。
   
   ![\[Create a resource group\] の \[Basics\] タブに上記の値が入力されている。](media/create-resource-group.png "[Create a resource group]")

4. \[**Review + Create**\] を選択します。

5. \[**Review + create**\] タブで、"Validation passed" というメッセージが表示されていることを確認した後、\[**Create**\] を選択します。

### タスク 2: Microsoft DataMigration リソース プロバイダーを登録する

このタスクでは、Azure サブスクリプションに `Microsoft.DataMigration` リソース プロバイダーを登録します。このリソース プロバイダーの登録は、サブスクリプション内での Azure Database Migration Service の作成を可能にするために必要となります。

1. [Azure portal](https://portal.azure.com) で、\[Azure services\] リストから \[**Subscriptions**\] を選択します。
   
   ![\[Azure services\] リストの \[Subscriptions\] が強調表示されている。](media/azure-services-subscriptions.png "[Azure services]")

2. リストから、このハンズオン ラボで使用するサブスクリプションを選択します。
   
   ![\[Subscriptions\] のリストで対象サブスクリプションが強調表示されている。](media/azure-subscriptions.png "[Subscriptions] のリスト")

3. \[Subscriptions\] ブレードで、左側のメニューから \[**Resource providers**\] を選択した後、フィルター ボックスに「migration」と入力します。
   
   ![\[Subscriptions\] ブレードの左側のメニューの \[Resource providers\] が強調表示されている。\[Resource providers\] ブレードのフィルター ボックスに「migration」と入力されている。](media/azure-portal-subscriptions-resource-providers-register-microsoft-datamigration.png "リソース プロバイダーの登録")

4. `Microsoft.DataMigration` プロバイダーの \[Status\] が `NotRegistered` になっている場合、ツールバーの \[**Register**\] を選択します。
   
   ![\[Resource providers\] のツールバーの \[Register\] ボタンが強調表示されている。](media/azure-subscriptions-resource-providers-toolbar.png "[Resource providers] のツールバー")

5. 登録が完了するまで数分程度かかる場合があります。必ず、\[Status\] が \[**Registered**\] に設定されていることを確認してから、次に進んでください。ツールバーの \[**Refresh**\] を選択して、\[Status\] を更新して確認する必要がある場合があります。
   
   ![Microsoft.DataMigration リソース プロバイダーの横の \[Registered\] が強調表示されている。](media/resource-providers-datamigration-registered.png "Microsoft DataMigration リソース プロバイダー")

### タスク 3: ARM テンプレートを実行してラボ リソースをプロビジョニングする

このタスクでは、Azure Resource Manager (ARM) テンプレートを実行して、このハンズオン ラボに必要なリソースを作成します。VM と SQL MI 間の通信を容易にするために、さまざまなコンポーネントが新しい仮想ネットワーク (VNet) 内に展開されます。ARM テンプレートは、ポート 3389 を開いて、JumpBox への RDP 接続を許可するなど、SQL MI と VM に関連付けられたネットワーク セキュリティ グループに受信および送信セキュリティ規則も追加します。ARM テンプレートは、リソースを作成するだけでなく、各 VM で PowerShell スクリプトを実行して、ソフトウェアをインストールし、サーバーを設定します。ARM テンプレートが作成するリソースには、以下のようなものがあります。

- 3 つのサブネット (ManagedInstance、Management、および Gateway) を持つ仮想ネットワーク
- Gateway サブネットに関連付けられた仮想ネットワーク ゲートウェイ
- ルート テーブル
- ManagedInstance サブネットに追加される Azure SQL Managed Instance (SQL MI)
- Visual Studio 2019 Community Edition と SQL Server Management Studio を備えた JumpBox (SSMS がインストールされ、Management サブネットに追加される)
- Data Migration Assistant (DMA) がインストールされ、Management サブネットに追加される SQL Server 2008 R2 VM
- Azure Database Migration Service (DMS)
- Azure App Service Plan と App Service (Web App)
- Azure Blob Storage アカウント

> **注**: ラボ リソースを手動でプロビジョニングおよび設定する手順については、[手動によるリソースのセットアップ ガイド](./Manual-resource-setup.md)を参照してください。

1. [Azure portal](https://portal.azure.com/) で、\[**Show portal menu**\] アイコンを選択した後、メニューから \[**+Create a resource**\] を選択します。
   
   ![\[Show portal menu\] アイコンが強調表示され、ポータル メニューが表示されている。ポータル メニューの \[Create a resource\] が強調表示されている。](media/create-a-resource.png "[Create a resource]")

2. ARM テンプレートを実行する前に、サブスクリプションで SQL MI をプロビジョニングできることを簡単に確認しておくことをお勧めします。[Azure portal](https://portal.azure.com) で、\[**+Create a resource**\] を選択して、Marketplace 検索ボックスに「sql managed instance」と入力した後、結果から \[**Azure SQL Managed Instance**\] を選択します。
   
   ![Azure のナビゲーション ペインで \[+Create a resource\] が選択され、Marketplace 検索ボックスに「sql managed instance」と入力されている。結果で \[Azure SQL Managed Instance\] が選択されている。](media/create-resource-sql-mi.png "SQL Managed Instance の作成")

3. \[Azure SQL Managed Instance\] ブレードで、\[**Create**\] を選択します。
   
   ![\[Azure SQL Managed Instance\] ブレードの \[Create\] ボタンが強調表示されている。](media/sql-mi-create.png "Azure SQL Managed Instance の作成")

4. \[SQL Managed Instance\] ブレードで、"Managed instance creation is not available for the chosen subscription type..." というメッセージを探します。このメッセージは、\[SQL Managed Instance\] ブレードの下部に表示されます。
   
   ![選択されたサブスクリプションで SQL MI を作成できないことを知らせるメッセージが表示されている。](media/sql-mi-creation-not-available.png "SQL MI を作成できないことを知らせるメッセージ")
   
   > **注**: Business Critical、vCore 16、32 GBで、指定のリージョンをセットして確認してください。もし東日本リージョンなどで作成できない場合は、米国など他のリージョンを指定することで作成できることがあります。その際は、先に作成したリソース グループのリージョンもそれに合わせる必要があります。リージョンを変更することだけで対応できないような"Managed instance creation is not available for the chosen subscription type" というメッセージが表示されている場合、[SQL Managed Instance のクォータを増加させる](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#obtaining-a-larger-quota-for-sql-managed-instance)手順を実行してから、次のステップに進みます。

5. これで、ARM テンプレートの展開を開始する準備が整いました。下の \[Deploy to Azure\] ボタンを選択して、Azure portal で \[Custom deployment\] 画面を開きます。
   
   <a href ="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2FMCW-Migrating-SQL-databases-to-Azure%2Fmaster%2FHands-on%20lab%2Flab-files%2FARM-template%2Fazure-deploy.json" target="_blank" title="Deploy to Azure">
   <img src="http://azuredeploy.net/deploybutton.png"/>
   </a>

   > **注**: ARM テンプレートを実行すると、`VnetSubnetConflictedWithIntendedPolicy` のコードで `ResourceDeploymentFailure` エラーが発生する場合があります。このエラーは、ARM テンプレートの問題によるものではなく、Azure のバックエンド リソースの展開に関する問題の結果として発生するものと考えられます。現時点における回避策として、まず、異なるリージョンでの展開を試してみます。うまくいかない場合は、[手動によるリソースのセットアップ ガイド](./Manual-resource-setup.md)を読んで、SQL MI データベースの作成を試してみます。

6. Azure portal の \[Custom deployment\] 画面で、以下を入力します。
   
   - **Subscription**: このハンズオン ラボで使用するサブスクリプションを選択します。
   - **Resource group**: ドロップダウン リストから \[hands-on-lab-SUFFIX\] リソース グループを選択します。
   - **Location**: \[hands-on-lab-SUFFIX\] リソース グループで使用した場所を選択します。
   - **Managed Instance Name**: 既定値 "**sqlmi**" をそのまま使用します。実際の名前はグローバルに一意である必要があるため、リソース グループから一意の文字列が生成され、プロビジョニング中に名前に追加されます。
   - **Admin Username**: 既定値 "**sqlmiuser**" をそのまま使用します。
   - **Admin Password**: 既定値 "**Password.1234567890**" をそのまま使用します。
   - **V Cores**: 既定値 "**16**" をそのまま使用します。
   - **Storage Size in GB**: 既定値 "**32**" をそのまま使用します。
   - チェックボックスをオンにして、Azure Marketplace の使用条件に同意します。
   
   ![\[Custom deployment\] ブレードが表示され、\[Custom deployment\] ブレードで上記の情報が入力されている。](media/azure-custom-deployment.png "[Custom deployment] ブレード")

7. \[**Purchase**\] を選択して、JumpBox VM と SQL Managed Instance のプロビジョニングを開始します。
   
   > **注**: SQL MI が含まれているため、カスタム ARM テンプレートの展開が完了するまで 6 時間以上かかる場合があります。ただし、大半のリソースの展開は、数分以内に完了します。JumpBox および SQL Server 2008 R2 VM は、15 分程度で完了するはずです。

8. 展開の進捗状況を監視するには、Azure portal で \[hands-on-lab-SUFFIX\] リソース グループにナビゲートした後、左側のメニューから \[**Deployments**\] を選択します。展開に **Microsoft.Template** という名前が付けられています。それを選択して、テンプレートの各項目の進捗状況を確認します。
   
   ![\[hands-on-lab-SUFFIX\] リソース グループの左側のメニューでメニュー項目 \[Deployments\] が選択され、展開名 Microsoft.Template が強調表示されている。](media/resource-group-deployments.png "リソース グループの展開")

> 数時間後に再び確認して、SQL MI のプロビジョニングの進捗状況を監視します。プロビジョニングが 7 時間以上続いている場合は、Azure portal でサポート チケットを発行して、プロビジョニング プロセスのブロック解除を Microsoft サポートに依頼する必要がある可能性があります。タイムアウトする場合は、[手動によるリソースのセットアップ ガイド](./Manual-resource-setup.md)を参考に、SQL MIを作成してください。SQL 2008 VM内のDBも作成されていない可能性があるので、その際はTailspinToys DBも作成してください。

ここで挙げられた手順のすべては、ハンズオン ラボの "参加前" に行う必要があります。