---
title: Verfügbarkeit von SAP HANA für Azure-VMs – Übersicht | Microsoft-Dokumentation
description: Vorgänge von SAP HANA auf nativen Azure-VMs
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Leitfaden zur Hochverfügbarkeit von SAP HANA für virtuelle Azure-Computer
Azure bietet zahlreiche Funktionen, die die Bereitstellung von unternehmenskritischen Datenbanken wie SAP HANA auf Azure-VMs ermöglichen. Dieses Dokument enthält Anweisungen dazu, wie Verfügbarkeit für in Azure Virtual Machines gehosteten SAP HANA-Instanzen erzielt werden kann. In diesem Dokument werden mehrere Szenarien beschrieben, die in der Azure-Infrastruktur zur Erhöhung der Verfügbarkeit von SAP HANA in Azure implementiert werden können. 

## <a name="prerequisites"></a>Voraussetzungen
Dieser Leitfaden setzt voraus, dass Sie mit den folgenden IaaS-Grundlagen (Infrastructure-as-a-Service) in Azure vertraut sind: 

- Bereitstellen virtueller Computer oder virtueller Netzwerke über das Azure-Portal oder PowerShell.
- Die plattformübergreifende Azure-Befehlszeilenschnittstelle (CLI), einschließlich der Möglichkeit, Vorlagen für JavaScript Object Notation (JSON) zu verwenden.

Dieser Leitfaden setzt außerdem voraus, dass Sie mit der Installation, der Verwaltung und dem Betrieb von SAP HANA-Instanzen vertraut sind. Dies gilt insbesondere für die Einrichtung sowie für Vorgänge im Zusammenhang mit der HANA-Systemreplikation oder Aufgaben wie die Sicherung und Wiederherstellung von SAP HANA-Datenbanken.

Die folgenden Artikel enthalten eine gute Übersicht über SAP HANA-Themen in Azure:

- [Manuelle Installation von SAP HANA (Einzelinstanz) auf Azure-VMs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Einrichten der SAP HANA-Systemreplikation auf virtuellen Azure-Computern](sap-hana-high-availability.md)
- [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Die Artikel und Inhalte, mit denen Sie in Bezug auf SAP HANA vertraut sein sollten, werden im Folgenden beispielhaft aufgeführt:

- [High Availability for SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html) (Hochverfügbarkeit für SAP HANA)
- [FAQ: High Availability for SAP HANA](https://archive.sap.com/documents/docs/DOC-66702) (FAQs: Hochverfügbarkeit für SAP HANA)
- [How to Perform System Replication for SAP HANA](https://archive.sap.com/documents/docs/DOC-47702) (Gewusst wie: Durchführen der Replikation für SAP HANA)
- [SAP HANA 2.0 SPS 01 What’s New: High Availability](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/) (SAP HANA 2.0 SPS 01 – Neuigkeiten: Hochverfügbarkeit)
- [Network Recommendations for SAP HANA System Replication](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html) (Empfehlungen zum Netzwerk für die SAP HANA-Systemreplikation)
- [SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) (SAP HANA-Systemreplikation)
- [SAP HANA Service Auto-Restart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) (Automatischer Neustart des SAP HANA-Diensts)
- [Configuring SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html) (Konfigurieren der SAP HANA-Systemreplikation)


Bevor Sie mit der Definition Ihrer Verfügbarkeitsarchitektur in Azure fortfahren, wird neben der Bereitstellung von VMs in Azure außerdem empfohlen, den Artikel [Verwalten der Verfügbarkeit virtueller Windows-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) zu lesen.

## <a name="service-level-agreements-for-different-azure-components"></a>Vereinbarungen zum Servicelevel für verschiedene Azure-Komponenten
Azure bietet unterschiedliche Verfügbarkeits-SLAs für verschiedene Komponenten wie Netzwerke, Speicher und VMs. All diese SLAs sind dokumentiert und können auf der Seite [Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/) eingesehen werden. Wenn Sie sich die [SLA für virtuelle Computer](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) ansehen, können Sie sehen, dass Azure zwei verschiedene SLAs mit zwei unterschiedlichen Konfigurationen anbietet. Die SLAs sind wie folgt definiert:

- Eine einzelne VM mit [Azure Storage Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) für den Betriebssystemdatenträger und alle Datenträger bietet eine monatliche Betriebszeit von 99,9 %.
- Mehrere (mindestens zwei) VMs, die in einer [Azure-Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) organisiert sind, bieten eine monatliche Betriebszeit von 99,95 %.

Gleichen Sie Ihre Verfügbarkeitsanforderungen mit den SLAs ab, die Azure-Komponenten bereitstellen können, und wählen Sie sich dann die verschiedenen Szenarien aus, die Sie mit SAP HANA implementieren müssen, um die von Ihnen bereitzustellende Verfügbarkeit zu erzielen.

## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit den folgenden Dokumenten fort:

- [Verfügbarkeit von SAP HANA innerhalb einer Azure-Region](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Verfügbarkeit von SAP HANA in verschiedenen Azure-Regionen](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


