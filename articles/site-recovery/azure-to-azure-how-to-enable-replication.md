---
title: "Konfigurieren der Replikation für virtuelle Azure-Computer in Azure Site Recovery | Microsoft-Dokumentation"
description: In diesem Artikel erfahren Sie, wie Sie die Replikation virtueller Azure-Computer zwischen Azure-Regionen mithilfe von Site Recovery konfigurieren.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: asgang
ms.openlocfilehash: 39d81ed6408e5f2c434a4fbaa681efc4c0b19a63
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/12/2018
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replizieren von virtuellen Azure-Computern in einer anderen Azure-Region


>[!NOTE]
>
> Die Site Recovery-Replikation für virtuelle Azure-Computer ist derzeit als Vorschauversion verfügbar.

In diesem Artikel erfahren Sie, wie Sie die Replikation virtueller Azure-Computer zwischen Azure-Regionen aktivieren.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie Site Recovery bereits für dieses Szenario eingerichtet haben, wie im Tutorial [Einrichten einer Notfallwiederherstellung für Azure-VMs in eine sekundäre Azure-Region (Vorschau)](azure-to-azure-tutorial-enable-replication.md) beschrieben. Vergewissern Sie sich, dass Sie die erforderlichen Komponenten vorbereitet und den Recovery Services-Tresor erstellt haben.



## <a name="enable-replication"></a>Aktivieren der Replikation

Aktivieren Sie die Replikation. In diesem Verfahren wird davon ausgegangen, dass als primäre Azure-Region „Asien, Osten“ und als sekundäre Region „Asien, Südosten“ verwendet wird.

1. Klicken Sie im Tresor auf **+Replizieren**.
2. Beachten Sie die folgenden Felder:
    - **Quelle:** Der Ursprung der virtuellen Computer (in diesem Fall **Azure**).
    - **Quellstandort:** Die Azure-Region, in der Sie Ihre virtuellen Computer schützen möchten. In diesem Beispiel ist der Quellspeicherort „Asien, Osten“.
    - **Bereitstellungsmodell:** Das Azure-Bereitstellungsmodell der Quellcomputer.
    - **Ressourcengruppe:** Die Ressourcengruppe, zu der Ihre virtuellen Quellcomputer gehören. Alle virtuellen Computer der ausgewählten Ressourcengruppe werden im nächsten Schritt für den Schutz aufgeführt.

    ![Aktivieren der Replikation](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Klicken Sie auf **Virtuelle Computer > Virtuelle Computer auswählen**, und wählen Sie die virtuellen Computer aus, die Sie replizieren möchten. Sie können nur Computer auswählen, für die die Replikation aktiviert werden kann. Klicken Sie dann auf **OK**.
    ![Replikation aktivieren](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Unter **Einstellungen** können Sie optional Einstellungen für den Zielstandort konfigurieren:

    - **Zielstandort:** Der Standort, an dem die Daten der virtuellen Quellcomputer repliziert werden. Abhängig vom ausgewählten Computerstandort, stellt Site Recovery eine Liste der geeigneten Zielregionen bereit. Es empfiehlt sich, als Zielstandort den gleichen Standort zu verwenden wie für den Recovery Services-Tresor.
    - **Zielressourcengruppe:** Die Ressourcengruppe, zu der alle Ihre replizierten virtuellen Computer gehören. Azure Site Recovery erstellt standardmäßig in der Zielregion eine neue Ressourcengruppe, deren Name das Suffix „asr“ aufweist. Falls bereits eine von Azure Site Recovery erstellte Ressourcengruppe vorhanden ist, wird diese wiederverwendet. Sie können die Gruppe auch anpassen, wie im Abschnitt unten gezeigt.
    - **Virtuelles Zielnetzwerk:** Standardmäßig erstellt Site Recovery in der Zielregion ein neues virtuelles Netzwerk und versieht dessen Name mit dem Suffix „asr“. Dieses wird Ihrem Quellnetzwerk zugeordnet und für alle zukünftigen Schutzaktivitäten verwendet werden. Informationen zur Netzwerkzuordnung finden Sie [hier](site-recovery-network-mapping-azure-to-azure.md).
    - **Zielspeicherkonten:** Standardmäßig erstellt Site Recovery ein neues Zielspeicherkonto und übernimmt dabei die Speicherkonfiguration Ihres virtuellen Quellcomputers. Sollte bereits ein Speicherkonto vorhanden sein, wird dieses wiederverwendet.
    - **Cachespeicherkonten:** Site Recovery benötigt als zusätzliches Speicherkonto in der Quellregion ein so genanntes Cachespeicherkonto. Alle Änderungen an den virtuellen Quellcomputern werden nachverfolgt und vor der Replikation dieser Computer am Zielspeicherort an das Cachespeicherkonto gesendet.
    - **Verfügbarkeitsgruppe:** Standardmäßig erstellt Azure Site Recovery in der Zielregion eine neue Verfügbarkeitsgruppe und versieht deren Name mit dem Suffix „asr“. Falls bereits eine von Azure Site Recovery erstellte Verfügbarkeitsgruppe vorhanden ist, wird diese wiederverwendet.
    - **Replikationsrichtlinie:** Diese Richtlinie definiert die Einstellungen für den Aufbewahrungsverlauf des Wiederherstellungspunkts und die App-konsistente Momentaufnahmenhäufigkeit. Standardmäßig erstellt Azure Site Recovery eine neue Replikationsrichtlinie mit der Standardeinstellung „24 Stunden“ für den Aufbewahrungszeitraum des Wiederherstellungspunkts und „60 Minuten“ für die App-konsistente Momentaufnahmenhäufigkeit.

    ![Aktivieren der Replikation](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Anpassen der Zielressourcen

Sie können die von Site Recovery verwendeten Standardzieleinstellungen ändern.

1. Klicken Sie zum Ändern der Standardeinstellungen auf **Anpassen**:
    - Wählen Sie unter **Zielressourcengruppe** aus der Liste mit allen Ressourcengruppen, die innerhalb des Abonnements am Zielspeicherort vorhanden sind, die gewünschte Ressourcengruppe aus.
    - Wählen Sie unter **Virtuelles Zielnetzwerk** in der Liste mit allen virtuellen Netzwerken am Zielspeicherort das gewünschte Netzwerk aus.
    - Unter **Verfügbarkeitsgruppe** können Sie dem virtuellen Computer Verfügbarkeitsgruppeneinstellungen hinzufügen, sofern dieser einer Verfügbarkeitsgruppe in der Quellregion angehört.
    - Wählen Sie unter **Zielspeicherkonten** das Konto aus, das Sie verwenden möchten.

        ![Aktivieren der Replikation](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Klicken Sie auf **Zielressource erstellen** > **Replikation aktivieren**.
3. Nachdem Sie die Replikation für die virtuellen Computer aktiviert haben, können Sie unter **Replizierte Elemente** den VM-Integritätsstatus überprüfen.

>[!NOTE]
>Die Aktualisierung des Status kann während der Erstreplikation einige Zeit dauern, und zu Beginn wird unter Umständen kein Fortschritt angezeigt. Klicken Sie auf die Schaltfläche **Aktualisieren**, um den aktuellen Status abzurufen.
>

# <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr](site-recovery-test-failover-to-azure.md) über die Ausführung von Testfailovervorgängen.
