---
title: Azure リソース ログ
description: Azure Monitor の Log Analytics ワークスペースに Azure リソース ログをストリーミングする方法を説明します。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 492aae69895d62c784d15cd77405d0c52ec13e3e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84946963"
---
# <a name="azure-resource-logs"></a>Azure リソース ログ
Azure リソース ログは、Azure リソース内で実行された操作に関する分析情報を提供する[プラットフォーム ログ](platform-logs-overview.md)です。 リソース ログの内容は、Azure サービスとリソースの種類によって異なります。 既定では、リソース ログは収集されません。 各 Azure リソースのリソース ログを、[Azure Monitor ログ](data-platform-logs.md)で使用するために Log Analytics ワークスペースに送信したり、Azure の外部に転送するために Azure Event Hubs に送信したり、アーカイブ用に Azure Storage に送信したりするために、各 Azure リソースの診断設定を作成する必要があります。

診断設定を作成する方法の詳細については、「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](diagnostic-settings.md)」を参照してください。Azure Policy を使用して、作成した各 Azure リソースの診断設定を自動的に作成する方法については、「[Azure Policy を使用して大規模に Azure Monitor をデプロイする](deploy-scale.md)」を参照してください。

## <a name="send-to-log-analytics-workspace"></a>Log Analytics ワークスペースに送信する
 次のような [Azure Monitor ログ](data-platform-logs.md)の機能を有効にするには、リソース ログを Log Analytics ワークスペースに送信します。

- リソース ログ データを、Azure Monitor によって収集されたその他の監視データと関連付けます。
- 複数の Azure リソース、サブスクリプション、およびテナントのログ エントリを 1 つの場所に統合して、まとめて分析できるようにします。
- ログ クエリを使用して複雑な分析を実行し、ログ データから詳細な分析情報を得ます。
- 複雑なアラート ロジックを持つログ アラートを使用します。

リソース ログを Log Analytics ワークスペースに送信するには、[診断設定を作成します](diagnostic-settings.md)。 このデータは、「[Azure Monitor Logs の構造](../log-query/logs-structure.md)」の説明に従って、テーブルに格納されます。 リソース ログで使用されるテーブルは、リソースで使用されているコレクションの種類によって異なります。

- Azure 診断 - 書き込まれたすべてのデータが _AzureDiagnostics_ テーブルに含められます。
- リソース固有 - データは、リソースのカテゴリごとに個別のテーブルに書き込まれます。

### <a name="azure-diagnostics-mode"></a>Azure 診断モード 
このモードでは、任意の診断設定に基づくすべてのデータが、_AzureDiagnostics_ テーブルに収集されます。 これは、ほとんどの Azure サービスで現在使用されている従来の方法です。 複数の種類のリソースから同じテーブルにデータが送信されるため、そのスキーマは、収集されるすべての種類のデータ型のスキーマのスーパーセットになります。

次の例を検討してください。ここでは次のデータ型について、同じワークスペースで診断設定が収集されています。

- サービス 1 の監査ログ (スキーマは列 A、B、C から構成される)  
- サービス 1 のエラー ログ (スキーマは列 D、E、F から構成される)  
- サービス 2 の監査ログ (スキーマは列 G、H、I から構成される)  

AzureDiagnostics テーブルは次のようになります。  

| ResourceProvider    | カテゴリ     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>リソース固有
このモードでは、診断設定で選択されたカテゴリごとに、個別のテーブルが選択されたワークスペース内に作成されます。 この方法をお勧めします。その理由は、ログ クエリのデータの操作がはるかに簡単になり、スキーマとその構造の探しやすさが向上し、取り込み遅延とクエリ時間の両方でパフォーマンスが向上し、さらに特定のテーブルで RBAC 権限を付与できることにあります。 すべての Azure サービスは、最終的にはリソース固有モードに移行します。 

上記の例では、3 つのテーブルが作成されることになります。
 
- テーブル *Service1AuditLogs* は次のようになります。

    | リソース プロバイダー | カテゴリ | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- テーブル *Service1ErrorLogs* は次のようになります。  

    | リソース プロバイダー | カテゴリ | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | w1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- テーブル *Service2AuditLogs* は次のようになります。  

    | リソース プロバイダー | カテゴリ | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | l1|
    | Service2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>コレクション モードを選択する
ほとんどの Azure リソースでは、ユーザーに選択肢を与えることなく、**Azure 診断**または**リソース固有モード**でワークスペースにデータが書き込まれます。 使用されるモードの詳細については、[各サービスのドキュメント](diagnostic-logs-schema.md)を参照してください。 最終的には、すべての Azure サービスで、リソース固有モードが使用されるようになります。 この移行の一環として、一部のリソースでは診断設定でモードを選択できるようになります。 新しい診断設定ではリソース固有モードを指定してください。これにより、データの管理が容易になり、後日行う移行が複雑になるのを回避できる場合があります。
  
   ![診断設定のモード セレクター](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> 現在、Azure portal で診断設定を構成する場合は、**Azure 診断**または**リソース固有**モードを選択できるだけとなっています。 CLI、PowerShell、または Rest API を使用して設定を構成する場合、既定では **Azure 診断**となります。

既存の診断設定をリソース固有モードに変更できます。 この場合、既に収集されたデータは、ワークスペースに対するご利用の保有期間設定に従って削除されるまで、_AzureDiagnostics_ テーブル内に残ります。 新しいデータは、専用のテーブルに収集されます。 両方のテーブルのデータに対してクエリを実行するには、[union](https://docs.microsoft.com/azure/kusto/query/unionoperator) 演算子を使用します。

リソース固有モードをサポートしている Azure サービスに関するお知らせは、[Azure の更新情報](https://azure.microsoft.com/updates/)を引き続きご覧ください。

### <a name="column-limit-in-azurediagnostics"></a>AzureDiagnostics での列の制限
Azure Monitor ログ内のいずれのテーブルに対しても、500 のプロパティ制限があります。 この上限に到達すると、最初の 500 プロパティから外れるデータを含む行は、取り込み時に破棄されます。 *AzureDiagnostics* テーブルは、このテーブルへの書き込みを行うすべての Azure サービスのプロパティを含んでいるため、特にこの制限の影響を受けやすくなっています。

複数のサービスからリソースログを収集している場合、_AzureDiagnostics_ は、この制限を超えることがあり、データが失われる可能性があります。 すべての Azure サービスでリソース固有モードがサポートされるようになるまでは、複数のワークスペースに書き込むようにリソースを構成して、500 列の制限に達する可能性を低くする必要があります。

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory サービスの場合は、詳細なログセットであるため、多数の列の書き込みが行われ、_AzureDiagnostics_ が制限を超える可能性があることがわかっています。 リソース固有モードを有効にする前に構成した診断設定の場合、任意のアクティビティに対して、一意の名前が付けられたユーザー パラメーターごとに新しい列が作成されます。 アクティビティの入力と出力がその性質上詳細であるため、より多くの列が作成されます。
 
ソース固有モードを使用するように、ご利用のログをできるだけ早く移行する必要があります。 今すぐ実行できない場合は、暫定措置として Azure Data Factory ログをそれぞれに固有のワークスペースに分離して、これらのログがワークスペースに収集される他のログの種類に影響を与える可能性を最小限に抑えるようにします。


## <a name="send-to-azure-event-hubs"></a>Azure Event Hubs に送信する
たとえば、サードパーティの SIEM や他のログ分析ソリューションなど、Azure の外部にリソース ログを送信するには、リソース ログをイベント ハブに送信します。 Event Hubs からのリソース ログは、レコードが含まれる `records` 要素が各ペイロードにある JSON 形式で使用されます。 「[Azure リソース ログの共通およびサービス固有のスキーマ](resource-logs-schema.md)」で説明されているように、スキーマはリソースの種類によって異なります。

次に示すのは、リソース ログの場合の Event Hubs からのサンプル出力データです。

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Azure Storage への送信
リソース ログを Azure Storage に送信し、アーカイブ用に保持します。 診断設定の作成後、有効にしたいずれかのログ カテゴリのイベントが発生するとすぐ、ストレージ アカウントにストレージ コンテナーが作成されます。 コンテナー内の BLOB には、次の名前付け規則が使用されます。

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

たとえば、ネットワーク セキュリティ グループの BLOB には、次のような名前を付けることができます。

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

各 PT1H.json BLOB には、BLOB の URL で指定された時間内に発生したイベントの JSON BLOB が含まれます (例: h = 12)。 現在の時間内にイベントが発生すると、PT1H.json ファイルにイベントが追加されます。 リソース ログ イベントは 1 時間ごとに個々の BLOB に分類されるため、分の値 (m = 00) は常に 00 です。

PT1H.json ファイル内では、各イベントは、次の形式で保存されます。 これには一般的な最上位スキーマを使用しますが、「[リソース ログのスキーマ](diagnostic-logs-schema.md)」で説明されているように、各 Azure サービスに固有です。

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> プラットフォーム ログは、[JSON Lines](http://jsonlines.org/) を使用して BLOB ストレージに書き込まれ、各イベントは 1 行で示され、改行文字は新しいイベントがあることを示します。 この形式は、2018 年 11 月に実装されました。 この日付より前は、「[ストレージ アカウントにアーカイブされている Azure Monitor プラットフォーム ログの形式変更のための準備](resource-logs-blob-format.md)」で説明されているように、ログはレコードの JSON 配列として BLOB ストレージに書き込まれていました。


## <a name="next-steps"></a>次のステップ

* [リソース ログの詳細について読む](platform-logs-overview.md)
* [プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](diagnostic-settings.md)
