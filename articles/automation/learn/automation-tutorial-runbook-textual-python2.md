---
title: Azure Automation で Python Runbook を作成する
description: この記事では、シンプルな Python Runbook を作成、テスト、発行する方法を説明します。
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: e12327651165606e6a9b571d410f547a09a8ec8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87847926"
---
# <a name="tutorial-create-a-python-runbook"></a>チュートリアル:Python Runbook を作成する

このチュートリアルでは、Azure Automation で [Python Runbook](../automation-runbook-types.md#python-runbooks) を作成する手順について説明します。 Python Runbook は Python 2 でコンパイルします。 Azure portal のテキスト エディターを使用して、Runbook のコードを直接編集することができます。

> [!div class="checklist"]
> * 簡単な Python Runbook を作成する
> * Runbook をテストして発行する
> * Runbook ジョブの状態を実行、追跡する
> * Runbook パラメーターで Azure 仮想マシンを起動するように Runbook を更新する

> [!NOTE]
> Webhook を使用して Python Runbook を開始することはできません。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
- [Automation アカウント](../index.yml)。Runbook の保存と Azure リソースの認証に使用します。 このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
- Azure 仮想マシン。 マシンを停止して起動するので、運用 VM は使用しないでください。

## <a name="create-a-new-runbook"></a>新しい Runbook の作成

最初に、*Hello World* というテキストを出力する簡単な Runbook を作成します。

1. Azure ポータルで、Automation アカウントを開きます。

    Automation アカウント ページでは、そのアカウントのリソースを簡単に確認できます。 既に資産がいくつかあります。 これらの資産のほとんどは、新しい Automation アカウントに自動的に含まれるモジュールです。 [前提条件](#prerequisites)で説明されている資格情報資産も必要です。

2. **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。

3. **[Runbook の追加]** を選択して、新しい Runbook を作成します。

4. Runbook に **MyFirstRunbook-Python** という名前を付けます。

5. **[Runbook の種類]** として **[Python 2]** を選択します。

6. **[作成]** をクリックして Runbook を作成し、テキスト エディターを開きます。

## <a name="add-code-to-the-runbook"></a>Runbook にコードを追加する

ここでは、`Hello World` というテキストを表示する簡単なコマンドを追加します。

```python
print("Hello World!")
```

**[保存]** をクリックして Runbook を保存します。

## <a name="test-the-runbook"></a>Runbook をテストする

Runbook を発行して運用環境で使用できるようにする前に、Runbook をテストして正常に動作することを確認します。 Runbook をテストするときは、ドラフト バージョンを実行し、その出力を対話形式で表示します。

1. **[テスト ウィンドウ]** をクリックして、テスト ウィンドウを開きます。

2. **[開始]** をクリックしてテストを開始します。 有効なオプションはこれだけです。

3. [Runbook ジョブ](../automation-runbook-execution.md) が作成され、その状態が表示されます。
   最初のジョブの状態は "キューに登録済み" であり、クラウドの Runbook worker が使用可能になるのを待機中であることを示します。 それは worker からの要求により開始中になり、Runbook が実際に実行を開始すると実行中になります。

4. Runbook ジョブが完了すると、その出力が表示されます。 この場合は、`Hello World` と表示されます。

5. テスト ウィンドウを閉じてキャンバスに戻ります。

## <a name="publish-and-start-the-runbook"></a>Runbook を発行して開始する

前の手順で作成した Runbook は、まだドラフト モードです。 運用環境で実行できるようにするには、発行する必要があります。 Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。 この例では、Runbook を作成したばかりなので、発行済みバージョンはまだありません。

1. **[発行]** をクリックして Runbook を発行し、確認を要求されたら **[はい]** をクリックします。

2. [Runbook] ページで左へスクロールして Runbook を表示すると、 **[編集状態]** は **[発行済み]** になります。

3. 右にスクロールして戻り、**MyFirstRunbook-Python** のウィンドウを表示します。

   上部のオプションを使用すると、Runbook の開始、Runbook の表示、Runbook の将来の開始スケジュールの設定を行うことができます。

4. **[開始]** をクリックし、[Runbook の開始] ブレードが開いたら **[OK]** をクリックします。

5. 作成した Runbook ジョブのジョブ ペインが開きます。 このペインは閉じてもかまいませんが、ジョブの進行状況を確認できるように開いたままにしましょう。

6. ジョブの状態が **[ジョブの概要]** に表示され、Runbook をテストしたときに確認した状態と一致しています。

7. Runbook の状態が [完了]になったら、 **[出力]** をクリックします。 出力ペインが開き、`Hello World` が表示されます。

8. [出力] ウィンドウを閉じます。

9. **[すべてのログ]** をクリックして、Runbook ジョブのストリーム ウィンドウを開きます。 出力ストリームには `Hello World` のみが表示されます。 ただし、このペインには Runbook ジョブの他の (詳細やエラーなどの) ストリームも表示できます (Runbook でそれらのストリームへの書き込みが行われている場合)。

10. ストリーム ウィンドウとジョブ ウィンドウを閉じると、MyFirstRunbook-Python のウィンドウに戻ります。

11. **[ジョブ]** をクリックして、この Runbook のジョブ ページを開きます。 このページには、この Runbook によって作成されたすべてのジョブの一覧が表示されます。 ジョブを 1 回実行しただけであるため、一覧に表示されるジョブは 1 つだけです。

12. このジョブをクリックすると、Runbook を開始したときに表示されたのと同じジョブ ウィンドウが開きます。 このペインでは、特定の Runbook に対して作成されたジョブの詳細を、時間をさかのぼって見ることができます。

## <a name="add-authentication-to-manage-azure-resources"></a>Azure リソースを管理するための認証を追加する

Runbook をテストして発行しましたが、これまでのところ役に立つことは何もしていません。 Azure リソースを管理させることにします。
これを行うには、スクリプトで、ご利用の Automation アカウントの資格情報を使用して認証する必要があります。 [Azure Automation ユーティリティ パッケージ](https://github.com/azureautomation/azure_automation_utility)を使用して、Azure リソースの認証とやりとりをより容易にできます。

> [!NOTE]
> Automation アカウントは、実行証明書を作成するために、サービス プリンシパルの機能を使用して作成されている必要があります。
> ご利用の Automation アカウントがサービス プリンシパルを使用せずに作成されたものである場合は、[Python 用 Azure 管理ライブラリを使用した認証](/azure/python/python-sdk-azure-authenticate)に関するページで説明されているようにして認証することができます。

1. MyFirstRunbook-Python のウィンドウで **[編集]** をクリックして、テキスト エディターを開きます。

2. Azure への認証に、次のコードを追加します。

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>コードを追加して、Python Compute クライアントを作成し、VM を起動する

Azure VM で作業するには、[Azure Compute client for Python](/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient) (Python 用の Azure Compute クライアント) のインスタンスを作成します。

Compute クライアントを使用して、VM を起動します。 Runbook に次のコードを追加します。

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

VM を含むリソース グループの名前は `MyResourceGroup` で、起動する VM の名前は `TestVM` です。

Runbook をテストし、もう一度実行して、VM が起動するか確認します。

## <a name="use-input-parameters"></a>入力パラメーターを使用する

現在、Runbook では、ハード コードされた値がリソース グループと VM の名前に使用されています。 ここでは、入力パラメーターからこれらの値を取得するコードを追加しましょう。

`sys.argv` 変数を使用して、パラメーター値を取得します。 他の `import` ステートメントの直後に、Runbook に次のコードを追加します。

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

これにより `sys` モジュールがインポートされ、リソース グループと VM の名前を保持する 2 つの変数が作成されます。 引数リストの要素である `sys.argv[0]` はスクリプトの名前であり、ユーザーが入力したものではありませんので注意してください。

ここでは Runbook の最後の 2 行を変更し、ハード コードされた値を使用する代わりに入力パラメーターの値をご利用いただけます。

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Python Runbook を (テスト ペイン上で、または発行済みの Runbook として) 開始すると、[Runbook の開始] ページの **[パラメーター]** でパラメーターの値を入力できます。

最初のボックスに値を入力し始めると、2 番目のボックスが表示され、順次必要に応じた数のパラメーター値を入力できます。

その値は、前の手順で追加したコードと同様に、`sys.argv` 配列でスクリプトに使用できます。

最初のパラメーターの値にご利用のリソース グループ名を入力し、2 番目のパラメーターの値に起動する VM の名前を入力します。

![パラメーター値を入力する](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

**[OK]** をクリックして Runbook を開始します。 Runbook が実行され、指定した VM を起動します。

## <a name="error-handling-in-python"></a>Python でのエラー処理

次の規則を使用して、Python Runbook からさまざまなストリーム (WARNING、ERROR、および DEBUG ストリームを含む) を取得することもできます。

```python
print("Hello World output")
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

次の例は、`try...except` ブロックで使用されるこの規則を示しています。

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Azure Automation では `sys.stderr` がサポートされていません。

## <a name="next-steps"></a>次のステップ

- PowerShell Runbook の使用を開始するには、「[PowerShell Runbook を作成する](automation-tutorial-runbook-textual-powershell.md)」を参照してください。
- グラフィカル Runbook の使用を開始するには、「[グラフィカル Runbook を作成する](automation-tutorial-runbook-graphical.md)」を参照してください。
- PowerShell Workflow Runbook の使用を開始するには、「[PowerShell Workflow Runbook を作成する](automation-tutorial-runbook-textual.md)」を参照してください。
- Runbook の種類とそれらの利点や制限事項の詳細については、「[Azure Automation の Runbook の種類](../automation-runbook-types.md)」を参照してください。
- Python を使用した Azure の開発については、「[Python 開発者向けの Azure](/azure/python/)」をご覧ください。
- サンプル Python 2 Runbook を表示するには、[Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python) を参照してください。
