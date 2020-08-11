# 手動によるリソースの展開およびセットアップ ガイド

このガイドでは、ハンズオン ラボ前のガイドで言及した、ARM テンプレートが作成したリソースを、手動でプロビジョニングおよび設定する手順について順を追って説明します。

2020 年 6 月

**目次:** 

- [手動によるリソースの展開およびセットアップ ガイド](#手動によるリソースの展開およびセットアップ-ガイド)
    - [タスク 1: 仮想ネットワークを作成する](#タスク-1-仮想ネットワークを作成する)
    - [タスク 2: VPN ゲートウェイを作成する](#タスク-2-vpn-ゲートウェイを作成する)
    - [タスク 3: SQL MI をプロビジョニングする](#タスク-3-sql-mi-をプロビジョニングする)
    - [タスク 4: JumpBox VM を作成する](#タスク-4-jumpbox-vm-を作成する)
    - [タスク 5: SQL Server 2008 R2 仮想マシンを作成する](#タスク-5-sql-server-2008-r2-仮想マシンを作成する)
    - [タスク 6: Azure Database Migration Service を作成する](#タスク-6-azure-database-migration-service-を作成する)
    - [タスク 7: Web App をプロビジョニングする](#タスク-7-web-app-をプロビジョニングする)
    - [タスク 8: Azure Blob Storage アカウントを作成する](#タスク-8-azure-blob-storage-アカウントを作成する)
    - [タスク 9: JumpBox に接続する](#タスク-9-jumpbox-に接続する)
    - [タスク 10: JumpBox に必要なソフトウェアをインストールする](#タスク-10-jumpbox-に必要なソフトウェアをインストールする)
    - [タスク 11: SqlServer2008 VM に接続する](#タスク-11-sqlserver2008-vm-に接続する)
    - [タスク 12: SqlServer2008 VM 上で WWI TailspinToys データベースを構成する](#タスク-12-sqlserver2008-vm-上で-wwi-tailspintoys-データベースを構成する)

> **重要**: 多くの Azure リソースにおいて、グローバルに一意の名前を付ける必要があります。これらのステップ全体を通して、リソース名の一部として "SUFFIX" という単語が使用されています。リソースに一意の名前を付けるには、これをマイクロソフトのエイリアス、イニシャル、または別の値に置き換える必要があります。

## タスク 1: 仮想ネットワークを作成する

このタスクでは、仮想ネットワーク (VNet) を作成および設定します。これには、SQL Managed Instance、JumpBox VM、およびこのハンズオン ラボ全体を通して使用される他のいくつかのリソースが含まれます。プロビジョニング後、ルート テーブルを ManagedInstance サブネットに関連付け、Management サブネットを VNet に追加します。

1. [Azure portal](https://portal.azure.com/) で、\[**Show portal menu**\] アイコンを選択した後、メニューから \[**+Create a resource**\] を選択します。
   
   ![ポータル メニュー表示アイコンが強調表示され、ポータル メニューが表示されている。ポータル メニューの \[Create a resource\] が強調表示されている。](media/create-a-resource.png "[Create a resource]")

2. Marketplace 検索ボックスに「virtual network」と入力した後、結果から \[**Virtual Network**\] を選択します。
   
   ![Marketplace 検索ボックスに「virtual network」と入力されている。結果で \[Virtual Network\] が選択されている。](media/create-resource-vnet.png "仮想ネットワークの作成")

3. \[Virtual Network\] ブレードで、\[**Create**\] を選択します。
   
   ![\[Virtual Network\] ブレードの \[Create\] ボタンが強調表示されている。](media/vnet-create.png "仮想ネットワークの作成")

4. \[Create virtual network\] の \[**Basics**\] タブで、以下を入力します。
   
   Project details:
   
   - **Subscription**: このハンズオン ラボで使用するサブスクリプションを選択します。
   - **Resource group**: リストから \[**hands-on-lab-SUFFIX**\] リソース グループを選択します。
   
   Instance details:
   
   - **Name**: 「`hands-on-lab-SUFFIX-vnet`」と入力します。
   - **Region**: このハンズオン ラボのリソースで使用するリージョンを選択します。
   
   ![\[Create virtual network\] の \[Basics\] タブの該当フィールドに上記の値が入力されている。](media/create-virtual-network-basics-tab.png "[Create virtual network] の [Basics] タブ")

5. \[**Next: IP Addresses\] を選択します。**

6. \[**IP Addresses**\] タブで、サブネット下の \[**default**\] を選択し、以下のようにサブネットのプロパティを編集します。
   
   - **Subnet** **name**: 「`ManagedInstance`」と入力します。
   - **Address space**: 既定値をそのまま使用します。**10.X.0.0/24** の形式で、/24 のサブネット マスクが使用され、VNet の IPv4 アドレス空間で示されているアドレス空間内に含まれているはずです。
   - \[**Save**\] を選択します。
   
   ![\[Create virtual network\] の \[IP Addresses\] タブの該当フィールドに上記の値が入力されている。](media/create-virtual-network-ip-addresses-tab.png "[Create virtual network] の [IP Addresses] タブ")

7. \[**Review + create**\] を選択します。残りのタブに関しては既定値が使用されるため、スキップして構いません。

8. \[**Review + create**\] タブで、"**Validation passed**" というメッセージが表示されていることを確認し、\[**Create**\] を選択します。仮想ネットワークのプロビジョニングが完了するまで数秒かかります。

9. 完了すると、Azure portal で \[Deployment succeeded\] 通知が届きます。通知内の \[**Go to resource**\] を選択します。
   
   ![Azure portal の \[Deployment succeeded\] 通知内の \[Go to resource\] ボタンが強調表示されている。](media/vnet-go-to-resource.png "[Deployment succeeded] 通知")

10. \[Virtual network\] ブレードで、左側のメニューの \[Settings\] 下の \[**Subnets**\] を選択した後、上部のメニューから \[**+ Subnet**\] を選択します。
    
    ![\[Virtual network\] ブレードの左側のメニュー項目の \[Subnets\] が強調表示および選択され、上部のメニューの \[+ Subnet\] が強調表示されている。](media/vnet-subnets-add.png "サブネットの追加")

11. \[Add subnet\] ブレードで、以下を入力します。
    
    - **Name**: 「`Management`」と入力します。
    - **Address range**: 既定値をそのまま使用します。/24 のサブネット マスクが使用され、VNet のアドレス範囲内に含まれているはずです。
    - **NAT gateway**: \[**None**\] が設定された状態のままにします。
    - **Network security group**: \[**None**\] が設定された状態のままにします。
    - **Route table**: \[**None**\] が設定された状態のままにします。
    - **Service endpoints**: \[**0 selected**\] が設定された状態のままにします。
    - **Subnet delegation**: \[**None**\] が設定された状態のままにします。
    
    ![\[Add subnet\] ブレードの \[Name\] フィールドに「Management」と入力され、残りの設定には既定値が指定されている。](media/add-subnet-management.png "[Add subnet]")

12. \[**OK**\] を選択します。

13. \[**Subnets**\] ブレードに戻り、\[**+ Gateway subnet**\] を選択します。
    
    ![左側のメニューの \[Subnets\] が選択および強調表示されている。\[Subnets\] ブレードの \[+ Gateway subnet\] が強調表示されている。](media/vnet-add-gateway-subnet.png "[Subnets]")

14. ゲートウェイ サブネット名として `GatewaySubnet` という値が \[**Name**\] に自動入力されます。この値は、Azure がサブネットをゲートウェイ サブネットとして認識するのに必要となります。\[Address range\] については自動入力された値をそのまま使用し、\[Route table\]、\[Service endpoints\]、および \[Subnet delegation\] については既定値が設定された状態のままにします。
    
    ![既定値が指定された \[Add subnet\] フォームが表示されている。](media/vnet-add-gateway-subnet-form.png "[Add subnet]")
    
    > **注**: 既定のアドレス範囲を使用すると、/24 の CIDR ブロックでゲートウェイ サブネットが作成されます。これにより、将来の新たな設定要件に対応するのに十分な IP アドレスが提供されます。

15. \[**OK**\] を選択します。

## タスク 2: VPN ゲートウェイを作成する

このタスクでは、仮想ネットワーク ゲートウェイをセットアップします。

1. [Azure portal](https://portal.azure.com/) で、\[**Show portal menu**\] アイコンを選択した後、メニューから \[**+Create a resource**\] を選択します。
   
   ![ポータル メニュー表示アイコンが強調表示され、ポータル メニューが表示されている。ポータル メニューの \[Create a resource\] が強調表示されている。](media/create-a-resource.png "[Create a resource]")

2. Marketplace 検索ボックスに「virtual network gateway」と入力して、結果から \[**Virtual network gateway**\] を選択します。
   
   ![Marketplace 検索ボックスに「virtual network gateway」と入力され、結果で \[Virtual network gateway\] が選択されている。](media/create-resource-virtual-network-gateway.png "[Create a resource]")

3. \[Virtual network gateway\] ブレードで、\[**Create**\] を選択します。
   
   ![\[Virtual network gateway\] ブレードの \[Create\] ボタンが強調表示されている。](media/virtual-network-gateway-create.png "[Virtual network gateway]")

4. \[Create virtual network gateway\] の \[**Basics**\] タブで、以下を入力します。
   
   - Project details:
     
     - **Subscription**: このハンズオン ラボで使用するサブスクリプションを選択します。
     - **Resource group**: 仮想ネットワークのリソース グループに基づいて指定されます。
   
   - Instance details:
     
     - **Name**: 「`hands-on-lab-SUFFIX-vnet-gateway`」と入力します。
     - **Region**: このハンズオン ラボのリソースで使用する場所を選択します。
     - **Gateway type**: \[**VPN**\] を選択します。
     - **VPN type**: \[**Route-based**\] を選択します。
     - **SKU**: \[**VpnGw1**\] を選択します。
     - **Virtual network**: \[**hands-on-lab-SUFFIX-vnet**\] を選択します。
   
   - Public IP address:
     
     - **Public IP address**: \[**Create new**\] を選択します。
     - **Public IP address name**: 「**vnet-gateway-ip**」と入力します。
     - **Enable active-active mode**: \[**Disabled**\] を選択します。
     - **Configure BGP ASN**: \[**Disabled**\] を選択します。
   
   ![\[Create virtual network gateway\] の \[Basics\] タブの該当フィールドに上記の値が入力されている。](media/virtual-network-gateway-create-basics.png "[Create virtual network gateway]")

5. \[**Review + create**\] を選択します。

6. \[**Review + create**\] タブで、"Validation passed" というメッセージが表示されていることを確認した後、\[**Create**\] を選択します。
   
   ![\[Review + create\] タブで、"Validation passed" というメッセージが表示されている。](media/virtual-network-gateway-create-summary.png "[Create virtual network gateway]")

7. 仮想ネットワーク ゲートウェイのプロビジョニングが完了するまで最長で 45 分かかる場合があります。

## タスク 3: SQL MI をプロビジョニングする

このタスクでは、Azure SQL Managed Instance を作成します。

1. [Azure portal](https://portal.azure.com/) で、\[**Show portal menu**\] アイコンを選択した後、メニューから \[**+Create a resource**\] を選択します。
   
   ![ポータル メニュー表示アイコンが強調表示され、ポータル メニューが表示されている。ポータル メニューの \[Create a resource\] が強調表示されている。](media/create-a-resource.png "[Create a resource]")

2. Marketplace 検索ボックスに「sql managed instance」と入力した後、結果から \[**Azure SQL Managed Instance**\] を選択します。
   
   ![Marketplace 検索ボックスに「sql managed instance」と入力されている。結果で \[Azure SQL Managed Instance\] が選択されている。](media/create-resource-sql-mi.png "SQL Managed Instance の作成")

3. \[Azure SQL Managed Instance\] ブレードで、\[**Create**\] を選択します。
   
   ![\[Azure SQL Managed Instance\] ブレードの \[Create\] ボタンが強調表示されている。](media/sql-mi-create.png "Azure SQL Managed Instance の作成")

4. \[Create Azure SQL Database Managed Instance\] の \[Basics\] タブで、以下を入力します。
   
   - Project details:
     
     - **Subscription**: このハンズオン ラボで使用するサブスクリプションを選択します。
     - **Resource group**: リストから \[**hands-on-lab-SUFFIX**\] を選択します。
   
   - Managed Instance details:
     
     - **Managed instance name**: 「`sqlmi-SUFFIX`」と入力します。
     - **Region**: このハンズオン ラボのリソースで使用するリージョンを選択します。
     - **Compute + storage**: \[**Configure Managed Instance**\] を選択して、\[Configure performance\] ブレードで \[**Business Critical**\]、\[**Gen5**\] を選択し、\[vCores\] を **16**、\[Storage\] を **32** に設定した後、\[**Apply**\] を選択します。
     
     ![\[Configure performance\] ブレードで、\[Business Critical\]、\[Gen5\] が選択され、\[vCores\] が 16 に設定され、\[Storage\] サイズが 32 に設定されている。](media/sql-mi-configure-performance.png "[Configure performance]")
   
   - Administrator account:
     
     - **Managed instance admin login**: 「`sqlmiuser`」と入力します。
     - **Password**: 「`Password.1234567890`」と入力します。
     
     ![\[Create Azure SQL Database Managed Instance\] の \[Basics\] タブの該当フィールドに上記の値が入力されている。](media/sql-managed-instance-basics-tab.png "[Create Azure SQL Database Managed Instance]")
     
     > **注**: "Managed instance creation is not available for the chosen subscription type" というメッセージが表示されている場合、[SQL Managed Instance のクォータを増加させる](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#obtaining-a-larger-quota-for-sql-managed-instance)手順を実行します。
     
     ![選択されたサブスクリプションで SQL MI を作成できないことを知らせるメッセージが表示されている。](media/sql-mi-creation-not-available.png "SQL MI を作成できないことを知らせるメッセージ")

5. \[**Next: Networking\] を選択して、\[**Networking**\] タブで、以下の構成を設定します。**
   
   - **Virtual network**: ドロップダウン リストから \[**hands-on-lab-SUFFIX-vnet/ManagedInstance**\] を選択します。
   - **Prepare subnet for Managed Instance**: \[**Automatic**\] を選択します。
   - **Connection type**: \[**Proxy (Default)**\] が選択された状態のままにします。
   - **Enable public endpoint**: \[**Disable**\] を選択します。
   
   ![\[Create Azure SQL Database Managed Instance\] の \[Networking\] タブのフォームに上記の設定が入力されている。](media/sql-managed-instance-networking-tab.png "[Create Azure SQL Database Managed Instance]")

6. \[**Next: Review + create\] を選択して、\[**Review + create**\] タブで構成を確認した後、\[**Create**\] を選択します。**
   
   > **注**: これがサブネットに展開する最初のインスタンスである場合、SQL Managed Instance のプロビジョニングが完了するまで 4 時間以上かかる場合があります。プロビジョニングの処理中、残りのタスクに進んでも構いません。展開プロセスが 6 時間を超える場合は、サポート チケットを送信して、セットアップ完了のサポートを依頼する必要がある可能性があります。

## タスク 4: JumpBox VM を作成する

このタスクでは、Azure で仮想マシン (VM) をプロビジョニングします。使用する VM イメージには、最新バージョンの Visual Studio Community 2019 がインストールされます。

1. [Azure portal](https://portal.azure.com/) で、\[**Show portal menu**\] アイコンを選択した後、メニューから \[**+Create a resource**\] を選択します。
   
   ![ポータル メニュー表示アイコンが強調表示され、ポータル メニューが表示されている。ポータル メニューの \[Create a resource\] が強調表示されている。](media/create-a-resource.png "[Create a resource]")

2. Marketplace 検索ボックスに「visual studio 2019」と入力した後、結果から \[**Visual Studio 2019 Latest**\] を選択します。
   
   ![Marketplace 検索ボックスに「visual studio 2019」と入力されている。検索結果で \[Visual Studio 2019 Latest\] が選択されている。](./media/create-resource-visual-studio-vm.png "Visual Studio 2019 仮想マシンの作成")

3. \[Visual Studio 2019\] ブレードで、\[Select a software plan\] ドロップダウン リストから \[**Visual Studio 2019 Community (latest release) on Windows Server 2019 (x64)**\] を選択した後、\[**Create**\] を選択します。
   
   ![\[Visual Studio 2019\] ブレードの \[Select a software plan\] ドロップダウン リストの \[Visual Studio 2019 Community (latest release) on Windows Server 2019 (x64)\] が強調表示されている。](media/visual-studio-create.png "[Visual Studio 2019]")

4. \[Create a virtual machine\] の \[**Basics**\] タブで、以下の構成を設定します。
   
   - Project details:
     
     - **Subscription**: このハンズオン ラボで使用するサブスクリプションを選択します。
     - **Resource group**: 既存のリソース グループのリストから \[**hands-on-lab-SUFFIX**\] リソース グループを選択します。
   
   - Instance details:
     
     - **Virtual machine name**: 「`JumpBox`」と入力します。
     - **Region**: このハンズオン ラボのリソースで使用するリージョンを選択します。
     - **Availability options**: \[**No infrastructure redundancy required**\] を選択します。
     - **Image**: \[**Visual Studio 2019 Community (latest release) on Windows Server 2019 (x64)**\] が選択された状態のままにします。
     - **Azure Spot instance**: \[**No**\] を選択します。
     - **Size**: 既定のサイズ "**Standard\_D4s\_v3**" をそのまま使用します。
   
   - Administrator account:
     
     - **Username**: 「`sqlmiuser`」と入力します。
     - **Password**: 「`Password.1234567890`」と入力します。
   
   - Inbound port rules:
     
     - **Public inbound ports**: \[**Allow selected ports**\] を選択します。
     - **Select inbound ports**: リストで \[**RDP (3389)**\] を選択します。
   
   ![\[Basics\] タブのスクリーンショット。各フィールドは前述のように設定されている。](media/lab-virtual-machine-basics-tab.png "[Create a virtual machine] の [Basics] タブ")

5. \[**Next: Disks\] を選択して、次のステップに進みます。**

6. \[**Disks**\] タブで、\[OS disk type\] が \[**Premium SSD**\] に設定されていることを確認した後、\[**Next: Networking\] を選択します。**
   
   ![\[Create a virtual machine\] の \[Disks\] タブの \[OS disk type\] が \[Premium SSD\] に設定されている。](media/lab-virtual-machine-disks-tab.png "[Create a virtual machine] の [Disks] タブ")

7. \[**Networking**\] タブで、次の構成を設定します。
   
   - **Virtual network**: \[**hands-on-lab-SUFFIX-vnet**\] を選択します。
   - **Subnet**: \[**Management**\] サブネットを選択します。
   - **Public IP**: \[**(new) JumpBox-ip**\] が選択された状態のままにします。
   - **NIC network security group**: \[**Basic**\] を選択します。
   - **Public inbound ports**: \[**Allow selected ports**\] が選択された状態のままにします。
   - **Select inbound ports**: \[**RDP**\] が選択された状態のままにします。
   
   ![\[Create a virtual machine\] の \[Networking\] タブの該当フィールドに上記の設定が入力されている。](media/lab-virtual-machine-networking-tab.png "[Create a virtual machine] の [Networking] タブ")

8. \[**Review + create**\] を選択して、構成を検証します。残りのタブはスキップできます。その場合、既定値が使用されます。

9. \[**Review + create**\] タブで、"Validation passed" というメッセージが表示されていることを確認した後、\[**Create**\] を選択して仮想マシンをプロビジョニングします。
   
   !["Validation passed" というメッセージが表示された \[Review + create\] タブが表示されている。](media/lab-virtual-machine-review-create-tab.png "[Create a virtual machine] の [Review + create] タブ")

10. VM のプロビジョニングが完了するまで 15 分程度かかります。待っている間に次のタスクに進んでも構いません。

## タスク 5: SQL Server 2008 R2 仮想マシンを作成する

このタスクでは、SQL Server 2008 R2 の "オンプレミス" インスタンスをホストする別の仮想マシン (VM) を Azure でプロビジョニングします。この VM は、SQL Server 2008 R2 SP3 Standard on Windows Server 2008 R2 イメージを使用します。

> **注**: SQL Server 2008 R2 は Windows Server 2016 ではサポートされないため、旧バージョンの Windows Server を使用しています。

1. [Azure portal](https://portal.azure.com/) で、\[**Show portal menu**\] アイコンを選択した後、メニューから \[**+Create a resource**\] を選択します。
   
   ![ポータル メニュー表示アイコンが強調表示され、ポータル メニューが表示されている。ポータル メニューの \[Create a resource\] が強調表示されている。](media/create-a-resource.png "[Create a resource]")

2. Marketplace 検索ボックスに「SQL Server 2008 R2 SP3 on Windows Server 2008 R2」と入力します。

3. \[**SQL Server 2008 R2 SP3 on Windows Server 2008 R2**\] ブレードで、ソフトウェア プランとして \[**SQL Server R2 SP3 Standard on Windows Server 2008 R2**\] を選択した後、\[**Create**\] を選択します。
   
   ![ソフトウェア プランとして Standard エディションが選択された \[SQL Server 2008 R2 SP3 on Windows Server 2008 R2\] ブレードが表示されている。\[Create\] ボタンが強調表示されている。](media/create-resource-sql-server-2008-r2.png "SQL Server 2008 R2 リソースの作成")

4. \[Create a virtual machine\] の \[**Basics**\] タブで、以下の構成を設定します。
   
   - Project details:
     
     - **Subscription**: このハンズオン ラボで使用するサブスクリプションを選択します。
     - **Resource group**: 既存のリソース グループのリストから \[**hands-on-lab-SUFFIX**\] リソース グループを選択します。
   
   - Instance Details:
     
     - **Virtual machine name**: 「`SqlServer2008`」と入力します。
     - **Region**: このハンズオン ラボのリソースで使用するリージョンを選択します。
     - **Availability options**: \[**No infrastructure redundancy required**\] を選択します。
     - **Image**: \[**SQL Server 2008 R2 SP3 Standard on Windows Server 2008 R2**\] が選択された状態のままにします。
     - **Azure Spot instance**: \[**No**\] を選択します。
     - **Size**: 既定のサイズ "**Standard\_D4s\_v3**" をそのまま使用します。
   
   - Administrator account:
     
     - **Username**: 「`sqlmiuser`」と入力します。
     - **Password**: 「`Password.1234567890`」と入力します。
   
   - Inbound port rules:
     
     - **Public inbound ports**: \[**Allow selected ports**\] を選択します。
     - **Select inbound ports**: リストで \[**RDP (3389)**\] を選択します。
   
   ![\[Basics\] タブのスクリーンショット。各フィールドは前述のように設定されている。](media/sql-server-2008-r2-vm-basics-tab.png "[Create a virtual machine] の [Basics] タブ")

5. \[**Next: Disks**\] を選択し、\[OS disk type\] が \[**Premium SSD**\] に設定されていることを確認した後、\[**Next: Networking**\] を選択します。
   
   ![\[Create a virtual machine\] の \[Disks\] タブの \[OS disk type\] が \[Premium SSD\] に設定されている。](media/lab-virtual-machine-disks-tab.png "[Create a virtual machine] の [Disks] タブ")

6. \[**Networking**\] タブで、次の構成を設定します。
   
   - **Virtual network**: \[**hands-on-lab-vnet**\] を選択します。
   - **Subnet**: \[**Management**\] サブネットを選択します。
   - **Public IP**: \[**(new) SqlServer2008-ip**\] が選択された状態のままにします。
   - **NIC network security group**: \[**Basic**\] を選択します。
   - **Public inbound ports**: \[**Allow selected ports**\] が選択された状態のままにします。
   - **Select inbound ports**: \[**RDP**\] が選択された状態のままにします。
   
   ![\[Create a virtual machine\] の \[Networking\] タブの該当フィールドに上記の設定が入力されている。](media/sql-virtual-machine-networking-tab.png "[Create a virtual machine] の [Networking] タブ")

7. 上部のメニューから \[**SQL Server settings**\] タブを選択します。
   
   ![\[Create a virtual machine\] のタブ リストの \[SQL Server settings\] タブが強調表示されている。](media/sql-server-2008-r2-vm-sql-settings-tab.png "[Create a virtual machine] の [SQL Server settings] タブ")
   
   > **注**: \[Management\] および \[Advanced\] タブはスキップできます。その場合、既定値が使用されます。

8. \[**SQL Server settings**\] タブで、以下を設定します。
   
   - Security \& Networking:
     
     - **SQL connectivity**: \[**Public (Internet)**\] を選択します。
     - **Port**: 「`1433`」に設定します。
   
   - SQL Authentication:
     
     - **SQL Authentication**: \[**Enable**\] を選択します。
     - **Login name**: 「`sqlmiuser`」と入力します。
     - **Password**: 「`Password.1234567890`」と入力します。
     
     ![\[SQL Server settings\] タブに上記の値が入力されている。](media/sql-server-2008-r2-vm-sql-settings.png "[SQL Server settings]")

9. \[**Review + create**\] を選択して、構成を検証します。

10. \[**Review + create**\] タブで、"Validation passed" というメッセージが表示されていることを確認した後、\[**Create**\] を選択して仮想マシンをプロビジョニングします。
    
    !["Validation passed" というメッセージが表示された \[Review + create\] タブが表示されている。](media/sql-virtual-machine-review-create-tab.png "[Create a virtual machine] の [Review + create] タブ")

11. SQL VM のプロビジョニングが完了するまで 10 分程度かかります。待っている間に次のタスクに進んでも構いません。

## タスク 6: Azure Database Migration Service を作成する

このタスクでは、Azure Database Migration Service (DMS) のインスタンスをプロビジョニングします。

> **重要**: このサービスを利用するには、Azure のサブスクリプション内で `Microsoft.DataMigration` リソース プロバイダーを登録しておく必要があります。これを完了する手順については、ハンズオン ラボ前のガイドを参照してください。

1. [Azure portal](https://portal.azure.com/) で、\[**Show portal menu**\] アイコンを選択した後、メニューから \[**+Create a resource**\] を選択します。
   
   ![ポータル メニュー表示アイコンが強調表示され、ポータル メニューが表示されている。ポータル メニューの \[Create a resource\] が強調表示されている。](media/create-a-resource.png "[Create a resource]")

2. Marketplace 検索ボックスに「database migration」と入力し、結果から \[**Azure Database Migration Service**\] を選択して、\[Azure Database Migration Service\] ブレードで \[**Create**\] を選択します。
   
   ![Marketplace 検索ボックスに「database migration」と入力されている。結果で \[Azure Database Migration Service\] が選択されている。](media/create-resource-azure-database-migration-service.png "Azure Database Migration Service の作成")

3. \[Create Migration Service\] の \[**Basics**\] タブで、以下を入力します。
   
   - Project details:
     
     - **Subscription**: このハンズオン ラボで使用するサブスクリプションを選択します。
     - **Resource group**: 既存のリソース グループのリストから \[**hands-on-lab-SUFFIX**\] リソース グループを選択します。
   
   - Instance details:
     
     - **Migration service Name**: 「`wwi-dms`」と入力します。
     - **Location**: このハンズオン ラボのリソースで使用する場所を選択します。
     - **Service mode**: \[**Azure**\] を選択します。
     - **Pricing tier**: \[**Configure tier**\] を選択し、\[**Premium**\] を選択して、\[**Apply**\] を選択します。
     
     ![該当フィールドに上記の値が入力された \[Create Migration Service\] の \[Basics\] タブが表示されている。](media/create-migration-service-basics-tab.png "[Create Migration Service]")

4. \[**Next: Networking\] を選択します。**

5. \[**Networking**\] タブの既存の仮想ネットワークのリストで、横にあるチェックボックスをオンにして、\[**hands-on-lab-SUFFIX-vnet/default**\] 仮想ネットワークを選択します。これにより、LabVM および SqlServer2008 仮想マシンと同じ VNet に DMS インスタンスが配置されます。
   
   ![\[Networking\] タブの既存の仮想ネットワークのリストで、\[hands-on-lab-vnet/default\] 仮想ネットワークが選択され、チェックボックスがオンになっている。](media/create-migration-service-networking-tab.png "[Create Migration Service]")

6. \[**Review + create**\] を選択します。

7. \[**Create**\] を選択します。

8. Azure Data Migration サービスの展開が完了するまで 15 分かかる場合があります。待っている間に次のタスクに進んでも構いません。

## タスク 7: Web App をプロビジョニングする

このタスクでは、WWI Tailspin Toys の Web アプリケーションのホスティングに使用される App Service (Web App) をプロビジョニングします。

1. [Azure portal](https://portal.azure.com/) で、\[**Show portal menu**\] アイコンを選択した後、メニューから \[**+Create a resource**\] を選択します。
   
   ![ポータル メニュー表示アイコンが強調表示され、ポータル メニューが表示されている。ポータル メニューの \[Create a resource\] が強調表示されている。](media/create-a-resource.png "[Create a resource]")

2. [Azure portal](https://portal.azure.com/) で、\[**+Create a resource**\] を選択し、Marketplace 検索ボックスに「web app」と入力して、結果から \[**Web App**\] を選択します。
   
   ![Azure のナビゲーション ペインで \[+Create a resource\] が選択され、Marketplace 検索ボックスに「web app」と入力されている。結果で \[Web App\] が選択されている。](media/create-resource-web-app.png "Web App の作成")

3. \[Web App\] ブレードで、\[**Create**\] を選択します。
   
   ![\[Web App\] ブレードの \[Create\] ボタンが強調表示されている。](media/create-web-app.png "Web App の作成")

4. \[Web App\] ブレードの \[**Basics**\] タブで、以下の構成を設定します。
   
   - Project Details:
     
     - **Subscription**: このハンズオン ラボで使用するサブスクリプションを選択します。
     - **Resource Group**: 既存のリソース グループのリストから \[**hands-on-lab-SUFFIX**\] リソース グループを選択します。
   
   - Instance Details:
     
     - **Name**: 「`tailspintoysSUFFIX`」と入力し、グローバルに一意の名前を作成します。
     - **Publish**: \[**Code**\] を選択します。
     - **Runtime stack**: \[**.NET Core 3.1 (LTS)**\] を選択します。
     - **Operating System**: \[**Windows**\] を選択します。
     - **Region**: このハンズオン ラボのリソースで使用するリージョンを選択します。
   
   - App Service Plan:
     
     - **Plan**: 新しい App Service プランを作成するために、既定値をそのまま使用します。
     - **Sku and size**: 既定値 "**Standard S1**" をそのまま使用します。
     
     ![\[Web App\] の \[Basics\] タブの該当フィールドに上記の値が入力されている。](media/create-web-app-basics-tab.png "[Web App] の [Basics] タブ")

5. \[**Review + create**\] を選択します。

6. \[**Review + create**\] タブで、\[**Create**\] を選択します。

7. Web App の作成が完了するまで数分程度かかります。待っている間に次のタスクに進んでも構いません。

## タスク 8: Azure Blob Storage アカウントを作成する

このタスクでは、Azure Storage アカウントを作成します。

1. [Azure portal](https://portal.azure.com/) で、\[**Show portal menu**\] アイコンを選択した後、メニューから \[**+Create a resource**\] を選択します。
   
   ![ポータル メニュー表示アイコンが強調表示され、ポータル メニューが表示されている。ポータル メニューの \[Create a resource\] が強調表示されている。](media/create-a-resource.png "[Create a resource]")

2. Marketplace 検索ボックスに「storage account」と入力し、結果から \[**Storage account - blob, file, table, queue**\] を選択した後、\[Storage account\] ブレードで \[**Create**\] を選択します。
   
   ![Marketplace 検索ボックスに「storage account」と入力されている。結果で \[Storage account\] が選択されている。](media/create-resource-storage-account.png "[Create storage account]")

3. \[Create storage account\] ブレードで、以下を入力します。
   
   - Project details:
     
     - **Subscription**: このハンズオン ラボで使用するサブスクリプションを選択します。
     - **Resource Group**: 既存のリソース グループのリストから \[**hands-on-lab-SUFFIX**\] リソース グループを選択します。
   
   - Instance details:
     
     - **Storage account name**: 「`sqlmistoreSUFFIX`」と入力します。
     - **Location**: このハンズオン ラボのリソースで使用する場所を選択します。
     - **Performance**: \[**Standard**\] を選択します。
     - **Account kind**: \[**StorageV2 (general purpose v2)**\] を選択します。
     - **Replication**: \[**Locally-redundant storage (LRS)**\] を選択します。
     - **Access tier**: \[**Hot**\] を選択します。
     
     ![\[Create storage account\] ブレードの該当フィールドに上記の値が入力されている。](media/storage-create-account-basics-tab.png "ストレージ アカウントの作成")

4. \[**Review + create**\] を選択します。

5. \[**Review + create**\] ブレードで、"Validation passed" というメッセージが表示されていることを確認した後、\[**Create**\] を選択します。
   
   ![\[Review + create\] ブレードの上部に "Validation passed" というメッセージが表示されている。](media/storage-create-account-review.png "[Create Storage account]")

## タスク 9: JumpBox に接続する

このタスクでは、JumpBox 仮想マシン (VM) への RDP 接続を作成し、\[Internet Explorer Enhanced Security Configuration\] を無効にします。

> **注**: SQL MI のプロビジョニングが完了するまで待たずに、残りのタスクを完了しても構いません。

1. JumpBox VM のプロビジョニングが完了したら、[Azure portal](https://portal.azure.com) に移動して、\[Azure services\] リストから \[**Resource groups**\] を選択します。
   
   ![\[Azure services\] リストの \[Resource groups\] が強調表示されている。](media/azure-services-resource-groups.png "[Azure services]")

2. リストから hands-on-lab-SUFFIX リソース グループを選択します。
   
   ![Azure のナビゲーション ペインで \[Resource groups\] が選択され、"hands-on-lab-SUFFIX" リソース グループが強調表示されている。](./media/resource-groups.png "リソース グループのリスト")

3. リソース グループのリソースのリストで、\[JumpBox\] VM を選択します。
   
   ![hands-on-lab-SUFFIX リソース グループのリスースのリストが表示され、\[JumpBox\] が強調表示されている。](./media/resource-group-resources-jumpbox.png "リソース グループのリストの [JumpBox]")

4. \[JumpBox VM\] ブレードで、上部のメニューから \[**Connect**\]、\[**RDP**\] の順に選択します。
   
   ![\[JumpBox VM\] ブレードが表示され、上部のメニューの \[Connect\] および \[RDP\] ボタンが強調表示されている。](./media/connect-vm-rdp.png "JumpBox VM への接続")

5. \[Connect with RDP\] ブレードで \[**Download RDP File**\] を選択した後、ダウンロードされた RDP ファイルを開きます。
   
   ![\[Connect with RDP\] ブレードが表示され、\[Download RDP File\] ボタンが強調表示されている。](./media/connect-to-virtual-machine-with-rdp.png "[Connect with RDP]")

6. \[Remote Desktop Connection\] ダイアログで \[**Connect**\] を選択します。
   
   ![\[Remote Desktop Connection\] ダイアログ ボックスの \[Connect\] ボタンが強調表示されている。](./media/remote-desktop-connection.png "[Remote Desktop Connection] ダイアログ")

7. プロンプトが表示されたら、以下の資格情報を入力し、\[**OK**\] を選択します。
   
   - **User name**: `sqlmiuser`
   - **Password**: `Password.1234567890`
   
   ![\[Enter your credentials\]　ダイアログに上記の資格情報が入力されている。\[Enter your credentials\]](media/rdc-credentials.png "[Enter your credentials]")

8. \[The identity of the remote computer cannot be verified\] というプロンプトが表示された場合、\[**Yes** \] を選択して接続しま す。
   
   ![\[Remote Desktop Connection\] ダイアログ ボックスで、このリモート コンピューターの ID が確認できないことを示し、このまま続行するか尋ねる警告メッセージが表示されている。下部の \[Yes\] ボタンが赤線で囲まれている。](./media/remote-desktop-connection-identity-verification-jumpbox.png "[Remote Desktop Connection] ダイアログ")

9. ログインしたら、**サーバー マネージャー**を起動します。これは自動的に起動するはずですが、起動しない場合は \[スタート\] メニューを使用してアクセスできます。

10. \[**ローカル サーバー**\] を選択した後、\[**IE セキュリティ強化の構成**\] の横の \[**オン**\] を選択します。
    
    ![サーバー マネージャーのスクリーンショット。左側のペインでは、\[**ローカル サーバー**\] が選択されている。右側の \[プロパティ (LabVM 用)\] ペインでは、\[オン\] に設定された \[IE セキュリティ強化の構成\] が強調表示されている。](./media/windows-server-manager-ie-enhanced-security-configuration.png "サーバー マネージャー")

11. \[Internet Explorer セキュリティ強化の構成\] ダイアログで、\[管理者\] と \[ユーザー\] の両方で \[**オフ**\] を選択した後、\[**OK**\] を選択します。
    
    ![\[管理者\] が \[オフ\] に設定された \[Internet Explorer セキュリティ強化の構成\] ダイアログ ボックスのスクリーンショット。](./media/internet-explorer-enhanced-security-configuration-dialog.png "[Internet Explorer セキュリティ強化の構成] ダイアログ ボックス")

12. サーバー マネージャーを閉じます。ただし、次のタスクのために、JumpBox への接続は開いた状態のままにしておきます。

## タスク 10: JumpBox に必要なソフトウェアをインストールする

このタスクでは、ラボ スターター ソリューションをダウンロードして、JumpBox に SQL Server Management Studio (SSMS) をインストールします。

1. まず、["MCW Migrating SQL databases to Azure" GitHub リポジトリ"](https://github.com/microsoft/MCW-Migrating-SQL-databases-to-Azure/archive/master.zip)からラボ スターター ソリューションをダウンロードします。

2. ダウンロードが許可されていないことを知らせるメッセージが表示された場合、Internet Explorer のブラウザー ウィンドウの右上にあるツール アイコンを選択した後、コンテキスト メニューから \[**インターネット オプション**\] を選択します。
   
   ![Internet Explorer のツールバーのツール アイコン、およびコンテキスト メニューの \[インターネット オプション\] が強調表示されている。](media/ie-tools-context-menu.png "Internet Explorer")

3. \[**インターネット オプション**\] ダイアログで、\[このゾーンのセキュリティのレベル\] ボックス内の \[**レベルのカスタマイズ**\] を選択します。
   
   ![\[インターネット オプション\] ダイアログの \[レベルのカスタマイズ\] ボタンが強調表示されている。](media/ie-internet-options.png "[Internet Options]")

4. \[セキュリティの設定 - インターネット ゾーン\] ダイアログで、\[**ダウンロード**\] の設定を見つけ、\[**有効**\] を選択した後、\[**OK**\] を選択します。
   
   ![\[セキュリティの設定\] ダイアログの \[ダウンロード\] プロパティが強調表示され、\[有効\] が選択されている。](media/ie-security-settings-internet-zone.png "[セキュリティの設定]")

5. \[インターネット オプション\] ダイアログで \[**OK**\] を選択した後、ダウンロードを再試行します。

6. プロンプトが表示されたら、ファイルの保存を選択した後、\[フォルダーを開く\] を選択します。
   
   ![Internet Explorer のダウンロード バーが表示され、\[フォルダーを開く\] が強調表示されている。](media/ie-download-open-folder.png "Internet Explorer")

7. ダウンロードが完了したら、ZIP ファイルを `C:\hands-on-lab` に展開します。
   
   ![\[圧縮 Zip ファイルの展開\] ダイアログの展開先フィールドに「C:\\hands-on-lab」と入力されている。](media/extract-compressed-zip.png "圧縮 ZIP の展開")
   
   > **重要**: 必ず、上記のパス、または同様の短いパスを使用してください。そうしないと、長いファイル パスが原因で、一部のファイルを開く際にエラーが発生するおそれがあります。

8. 次に、JumpBox に SQL Server Management Studio (SSMS) をインストールします。JumpBox で Web ブラウザーを開き、<https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms> にナビゲートした後、\[**Download SQL Server Management Studio (SSMS).x**\] リンクを選択して、最新バージョンの SSMS をダウンロードします。
   
   ![上記のページの \[Download SQL Server Management Studio (SSMS)\] リンクが強調表示されている。](media/download-ssms.png "SSMS のダウンロード")
   
   > **注**: バージョンは頻繁に変更されるため、バージョン番号がスクリーンショットと一致しない場合は、最新バージョンをダウンロードおよびインストールしてください。

9. ダウンロードしたインストーラーを実行します。

10. ウェルカム画面で、\[**インストール**\] を選択して、インストールを開始します。
    
    ![SSMS のインストールのウェルカム画面の \[インストール\] ボタンが強調表示されている。](media/ssms-install.png "SSMS のインストール")

11. インストールが完了したら、\[**閉じる**\] を選択します。
    
    ![SSMS の \[設定が完了しました\] ダイアログの \[閉じる\] が強調表示されている。](media/ssms-install-close.png "[設定が完了しました]")

## タスク 11: SqlServer2008 VM に接続する

このタスクでは、SqlServer2008 VM への RDP 接続を開き、Internet Explorer Enhanced Security Configuration を無効にし、受信 TCP トラフィックに対してポート 1433 を開くファイアウォール規則を追加します。また、Microsoft Data Migration Assistant (DMA) もインストールします。

1. JumpBox で行ったように、Azure portal で SqlServer2008 VM のブレードに移動し、左側のメニューから \[**Overview**\] を選択した後、上部のメニューで \[**Connect**\]、\[**RDP**\] の順に選択します。
   
   ![\[SqlServer2008 Virtual machine\] ブレードが表示され、上部のメニューの \[Connect\] ボタンが強調表示されている。](./media/connect-vm-rdp.png "SqlServer2008 VM への接続")

2. \[Connect with RDP\] ブレードで \[**Download RDP File**\] を選択した後、ダウンロードされた RDP ファイルを開きます。

3. \[Remote Desktop Connection\] ダイアログで \[**Connect**\] を選択します。
   
   ![\[Remote Desktop Connection\] ダイアログ ボックスの \[Connect\] ボタンが強調表示されている。](./media/remote-desktop-connection-sql-2008.png "[Remote Desktop Connection] ダイアログ")

4. プロンプトが表示されたら、以下の資格情報を入力し、\[**OK**\] を選択します。
   
   - **User name**: `sqlmiuser`
   - **Password**: `Password.1234567890`
   
   ![\[Enter your crede ntials\]　ダイアログに上記の資格情報が入力されている。](media/rdc-credentials-sql-2008.png "[Enter your credentials]")

5. \[The identity of the remote computer cannot be verified\] というプロンプトが表示された場合、\[**Yes** \] を選択して接続しま す。
   
   ![\[Remote Desktop Connection\] ダイアログ ボックスで、このリモート コンピューターの ID が確認できないことを示し、このまま続行するか尋ねる警告メッセージが表示されている。下部の \[Yes\] ボタンが赤線で囲まれている。](./media/remote-desktop-connection-identity-verification-sqlserver2008.png "[Remote Desktop Connection] ダイアログ")

6. ログインしたら、**サーバー マネージャー**を起動します。これは自動的に起動するはずですが、起動しない場合は \[スタート\] メニューを使用してアクセスできます。

7. **サーバー マネージャー**のビューで、\[セキュリティ情報\] 下の \[**IE ESC の構成**\] を選択します。
   
   ![サーバー マネージャーのスクリーンショット。左側のペインでは、\[ローカル サーバー\] が選択されている。右側の \[プロパティ (LabVM 用)\] ペインでは、\[オン\] に設定された \[IE セキュリティ強化の構成\] が強調表示されている。](./media/windows-server-2008-manager-ie-enhanced-security-configuration.png "サーバー マネージャー")

8. \[Internet Explorer セキュリティ強化の構成\] ダイアログで、\[管理者\] と \[ユーザー\] の両方で \[**オフ**\] を選択した後、\[**OK**\] を選択します。
   
   ![\[管理者\] が \[オフ\] に設定された \[Internet Explorer セキュリティ強化の構成\] ダイアログ ボックスのスクリーンショット。](./media/2008-internet-explorer-enhanced-security-configuration-dialog.png "[Internet Explorer セキュリティ強化の構成] ダイアログ ボックス")

9. サーバー マネージャーに戻り、\[**構成**\] および \[**セキュリティ強化機能搭載 Windows ファイアウォール**\] を展開した後、\[**受信の規則**\] を選択します。
   
   ![サーバー マネージャーで、\[構成\] および \[**セキュリティ強化機能搭載 Windows ファイアウォール**\] が展開され、\[受信の規則\] が選択および強調表示されている。](media/windows-firewall-inbound-rules.png "Windows ファイアウォール")

10. \[**受信の規則**\] を右クリックした後、コンテキスト メニューから \[**新しいルール**\] を選択します。
    
    ![\[受信の規則\] が選択され、コンテキスト メニューの \[新しいルール\] が強調表示されている。](media/windows-firewall-with-advanced-security-new-inbound-rule.png "[新しいルール]")

11. \[新規の受信の規則ウィザード\] の \[ルールの種類\] で、\[**ポート**\] を選択した後、\[**次へ**\] を選択します。
    
    ![\[新規の受信の規則ウィザード\] の左側で \[ルールの種類\] が選択および強調表示され、右側で \[ポート\] が選択および強調表示されている。](media/windows-2008-new-inbound-rule-wizard-rule-type.png "[ポート] の選択")

12. \[プロトコルおよびポート\] ダイアログで、既定の \[**TCP**\] を使用し、\[特定のローカル ポート\] テキスト ボックスに「**1433**」と入力した後、\[**次へ**\] を選択します。
    
    ![\[新規の受信の規則ウィザード\] の左側で \[プロトコルおよびポート\] が選択され、右側で \[特定のローカル ポート\] ボックスが選択され、「1433」と入力されている。](media/windows-2008-new-inbound-rule-wizard-protocol-and-ports.png "特定のローカル ポートの選択")

13. \[アクション\] ダイアログで、\[**接続を許可する**\] を選択した後、\[**次へ**\] を選択します。
    
    ![\[新規の受信の規則ウィザード\] の左側で \[アクション\] が選択され、右側で \[接続を許可する\] が選択されている。](media/windows-2008-new-inbound-rule-wizard-action.png "アクションの指定")

14. \[プロファイル\] のステップで、\[**ドメイン**\]、\[**プライベート**\]、および \[**パブリック**\] のチェックボックスをオンにした後、\[**次へ**\] を選択します。
    
    ![\[新規の受信の規則ウィザード\] の左側で \[プロファイル\] が選択され、右側で \[ドメイン\]、\[プライベート\]、および \[パブリック\] が選択されていまる。](media/windows-2008-new-inbound-rule-wizard-profile.png "[ドメイン]、[プライベート]、および [パブリック\] の選択")

15. \[名前\] 画面で、\[名前\] ボックスに「**SqlServer**」と入力して、\[**完了**\] を選択します。
    
    ![\[新規の受信の規則ウィザード\] の左側で \[名前\] が選択され、右側で \[名前\] ボックスに「SqlServer」と入力されている。](media/windows-2008-new-inbound-rule-wizard-name.png "名前の指定")

16. サーバー マネージャーを閉じます。

17. 次に、SqlServer2008 VM の Web ブラウザーで <https://www.microsoft.com/en-us/download/details.aspx?id=53595> にナビゲートした後、\[**ダウンロード**\] ボタンを選択して、Microsoft Data Migration Assistant v5.x をインストールします。
    
    ![Data Migration Assistant のダウンロード ページの \[ダウンロード\] ボタンが強調表示されている。](media/dma-download.png "Data Migration Assistant のダウンロード")
    
    > **注**: バージョンは頻繁に変更されるため、バージョン番号がスクリーンショットと一致しない場合は、最新バージョンをダウンロードおよびインストールしてください。

18. ダウンロードしたインストーラーを実行します。

19. 途中、ライセンス条項とプライバシー ポリシーに同意しながら、各画面で \[**Next**\] を選択します。

20. \[Privacy Policy\] 画面で、\[**Install**\] を選択して、インストールを開始します。

21. 最後の画面で、\[**Finish**\] を選択して、インストーラーを閉じます。
    
    ![\[Microsoft Data Migration Assistant Setup\] ダイアログで、\[Finish\] ボタンが選択されている。](./media/data-migration-assistant-setup-finish.png "Microsoft Data Migration Assistant の実行")

## タスク 12: SqlServer2008 VM 上で WWI TailspinToys データベースを構成する

このタスクでは、SQL Server 2008 R2 インスタンスで Wide World Importers の `TailspinToys` データベースを復元および設定します。

1. SqlServer2008 VM で、[WWI TailspinToys データベースのバックアップ](https://raw.githubusercontent.com/microsoft/Migrating-SQL-databases-to-Azure/master/Hands-on%20lab/lab-files/Database/TailspinToys.bak)をダウンロードし、それを VM の `C:\` に保存します。

2. 次に、Windows の \[スタート\] メニューの検索バーに「sql server」と入力し、検索結果から \[**Microsoft SQL Server Management Studio 17**\] を選択して、**Microsoft SQL Server Management Studio 17** (SSMS) を開きます。
   
   ![Windows の \[スタート\] メニューの検索ボックスに「sql server」と入力され、検索結果の \[Microsoft SQL Server Management Studio 17\] が強調表示されている。](media/start-menu-ssms-17.png "Windows の [スタート] メニューの検索")

3. SSMS の \[**サーバーに接続**\] ダイアログで、\[サーバー名\] ボックスに「**SQLSERVER2008**」と入力し、\[**Windows 認証**\] が選択されていることを確認した後、\[**接続**\] を選択します。
   
   ![\[サーバー名\] に「SQLSERVER2008」と入力され、\[Windows 認証\] が選択された、SQL Server の \[**サーバーに接続**\] ダイアログが表示されている。](media/sql-server-connect-to-server.png "[bbサーバーに接続bb]")

4. 接続したら、Object Explorer で \[**SQLSERVER2008**\] 下の \[**データベース**\] を右クリックした後、コンテキスト メニューから \[**データベースの復元**\] を選択します。
   
   ![SSMS の Object Explorer の \[データベース\] のコンテキスト メニューが表示され、\[データベースの復元\] が強調表示されている。](media/ssms-databases-restore.png "SSMS の Object Explorer")

5. ダウンロードした `TailspinToys.bak` ファイルを使用して、WWI `TailspinToys` データベースを復元します。\[データベースの復元\] ダイアログの \[**全般**\] ページで、\[ソース\] 下の \[**デバイス**\] を選択した後、\[デバイス\] ボックスの右側にある参照 (`...`) ボタンを選択します。
   
   ![\[データベースの復元\] ダイアログの \[ソース\] 下の \[デバイス\] が選択および強調表示され、参照ボタンが強調表示されている。](media/ssms-restore-database-source.png "データベースの復元元")

6. 表示される \[**バックアップ デバイスの選択**\] ダイアログで、\[**追加**\] を選択します。
   
   ![\[バックアップ デバイスの選択\] ダイアログの \[追加\] ボタンが強調表示されている。](media/ssms-restore-database-select-devices.png "[バックアップ デバイスの選択]")

7. \[**バックアップ ファイルの検索**\] ダイアログで、ダウンロードした `TailspinToys.bak` ファイルの保存場所を参照して、そのファイルを選択した後、\[**OK**\] を選択します。
   
   ![\[バックアップ ファイルの検索\] ダイアログで、TailspinToys.bak ファイルが選択および強調表示されている。](media/ssms-restore-database-locate-backup-file.png "バックアップ ファイルの検索]")

8. \[**バックアップ デバイスの選択**\] ダイアログで、\[**OK**\] を選択します。選択すると、\[データベースの復元\] ダイアログに戻ります。WWI `TailspinToys` データベースの復元に必要な情報がダイアログに入力されています。
   
   ![必要な情報がすべて入力された \[データベースの復元\] ダイアログが表示され、WWI TailSpinToys データベースが復元先として指定されている。](media/ssms-restore-database.png "[データベースの復元]")

9. \[**OK**\] を選択して、復元を開始します。

10. データベースの復元が完了したら、ダイアログで \[**OK**\] を選択します。
    
    ![TailspinToys データベースが正常に復元されたことを知らせるメッセージを含むダイアログが表示されている。](media/ssms-restore-database-success.png "正常に復元")

11. 次に、SSMS でスクリプトを実行して Service Broker を有効にし、`WorkshopUser` アカウントを作成して、データベースの復旧モデルを FULL に変更します。スクリプトを作成するために、SSMS のツールバーで \[**新しいクエリ**\] を選択して、SSMS で新しいクエリ ウィンドウを開きます。
    
    ![SSMS のツールバーの \[**新しいクエリ**\] ボタンが強調表示されている。](media/ssms-new-query.png "SSMS のツールバー")

12. 以下の SQL スクリプトを新しいクエリ ウィンドウにコピーして貼り付けます。
    
    ```sql
    USE master;
    GO
    
    -- Create a login and user named WorkshopUser
    CREATE LOGIN WorkshopUser WITH PASSWORD = N'Password.1234567890';
    GO
    
    EXEC sp_addsrvrolemember
        @loginame = N'WorkshopUser',
        @rolename = N'sysadmin';
    GO
    
    -- Assign the user to the TailspinToys database
    USE TailspinToys;
    GO
    
    IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = N'WorkshopUser')
    BEGIN
        CREATE USER [WorkshopUser] FOR LOGIN [WorkshopUser]
        EXEC sp_addrolemember N'db_datareader', N'WorkshopUser'
    END;
    GO
    ```

13. スクリプトを実行するために、SSMS のツールバーから \[**実行**\] を選択します。
    
    ![SSMS のツールバーの \[実行\] ボタンが強調表示されている。](media/ssms-execute.png "SSMS のツールバー")