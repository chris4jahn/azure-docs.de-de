---
title: "Einführung in Azure Advisor | Microsoft Docs"
description: Nutzen Sie Azure Advisor, um Ihre Azure-Bereitstellungen zu optimieren.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: a4096b11a828cf6676aa22b11c4dd4d75f3b0286
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/24/2018
---
# <a name="introduction-to-azure-advisor"></a>Einführung in Azure Advisor

Hier erfahren Sie mehr über die wichtigsten Funktionen vom Azure Advisor und erhalten Antworten auf häufig gestellte Fragen.

## <a name="what-is-advisor"></a>Was ist der Advisor?
Beim Advisor handelt es sich um einen personalisierten Cloudberater, der Sie mit bewährten Methoden zum Optimieren von Azure-Bereitstellungen unterstützt. Das Tool analysiert die Konfiguration Ihrer Ressourcen und Telemetriedaten zur Nutzung und macht anschließend Vorschläge, wie Sie die Wirtschaftlichkeit, Leistung, Hochverfügbarkeit und Sicherheit Ihrer Azure-Ressourcen steigern können.

Der Advisor ermöglicht Folgendes:
* Abrufen proaktiver, umsetzbarer und individueller Empfehlungen und bewährter Methoden. 
* Verbessern der Leistung, Sicherheit und Hochverfügbarkeit Ihrer Ressourcen und Ermitteln von Möglichkeiten der Senkung Ihrer Gesamtausgaben für Azure.
* Abrufen von Empfehlungen mit vorgeschlagenen Inlineaktionen.

Der Zugriff auf Advisor erfolgt im [Azure-Portal](https://aka.ms/azureadvisordashboard). Melden Sie sich beim [Portal](https://portal.azure.com) an, und suchen Sie den **Advisor** im Navigationsmenü oder im Menü **Alle Dienste**.

Auf dem Advisor-Dashboard werden personalisierte Empfehlungen für alle Ihre Abonnements angezeigt.  Sie können Filter zum Anzeigen von Empfehlungen für bestimmte Abonnements und Ressourcentypen anwenden.  Die Empfehlungen sind in vier Kategorien unterteilt: 

* **Hochverfügbarkeit**: Der Ratgeber hilft Ihnen, die ununterbrochene Verfügbarkeit Ihrer unternehmenskritischen Anwendungen zu gewährleisten und zu verbessern. Weitere Informationen finden Sie unter [Advisor-Empfehlungen für Hochverfügbarkeit](advisor-high-availability-recommendations.md).
* **Sicherheit**: Der Ratgeber hilft beim Erkennen von Bedrohungen und Sicherheitsrisiken, die zu Sicherheitslücken führen können. Weitere Informationen finden Sie unter [Advisor-Empfehlungen zur Sicherheit](advisor-security-recommendations.md).
* **Leistung**: Der Ratgeber hilft, die Geschwindigkeit Ihrer Anwendungen zu verbessern. Weitere Informationen finden Sie unter [Advisor-Empfehlungen zur Leistung](advisor-performance-recommendations.md).
* **Kosten**: Mit dem Ratgeber können Sie Ihre Gesamtausgaben für Azure senken und optimieren. Weitere Informationen finden Sie unter [Advisor-Empfehlungen zu Kosten](advisor-cost-recommendations.md).

  ![Typen von Advisor-Empfehlungen](./media/advisor-overview/advisor-dashboard.png)

> [!NOTE]
> Um den Azure Advisor mit einem Abonnement zu verwenden, muss ein *Besitzer* eines Abonnements das Ratgeber-Dashboard neu aufrufen.  Bei dieser Aktion wird das Abonnement beim Advisor registriert.  Ab diesem Punkt kann jeder *Besitzer*, *Mitwirkende* oder *Leser* eines Abonnements auf die Advisor-Empfehlungen für das Abonnement zugreifen. 

Sie können auf eine Kategorie klicken, um die Liste der Empfehlungen innerhalb dieser Kategorie anzuzeigen, und eine Empfehlung auswählen, um weitere Informationen zu erhalten.  Sie lernen außerdem Aktionen kennen, die Sie ausführen können, um eine Chance zu nutzen oder ein Problem zu beheben.

![Kategorie der Advisor-Empfehlungen](./media/advisor-overview/advisor-ha-category-example.png) 

Wählen Sie die empfohlene Aktion für eine Empfehlung aus, um die Empfehlung zu implementieren.  Eine einfache Benutzeroberfläche wird geöffnet, in der Sie die Empfehlung implementieren können, oder um Sie auf die Dokumentation zu verweisen, die Ihnen bei der Implementierung hilft.  Sobald Sie eine Empfehlung implementieren, kann es einen Tag dauern, bis der Advisor dies erkennt.

Wenn Sie nicht vorhaben, bei einer Empfehlung direkt eine Maßnahme zu ergreifen, können Sie sie für einen bestimmten Zeitraum zurückstellen oder verwerfen.  Wenn Sie für ein bestimmtes Abonnement oder eine bestimmte Ressourcengruppe keine Empfehlungen erhalten möchten, können Sie den Advisor so konfigurieren, dass er nur Empfehlungen für angegebene Abonnements und Ressourcengruppen generiert.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-do-i-access-advisor"></a>Wie greife ich auf Advisor zu?
Der Zugriff auf Advisor erfolgt im [Azure-Portal](https://aka.ms/azureadvisordashboard). Melden Sie sich beim [Portal](https://portal.azure.com) an, und suchen Sie den **Advisor** im Navigationsmenü oder im Menü **Alle Dienste**.

Sie können die Advisor-Empfehlungen auch auf der VM-Ressourcenbenutzeroberfläche anzeigen. Wählen Sie einen virtuellen Computer aus, und wechseln Sie dann zu den Advisor-Empfehlungen im Menü. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Welche Berechtigungen benötige ich für den Zugriff auf Advisor?

Um Advisor-Empfehlungen für ein Abonnement zu erhalten, müssen Sie Ihr Abonnement zunächst beim Advisor registrieren. Ein Abonnement wird registriert, wenn ein *Besitzer* das Advisor-Dashboard aufruft. Dies ist ein einmaliger Vorgang. Sobald ein Abonnement registriert wurde, können *Besitzer*, *Mitwirkende* oder *Leser* eines Abonnements auf die Advisor-Empfehlungen zugreifen.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Für welche Ressourcen bietet Advisor Empfehlungen?

Der Advisor bietet Empfehlungen für virtuelle Computer, Verfügbarkeitsgruppen, Anwendungsgateways, App Services, SQL-Server, SQL-Datenbanken und Redis Cache.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Kann ich eine Empfehlung zurückstellen oder verwerfen?

Um eine Empfehlung zurückzustellen oder zu verwerfen, klicken Sie auf den Link **Zurückstellen**. Sie können einen Zurückstellungszeitraum angeben oder **Nie** auswählen, um die Empfehlung zu verwerfen.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie weitere Informationen zu Empfehlungen des Advisor:

* [Erste Schritte mit Advisor](advisor-get-started.md)
* [Advisor-Empfehlungen für Hochverfügbarkeit](advisor-high-availability-recommendations.md)
* [Advisor-Empfehlungen zur Sicherheit](advisor-security-recommendations.md)
* [Advisor-Empfehlungen zur Leistung](advisor-performance-recommendations.md)
* [Advisor-Empfehlungen zu Kosten](advisor-cost-recommendations.md)
