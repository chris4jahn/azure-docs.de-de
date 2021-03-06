---
title: 'Azure AD Connect Health: Versionsverlauf'
description: In diesem Dokument werden die Versionen von Azure AD Connect Health beschrieben und was darin enthalten ist.
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: edc1771153581e73398e8df25e70660f9f85ceba
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/09/2018
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Versionsveröffentlichungsverlauf
Das Azure Active Directory-Team aktualisiert Azure AD Connect Health regelmäßig mit neuen Features und Funktionen. In diesem Artikel werden die veröffentlichten Versionen und Features beschrieben.

## <a name="march-2018"></a>März 2018
**Agent-Aktualisierung:**

*   Azure AD Connect Health-Agent für AD DS (Version 3.0.176.0)
  1. Verbesserungen bei der Agent-Verfügbarkeit 
  2. Fehlerbehebungen und allgemeine Verbesserungen
*   Azure AD Connect Health-Agent für AD FS (Version 3.0.176.0)
  1. Verbesserungen bei der Agent-Verfügbarkeit 
  2. Fehlerbehebungen und allgemeine Verbesserungen
* Azure AD Connect Health-Agent für Sync (Version 3.0.176.0)
  1. Verbesserungen bei der Agent-Verfügbarkeit 
  2. Fehlerbehebungen und allgemeine Verbesserungen

## <a name="december-2017"></a>Dezember 2017
**Agent-Aktualisierung:**

*   Azure AD Connect Health-Agent für AD DS (Version 3.0.145.0)
  1. Verbesserungen bei der Agent-Verfügbarkeit 
  2. Neue Befehle zur Problembehandlung beim Agent hinzugefügt
  3. Fehlerbehebungen und allgemeine Verbesserungen
*   Azure AD Connect Health-Agent für AD FS (Version 3.0.145.0)
  1. Neue Befehle zur Problembehandlung beim Agent hinzugefügt
  2. Verbesserungen bei der Agent-Verfügbarkeit 
  3. Fehlerbehebungen und allgemeine Verbesserungen
  
## <a name="october-2017"></a>Oktober 2017
**Agent-Aktualisierung:**

 * Azure AD Connect Health-Agent für Sync (Version 3.0.129.0), veröffentlicht mit Azure AD Connect Version 1.1.649.0
<br></br> Ein Problem mit der Versionskompatibilität zwischen Azure AD Connect und dem Azure AD Connect Health-Agent für die Synchronisierung wurde behoben. Dieses Problem betrifft Kunden, die ein direktes Upgrade von Azure AD Connect auf Version 1.1.647.0 durchführen, aber derzeit die Health-Agent Version 3.0.127.0 verwenden. Nach dem Upgrade kann der Health-Agent keine Integritätsdaten mehr über den Azure AD Connect-Synchronisierungsdienst an den Azure AD-Integritätsdienst senden. Behoben wird dieses Problem, indem Version 3.0.129.0 des Health-Agents während des direkten Upgrades von Azure AD Connect installiert wird. Zwischen Version 3.0.129.0 des Health-Agents und Version 1.1.649.0 von Azure AD Connect bestehen keine Kompatibilitätsprobleme.

## <a name="july-2017"></a>Juli 2017
**Agent-Aktualisierung:**

*   Azure AD Connect Health-Agent für AD DS (Version 3.0.68.0)
  1. Fehlerbehebungen und allgemeine Verbesserungen
  2. Unterstützung für unabhängige Clouds
*   Azure AD Connect Health-Agent für AD FS (Version 3.0.68.0)
  1. Fehlerbehebungen und allgemeine Verbesserungen
  2. Unterstützung für unabhängige Clouds
* Azure AD Connect Health-Agent für Sync (Version 3.0.68.0), veröffentlicht mit Azure AD Connect Version 1.1.614.0
1. Unterstützung für Microsoft Azure Government-Cloud und Microsoft Cloud Deutschland

## <a name="april-2017"></a>April 2017      
**Agent-Aktualisierung:**

*   Azure AD Connect Health-Agent für AD FS (Version 3.0.12.0)
  1. Fehlerbehebungen und allgemeine Verbesserungen
*   Azure AD Connect Health-Agent für AD DS (Version 3.0.12.0)
  1. Verbesserungen beim Hochladen von Leistungsindikatoren
  2. Fehlerbehebungen und allgemeine Verbesserungen

## <a name="october-2016"></a>Oktober 2016
**Agent-Aktualisierung:**

* Azure AD Connect Health-Agent für AD FS \(Version 2.6.408.0\)
1. Verbesserungen beim Erkennen von Client-IP-Adressen bei Authentifizierungsanforderungen
2. Fehlerbehebungen im Zusammenhang mit Warnungen
* Azure AD Connect Health-Agent für AD DS (Version 2.6.408.0)
1. Fehlerbehebungen im Zusammenhang mit Warnungen.
* Azure AD Connect Health-Agent für Sync (Version 2.6.353.0), veröffentlicht mit Azure AD Connect Version 1.1.281.0
1. Bereitstellen der für die Berichte zu Synchronisierungsfehlern benötigten Daten
2. Fehlerbehebungen im Zusammenhang mit Warnungen

**Neue Vorschaufeatures:**

* Berichte zu Synchronisierungsfehlern für Azure AD Connect

**Neue Features:**

* Azure AD Connect Health für AD FS – IP-Adressfeld ist im Bericht über die 50 häufigsten Benutzer mit falschem Benutzernamen/Kennwort verfügbar.

## <a name="july-2016"></a>Juli 2016
**Neue Vorschaufeatures:**

* [Azure AD Connect Health für AD DS](active-directory-aadconnect-health-adds.md).

## <a name="january-2016"></a>Januar 2016
**Agent-Aktualisierung:**

* Azure AD Connect Health-Agent für AD FS (Version 2.6.91.1512)

**Neue Features:**

* [Tool zum Testen der Verbindung für Azure AD Connect Health-Agents](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>November 2015
**Neue Features:**

* Unterstützung für die [rollenbasierte Zugriffssteuerung](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**Neue Vorschaufeatures:**

* [Azure AD Connect Health für die Synchronisierung](active-directory-aadconnect-health-sync.md)

**Behobene Probleme:**

* Fehlerbehebungen für bei Agent-Registrierungen aufgetretene Fehler

## <a name="september-2015"></a>September 2015
**Neue Features:**

* Bericht über falschen Benutzernamen/falsches Kennwort für AD FS
* Konfigurationsunterstützung für nicht authentifizierten HTTP-Proxy
* Konfigurationsunterstützung für Agents auf Serverkern
* Verbesserte Warnungen für AD FS
* Verbesserte Verbindungen und Datenuploads beim Azure AD Connect Health-Agent für AD FS

**Behobene Probleme:**

* Fehlerbehebungen bei den Nutzungsinformationen für AD FS-Fehlertypen

## <a name="june-2015"></a>Juni 2015
**Erste Version von Azure AD Connect Health für AD FS und AD FS-Proxy**

**Neue Features:**

* Warnungen für die Überwachung von AD FS und AD FS-Proxy-Server mit E-Mail-Benachrichtigungen
* Einfacher Zugriff auf AD FS-Topologien und Muster in AD FS-Leistungsindikatoren
* Trend bei erfolgreichen Tokenanforderungen auf AD FS-Servern gruppiert nach Anwendungen, Authentifizierungsmethoden, Netzwerkspeicherort-Anforderungen usw.
* Trends bei fehlgeschlagenen Anforderungen an AD FS-Server gruppiert nach Anwendungen, Fehlertypen usw.
* Einfachere Agent-Bereitstellung mit den Anmeldeinformationen für globale Azure AD-Administratoren  

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Überwachen Ihrer lokalen Identitätsinfrastruktur und Synchronisierung von Diensten in der Cloud](active-directory-aadconnect-health.md)

