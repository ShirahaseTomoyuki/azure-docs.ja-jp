---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/13/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1d14ee849c89e6c3807636d0a728157abd9de97a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650662"
---
## <a name="1-download-the-file"></a>1.ファイルをダウンロードする

次のコマンドを実行します。 プロファイルの ZIP ファイルをダウンロードするには、結果の URL をブラウザーにコピーします。

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2.ZIP ファイルを解凍する

zip ファイルを解凍します。 このファイルには、次のフォルダーが含まれています。

* AzureVPN
* ジェネリック
* OpenVPN (ゲートウェイで OpenVPN および Azure AD の認証設定を有効にした場合)。 VPN Gateway については、[テナントの作成](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)に関する記事をご覧ください。 Virtual WAN については、[テナントの作成 - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md) に関する記事をご覧ください。

## <a name="3-retrieve-information"></a>3.情報を取得する

**AzureVPN** フォルダーで、***azurevpnconfig.xml*** ファイルに移動し、メモ帳で開きます。 次のタグの間のテキストを書き留めておきます。

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>プロファイルの詳細

接続を追加するときは、前の手順で収集した情報をプロファイルの詳細ページで使用します。 各フィールドは次の情報に対応しています。

   * **Audience:** トークンの対象となる受信者リソースを識別します。
   * **Issuer:** トークンを生成したセキュリティ トークン サービス (STS) と Azure AD テナントを識別します。
   * **Tenant:** トークンを発行したディレクトリ テナントの変更できない一意の識別子が含まれます。
   * **FQDN:** Azure VPN Gateway での完全修飾ドメイン名 (FQDN)。
   * **ServerSecret:** VPN Gateway の事前共有キー。

## <a name="folder-contents"></a>フォルダーの内容

* **generic フォルダー**には、パブリック サーバー証明書とVpnSettings.xml ファイルが含まれています。 VpnSettings.xml ファイルには、汎用クライアントの構成に必要な情報が含まれています。

* ダウンロードした ZIP ファイルには、**WindowsAmd64** および **WindowsX86** フォルダーも含まれている場合があります。 これらのフォルダーには、Windows クライアント用の SSTP および IKEv2 のインストーラーが含まれています。 それらをインストールするには、クライアントの管理者権限が必要です。
