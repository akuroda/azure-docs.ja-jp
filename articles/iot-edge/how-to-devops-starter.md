---
title: Azure DevOps Starter を使用した CI/CD パイプライン - Azure IoT Edge | Microsoft Docs
description: Azure DevOps Starter を利用すると、Azure での作業を簡単に開始できるようになります。 いくつかの簡単な手順によって、Azure IoT Edge アプリを起動するのに役立ちます。
author: kgremban
ms.author: kgremban
ms.date: 08/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8a23041a714089c447ab35ee05aae0de0c9c33d2
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201151"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-starter"></a>Azure DevOps Starter を使用して IoT Edge 用の CI/CD パイプラインを作成する

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

DevOps Projects を使用して、IoT Edge アプリケーション用に継続的インテグレーション (CI) と継続的デリバリー (CD) を構成します。 DevOps Starter によって、Azure Pipelines のビルドおよびリリース パイプラインの初期構成が簡略化されます。

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free)を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

DevOps Starter によって、Azure DevOps に CI/CD パイプラインが作成されます。 新しい Azure DevOps 組織を作成するか、既存の組織を使用できます。 DevOps Starter では、選択した Azure サブスクリプションに Azure リソースも作成されます。

1. [Microsoft Azure portal](https://portal.azure.com) にサインインします。

1. 左のウィンドウで **[リソースの作成]** を選択し、**DevOps Starter** を検索します。  

1. **［作成］** を選択します

1. 既定では、DevOps Starter は GitHub を使用して設定されます。 このハウツーで説明している機能を利用するには、Azure DevOps を使用して設定するように、DevOps Starter を切り替えます。 **[設定の変更はこちら]** リンクに従います。

   ![[設定の変更はこちら] を選択して、GitHub から Azure DevOps に切り替えます](./media/how-to-devops-starter/create-with-github-change-settings.png)

1. 右側のペインで、 **[Azure DevOps]** タイルを選択し、 **[完了]** を選択します。

   ![DevOps Starter の設定で Azure DevOps を選択します](./media/how-to-devops-starter/select-azure-devops.png)

   これで、DevOps Starter が Azure DevOps を使って設定される状態になりました。

## <a name="create-a-new-application-pipeline"></a>新しいアプリケーション パイプラインを作成する

1. Azure IoT Edge モジュールは、[C#](tutorial-csharp-module.md)、[Node.js](tutorial-node-module.md)、[Python](tutorial-python-module.md)、[C](tutorial-c-module.md)、および [Java](tutorial-java-module.md) で記述できます。 **.NET**、**Node.js**、**Python**、**C**、または **Java** から、新しいアプリケーションを始めるために使用する言語を選択します。 **[次へ]** をクリックして続行します。

   ![言語を選択して新しいアプリケーションを作成する](./media/how-to-devops-starter/select-language.png)

2. アプリケーション フレームワークとして **[単純な IoT]** を選択し、 **[次へ]** を選択します。

   ![単純な IoT フレームワークを選択する](media/how-to-devops-starter/select-iot.png)

3. アプリケーションをデプロイする Azure サービスとして **[IoT Edge]** を選択し、 **[次へ]** を選択します。

   ![IoT Edge サービスを選択する](media/how-to-devops-starter/select-iot-edge.png)

4. 新しい無料の Azure DevOps 組織を作成するか、既存の組織を選択します。

   1. プロジェクトの名前を指定します。

   2. Azure DevOps 組織を選択します。 既存の組織がない場合は、 **[追加設定]** を選択し、新しい組織を作成します。

   3. Azure サブスクリプションを選択します。

   4. プロジェクト名によって生成された IoT Hub 名を使用するか、または独自に指定します。

   5. 既定の場所をそのまま使用するか、近くの名前を選択します。

   6. **[追加設定]** を選択して、DevOps Starter が自動的に作成する Azure リソースを構成します。

   7. **[完了]** を選択して、プロジェクトの作成を完了します。

   ![プロジェクトの名前を指定して作成する](media/how-to-devops-starter/create-project.png)

数分後、DevOps Starter ダッシュボードが Azure portal に表示されます。 プロジェクト名を選択して、進行状況を表示します。 ページを更新する必要がある場合があります。 サンプルの IoT Edge アプリケーションが Azure DevOps 組織内のリポジトリに設定され、ビルドが実行され、アプリケーションが IoT Edge デバイスにデプロイされます。 このダッシュボードでは、コード リポジトリ、CI/CD パイプライン、および Azure のアプリケーションが可視化されます。

   ![Azure portal でプロジェクトを表示する](./media/how-to-devops-starter/portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>コードの変更をコミットし、CI/CD を実行する

DevOps Starter によって、Azure Repos にプロジェクトの Git リポジトリが作成されました。 このセクションでは、リポジトリを表示し、アプリケーションにコード変更を加えます。

1. プロジェクト用に作成されたリポジトリに移動するには、プロジェクト ダッシュボードのメニューで、 **[リポジトリ]** を選択します。 このリンクによって、ブラウザーのタブが開かれ、新しいプロジェクトの Azure DevOps リポジトリが表示されます。

   ![Azure Repos 内に生成されたリポジトリを表示する](./media/how-to-devops-starter/view-repositories.png)

   > [!NOTE]
   > 次の手順では、Web ブラウザーを使用して、コードを変更します。 代わりにローカルにリポジトリを複製する場合は、ウィンドウの右上の **[複製]** を選択します。 指定された URL を使用し、Visual Studio Code またはお好みの開発ツールで、Git リポジトリを複製します。

2. リポジトリには、作成プロセスで選択したアプリケーションの言語に基づいて、**FilterModule** というモジュールに既にコードが含まれています。 **modules/FilterModule/module.json** ファイルを開きます。

   ![Azure Repos 内の module.json ファイルを開く](./media/how-to-devops-starter/open-module-json.png)

3. このファイルの **version** パラメーターでは、[Azure DevOps ビルド変数](/azure/devops/pipelines/build/variables#build-variables)が使用されていることに注意してください。 この構成により、新しいビルドを実行するたびに、新しいバージョンのモジュールが作成されるようになります。

## <a name="examine-the-cicd-pipeline"></a>CI/CD パイプラインを確認する

前のセクションで、Azure DevOps Starter によって、お使いの IoT Edge アプリケーション用の完全な CI/CD パイプラインが自動的に構成されました。 ここで、パイプラインを調査し、必要に応じてカスタマイズします。 Azure DevOps のビルドおよびリリース パイプラインについて理解するには、次の手順を使用します。

1. DevOps プロジェクトのビルド パイプラインを表示するには、プロジェクト ダッシュボードのメニューで、 **[ビルド パイプライン]** を選択します。 このリンクによって、ブラウザーのタブが開かれ、新しいプロジェクトの Azure DevOps ビルド パイプラインが表示されます。

   ![Azure Pipelines のビルド パイプラインを表示する](./media/how-to-devops-starter/view-build-pipelines.png)

2. 自動的に生成されたビルド パイプラインを開き、右上にある **[編集]** を選択します。

    ![ビルド パイプラインの編集](media/how-to-devops-starter/click-edit-button.png)

3. 開いたパネルで、ビルド パイプラインの実行時に行われるタスクを確認できます。 ビルド パイプラインでは、Git リポジトリからのソースのフェッチ、IoT Edge モジュール イメージの構築、IoT Edge モジュールのコンテナー レジストリへのプッシュ、デプロイに使用された出力の発行など、さまざまなタスクが実行されます。 Azure DevOps の Azure IoT Edge タスクに関する詳細を確認するには、「[継続的インテグレーションのための Azure Pipelines の構成](how-to-continuous-integration-continuous-deployment-classic.md#create-a-build-pipeline-for-continuous-integration)」を参照してください。

4. ビルド パイプラインの上部にある **[パイプライン]** ヘッダーを選択し、パイプラインの詳細を表示します。 ビルド パイプラインの名前をわかりやすい名前に変更します。

   ![パイプラインの詳細を編集する](./media/how-to-devops-starter/edit-build-pipeline.png)

5. **[保存してキューに登録]** を選択し、 **[保存]** を選択します。 コメントは省略できます。

6. [ビルド パイプライン] メニューから **[トリガー]** を選択します。 DevOps Starter では、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。  必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

7. **[保持]** を選択します。 リンクをたどると、保持ポリシーが配置されているプロジェクト設定にリダイレクトされます。 シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

8. **[履歴]** を選択します。 履歴パネルには、ビルドに対する最近の変更の監査証跡が含まれます。 ビルド パイプラインに対するすべての変更が Azure Pipelines によって追跡されるため、各バージョンを比較できます。

9. ビルド パイプラインの調査を完了したら、対応するリリース パイプラインに移動します。 **[パイプライン]** の下の **[リリース]** を選択し、 **[編集]** を選択して、パイプラインの詳細を表示します。

    ![リリース パイプラインの表示](media/how-to-devops-starter/release-pipeline.png)

10. **[成果物]** で、 **[ドロップ]** を選択します。 この成果物によって監視されるソースは、前の手順で調べたビルド パイプラインの出力です。

11. **[ドロップ]** アイコンの横にある稲妻アイコンのように見える **[継続的配置トリガー]** を選択します。 このリリース パイプラインでは、新しいビルド成果物が使用可能になるたびにデプロイが実行されるトリガーが有効にされています。 必要に応じて、手動でのデプロイが必須となるように、トリガーを無効にすることができます。  

12. リリース パイプラインのメニューで、 **[タスク]** を選択し、ドロップダウン リストから **[開発]** ステージを選択します。 DevOps Projects によって、IoT hub を作成し、そのハブに IoT Edge デバイスを作成して、ビルド パイプラインからサンプル モジュールをデプロイし、IoT Edge デバイスとして実行する仮想マシンをプロビジョニングするリリース ステージが自動的に作成されています。 CD 用の Azure IoT Edge タスクに関する詳細を確認するには、「[継続的配置のための Azure Pipelines の構成](how-to-continuous-integration-continuous-deployment-classic.md#create-a-release-pipeline-for-continuous-deployment)」を参照してください。

    ![継続的配置タスクの表示](media/how-to-devops-starter/choose-release.png)

13. 右側の **[リリースの表示]** を選択します。 このビューには、リリースの履歴が表示されます。

14. リリースの名前を選択して、その詳細情報を表示します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した Azure App Service とその他の関連リソースが必要なくなったら、削除してかまいません。 DevOps Starter ダッシュボードで **削除** 機能を使用します。

## <a name="next-steps"></a>次のステップ

* 「[Azure IoT Edge に対する継続的インテグレーションと継続的配置](how-to-continuous-integration-continuous-deployment.md)」で Azure DevOps 上の Azure IoT Edge のタスクについて確認します。
* 「[1 台のデバイスまたは一群のデバイスを対象とした IoT Edge デプロイについて](module-deployment-monitoring.md)」で IoT Edge デプロイについて理解します。
* 「[Deploy and monitor IoT Edge modules at scale (IoT Edge モジュールを大規模にデプロイして監視する)](how-to-deploy-at-scale.md)」で、デプロイを作成、更新、または削除するための手順を学習してください。
