---
title: Verwalten Ihres StorSimple-Sicherungskatalogs | Microsoft Docs
description: "Erläutert, wie Sie auf der Seite „Sicherungskatalog“ des StorSimple-Geräte-Manager-Diensts Sicherungssätze auflisten, auswählen und löschen können."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: ac2577c6cd350d6d437d55e61ec73d954cb24893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Verwalten des Sicherungskatalogs mithilfe des StorSimple-Geräte-Manager-Diensts
## <a name="overview"></a>Übersicht
Auf dem Blatt **Sicherungskatalog** des StorSimple-Geräte-Manager-Diensts werden alle Sicherungssätze angezeigt, die mithilfe manueller oder geplanter Sicherungen erstellt wurden. Sie können auf dieser Seite alle Sicherungen für eine Sicherungsrichtlinie oder ein Volume auflisten, Sicherungen auswählen oder löschen oder eine Sicherung zum Wiederherstellen oder Klonen eines Volumes verwenden.

In diesem Tutorial wird erläutert, wie ein Sicherungssatz aufgeführt, ausgewählt und gelöscht wird. Um zu erfahren, wie Sie Ihr Gerät aus einer Sicherung wiederherstellen, gehen Sie zu [Wiederherstellen Ihres Geräts aus einem Sicherungssatz](storsimple-8000-restore-from-backup-set-u2.md). Um zu erfahren, wie Sie ein Volume klonen, gehen Sie zu [Klonen von StorSimple-Volumes](storsimple-8000-clone-volume-u2.md).

![Sicherungskatalog](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

Das Blatt **Sicherungskatalog** bietet eine Abfrage, mit der Sie die Auswahl der Sicherungssätze einschränken können. Sie können die abgerufenen Sicherungssätze anhand der folgenden Parameter filtern:

* **Gerät**: Das Gerät, auf dem der Sicherungssatz erstellt wurde.
* **Sicherungsrichtlinie oder Volume**: Die Sicherungsrichtlinie oder das Volume, der oder dem dieser Sicherungssatz zugeordnet ist.
* **Von und Bis**: Datums- und Uhrzeitbereich, in dem die Sicherung erstellt wurde.

Die gefilterten Sicherungssätze werden dann basierend auf den folgenden Attributen in Tabellenform angezeigt:

* **Name** – der Name der Sicherungsrichtlinie oder des Volumes, der oder dem dieser Sicherungssatz zugeordnet ist.
* **Größe** : die tatsächliche Größe des Sicherungssatzes.
* **Erstellt am** : das Datum und die Uhrzeit der Erstellung der Sicherungen. 
* **Typ** : Sicherungssätze können lokale Momentaufnahmen oder Cloudmomentaufnahmen sein. Eine lokale Momentaufnahme ist eine Sicherung aller Volumedaten, die auf dem lokalen Gerät gespeichert ist, während die Sicherung von Volumedaten in der Cloud als Cloudmomentaufnahme bezeichnet wird. Lokale Momentaufnahmen bieten schnelleren Zugriff, während Cloudmomentaufnahmen für Datenstabilität ausgewählt werden.
* **Initiiert von** : Die Sicherungen können automatisch nach einem Zeitplan oder manuell durch einen Benutzer initiiert werden. Sie können eine Sicherungsrichtlinie verwenden, um Sicherungen zu planen. Es ist aber auch möglich, mithilfe der Option **Sicherung erstellen** eine manuelle Sicherung durchzuführen.

## <a name="list-backup-sets-for-a-backup-policy"></a>Aufführen der Sicherungssätze für eine Sicherungsrichtlinie
Führen Sie die folgenden Schritte aus, um alle Sicherungen für eine Sicherungsrichtlinie aufzuführen.

#### <a name="to-list-backup-sets"></a>So listen Sie Sicherungssätze auf
1. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Sicherungskatalog**.

2. Filtern Sie die Auswahl wie folgt:
   
   1. Geben Sie den Zeitraum an.
   2. Wählen Sie das entsprechende Gerät aus.
   3. Filtern Sie nach **Sicherungsrichtlinie**, um die entsprechenden Sicherungen anzuzeigen.
   3. Wählen Sie in der Dropdownliste „Sicherungsrichtlinie“ **Alle** aus, um alle Sicherungen auf dem ausgewählten Gerät anzuzeigen.
   4. Klicken Sie auf **Anwenden**, um diese Abfrage durchzuführen.
      
      Die der ausgewählten Sicherungsrichtlinie zugeordneten Sicherungen sollten in der Liste der Sicherungssätze angezeigt werden.

      ![Wechseln zum Sicherungskatalog](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Wählen Sie einen Sicherungssatz aus
Führen Sie die folgenden Schritte aus, um einen Sicherungssatz für ein Volume oder eine Sicherungsrichtlinie auszuwählen.

#### <a name="to-select-a-backup-set"></a>Auswählen eines Sicherungssatzes
1. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Sicherungskatalog**.
2. Filtern Sie die Auswahl wie folgt:
   
   1. Geben Sie den Zeitraum an. 
   2. Wählen Sie das entsprechende Gerät aus. 
   3. Filtern Sie nach dem Volume oder der Sicherungsrichtlinie für die gewünschte Sicherung.
   4. Klicken Sie auf **Anwenden**, um diese Abfrage durchzuführen.
      
      Die dem ausgewählten Volume oder der Sicherungsrichtlinie zugeordneten Sicherungen sollten in der Liste der Sicherungssätze angezeigt werden.

      ![Wechseln zum Sicherungskatalog](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Wählen Sie einen Sicherungssatz aus und erweitern Sie diesen. Sie sehen nun die Sicherungssätze aufgeschlüsselt nach den Volumes, die sie enthalten. Die Optionen **Wiederherstellen** und **Löschen** stehen über das (über einen Klick mit der rechten Maustaste zu öffnende) Kontextmenü für den Sicherungssatz zur Verfügung. Sie können beide Aktionen in dem Sicherungssatz durchführen, den Sie ausgewählt haben.

    ![Wechseln zum Sicherungskatalog](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Löschen eines Sicherungssatzes
Löschen Sie eine Sicherung, wenn Sie die zugeordneten Daten nicht mehr beibehalten möchten. Führen Sie die folgenden Schritte aus, um einen Sicherungssatz zu löschen.

#### <a name="to-delete-a-backup-set"></a>Löschen eines Sicherungssatzes
 Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Sicherungskatalog**.
2. Filtern Sie die Auswahl wie folgt:
   
   1. Geben Sie den Zeitraum an. 
   2. Wählen Sie das entsprechende Gerät aus. 
   3. Filtern Sie nach dem Volume oder der Sicherungsrichtlinie für die gewünschte Sicherung.
   4. Klicken Sie auf **Anwenden**, um diese Abfrage durchzuführen.
      
      Die dem ausgewählten Volume oder der Sicherungsrichtlinie zugeordneten Sicherungen sollten in der Liste der Sicherungssätze angezeigt werden.

      ![Wechseln zum Sicherungskatalog](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Wählen Sie einen Sicherungssatz aus und erweitern Sie diesen. Sie sehen nun die Sicherungssätze aufgeschlüsselt nach den Volumes, die sie enthalten. Die Optionen **Wiederherstellen** und **Löschen** stehen über das (über einen Klick mit der rechten Maustaste zu öffnende) Kontextmenü für den Sicherungssatz zur Verfügung. Klicken Sie mit der rechten Maustaste auf den ausgewählten Sicherungssatz, und wählen Sie **Löschen** aus.

    ![Wechseln zum Sicherungskatalog](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

4. Wenn Sie zur Bestätigung aufgefordert werden, überprüfen Sie die angezeigten Informationen, und klicken Sie auf **Löschen**. Die ausgewählte Sicherung wird endgültig gelöscht.

    ![Wechseln zum Sicherungskatalog](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

5. Sie werden benachrichtigt, wenn der Löschvorgang durchgeführt wird und wenn er erfolgreich abgeschlossen wurde. Aktualisieren Sie die Abfrage auf dieser Seite, nachdem der Löschvorgang abgeschlossen wurde. Der gelöschte Sicherungssatz wird nicht mehr in der Liste der Sicherungssätze angezeigt.

    ![Wechseln zum Sicherungskatalog](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Nächste Schritte
* Informationen zur [Verwendung des Sicherungskatalogs zum Wiederherstellen Ihres Geräts aus einem Sicherungssatz](storsimple-8000-restore-from-backup-set-u2.md).
* Erfahren Sie, wie Sie [Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts verwalten](storsimple-8000-manager-service-administration.md).

