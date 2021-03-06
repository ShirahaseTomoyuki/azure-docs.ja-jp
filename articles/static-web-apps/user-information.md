---
title: Azure Static Web Apps でのユーザー情報へのアクセス
description: 承認プロバイダーから返されたユーザー データを読み取る方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 273ab5c20cf41b7305d72f1bfef5deeeb046c49a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595141"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Azure Static Web Apps プレビューでのユーザー情報へのアクセス

Azure Static Web Apps では、[直接アクセス エンドポイント](#direct-access-endpoint)および [API 関数](#api-functions)を介して、認証関連のユーザー情報が提供されます。

多くのユーザー インターフェイスは、ユーザー認証データに大きく依存しています。 直接アクセス エンドポイントは、カスタム関数を実装する必要なしにユーザー情報が公開されるユーティリティ API です。 便利さ以外に、直接アクセス エンドポイントは、サーバーレス アーキテクチャに付きもののコールド スタートの遅延による影響を受けません。

## <a name="client-principal-data"></a>クライアント プリンシパル データ

クライアント プリンシパル データ オブジェクトでは、ユーザーを特定できる情報がアプリに公開されます。 クライアント プリンシパル オブジェクトには、次のプロパティが含まれます。

| プロパティ  | 説明 |
|-----------|---------|
| `identityProvider` | [ID プロバイダー](authentication-authorization.md)の名前。 |
| `userId` | Azure Static Web Apps 固有のユーザーの一意識別子。 <ul><li>値は、アプリごとに一意です。 たとえば、同じユーザーでも、異なる Static Web Apps リソースでは異なる `userId` 値が返されます。<li>値は、ユーザーの有効期間にわたって保持されます。 ユーザーを削除し、同じユーザーをアプリに再び追加すると、新しい `userId` が生成されます。</ul>|
| `userDetails` | ユーザーのユーザー名またはメール アドレス。 [ユーザーのメール アドレス](authentication-authorization.md)を返すプロバイダーもあれば、[ユーザー ハンドル](authentication-authorization.md)を送信するプロバイダーもあります。 |
| `userRoles`     | [ユーザーに割り当てられたロール](authentication-authorization.md)の配列。 |

クライアント プリンシパル オブジェクトの例を次に示します。

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>直接アクセス エンドポイント

`/.auth/me` ルートに `GET` 要求を送信し、クライアント プリンシパル データへの直接アクセスを受け取ることができます。 ビューの状態が承認データに依存している場合は、この方法を使用すると最適なパフォーマンスが得られます。

ログインしているユーザーの場合、応答にはクライアント プリンシパルの JSON オブジェクトが含まれます。 認証されていないユーザーからの要求では、`null` が返されます。

[fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> API を使用すると、次の構文を使用してクライアント プリンシパル データにアクセスできます。

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>API 関数

クライアント プリンシパル データは、`x-ms-client-principal` 要求ヘッダーで API 関数に渡されます。 クライアント プリンシパル データは、シリアル化された JSON オブジェクトを含む [Base64](https://www.wikipedia.org/wiki/Base64) でエンコードされた文字列として送信されます。

次の関数の例では、ユーザー情報を読み取って返す方法を示します。

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

上の関数の名前が `user` であるとすると、[fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> ブラウザー API を使用し、次の構文を使用して API の応答にアクセスできます。

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUser());
```

<sup>1</sup> [fetch](https://caniuse.com/#feat=fetch) API と [await](https://caniuse.com/#feat=mdn-javascript_operators_await) 演算子は、Internet Explorer ではサポートされていません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリケーションの設定の構成](application-settings.md)
