---
title: Azure Monitor ログ クエリの関数 | Microsoft Docs
description: この記事では、Azure Monitor で関数を使用して、あるクエリを別のログ クエリから呼び出す方法について説明します。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: 5fb9e48a6d6a0b95b61478a7877e9b46dd8963e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "83649385"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Azure Monitor ログ クエリでの関数の使用

ログ クエリは、別のクエリで使用するために関数として保存できます。 これにより、複雑なクエリを複数のパーツに分割することで単純化でき、共通のコードを複数のクエリで再利用できます。

## <a name="create-a-function"></a>関数を作成する

Azure portal の Log Analytics で **[保存]** をクリックした後、次の表の情報を指定して関数を作成します。

| 設定 | 説明 |
|:---|:---|
| 名前           | **クエリ エクスプローラー**に表示されるクエリの名前。 |
| 名前を付けて保存        | 機能 |
| 関数のエイリアス | この関数を他のクエリの中で使用するための短い名前。 スペースを含めることはできず、一意である必要があります。 |
| カテゴリ       | 保存したクエリと関数を**クエリ エクスプローラー**内で整理するためのカテゴリ。 |




## <a name="use-a-function"></a>関数を使用する
関数は、そのエイリアスを別のクエリに含めることによって使用します。 それは他のテーブルと同じように使用できます。

## <a name="example"></a>例
次のサンプル クエリでは、最終日に報告された、更新されていないセキュリティ更新プログラムが返されます。 このクエリを、_security_updates_last_day_ というエイリアスを持つ関数として保存します。 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

別のクエリを作成し、_security_updates_last_day_ 関数を参照して、SQL に関連する更新が必要なセキュリティ更新プログラムを検索します。

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>次のステップ
Azure Monitor ログ クエリの記述に関するその他のレッスンを参照してください。

- [文字列操作](string-operations.md)
- [日付と時刻の操作](datetime-operations.md)
- [集計関数](aggregations.md)
- [高度な集計](advanced-aggregations.md)
- [JSON とデータ構造](json-data-structures.md)
- [結合](joins.md)
- [グラフ](charts.md)
