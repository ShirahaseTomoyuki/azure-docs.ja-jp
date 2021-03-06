---
title: Azure Portal によるデバイスの管理 | Microsoft Docs
description: Azure Portal を使用してデバイスを管理する方法を説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 05/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf3f6455be992502182fb942f0e6db089051ab1a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253156"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Azure portal を使用してデバイス ID を管理する

Azure Active Directory (Azure AD) のデバイス ID 管理を使用して、ユーザーがセキュリティとコンプライアンスの基準と一致するデバイスからリソースにアクセスしていることを保証できます。

この記事の内容は次のとおりです。

- [Azure Active Directory でのデバイス ID 管理の概要](overview.md)を理解していることを前提とします
- Azure AD ポータルを使用してデバイス ID を管理する方法について説明します

![Azure portal の [すべてのデバイス] ビュー](./media/device-management-azure-portal/all-devices-azure-portal.png)

## <a name="manage-device-identities"></a>デバイス ID を管理する

Azure AD ポータルは、お客様のデバイス ID を一元的に管理する場所を提供します。 この場所にアクセスするには、[直接リンク](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)を使用するか、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]**  >  **[デバイス]** の順に移動します。

**[デバイス]** ページでは、次の操作が可能です。

- デバイス設定を構成する
- デバイスの検索
- デバイス ID 管理タスクを実行する
- デバイス関連の監査ログを確認する  
  
## <a name="configure-device-settings"></a>デバイス設定の構成

Azure AD ポータルを使ってデバイス ID を管理するには、デバイスが Azure AD に[登録されているか参加している](overview.md)必要があります。 管理者は、デバイスの設定を構成することによって、デバイスの登録および参加のプロセスを調整できます。

デバイスの設定ページでは、デバイス ID に関連する設定を構成できます。

![Azure AD に関連するデバイス設定](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **[ユーザーはデバイスを Azure AD に参加させることができます]** - この設定を使用すると、Azure AD 参加済みデバイスとしてデバイスを登録できるユーザーを選択できます。 既定値は **[すべて]** です。

> [!NOTE]
> **[ユーザーはデバイスを Azure AD に参加させることができます]** 設定は、Windows 10 上の Azure AD 参加にのみ適用されます。

- **[Azure AD 参加済みデバイスの追加のローカル管理者]** - デバイスに対するローカル管理者権限が付与されるユーザーを選択できます。 ここに追加されたユーザーは、Azure AD の "*デバイス管理者*" ロールに追加されます。 Azure AD のグローバル管理者とデバイスの所有者には、既定でローカル管理者権限が付与されます。 このオプションは、Azure AD Premium や Enterprise Mobility Suite (EMS) などの製品を通じて使用できる Premium Edition 機能です。
- **[ユーザーはデバイスを Azure AD に登録できます]** - Windows 10 (個人用)、iOS、Android、および macOS デバイスを Azure AD に登録できるようにするには、この設定を構成する必要があります。 **[なし]** を選択した場合、デバイスは Azure AD に登録できません。 Microsoft Intune または Mobile Device Management (MDM) for Office 365 への登録には、この登録が必要です。 これらのサービスのいずれかを構成した場合は、 **[すべて]** が選択され、 **[なし]** は選択できなくなります。
- **[デバイスを参加させるには Multi-factor Auth が必要]** - デバイスを Azure AD に参加させるときに、ユーザーが追加の認証要素の提供を求められるようにするかどうかを選ぶことができます。 既定値は **No** です。 デバイスの登録時に多要素認証を必要とすることをお勧めします。 このサービスの多要素認証を有効にする前に、デバイスを登録するユーザーに対して多要素認証が構成されていることを確認する必要があります。 他の Azure Multi-Factor Authentication サービスの詳細については、[Azure Multi-Factor Authentication の概要](../authentication/concept-mfa-whichversion.md)に関するページを参照してください。 

> [!NOTE]
> **[デバイスを参加させるには多要素認証が必要]** 設定は、Azure AD に参加しているか Azure AD に登録されているデバイスに適用されます。 この設定は、ハイブリッド Azure AD 参加済みデバイスには適用されません。

- **[デバイスの最大数]** - この設定では、Azure AD でユーザーが持つことができる、Azure AD に参加しているか Azure AD に登録されているデバイスの最大数を選択できます。 ユーザーがこのクォータに達した場合、1 つ以上の既存のデバイスを削除するまでデバイスを追加できなくなります。 既定値は **20** です。

> [!NOTE]
> **[デバイスの最大数]** 設定は、Azure AD に参加しているか Azure AD に登録されているデバイスに適用されます。 この設定は、ハイブリッド Azure AD 参加済みデバイスには適用されません。

- **[デバイス間での設定とアプリ データの同期が許可されるユーザー]** - 既定では、この設定は **[なし]** に設定されています。 特定のユーザーまたはグループ、あるいは [すべて] を選択すると、ユーザーの設定とアプリ データを Windows 10 デバイス間で同期させることができます。 Windows 10 の同期のしくみの詳細を学習してください。
このオプションは、Azure AD Premium や Enterprise Mobility Suite (EMS) などの製品を通じて使用できる Premium 機能です。

## <a name="locate-devices"></a>デバイスの検索

登録済みおよび参加済みのデバイスを検索するには、次の 2 つのオプションがあります。

- **[デバイス]** ページの **[管理]** セクションにある **[すべてのデバイス]**  
- **[ユーザー]** ページの **[管理]** セクションにある **[デバイス]**

どちらのオプションでも、次のようなビューが表示されます。

- 表示名またはデバイス ID をフィルターとして使用してデバイスを検索できる。
- 登録済みおよび参加済みデバイスの概要情報を確認できる
- 一般的なデバイス管理タスクを実行できる

>[!TIP]
>
>* [登録済み] 列の下の状態が "保留中" である "ハイブリッド Azure AD 参加済み" のデバイスが表示されている場合、そのデバイスが Azure AD 接続から同期されており、クライアントからの登録の完了を待機していることを示します。 [Hybrid Azure AD 参加の実装を計画する](hybrid-azuread-join-plan.md)方法について詳細を参照してください。 追加情報については、[デバイスについてよく寄せられる質問](faq.md)に関する記事を参照してください。
>
>* 一部の iOS デバイスの名前で使用されているアポストロフィは、アポストロフィに見える別の文字である可能性があります。 このため、このようなデバイスを検索するときは少し注意が必要です。正しい検索結果が表示されない場合は、検索文字列のアポストロフィが、正しいアポストロフィ文字であることを確認してください。

## <a name="device-identity-management-tasks"></a>デバイス ID 管理タスク

グローバル管理者またはクラウド デバイス管理者は、登録済みまたは参加済みデバイスを管理することができます。 Intune サービス管理者は次のことができます。

- デバイスを更新する - たとえば、デバイスを有効化/無効化するなどといった日常の操作
- デバイスを削除する - デバイスが廃止された場合や、Azure AD から削除する必要がある場合

このセクションでは、一般的なデバイス ID 管理タスクについて説明します。

### <a name="manage-an-intune-device"></a>Intune デバイスの管理

Intune 管理者の場合は、**Microsoft Intune** としてマークされているデバイスを管理することができます。 デバイスが Microsoft Intune に登録されていない場合、[管理] オプションは灰色で表示されます。

![Intune デバイスの管理](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Azure AD デバイスを有効/無効にする

デバイスを有効/無効にするには、次の 2 つのオプションがあります。

- **[すべてのデバイス]** ページのタスク メニュー [...]

   ![Intune デバイスの管理](./media/device-management-azure-portal/71.png)

- **[デバイス]** ページのツール バー

   ![Intune デバイスの管理](./media/device-management-azure-portal/32.png)

**備考:**

- デバイスを有効/無効にするには、Azure AD のグローバル管理者またはクラウド デバイス管理者である必要があります。 
- デバイスを無効にすると、デバイスは Azure AD で正常に認証されなくなり、それによってデバイスは、デバイス CA または WH4B 資格情報の使用によって保護されている Azure AD リソースにアクセスできなくなります。
- デバイスを無効にすると、デバイス上のプライマリ更新トークン (PRT) とすべての更新トークン (RT) の両方が取り消されます。

### <a name="delete-an-azure-ad-device"></a>Azure AD デバイスを削除する

デバイスを削除するには、次の 2 つのオプションがあります。

- **[すべてのデバイス]** ページのタスク メニュー [...]

   ![Intune デバイスの管理](./media/device-management-azure-portal/72.png)

- **[デバイス]** ページのツール バー

   ![デバイスの削除](./media/device-management-azure-portal/34.png)

**備考:**

- デバイスを削除するには、Azure AD のグローバル管理者または Intune 管理者である必要があります。
- デバイスを削除する場合、以下の点に注意してください。
   - デバイスは Azure AD リソースにアクセスできなくなります。
   - Windows デバイスの BitLocker キーなど、デバイスに関連付けられているすべての詳細が削除されます。  
   - 削除は回復不可能な操作であり、必須の場合以外は推奨されません。

デバイスが別の管理機関 (Microsoft Intune など) によって管理されている場合は、Azure AD でデバイスを削除する前に、デバイスがワイプ/使用中止されているかどうかを確認してください。 デバイスを削除する前に、[古いデバイスを管理する](manage-stale-devices.md)方法を確認してください。

### <a name="view-or-copy-device-id"></a>デバイス ID を表示またはコピーする

デバイス ID を使うと、デバイス上で、またはトラブルシューティング中に PowerShell を使用して、デバイス ID の詳細を確認することができます。 コピー オプションにアクセスするには、デバイスをクリックします。

![デバイス ID の表示](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker キーを表示またはコピーする

暗号化されたドライブのユーザーによる復旧を支援するため、BitLocker キーを表示またはコピーできます。 これらのキーを使用できるのは、暗号化され、キーが Azure AD に格納されている Windows デバイスだけです。 デバイスの詳細にアクセスする場合は、これらのキーをコピーすることができます。

![BitLocker キーの表示](./media/device-management-azure-portal/36.png)

BitLocker キーを表示またはコピーするには、デバイスの所有者であるか、次のロールが 1 つ以上割り当てられているユーザーである必要があります。

- クラウド デバイス管理者
- グローバル管理者
- ヘルプデスク管理者
- Intune サービス管理者
- セキュリティ管理者
- セキュリティ閲覧者

> [!NOTE]
> ハイブリッド Azure AD 参加済みの Windows 10 デバイスには、所有者がありません。 そのため、所有者でデバイスを検索していて、見つからなかった場合は、デバイス ID で検索してください。

### <a name="device-list-filtering-preview"></a>デバイス一覧のフィルター処理 (プレビュー)

以前は、デバイスの一覧はアクティビティと有効な状態でのみフィルター処理できていました。 このプレビューでは、デバイスの次の属性によってデバイス一覧をフィルター処理できます。

- 有効な状態
- 準拠状態
- 参加の種類 (Azure AD 参加済み、ハイブリッド Azure AD 参加済み、Azure AD に登録済み)
- アクティビティ タイムスタンプ
- OS
- デバイスの種類 (プリンター、セキュリティで保護された VM、共有デバイス、登録済みのデバイス)

**[すべてのデバイス]** ビューのプレビュー フィルター機能を有効にするには、次のようにします。

![プレビュー フィルター機能を有効にする](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]**  >  **[デバイス]** の順に移動します。
1. **[新しいデバイスのフィルターの改善された機能をお試しください。クリックすると、プレビューが有効になります。]** というバナーを選択します。

**[すべてのデバイス]** ビューに**フィルターを追加**できるようになりました。

## <a name="audit-logs"></a>監査ログ

デバイスのアクティビティは、アクティビティ ログを通じて利用できます。 これらのログには、デバイス登録サービスおよびユーザーによってトリガーされる、次のようなアクティビティが含まれます。

- デバイスの作成と、デバイスへの所有者/ユーザーの追加
- デバイス設定への変更
- デバイスの削除や更新などのデバイス操作

監査データへのエントリ ポイントは、 **[デバイス]** ページの **[アクティビティ]** セクションの **[監査ログ]** です。

監査ログには、次のものを示す既定のリスト ビューがあります。

- 発生の日付と時刻
- ターゲット
- アクティビティのイニシエーターまたはアクター ("だれが" を指す)
- アクティビティ ("どうした" を指す)

![監査ログ](./media/device-management-azure-portal/63.png)

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![監査ログ](./media/device-management-azure-portal/64.png)

報告されるデータを有用なものだけに絞り込むために、次のフィールドを使用して監査データをフィルター処理できます。

- カテゴリ
- アクティビティのリソースの種類
- アクティビティ
- 期間
- 移行先
- 開始者 (アクター)

フィルターだけでなく、特定のエントリを検索することができます。

![監査ログ](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>次のステップ

[方法: Azure AD で古いデバイスを管理する](manage-stale-devices.md)
