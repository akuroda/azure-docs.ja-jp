---
title: キャッシュからトークンを取得する (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET) を使用して、アクセス トークンを自動的に取得する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 94e4a4d5b80e246ce822e977deafe5c41c9ff4ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064778"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>MSAL.NET を使用してトークン キャッシュからトークンを取得する

Microsoft Authentication Library for .NET (MSAL.NET) を使用してアクセス トークンを取得すると、トークンはキャッシュされます。 トークンが必要な場合に、アプリケーションは最初に `AcquireTokenSilent` メソッドを呼び出して、キャッシュ内に利用可能なトークンがあるかどうかを確認します。 多くの場合、キャッシュ内のトークンに基づいて、より多くのスコープを備える別のトークンが取得されます。 また、期限切れが近いトークンを更新することもできます (トークン キャッシュには更新トークンも含まれるため)。

推奨されるパターンは、最初に `AcquireTokenSilent` メソッドを呼び出すことです。  `AcquireTokenSilent` が失敗した場合は、他の方法を使用してトークンを取得します。

次の例では、アプリケーションはまずトークン キャッシュからトークンを取得しようとします。  `MsalUiRequiredException` 例外がスローされると、アプリケーションは対話的にトークンを取得します。 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
