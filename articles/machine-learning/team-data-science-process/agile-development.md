---
title: "Agile Entwicklung von Data Science-Projekten – Azure Machine Learning | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Entwickler ein Data Science-Projekt auf systematische Weise mit Versionskontrolle und Zusammenarbeit innerhalb eines Projektteams über den Team Data Science-Prozess ausführen können."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: 686f751b241d49d116948711c683f4b504d5d5f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2017
---
# <a name="agile-development-of-data-science-projects"></a>Agile Entwicklung von Data Science-Projekten

Dieses Dokument beschreibt, wie Entwickler ein Data Science-Projekt auf systematische Weise mit Versionskontrolle und Zusammenarbeit innerhalb eines Projektteams über den [Team Data Science-Prozess](overview.md) (TDSP) ausführen können. Der TDSP ist ein von Microsoft entwickeltes Framework, das eine strukturierte Abfolge von Aktivitäten für die effiziente Ausführung cloudbasierter Predictive Analytics-Lösungen bereitstellt. Unter [Rollen und Aufgaben im Team Data Science-Prozess](roles-tasks.md) finden Sie eine Übersicht über die wichtigsten Mitarbeiterrollen und zugeordneten Aufgaben für ein Data Science-Team, das eine Vereinheitlichung gemäß diesem Prozess anstrebt. 

Dieser Artikel enthält Anweisungen zu folgenden Aufgaben: 

1. Durchführen einer **Sprintplanung** für Arbeitsaufgaben in einem Projekt.<br> Wenn Sie mit der Sprintplanung nicht vertraut sind, finden Sie allgemeine Informationen und Details [hier](https://en.wikipedia.org/wiki/Sprint_(software_development) "hier"). 
2. **Hinzufügen von Arbeitselementen** zu Sprints. 

> [!NOTE]
> Die erforderlichen Schritte zum Einrichten einer TDSP-Teamumgebung mithilfe von Visual Studio Team Services (VSTS) werden in den folgenden Anweisungen beschrieben. Sie geben an, wie diese Aufgaben mit VSTS erledigt werden können, da TDSP auf diese Weise bei Microsoft implementiert wird.  Die Punkte (3) und (4) in der vorherigen Liste sind Vorteile, die Sie mit VSTS immer erhalten. Wenn für Ihre Gruppe eine andere Plattform zum Hosten des Codes verwendet wird, ändern sich die vom Teamleiter auszuführenden Aufgaben in der Regel nicht. Sie müssen aber auf andere Weise ausgeführt werden. Beispielsweise lässt sich der Punkt **Verknüpfen einer Arbeitsaufgabe mit einem Git-Branch** in Abschnitt 6 möglicherweise nicht so einfach umsetzen wie mit VSTS.
>
>

Die folgende Abbildung veranschaulicht einen typischen Workflow mit Sprintplanung, Codierung und Quellcodeverwaltung für die Implementierung eines Data Science-Projekts:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologie 

Im TDSP-Sprintplanungsframework gibt es vier häufig verwendete Typen von **Arbeitselementen**: **Feature**, **User Story**, **Aufgabe** und **Fehler**. Jedes Teamprojekt verwaltet ein einzelnes Backlog für alle Arbeitselemente. Es gibt kein Backlog auf Git-Repositoryebene unter einem Teamprojekt. Hier sind die Definitionen:

- **Feature:** Ein Feature entspricht einem Projektauftrag. Andere Aufträge eines Kunden werden als unterschiedliche Features betrachtet. Ebenso werden verschiedene Phasen eines Projekts bei einem Kunden am besten als unterschiedliche Features betrachtet. Wenn Sie zum Benennen Ihrer Features ein Schema wie ***Kundenname-Auftragsname*** verwenden, können Sie den Kontext des Projekts/Auftrags dem Namen entnehmen.
- **Story:** Storys sind unterschiedliche Arbeitselemente, die zum vollständigen Abschließen eines Features (Projekts) erforderlich sind. Beispiele für Storys sind:
    - Abrufen von Daten 
    - Durchsuchen von Daten 
    - Generieren von Features
    - Erstellen von Modellen
    - Operationalisieren von Modellen 
    - Erneutes Trainieren von Modellen
- **Aufgabe:** Aufgaben sind zuweisbare Code- oder Dokumentarbeitselemente oder andere Aktivitäten, die zum Abschließen einer bestimmten Story abgeschlossen werden müssen. Beispiele für Aufgaben in der Story *Abrufen von Daten* sind:
    -  Abrufen von Anmeldeinformationen von SQL Server 
    -  Hochladen von Daten in SQL Data Warehouse 
- **Fehler:** Als Fehler werden in der Regel Korrekturen bezeichnet, die für einen vorhandenen Code oder ein Dokument erforderlich sind und zum Abschließen einer Aufgabe ausgeführt werden. Wenn der Fehler durch fehlende Phasen oder Aufgaben verursacht wird, kann dies bis zu einer Story oder Aufgabe eskaliert werden. 

> [!NOTE]
> Konzepte werden von Features, Storys, Aufgaben und Fehlern aus der Softwarecodeverwaltung (Software Code Management, SCM) für die Verwendung in Data Science entlehnt. Sie können von den herkömmlichen SCM-Definitionen leicht abweichen.
>
>

> [!NOTE]
> Datenanalysten fühlen sich möglicherweise wohler, wenn sie eine Agile-Vorlage verwenden, die speziell auf die TDSP-Lebenszyklusphasen abgestimmt ist. Zu diesem Zweck wurde eine von Agile abgeleitete Sprintplanungsvorlage erstellt, bei der Epen, Storys usw. durch TDSP-Lebenszyklusphasen oder Unterstufen ersetzt werden. Anleitungen zum Erstellen von Agile-Vorlagen finden Sie unter [Einrichten des Agile Data Science-Prozesses in Visual Studio Online](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Sprintplanung 

Die Sprintplanung ist für die Projektpriorisierung sowie die Ressourcenplanung und -zuordnung nützlich. Viele Datenanalysten sind mit mehreren Projekten beschäftigt, die bis zum Abschluss mehrere Monate dauern können. Projekte schreiten häufig in verschiedenen Geschwindigkeiten voran. Auf dem VSTS-Server können Sie problemlos Arbeitselemente in Ihrem Teamprojekt erstellen, verwalten und nachverfolgen und eine Sprintplanung durchführen, um sicherzustellen, dass Ihre Projekte sich wie erwartet entwickeln. 

Unter [diesem Link](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) finden Sie eine ausführliche Anleitung zur Sprintplanung in VSTS. 


## 3. <a name='AddFeature-3'></a>Hinzufügen eines Features  

Nachdem Ihr Projektrepository in einem Teamprojekt erstellt wurde, wechseln Sie zur Seite **Übersicht** des Teams, und klicken Sie auf **Aufgaben verwalten**.

![2](./media/agile-development/2-sprint-team-overview.png)

Um ein Feature in das Backlog einzuschließen, klicken Sie auf **Backlogs** --> **Features** --> **Neu**, geben Sie den **Titel** des Features (in der Regel den Projektnamen) ein, und klicken Sie dann auf **Hinzufügen**.

![3](./media/agile-development/3-sprint-team-add-work.png)

Doppelklicken Sie auf das Feature, das Sie erstellt haben. Geben Sie die Beschreibungen ein, weisen Sie Teammitglieder für dieses Feature zu, und legen Sie die Planungsparameter für dieses Feature fest. 

Sie können dieses Feature auch mit dem Projektrepository verknüpfen. Klicken Sie auf **Link hinzufügen** im Abschnitt **Entwicklung**. Nachdem Sie mit dem Bearbeiten des Features fertig sind, klicken Sie auf **Speichern und schließen**, um die Bearbeitung zu beenden.


## 4. <a name='AddStoryunderfeature-4'></a>Hinzufügen einer Story unter einem Feature 

Unter dem Feature können Storys hinzugefügt werden, um wichtige erforderliche Schritte zum Abschließen des Projekts (Features) zu beschreiben. Um eine neue Story hinzuzufügen, klicken Sie auf das Symbol **+** neben dem Feature in der Backlogansicht.  

![4](./media/agile-development/4-sprint-add-story.png)

Sie können die Details der Story im Popupfenster bearbeiten, z.B. Status, Beschreibung, Kommentare, Planung und Priorität.

![5](./media/agile-development/5-sprint-edit-story.png)

Sie können diese Story mit einem vorhandenen Repository verknüpfen, indem Sie auf **+ Link hinzufügen** unter **Entwicklung** klicken. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Hinzufügen einer Aufgabe zu einer Story 

Aufgaben sind bestimmte ausführliche Schritte, die zum Abschließen einer Story erforderlich sind. Nachdem alle Aufgaben einer Story abgeschlossen sind, sollte auch die Story abgeschlossen werden. 

Um einer Story eine Aufgabe hinzuzufügen, klicken Sie auf das Symbol **+** neben dem Storyeintrag, wählen Sie **Aufgabe** aus, und geben Sie dann die Detailinformationen für diese Aufgabe im Popupfenster ein.

![7](./media/agile-development/7-sprint-add-task.png)

Nachdem die Features, Storys und Aufgaben erstellt wurden, können Sie in den Ansichten **Backlog** und **Board** ihren Status nachverfolgen.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Einrichten einer Agile-TDSP-Arbeitsvorlage in Visual Studio Online

In diesem Artikel wird erläutert, wie Sie eine Agile Data Science-Prozessvorlage einrichten, die die TDSP Data Science-Lebenszyklusphasen verwendet und Arbeitselemente mit Visual Studio Online (VSO) verfolgt. In den nachstehenden Schritten wird exemplarisch ein Beispiel für das Einrichten der Data Science-spezifischen Agile-Prozessvorlage *AgileDataScienceProcess* durchlaufen, und es wird gezeigt, wie Sie anhand der Vorlage Data Science-Arbeitselemente erstellen.

### <a name="agile-data-science-process-template-setup"></a>Agile Data Science-Prozessvorlageneinrichtung

1. Navigieren Sie zur Serverhomepage, **Konfigurieren** -> **Prozess**.

    ![10](./media/agile-development/10-settings.png) 

2. Navigieren Sie zu **Alle Prozesse** -> **Prozesse** unter **Agile**, und klicken Sie auf **Geerbten Prozess erstellen**. Fügen Sie dann den Prozessnamen „AgileDataScienceProcess“ ein, und klicken Sie auf **Prozess erstellen**.

    ![11](./media/agile-development/11-agileds.png)

3. Deaktivieren Sie auf der Registerkarte **AgileDataScienceProcess** -> **Arbeitsaufgabentypen** die Arbeitsaufgabentypen **Epic**, **Feature**, **User Story** und **Task** über **Konfigurieren -> Deaktivieren**

    ![12](./media/agile-development/12-disable.png)

4. Navigieren Sie zur Registerkarte **AgileDataScienceProcess** -> **Backlogebenen**. Benennen Sie „Epics“ in „TDSP-Projekte“ um, indem Sie auf **Konfigurieren** -> **Bearbeiten/Umbenennen** klicken. Klicken Sie im gleichen Dialogfeld unter „Data Science-Projekt“ auf **+ Neuer Arbeitsaufgabentyp**, und legen Sie den Wert von **Standardmäßiger Arbeitsaufgabentyp** auf „TDSP-Projekt“ fest. 

    ![13](./media/agile-development/13-rename.png)  

5. Ändern Sie entsprechend den Backlognamen „Features“ in „TDSP-Phasen“, und fügen Sie für **Neuer Arbeitsaufgabentyp** Folgendes ein:

    - Geschäftliche Aspekte
    - Datenerfassung
    - Modellierung
    - Bereitstellung

6. Benennen Sie „User Story“ in „TDSP-Unterphasen“ um, wobei der standardmäßige Arbeitsaufgabentyp auf den neu erstellten Typ „TDSP-Unterphase“ festgelegt wird.

7. Legen Sie für „Tasks“ den neu erstellten Arbeitsaufgabentyp „TDSP-Task“ fest. 

8. Nach diesen Schritten sollten folgende Backlogebenen vorhanden sein:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Erstellen von Data Science-Arbeitselementen

Nach dem Erstellen der Data Science-Prozessvorlage können Sie Data Science-Arbeitselemente erstellen, die an den TDSP-Lebenszyklus angelehnt sind.

1. Wenn Sie ein neues Teamprojekt erstellen, wählen Sie „Agile\AgileDataScienceProcess“ als **Arbeitselementprozess** aus:

    ![15](./media/agile-development/15-newproject.png)

2. Navigieren Sie zu dem neu erstellten Teamprojekt, und klicken Sie auf **Arbeit** -> **Backlogs**.

3. Machen Sie „TDSP-Projekte“ sichtbar, indem Sie auf **Teameinstellungen konfigurieren** klicken und „TDSP-Projekte“ aktivieren. Speichern Sie anschließend Ihre Änderungen.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Nun können Sie mit dem Erstellen der Data Science-spezifischen Arbeitselemente beginnen.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Hier ist ein Beispiel dafür, wie die Data Science-Projektarbeitselemente angezeigt werden sollten:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Nächste Schritte

Unter [Gemeinsames Schreiben von Code mit Git](collaborative-coding-with-git.md) wird beschrieben, wie die gemeinschaftliche Codeentwicklung für Data Science-Projekte mithilfe von Git als Entwicklungsframework für freigegebenen Code funktioniert und wie diese Codeentwicklungsaktivitäten mit der mit dem Agile-Prozess geplanten Arbeit verbunden werden.

Hier finden Sie zusätzliche Links zu Ressourcen für Agile-Prozesse.

- Agile-Prozess: [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Arbeitsaufgabentypen und Workflow für den Agile-Prozess: [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Exemplarische Vorgehensweisen, in denen sämtliche Schritte im Prozess für **bestimmte Szenarien** gezeigt werden, sind ebenfalls verfügbar. Sie sind im Artikel [Exemplarische Vorgehensweisen](walkthroughs.md) aufgeführt und mit Miniaturansichtsbeschreibungen verlinkt. Sie zeigen, wie Cloud- und lokale Tools und Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden. 
