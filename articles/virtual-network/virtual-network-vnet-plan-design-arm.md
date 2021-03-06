---
title: "Azure Virtual Network (VNET) – Planungs- und Entwurfshandbuch | Microsoft Docs"
description: Es wird beschrieben, wie Sie virtuelle Netzwerke in Azure basierend auf Ihren Anforderungen in Bezug auf Isolierung, Verbindung und Standort planen und entwerfen.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2016
ms.author: jdial
ms.openlocfilehash: ecdc3a847821fd83718f9cfc42308667460feabc
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/09/2018
---
# <a name="plan-and-design-azure-virtual-networks"></a>Planen und Entwerfen von Azure Virtual Networks
Das Erstellen eines VNET zum Experimentieren ist einfach. Aber die Wahrscheinlichkeit ist hoch, dass Sie im Laufe der Zeit mehrere VNETs bereitstellen, um die Produktionsanforderungen Ihres Unternehmens zu erfüllen. Mit etwas Planungs- und Entwurfsarbeit können Sie beim Bereitstellen von VNETs und beim Herstellen einer Verbindung mit den Ressourcen effektiver vorgehen. Falls Sie mit VNETs noch nicht vertraut sind, sollten Sie sich die [Informationen zu VNETs](virtual-networks-overview.md) und die [Informationen zur Bereitstellung](quick-create-portal.md) durchlesen, bevor Sie fortfahren.

## <a name="plan"></a>Plan
Ein gutes Verständnis von Azure-Abonnements, -Regionen und -Netzwerkressourcen ist wichtig, um erfolgreich zu sein. Sie können die unten angegebene Liste mit Überlegungen als Ausgangspunkt verwenden. Nachdem Sie sich damit vertraut gemacht haben, können Sie die Anforderungen für Ihren Netzwerkentwurf definieren.

### <a name="considerations"></a>Überlegungen
Machen Sie sich Folgendes klar, bevor Sie die Fragen zur Planung weiter unten beantworten:

* Alle Elemente, die Sie in Azure erstellen, bestehen aus einer oder mehreren Ressourcen. Eine virtuelle Maschine (VM) ist eine Ressource, die von einer VM verwendete Netzwerkschnittstellenkarte (NIC) ist eine Ressource, die von einer NIC verwendete öffentliche IP-Adresse ist eine Ressource, und das VNET, mit dem die NIC verbunden ist, ist auch eine Ressource.
* Sie erstellen Ressourcen in einer [Azure-Region](https://azure.microsoft.com/regions/#services) und unter einem Abonnement. Ressourcen können nur mit einem virtuellen Netzwerk verbunden werden, das in derselben Region und unter demselben Abonnement vorhanden ist.
* Virtuelle Netzwerke können über folgende Methoden untereinander verbunden werden:
    * **[Peering virtueller Netzwerke](virtual-network-peering-overview.md)**: Die virtuellen Netzwerke müssen sich in derselben Azure-Region befinden. Zwischen Ressourcen in virtuellen Netzwerken, die mittels Peering miteinander verknüpft sind, ist dieselbe Bandbreitenmenge verfügbar wie bei Ressourcen, die mit demselben virtuellen Netzwerk verbunden sind.
    * **Azure [VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)**: Die virtuellen Netzwerke können sich in derselben Azure-Region oder in verschiedenen Azure-Regionen befinden. Die Bandbreite zwischen den Ressourcen in virtuellen Netzwerken, die über ein VPN-Gateway verbunden sind, wird durch die Bandbreite des VPN-Gateways beschränkt.
* Sie können VNETs mit Ihrem lokalen Netzwerk verbinden, indem Sie eine der [Konnektivitätsoptionen](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) in Azure verwenden.
* Unterschiedliche Ressourcen können in [Ressourcengruppen](../azure-resource-manager/resource-group-overview.md#resource-groups) zusammengefasst werden, um die Verwaltung der Ressource als Einheit zu vereinfachen. Eine Ressourcengruppe kann Ressourcen aus mehreren Regionen enthalten, solange die Ressourcen demselben Abonnement angehören.

### <a name="define-requirements"></a>Definieren von Anforderungen
Verwenden Sie die Fragen unten als Ausgangspunkt für Ihren Azure-Netzwerkentwurf.    

1. Welche Azure-Standorte verwenden Sie zum Hosten von VNETs?
2. Müssen Sie die Kommunikation zwischen diesen Azure-Speicherorten ermöglichen?
3. Müssen Sie die Kommunikation zwischen Ihrem Azure-VNET (bzw. mehreren) und dem lokalen Rechenzentrum (bzw. mehreren) bereitstellen?
4. Wie viele Infrastructure as a Service-VMs (IaaS), Clouddienstrollen und Web-Apps benötigen Sie für Ihre Lösung?
5. Müssen Sie Datenverkehr basierend auf VM-Gruppen (also Front-End-Webserver und Back-End-Datenbankserver) isolieren?
6. Müssen Sie Datenverkehr mit virtuellen Geräten steuern?
7. Benötigen Benutzer unterschiedliche Berechtigungssätze für unterschiedliche Azure-Ressourcen?

### <a name="understand-vnet-and-subnet-properties"></a>Grundlagen von VNET- und Subnetzeigenschaften
Mit VNET- und Subnetzressourcen können Sie eine Sicherheitsbegrenzung für in Azure ausgeführte Workloads definieren. Ein VNet ist durch eine Auflistung von Adressräumen gekennzeichnet, die als CIDR-Blöcke bezeichnet werden.

> [!NOTE]
> Netzwerkadministratoren sind mit der CIDR-Notation vertraut. Wenn Sie CIDR nicht kennen, [erfahren Sie hier mehr darüber](http://whatismyipaddress.com/cidr).
>
>

VNets umfassen die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Einschränkungen |
| --- | --- | --- |
| **name** |VNet-Name |Zeichenfolge mit bis zu 80 Zeichen. Sie kann Buchstaben, Zahlen, Unterstriche, Punkte und Bindestriche enthalten. Sie muss mit einem Buchstaben oder einer Zahl beginnen. Sie muss mit einem Buchstaben, einer Zahl oder einem Unterstrich enden. Sie kann Groß- oder Kleinbuchstaben enthalten. |
| **Speicherort** |Azure-Standort (auch als Region bezeichnet). |Dies muss einer der gültigen Azure-Standorte sein. |
| **addressSpace** |Auflistung der Adresspräfixe, aus denen das VNET besteht, in CIDR-Notation. |Es muss ein Array mit gültigen CIDR-Adressblöcken sein, einschließlich öffentlicher IP-Adressbereiche. |
| **Subnetze** |Auflistung von Subnetzen, aus denen das VNet besteht |Siehe Tabelle mit den Subnetzeigenschaften unten. |
| **dhcpOptions** |Objekt, das eine einzelne erforderliche Eigenschaft mit dem Namen **dnsServers**enthält. | |
| **dnsServers** |Array mit DNS-Servern, die vom VNET verwendet werden. Wenn kein Server angegeben ist, wird die interne Namensauflösung von Azure verwendet. |Es muss ein Array mit bis zu 10 DNS-Servern (nach IP-Adresse) sein. |

Ein Subnetz ist eine untergeordnete Ressource eines VNet und hilft, die Segmente von Adressräumen innerhalb eines CIDR-Blocks mithilfe von IP-Adressenpräfixen zu definieren. NICs können zu Subnetzen hinzugefügt und mit virtuellen Computern verbunden werden, sodass sie Konnektivität für verschiedene Workloads bereitstellen.

Subnetze umfassen die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Einschränkungen |
| --- | --- | --- |
| **name** |Subnetzname |Zeichenfolge mit bis zu 80 Zeichen. Sie kann Buchstaben, Zahlen, Unterstriche, Punkte und Bindestriche enthalten. Sie muss mit einem Buchstaben oder einer Zahl beginnen. Sie muss mit einem Buchstaben, einer Zahl oder einem Unterstrich enden. Sie kann Groß- oder Kleinbuchstaben enthalten. |
| **Speicherort** |Azure-Standort (auch als Region bezeichnet). |Dies muss einer der gültigen Azure-Standorte sein. |
| **addressPrefix** |Einzelnes Adresspräfix für das Subnetz in CIDR-Notation |Es muss ein einzelner CIDR-Block sein, der Teil von einem der VNET-Adressbereiche ist. |
| **networkSecurityGroup** |Auf das Subnetz angewendete NSG | |
| **routeTable** |Auf das Subnetz angewendete Routentabelle | |
| **ipConfigurations** |Auflistung von IP-Konfigurationsobjekten, die von mit dem Subnetz verbundenen NICs verwendet werden | |

### <a name="name-resolution"></a>Namensauflösung
Standardmäßig verwendet Ihr VNet die [von Azure bereitgestellte Namensauflösung](virtual-networks-name-resolution-for-vms-and-role-instances.md), um die Namen im VNet und im öffentlichen Internet aufzulösen. Wenn Sie Ihre VNETs aber mit Ihren lokalen Rechenzentren verbinden, müssen Sie einen [eigenen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md) angeben, um Namen zwischen Ihren Netzwerken aufzulösen.  

### <a name="limits"></a>Einschränkungen
Informieren Sie sich im Artikel zu den [Grenzwerten in Azure](../azure-subscription-service-limits.md#networking-limits) über die Grenzwerte für Netzwerke, um sicherzustellen, dass Ihr Entwurf keinen Konflikt mit einem dieser Grenzwerte verursacht. Einige Einschränkungen können durch Öffnen eines Supporttickets erhöht werden.

### <a name="role-based-access-control-rbac"></a>Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)
Sie können [Azure RBAC](../active-directory/role-based-access-built-in-roles.md) verwenden, um die Zugriffsebene zu steuern, die unterschiedlichen Benutzern für die Ressourcen in Azure gewährt wird. Auf diese Weise können Sie die Arbeit Ihres Teams je nach Bedarf aufteilen.

Bei virtuellen Netzwerken haben Benutzer mit der Rolle **Mitwirkender von virtuellem Netzwerk** die vollständige Kontrolle über die Virtual Network-Ressourcen von Azure-Ressourcen-Manager. Ebenso haben Benutzer mit der Rolle **Mitwirkender von klassischem Netzwerk** die vollständige Kontrolle über herkömmliche Virtual Network-Ressourcen.

> [!NOTE]
> Sie können auch [eigene Rollen erstellen](../active-directory/role-based-access-control-configure.md) , um Ihre administrativen Anforderungen zu erfüllen.
>
>

## <a name="design"></a>Entwurf
Wenn Sie die Antworten auf die Fragen im Abschnitt [Plan](#Plan) kennen, sollten Sie die folgenden Punkte prüfen, bevor Sie Ihre VNETs definieren:

### <a name="number-of-subscriptions-and-vnets"></a>Anzahl der Abonnements und VNETs
Erwägen Sie für die unten angegebenen Fälle die Erstellung mehrerer VNETs:

* **VMs, die an verschiedenen Azure-Standorten angeordnet werden müssen**. VNETs sind unter Azure regional. Sie können nicht standortübergreifend genutzt werden. Daher benötigen Sie mindestens ein VNET für jeden Azure-Standort, an dem Sie VMs hosten möchten.
* **Workloads, die vollständig voneinander isoliert sein müssen**. Sie können separate VNETs erstellen, für die die gleichen IP-Adressbereiche verwendet werden, um unterschiedliche Workloads voneinander zu isolieren.

Beachten Sie, dass die oben angegebenen Grenzen pro Region und Abonnement gelten. Dies bedeutet, dass Sie mehrere Abonnements verwenden können, um den Grenzwert für die Ressourcen zu erhöhen, die Sie unter Azure verwalten können. Sie können ein Site-to-Site-VPN oder eine ExpressRoute-Verbindung verwenden, um VNETs in unterschiedlichen Abonnements zu verbinden.

### <a name="subscription-and-vnet-design-patterns"></a>Abonnement- und VNET-Entwurfsmuster
Die Tabelle unten enthält einige gängige Entwurfsmuster für die Verwendung von Abonnements und VNETs.

| Szenario | Diagramm | Vorteile | Nachteile |
| --- | --- | --- | --- |
| Ein Abonnement, zwei VNETs pro App |![Ein Abonnement](./media/virtual-network-vnet-plan-design-arm/figure1.png) |Es muss nur ein Abonnement verwaltet werden. |Maximale Anzahl von VNETs pro Azure-Region. Danach benötigen Sie weitere Abonnements. Lesen Sie den Artikel zu [Grenzwerten in Azure](../azure-subscription-service-limits.md#networking-limits), um weitere Informationen zu erhalten. |
| Ein Abonnement pro App, zwei VNETs pro App |![Ein Abonnement](./media/virtual-network-vnet-plan-design-arm/figure2.png) |Es werden nur zwei VNETs pro Abonnement verwendet. |Die Verwaltung ist schwieriger, wenn zu viele Apps vorhanden sind. |
| Ein Abonnement pro Geschäftseinheit, zwei VNETs pro App |![Ein Abonnement](./media/virtual-network-vnet-plan-design-arm/figure3.png) |Balance zwischen Abonnements und VNETs. |Maximale Anzahl von VNETs pro Geschäftseinheit (Abonnement). Lesen Sie den Artikel zu [Grenzwerten in Azure](../azure-subscription-service-limits.md#networking-limits), um weitere Informationen zu erhalten. |
| Ein Abonnement pro Geschäftseinheit, zwei VNETs pro App-Gruppe |![Ein Abonnement](./media/virtual-network-vnet-plan-design-arm/figure4.png) |Balance zwischen Abonnements und VNETs. |Apps müssen mit Subnetzen und Netzwerksicherheitsgruppen isoliert werden. |

### <a name="number-of-subnets"></a>Anzahl von Subnetzen
In den folgenden Fällen sollten Sie die Verwendung mehrerer Subnetze in einem VNET erwägen:

* **Nicht genügend private IP-Adressen für alle Netzwerkkarten in einem Subnetz**. Wenn Ihr Subnetz-Adressbereich nicht genügend IP-Adressen für die Anzahl von Netzwerkkarten im Subnetz enthält, müssen Sie mehrere Subnetze erstellen. Beachten Sie, dass Azure für jedes Subnetz fünf private IP-Adressen reserviert, die nicht verwendet werden können: die erste und letzte Adresse des Adressbereichs (für die Subnetzadresse und Multicast) und drei Adressen für die interne Verwendung (für DHCP- und DNS-Zwecke).
* **Sicherheit**: Sie können Subnetze verwenden, um VM-Gruppen für Workloads voneinander zu trennen, die über eine Struktur mit mehreren Schichten verfügen. Für diese Subnetze können Sie unterschiedliche [Netzwerksicherheitsgruppen (NSGs)](virtual-networks-nsg.md#subnets) anwenden.
* **Hybridkonnektivität**. Sie können VPN-Gateways und ExpressRoute-Verbindungen verwenden, um Ihre VNETs miteinander und mit Ihren lokalen Rechenzentren zu [verbinden](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti). VPN Gateways und ExpressRoute-Verbindungen benötigen für ihre Erstellung ein eigenes Subnetz.
* **Virtuelle Geräte**. Sie können ein virtuelles Gerät, z. B. eine Firewall, einen WAN Accelerator oder ein VPN Gateway in einem Azure VNET verwenden. In diesem Fall müssen Sie [Datenverkehr an diese Geräte weiterleiten](virtual-networks-udr-overview.md) und sie in einem eigenen Subnetz isolieren.

### <a name="subnet-and-nsg-design-patterns"></a>Subnetz- und NSG-Entwurfsmuster
Die Tabelle unten enthält einige gängige Entwurfsmuster für die Verwendung von Subnetzen.

| Szenario | Diagramm | Vorteile | Nachteile |
| --- | --- | --- | --- |
| Ein Subnetz, NSGs pro Anwendungsschicht und App |![Ein Subnetz](./media/virtual-network-vnet-plan-design-arm/figure5.png) |Es muss nur ein Subnetz verwaltet werden. |Es sind mehrere NSGs erforderlich, um jede Anwendung zu isolieren. |
| Ein Subnetz pro App, NSGs pro Anwendungsschicht |![Subnetz pro App](./media/virtual-network-vnet-plan-design-arm/figure6.png) |Es müssen weniger NSGs verwaltet werden. |Es müssen mehrere Subnetze verwaltet werden. |
| Ein Subnetz pro Anwendungsschicht, NSGs pro App |![Subnetz pro Schicht](./media/virtual-network-vnet-plan-design-arm/figure7.png) |Balance zwischen der Anzahl von Subnetzen und NSGs. |Maximale Anzahl von NSGs pro Abonnement. Lesen Sie den Artikel zu [Grenzwerten in Azure](../azure-subscription-service-limits.md#networking-limits), um weitere Informationen zu erhalten. |
| Ein Subnetz pro Anwendungsschicht und App, NSGs pro Subnetz |![Subnetz pro Schicht und App](./media/virtual-network-vnet-plan-design-arm/figure8.png) |Unter Umständen ist eine geringere Anzahl von Netzwerksicherheitsgruppen erforderlich. |Es müssen mehrere Subnetze verwaltet werden. |

## <a name="sample-design"></a>Beispielentwurf
Sehen Sie sich das folgende Szenario an, mit dem die Anwendung der Informationen in diesem Artikel dargestellt wird.

Sie arbeiten für ein Unternehmen, das zwei Rechenzentren in Nordamerika und zwei Rechenzentren in Europa betreibt. Sie haben sechs unterschiedliche Anwendungen für Kunden identifiziert, die von zwei unterschiedlichen Geschäftseinheiten verwaltet werden. Diese sollen im Rahmen eines Pilotprojekts zu Azure migriert werden. Die grundlegende Architektur für die Anwendungen lautet wie folgt:

* App1, App2, App3 und App4 sind Webanwendungen, die auf Linux-Servern mit Ubuntu gehostet werden. Jede Anwendung stellt eine Verbindung mit einem separaten Anwendungsserver her, mit dem RESTful-Dienste auf Linux-Servern gehostet werden. Für die RESTful-Dienste wird eine Verbindung mit einer MySQL-Back-End-Datenbank hergestellt.
* App5 und App6 sind Webanwendungen, die auf Windows-Servern mit Windows Server 2012 R2 gehostet werden. Für jede Anwendung wird eine Verbindung mit einer SQL Server-Back-End-Datenbank hergestellt.
* Alle Apps werden derzeit in einem der Rechenzentren des Unternehmens in Nordamerika gehostet.
* Für lokale Rechenzentren wird der Adressbereich „10.0.0.0/8“ verwendet.

Sie müssen eine Virtual Network-Lösung erstellen, die die folgenden Anforderungen erfüllt:

* Die einzelnen Geschäftseinheiten sollten durch den Ressourcenverbrauch anderer Geschäftseinheiten nicht beeinträchtigt werden.
* Sie sollten die Menge von VNETs und Subnetzen verringern, um die Verwaltung zu vereinfachen.
* Jede Geschäftseinheit sollte über ein einzelnes Test-/Entwicklungs-VNET verfügen, das für alle Anwendungen verwendet wird.
* Jede Anwendung wird in zwei unterschiedlichen Azure-Rechenzentren pro Kontinent (Nordamerika und Europa) gehostet.
* Die einzelnen Anwendungen sind vollständig voneinander isoliert.
* Auf jede Anwendung kann von Kunden über das Internet per HTTP zugegriffen werden.
* Auf jede Anwendung kann von Benutzern zugegriffen werden, die über einen verschlüsselten Tunnel mit den lokalen Rechenzentren verbunden sind.
* Für die Verbindung mit lokalen Rechenzentren sollten vorhandene VPN-Geräte verwendet werden.
* Die Netzwerkgruppe des Unternehmens sollte die vollständige Kontrolle über die VNET-Konfiguration haben.
* Für Entwickler in den einzelnen Geschäftseinheiten sollte es nur möglich sein, VMs in vorhandenen Subnetzen bereitzustellen.
* Alle Anwendungen werden in unverändertem Zustand zu Azure migriert („Lift-and-Shift“-Vorgang).
* Die Datenbanken an jedem Standort sollten einmal pro Tag an andere Azure-Standorte repliziert werden.
* Für jede Anwendung sollten fünf Front-End-Webserver, zwei Anwendungsserver (falls erforderlich) und zwei Datenbankserver verwendet werden.

### <a name="plan"></a>Plan
Beginnen Sie mit Ihrer Entwurfsplanung, indem Sie die Fragen im Abschnitt [Definieren von Anforderungen](#Define-requirements) wie unten angegeben beantworten.

1. Welche Azure-Standorte verwenden Sie zum Hosten von VNETs?

    Zwei Standorte in Nordamerika und zwei Standorte in Europa. Sie sollten diese basierend auf dem physischen Standort Ihrer lokalen Rechenzentren auswählen. Auf diese Weise ergibt sich für die Verbindung von Ihren physischen Standorten mit Azure eine bessere Latenz.
2. Müssen Sie die Kommunikation zwischen diesen Azure-Speicherorten ermöglichen?

    Ja. Der Grund ist, dass die Datenbanken an alle Standorte repliziert werden müssen.
3. Müssen Sie die Kommunikation zwischen Ihrem Azure-VNET (bzw. mehreren) und dem lokalen Rechenzentrum (bzw. mehreren) bereitstellen?

    Ja. Der Grund ist, dass Benutzer, die mit den lokalen Rechenzentren verbunden sind, auf die Anwendungen über einen verschlüsselten Tunnel zugreifen können müssen.
4. Wie viele IaaS-VMs benötigen Sie für Ihre Lösung?

    200 IaaS-VMs. Für App1, App2, App3 und App4 sind jeweils fünf Webserver, zwei Anwendungsserver und zwei Datenbankserver erforderlich. Dies ergibt insgesamt neun IaaS-VMs pro Anwendung bzw. 36 IaaS-VMs. Für App5 und App6 werden jeweils fünf Webserver und zwei Datenbankserver benötigt. Dies ergibt insgesamt sieben IaaS-VMs pro Anwendung bzw. 14 IaaS-VMs. Aus diesem Grund benötigen Sie für alle Anwendungen in jeder Azure-Region 50 IaaS-VMs. Da wir vier Regionen verwenden müssen, ergeben sich 200 IaaS-VMs.

    Sie müssen auch DNS-Server in jedem VNET oder in Ihren lokalen Rechenzentren bereitstellen, um Namen zwischen Ihren Azure IaaS-VMs und dem lokalen Netzwerk aufzulösen.
5. Müssen Sie Datenverkehr basierend auf VM-Gruppen (also Front-End-Webserver und Back-End-Datenbankserver) isolieren?

    Ja. Alle Anwendungen müssen vollständig voneinander isoliert sein, und auch jede Anwendungsschicht sollte isoliert sein.
6. Müssen Sie Datenverkehr mit virtuellen Geräten steuern?

    Nein. Virtuelle Geräte können verwendet werden, um mehr Kontrolle über den Datenverkehrsfluss zu erhalten, einschließlich einer ausführlicheren Protokollierung der Datenebene.
7. Benötigen Benutzer unterschiedliche Berechtigungssätze für unterschiedliche Azure-Ressourcen?

    Ja. Das Netzwerkteam benötigt Vollzugriff auf die Einstellungen für virtuelle Netzwerke, und Entwickler sollten nur dazu berechtigt sein, ihre VMs in bereits vorhandenen Subnetzen bereitzustellen.

### <a name="design"></a>Entwurf
Halten Sie sich beim Angeben von Abonnements, VNETs, Subnetzen und NSGs an die Entwurfsvorgaben. NSGs werden hier beschrieben, aber Sie sollten weitere Informationen zu [NSGs](virtual-networks-nsg.md) lesen, bevor Sie Ihren Entwurf fertigstellen.

**Anzahl der Abonnements und VNETs**

Die folgenden Anforderungen gelten für Abonnements und VNETs:

* Die einzelnen Geschäftseinheiten sollten durch den Ressourcenverbrauch anderer Geschäftseinheiten nicht beeinträchtigt werden.
* Sie sollten die Anzahl von VNETs und Subnetzen verringern.
* Jede Geschäftseinheit sollte über ein einzelnes Test-/Entwicklungs-VNET verfügen, das für alle Anwendungen verwendet wird.
* Jede Anwendung wird in zwei unterschiedlichen Azure-Rechenzentren pro Kontinent (Nordamerika und Europa) gehostet.

Basierend auf diesen Anforderungen benötigen Sie ein Abonnement für jede Geschäftseinheit. Der Ressourcenverbrauch einer Geschäftseinheit zählt dann nicht in Bezug auf Beschränkungen für andere Geschäftseinheiten. Und da Sie die Anzahl von VNETs verringern möchten, sollten Sie erwägen, wie unten gezeigt das Muster **Ein Abonnement pro Geschäftseinheit, zwei VNETs pro App-Gruppe** zu verwenden.

![Ein Abonnement](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Außerdem müssen Sie den Adressbereich für jedes VNET angeben. Da Sie Konnektivität zwischen den lokalen Rechenzentren und den Azure-Regionen benötigen, darf der für Azure VNETs verwendete Adressraum nicht mit dem lokalen Netzwerk kollidieren, und der von den einzelnen VNETs verwendete Adressbereich sollte nicht mit anderen vorhandenen VNETs kollidieren. Sie können die Adressbereiche in der Tabelle unten verwenden, um diese Anforderungen zu erfüllen.  

| **Abonnement** | **VNET** | **Azure-Region** | **Adressraum** |
| --- | --- | --- | --- |
| BU1 |ProdBU1US1 |USA (Westen) |172.16.0.0/16 |
| BU1 |ProdBU1US2 |USA (Ost) |172.17.0.0/16 |
| BU1 |ProdBU1EU1 |Nordeuropa |172.18.0.0/16 |
| BU1 |ProdBU1EU2 |Europa, Westen |172.19.0.0/16 |
| BU1 |TestDevBU1 |USA (Westen) |172.20.0.0/16 |
| BU2 |TestDevBU2 |USA (Westen) |172.21.0.0/16 |
| BU2 |ProdBU2US1 |USA (Westen) |172.22.0.0/16 |
| BU2 |ProdBU2US2 |USA (Ost) |172.23.0.0/16 |
| BU2 |ProdBU2EU1 |Nordeuropa |172.24.0.0/16 |
| BU2 |ProdBU2EU2 |Europa, Westen |172.25.0.0/16 |

**Anzahl der Subnetze und Netzwerksicherheitsgruppen**

Die folgenden Anforderungen beziehen sich auf Subnetze und Netzwerksicherheitsgruppen:

* Sie sollten die Anzahl von VNETs und Subnetzen verringern.
* Die einzelnen Anwendungen sind vollständig voneinander isoliert.
* Auf jede Anwendung kann von Kunden über das Internet per HTTP zugegriffen werden.
* Auf jede Anwendung kann von Benutzern zugegriffen werden, die über einen verschlüsselten Tunnel mit den lokalen Rechenzentren verbunden sind.
* Für die Verbindung mit lokalen Rechenzentren sollten vorhandene VPN-Geräte verwendet werden.
* Die Datenbanken an jedem Standort sollten einmal pro Tag an andere Azure-Standorte repliziert werden.

Basierend auf diesen Anforderungen können Sie ein Subnetz pro Anwendungsschicht verwenden und NSGs nutzen, um den Datenverkehr pro Anwendung zu filtern. Auf diese Weise haben Sie nur drei Subnetze in jedem VNET (Front-End, Anwendungsschicht und Datenschicht) und eine NSG pro Anwendung und Subnetz. In diesem Fall sollten Sie erwägen, das Entwurfsmuster **Ein Subnetz pro Anwendungsschicht, NSGs pro App** zu verwenden. Die folgende Abbildung zeigt die Verwendung des Entwurfsmusters für das VNET **ProdBU1US1** .

![Ein Subnetz pro Schicht, eine NSG pro Anwendung und Schicht](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Sie müssen aber auch ein zusätzliches Subnetz für die VPN-Konnektivität zwischen den VNETs und Ihren lokalen Rechenzentren erstellen. Außerdem müssen Sie den Adressbereich für jedes Subnetz angeben. In der Abbildung unten ist eine Beispiellösung für das VNET **ProdBU1US1** dargestellt. Sie würden dieses Szenario dann für jedes VNET replizieren. Jede Farbe steht für eine andere Anwendung.

![Beispiel-VNET](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Zugriffssteuerung**

Die folgenden Anforderungen beziehen sich auf die Zugriffssteuerung (Access Control):

* Die Netzwerkgruppe des Unternehmens sollte die vollständige Kontrolle über die VNET-Konfiguration haben.
* Für Entwickler in den einzelnen Geschäftseinheiten sollte es nur möglich sein, VMs in vorhandenen Subnetzen bereitzustellen.

Basierend auf diesen Anforderungen können Sie Benutzer aus dem Netzwerkteam in jedem Abonnement zur integrierten Rolle **Netzwerkmitwirkender** hinzufügen. Außerdem können Sie eine benutzerdefinierten Rolle für die Anwendungsentwickler in jedem Abonnement erstellen und ihnen Rechte zum Hinzufügen von VMs zu vorhandenen Subnetzen gewähren.

## <a name="next-steps"></a>Nächste Schritte
* [Stellen Sie basierend auf einem bestimmten Szenario ein virtuelles Netzwerk bereit](virtual-networks-create-vnet-arm-template-click.md) .
* Informieren Sie sich, wie Sie den [Lastenausgleich](../load-balancer/load-balancer-overview.md) für IaaS-VMs durchführen und das [Routing über mehrere Azure-Regionen hinweg verwalten](../traffic-manager/traffic-manager-overview.md).
* Erfahren Sie mehr über [NSGs und das Planen und Entwerfen](virtual-networks-nsg.md) einer NSG-Lösung.
* Erfahren Sie mehr über Ihre [standortübergreifenden und VNET-Verbindungsoptionen](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti).
