---
title: "Problembehandlung: Fehlende Daten in den heruntergeladenen Azure Active Directory-Aktivitätsprotokollen | Microsoft-Dokumentation"
description: "Bietet Ihnen eine Lösung für fehlende Daten in heruntergeladenen Azure Active Directory-Aktivitätsprotokollen."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: d976e2476001ad4d23e55913681500f14e285014
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2018
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>Ich kann keine Daten in den Azure Active Directory-Aktivitätsprotokollen finden, die ich heruntergeladen habe.


## <a name="symptoms"></a>Symptome

Ich habe die Aktivitätsprotokolle (Überwachung oder Anmeldungen) heruntergeladen, und für den ausgewählten Zeitraum werden nicht alle Datensätze angezeigt. Warum? 

 ![Berichterstellung](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Ursache

Beim Herunterladen von Aktivitätsprotokollen im Azure-Portal gilt eine Obergrenze von 120.000 Datensätzen, die nach Aktualität sortiert werden. 

## <a name="resolution"></a>Lösung

Mithilfe von [Azure AD-Berichterstellungs-APIs](active-directory-reporting-api-getting-started.md) können jederzeit bis zu einer Millionen Datensätze abgerufen werden. Unsere empfohlene Vorgehensweise ist das Ausführen eines Skripts auf Basis eines Zeitplans, der die Berichterstellungs-APIs zum inkrementellen Abrufen von Datensätzen über eine bestimmte Zeitspanne (z.B. täglich oder wöchentlich) aufruft.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu Azure Active Directory-Berichten](active-directory-reporting-faq.md).

