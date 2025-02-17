---
title: すべてのキー コンテナー オブジェクトで論理的な削除を有効にする - Azure Key Vault | Microsoft Docs
description: このドキュメントは、すべてのキー コンテナーに論理的な削除を導入すると共に、アプリケーションおよび管理機能の変更を行って競合エラーを防ぐためにご利用ください。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: b96f2ca4f925846bd252e5cfd35088d832f5c216
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572869"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>すべてのキー コンテナーでの論理的な削除の有効化

> [!WARNING]
> 破壊的変更: 論理的な削除をオプトアウトする機能は、間もなく非推奨になります。 Azure Key Vault のユーザーと管理者は、キー コンテナーの論理的な削除を直ちに有効にする必要があります。
>
> Azure Key Vault Managed HSM の場合、論理的な削除は既定で有効になっており、無効にすることはできません。

論理的な削除の保護なしでキー コンテナーからシークレットを削除すると、シークレットは完全に削除されます。 現在、論理的な削除は、ユーザーがキー コンテナーの作成時にオプトアウトできます。 ただし、ユーザーの偶発的または悪意のある削除からシークレットを保護するために、近い将来、論理的な削除がすべてのキー コンテナーで有効になる予定です。 ユーザーは論理的な削除をオプトアウトしたり無効にしたりすることができなくなります。

:::image type="content" source="../media/softdeletediagram.png" alt-text="キー コンテナーが削除されるしくみを論理的な削除の保護を使用した場合と使用しなかった場合で比較した図。":::

論理的な削除機能の詳細については、「[Azure Key Vault の論理的な削除の概要](soft-delete-overview.md)」を参照してください。

## <a name="can-my-application-work-with-soft-delete-enabled"></a>論理的な削除を有効にした場合のアプリケーションの機能

> [!Important] 
> キー コンテナーの論理的な削除を有効にする前に、以下の情報を慎重に確認してください。

キー コンテナーの名前はグローバルに一意です。 キー コンテナーに格納されているシークレットの名前も一意です。 論理的に削除された状態であるキー コンテナーやキー コンテナー オブジェクトの名前は再利用できません。 

たとえば、アプリケーションのプログラムによって "Vault A" という名前のキー コンテナーが作成され、その後 "Vault A" が削除された場合、そのキー コンテナーは論理的に削除された状態に移行します。 このアプリケーションは、論理的に削除された状態のこのキー コンテナーが消去されるまでは、"Vault A" という名前の別のキー コンテナーを再作成できません。 

また、アプリケーションによって "Vault A" に `test key` という名前のキーが作成され、その後、そのキーが削除された場合、論理的に削除された状態の `test key` オブジェクトが消去されるまでは、"Vault A" に `test key` という名前の新しいキーを作成できません。 

キー コンテナー オブジェクトを削除し、論理的に削除された状態から先に消去せずに同じ名前で再作成しようとすると、競合エラーが発生する可能性があります。 これらのエラーが原因で、アプリケーションまたはオートメーションで障害が発生する可能性があります。 次に示す、必要なアプリケーションと管理の変更を行う前に、開発チームに相談してください。 

### <a name="application-changes"></a>アプリケーションの変更

お使いのアプリケーションで、論理的な削除が有効になっておらず、削除されたシークレットまたはキー コンテナーの名前を即時に再利用できることが想定されている場合は、アプリケーション ロジックに次の変更を行う必要があります。

1. 元のキー コンテナーまたはシークレットを削除します。
1. 論理的に削除された状態のキー コンテナーまたはシークレットを消去します。
1. 消去が完了するまで待ちます。 すぐに再作成すると、競合が発生する可能性があります。
1. 同じ名前でキー コンテナーを再作成します。
1. 作成操作で名前の競合エラーが引き続き発生する場合は、もう一度キー コンテナーの再作成を試してください。 最悪のシナリオでは、Azure DNS レコードが更新されるまでに最大で 10 分かかることがあります。

### <a name="administration-changes"></a>管理の変更

シークレットを完全に削除するためのアクセス権が必要なセキュリティ プリンシパルには、それらのシークレットとキー コンテナーを消去するための追加のアクセス ポリシー アクセス許可を付与する必要があります。

論理的な削除の無効化を必須とする Azure ポリシーがキー コンテナーに適用されている場合は無効にします。 この問題は、お使いの環境に適用されている Azure ポリシーを制御する管理者にエスカレーションする必要がある場合があります。 このポリシーが無効になっていない場合、適用されているポリシーのスコープ内では新しいキー コンテナーを作成できなくなる可能性があります。

所属する組織が法的なコンプライアンス要件の対象となり、削除されたキー コンテナーとシークレットを長期間回復可能な状態で維持することを許容できない場合は、組織の基準を満たすために、論理的な削除の保持期間を調整する必要があります。 この保持期間は、7 日間から 90 日間続くよう構成できます。

## <a name="procedures"></a>手順

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>キー コンテナーを監査して、論理的な削除が有効になっているかどうか確認する

1. Azure portal にサインインします。
1. **Azure Policy** を検索します。
1. **[定義]** を選択します。
1. **[カテゴリ]** で、フィルターの **[キー コンテナー]** を選択します。
1. **[キー コンテナーでは論理的な削除を有効にする必要がある]** ポリシーを選択します。
1. **[割り当て]** を選択します。
1. スコープをサブスクリプションに設定します。
1. このポリシーの効果が **[監査]** に設定されていることを確認します。
1. **[確認および作成]** を選択します。 環境のフル スキャンが完了するまで最大 24 時間かかる場合があります。
1. **[Azure Policy]** ペインで **[コンプライアンス]** を選択します。
1. 適用したポリシーを選択します。

これで、論理的な削除が有効になっているキー コンテナー (準拠しているリソース) と論理的な削除が有効になっていないキー コンテナー (準拠していないリソース) をフィルター処理して確認できるようになります。

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>既存のキー コンテナーの論理的な削除を有効にする

1. Azure portal にサインインします。
1. キー コンテナーを検索します。
1. **[設定]** で **[プロパティ]** を選択します。
1. **[論理的な削除]** で、 **[このコンテナーとそのオブジェクトの回復を有効にする]** オプションを選択します。
1. 論理的な削除の保持期間を設定します。
1. **[保存]** を選択します。

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>消去アクセス ポリシー アクセス許可をセキュリティ プリンシパルに付与する

1. Azure portal にサインインします。
1. キー コンテナーを検索します。
1. **[設定]** で **[アクセス ポリシー]** を選択します。
1. アクセス権を付与するサービス プリンシパルを選択します。
1. **[キーのアクセス許可]** 、 **[シークレットのアクセス許可]** 、 **[証明書のアクセス許可]** の各ドロップダウン メニューで **[Privileged Operations]\(特権操作\)** が見えるまで移動します。 **[消去]** アクセス許可を選択します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="does-this-change-affect-me"></a>この変更は私に影響しますか?

既に論理的な削除が有効になっている場合、またはキー コンテナー オブジェクトを削除して同じ名前で再作成しない場合は、キー コンテナーの動作の変更に気付かない可能性があります。

頻繁にキー コンテナー オブジェクトを削除して同じ名前付け規則を使用して再作成するアプリケーションを使用している場合は、想定されている動作を維持するためにアプリケーション ロジックに変更を加える必要があります。 この記事の「[アプリケーションの変更](#application-changes)」セクションを参照してください。

### <a name="how-do-i-benefit-from-this-change"></a>この変更にはどのような利点がありますか?

論理的な削除の保護により、組織は、偶発的または悪意のある削除に対する保護をいっそう強化することができます。 キー コンテナー管理者は、復旧アクセス許可と消去アクセス許可の両方へのアクセス権を制限できます。

ユーザーが誤ってキー コンテナーまたはシークレットを削除しても、そのシークレットまたはキー コンテナーが完全に削除されるリスクを生じさせずに、シークレット自体を復旧するためのアクセス許可をそのユーザーに付与できます。 このセルフサービス プロセスにより、環境のダウンタイムが最小限に抑えられ、シークレットの可用性が保証されます。

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>アクションを実行する必要があるかどうかはどのように判断しますか?

この記事の「[キー コンテナーを監査して、論理的な削除が有効になっているかどうか確認する](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled)」セクションの手順に従ってください。 この変更は、論理的な削除が有効になっていないキー コンテナーにも影響します。

### <a name="what-action-do-i-need-to-take"></a>どのようなアクションを実行する必要がありますか?

アプリケーション ロジックに対する変更が不要であることを確認したら、すべてのキー コンテナーの論理的な削除を有効にしてください。

### <a name="when-do-i-need-to-take-action"></a>いつまでに措置を講じる必要がありますか?

アプリケーションが影響を受けないようにするには、できるだけ早くキー コンテナーの論理的な削除を有効にしてください。

## <a name="next-steps"></a>次の手順

- この変更についてご質問がある場合は、[akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) までお問い合わせください。
- [論理的な削除の概要](soft-delete-overview.md)に関するページを参照してください。
