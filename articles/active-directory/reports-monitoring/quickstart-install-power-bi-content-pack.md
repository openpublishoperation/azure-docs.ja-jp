---
title: Azure AD Power BI コンテンツ パックをインストールする | Microsoft Docs
description: Azure AD Power BI コンテンツ パックをインストールする方法について説明します
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ba2784663a585aebb82ee149be3b54e73920f6dd
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816652"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>クイック スタート: Azure Active Directory Power BI コンテンツ パックをインストールする

|  |
|--|
|現在、Azure AD Power BI コンテンツ パックでは Azure AD Graph API を使用して Azure AD テナントからデータを取得します。 そのため、コンテンツ パック内のデータと[レポート用の Microsoft Graph API](concept-reporting-api.md) を使用して取得したデータに差異が発生する可能性があります。 |
|  |

Azure Active Directory 用 Power BI コンテンツ パックを使用すると、Active Directory で起きていることについての詳しい分析情報を得ることができます。 既製のコンテンツ パックをダウンロードして使用することにより、Power BI に備わっている豊富なビジュアル エクスペリエンスを使用して、ディレクトリ内のアクティビティをすべてレポートすることができます。 また、そうして得た情報は、独自のダッシュボードを作成することで、社内のだれとでも簡単に共有することができます。 

このクイック スタートでは、コンテンツ パックをインストールする方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

* Power BI アカウント。 これは、ご利用の O365 または Azure AD アカウントと同じアカウントです。 
* Azure AD テナント ID。 これは、Azure portal の[プロパティ ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)で確認できるディレクトリの ID (**[ディレクトリ ID]**) です。
* Azure AD Premium (P1/P2) ライセンス。 

## <a name="install-azure-ad-power-bi-content-pack"></a>Azure AD Power BI コンテンツ パックをインストールする 

1. ご利用の Power BI アカウントで [Power BI](https://app.powerbi.com/groups/me/getdata/services) にログインします。 これは、ご利用の O365 または Azure AD アカウントと同じアカウントです。

2. **[アプリ]** ページで **[Azure Active Directory Activity Logs]\(Azure Active Directory のアクティビティ ログ\)** を検索し、**[今すぐ入手する]** を選択します。 

   ![Azure Active Directory Power BI コンテンツ パック](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. ポップアップ ウィンドウで該当する Azure AD テナント ID を入力し、クエリの対象となる日数に「**7**」を入力して **[次へ]** を選択します。
    
   ![Azure Active Directory Power BI コンテンツ パック](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. Azure Active Directory アクティビティ ログのダッシュボードが作成されたら、それを選択します。

   ![Azure Active Directory Power BI コンテンツ パック](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>次の手順

* [Power BI コンテンツ パックを使用する](howto-power-bi-content-pack.md)。
* [コンテンツ パックのエラーをトラブルシューティングする](troubleshoot-content-pack.md)。
