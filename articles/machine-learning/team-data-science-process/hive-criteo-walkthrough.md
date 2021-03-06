---
title: 'Der Team Data Science-Prozess in Aktion: Verwenden von Azure HDInsight Hadoop-Clustern in einem 1-TB-Dataset | Microsoft-Dokumentation'
description: "Verwenden des Team Data Science-Prozesses für ein vollständiges Szenario mit einem HDInsight Hadoop-Cluster zum Erstellen und Bereitstellen eines Modells unter Verwendung eines (1 TB) großen öffentlich zugänglichen Datasets"
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 72d958c4-3205-49b9-ad82-47998d400d2b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev
ms.openlocfilehash: 760e08643fb3e71478fc899278591569da1d515b
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2017
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Der Team Data Science-Prozess in Aktion: Verwenden von Azure HDInsight Hadoop-Clustern in einem 1-TB-Dataset

In dieser exemplarischen Vorgehensweise wird das Verwenden des Team Data Science-Prozesses in einem vollständigen Szenario mit einem [Azure HDInsight Hadoop-Cluster](https://azure.microsoft.com/services/hdinsight/) gezeigt, der zum Speichern, Untersuchen, Entwickeln von Features und Downsampling von Beispieldaten aus einem der öffentlich zugänglichen [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/)-Datasets genutzt wird. Mithilfe von Azure Machine Learning wird ein binäres Klassifizierungsmodell für diese Daten erstellt. Zudem erfahren Sie, wie eines dieser Modelle als Webdienst veröffentlicht wird.

Die in dieser exemplarischen Vorgehensweise vorgestellten Aufgaben können auch mit einem IPython-Notizbuch umgesetzt werden. Benutzer, die diesem Ansatz folgen möchten, sollten das Thema [Criteo walkthrough using a Hive ODBC connection](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) (in englischer Sprache) beachten.

## <a name="dataset"></a>Beschreibung des Criteo-DataSets
Bei den Criteo-Daten handelt es sich um ein Klickvorhersage-DataSet mit etwa 370 GB an gzip-komprimierten TSV-Dateien (unkomprimiert ca. 1,3 TB), das aus über 4,3 Milliarden Datensätzen besteht. Es beruht auf den Klickdaten für 24 Tage, die von [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) bereitgestellt werden. Für Datenanalysten stehen zudem unkomprimierte Daten für Experimente zur Verfügung.

Alle Datensätze im DataSet enthalten je 40 Spalten:

* Die erste Spalte ist eine Bezeichnungsspalte, die angibt, ob ein Benutzer auf eine **Anzeige** klickt (Wert „1“) oder nicht (Wert „0“).
* Die nächsten 13 Spalten sind numerische und
* die letzten 26 nach Kategorien sortierte Spalten.

Die Spalten werden anonymisiert und mit Aufzählungsnamen versehen: "Col1" (für die Bezeichnungsspalte) bis "Col40" (für die letzte Kategoriespalte).            

Hier finden Sie einen Auszug der ersten 20 Spalten der zwei Beobachtungen (Zeilen) aus diesem DataSet:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Sowohl in den numerischen als auch den Kategoriespalten dieses DataSets fehlen Werte. Eine einfache Methode zum Umgang mit den fehlenden Werten wird beschrieben. Weitere Details der Daten werden beim Speichern in Hive-Tabellen untersucht.

**Definition:***Durchklickrate (Clickthrough Rate, CTR):* Dies ist der Prozentsatz an Klicks in den Daten. In diesem Criteo-DataSet beträgt die CTR etwa 3,3 % oder 0,033.

## <a name="mltasks"></a>Beispiele für Vorhersageaufgaben
In dieser exemplarischen Vorgehensweise werden zwei beispielhafte Vorhersageprobleme behandelt:

1. **Binäre Klassifikation:**sagt vorher, ob ein Benutzer auf eine Anzeige geklickt hat:
   
   * Klasse 0: Kein Klick
   * Klasse 1: Klick
2. **Regression:**sagt anhand der Benutzerfunktionen die Wahrscheinlichkeit eines Anzeigenklicks vorher.

## <a name="setup"></a>Einrichten eines HDInsight Hadoop-Clusters für Data Science
**Hinweis**: Dies ist normalerweise eine **Admin**-Aufgabe.

Richten Sie Ihre Azure Data Science-Umgebung ein, um in drei Schritten Lösungen für Vorhersageanalysen mit HDInsight-Clustern zu erstellen:

1. [Erstellen eines Speicherkontos:](../../storage/common/storage-create-storage-account.md)Mit diesem Speicherkonto werden Daten im Azure-Blob-Speicher gespeichert. Die in HDInsight-Clustern verwendeten Daten werden hier gespeichert.
2. [Anpassen von Azure HDInsight Hadoop-Clustern für Data Science:](customize-hadoop-cluster.md)Mit diesem Schritt wird ein Azure HDInsight Hadoop-Cluster mit 64-Bit-Anaconda Python 2.7 auf allen Knoten erstellt. Beim Anpassen des HDInsight-Clusters müssen zwei (in diesem Thema beschriebene) wichtige Schritte durchgeführt werden.
   
   * Verknüpfen Sie das in Schritt 1 erstellte Speicherkonto mit dem HDInsight-Cluster. Mit diesem Speicherkonto wird auf Daten zugegriffen, die innerhalb des Clusters verarbeitet werden können.
   * Sie müssen nach dem Erstellen den Remotezugriff auf den Hauptknoten des Clusters aktivieren. Merken Sie sich die hier angegebenen RAS-Anmeldeinformationen (die sich von denen beim Erstellen des Clusters unterscheiden). Sie benötigen diese später, um die folgenden Schritte auszuführen.
3. [Erstellen eines Azure ML-Arbeitsbereichs:](../studio/create-workspace.md)Mit diesem Azure Machine Learning-Arbeitsbereich werden nach dem erstmaligen Untersuchen der Daten und der Komprimierung im HDInsight-Cluster Machine Learning-Modelle erstellt.

## <a name="getdata"></a>Abrufen und Verwenden von Daten aus einer öffentlichen Quelle
Um auf das [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) -DataSet zuzugreifen, klicken Sie auf den Link, akzeptieren Sie die Nutzungsbedingungen, und geben Sie einen Namen an. Hier sehen Sie eine Momentaufnahme:

![Criteo-Bestimmungen akzeptieren](./media/hive-criteo-walkthrough/hLxfI2E.png)

Klicken Sie auf **Download fortsetzen** , um weitere Informationen über das DataSet und seine Verfügbarkeit zu erhalten.

Die Daten befinden sich an einem öffentlichen [Azure Blob Storage](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)-Speicherort: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" bezieht sich auf den Azure-Blob-Speicherort. 

1. Die Daten in diesem öffentlichen Blob-Speicher bestehen aus drei untergeordneten Ordnern mit extrahierten Daten.
   
   1. Der Unterordner *raw/count/* enthält die Daten der ersten 21 Tage – von „day\_00“ bis „day\_20“.
   2. Der Unterordner *raw/train/* enthält nur die Daten des Tages „day\_21“.
   3. Der Unterordner *raw/test/* enthält die Daten der beiden Tage „day\_22“ und „day\_23“.
2. Wenn Sie mit den gzip-Rohdaten beginnen möchten, finden Sie diese im Hauptordner */raw* als „day_NN.gz“, wobei „NN“ von 00 bis 23 reicht.

Ein alternativer Ansatz für das Zugreifen, Untersuchen und Modellieren, der keine lokalen Downloads erfordert, wird später in dieser exemplarischen Vorgehensweise erläutert, wenn wir die Hive-Tabellen erstellen.

## <a name="login"></a>Anmelden beim Cluster-Hauptknoten
Um sich beim Hauptknoten des Clusters anzumelden, suchen Sie im [Azure-Portal](https://ms.portal.azure.com) den Cluster. Klicken Sie links auf das HDInsight-Elefantensymbol, und doppelklicken Sie anschließend auf den Namen des Clusters. Navigieren Sie zur Registerkarte **Konfiguration** , doppelklicken Sie unten auf der Seite auf das Verbindungssymbol, und geben Sie Ihre RAS-Anmeldeinformationen ein, wenn Sie dazu aufgefordert werden. Dadurch gelangen Sie zum Hauptknoten des Clusters.

Im Folgenden wird eine typische Erstanmeldung am Cluster-Hauptknoten dargestellt:

![Im Cluster anmelden](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Auf der linken Seite sehen Sie die „Hadoop-Befehlszeile“, mit der wir die Daten untersuchen werden, sowie zwei nützliche URLs: „Hadoop Yarn Status“ und „Hadoop Name Node“. Erstere führt zum Auftragsstatus, während Sie über die zweite zu Einzelheiten über die Clusterkonfiguration gelangen.

Nun sind Sie bereit für den ersten Teil der exemplarischen Vorgehensweise: Das Untersuchen der Daten mithilfe von Hive und das Vorbereiten der Daten für Azure Machine Learning.

## <a name="hive-db-tables"></a> Erstellen von Hive-Datenbanken und -Tabellen
Öffnen Sie zum Erstellen von Hive-Tabellen für das Criteo-DataSet auf dem Desktopcomputer des Hauptknotens die ***Hadoop-Befehlszeile***, und wechseln Sie mithilfe des folgenden Befehls zum Hive-Verzeichnis.

    cd %hive_home%\bin

> [!NOTE]
> Führen Sie alle Hive-Befehle dieser exemplarischen Vorgehensweise über die Eingabeaufforderung im Hive-Verzeichnis „bin/“ aus. So werden alle Pfadprobleme automatisch behoben. Die hier verwendeten Begriffe „Hive-Eingabeaufforderung“, „Hive-Eingabeaufforderung im bin-Verzeichnis“ und „Hadoop-Befehlszeile“ sind austauschbar.
> 
> [!NOTE]
> Um alle Hive-Abfragen auszuführen, können Sie einen der folgenden Befehle verwenden:
> 
> 

        cd %hive_home%\bin
        hive

Wenn Hive REPL mit dem Symbol "hive >" angezeigt wird, können Sie die Abfrage einfach ausschneiden und einfügen, um sie auszuführen.

Mit dem folgendem Code werden die Datenbank „criteo“ und anschließend vier Tabellen erstellt:

* eine *Zahlentabelle anhand der Tage* „day\_00“ bis „day\_20“,
* eine auf „day\_21“ *beruhende Trainingstabelle* und
* zwei auf „day\_22“ und „day\_23“ *beruhende Testtabellen*.

Teilen Sie das Testdataset in zwei verschiedene Tabellen auf, da einer der Tage ein Feiertag ist. Sie möchten ermitteln, ob das Modell anhand der Klickrate zwischen Feier- und Arbeitstagen unterscheiden kann.

Hier wird als Referenz das Skript [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) angeführt:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Da all diese Tabellen extern sind, können Sie einfach auf ihren Azure Blob Storage-Speicherorte (wasb) verweisen.

**ALLE Hive-Abfragen können auf zwei Arten ausgeführt werden:**

1. **Über die Hive REPL-Befehlszeile:**Zunächst wird ein „hive“-Befehl ausgegeben sowie eine Abfrage kopiert und in die Hive REPL-Befehlszeile kopiert. Gehen Sie hierfür folgendermaßen vor:
   
        cd %hive_home%\bin
        hive
   
     Wenn Sie die Abfrage nun in der REPL-Befehlszeile ausschneiden und einfügen, wird sie ausgeführt.
2. **Speichern von Abfragen in einer Datei und Ausführen des Befehls**: Die zweiten Methode besteht im Speichern der Abfragen in einer HQL-Datei ([sample&amp;#95;hive&amp;#95;create&amp;#95;criteo&amp;#95;database&amp;#95;and&amp;#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)). Anschließend wird zum Ausführen der Abfrage folgender Befehl ausgegeben:
   
        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Bestätigen des Erstellens der Datenbank und der Tabellen
Anschließend bestätigen Sie, dass die Datenbank erstellt wurde, indem Sie den folgenden Befehl in der Eingabeaufforderung im Hive-Verzeichnis „/bin“ eingeben:

        hive -e "show databases;"

Dies ergibt:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Hiermit wird das Erstellen der neuen Datenbank "criteo" bestätigt.

Um zu ermitteln, welche Tabellen erstellt wurden, geben Sie einfach den folgenden Befehl in der Eingabeaufforderung im Hive-Verzeichnis „/bin“ aus:

        hive -e "show tables in criteo;"

Dann wird die folgende Ausgabe angezeigt:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a> Untersuchen von Daten in Hive
Nun können Sie einige grundlegende Datenuntersuchungen in Hive vornehmen. Zunächst zählen Sie die Anzahl der Beispiele in den Trainings- und Testdatentabellen.

### <a name="number-of-train-examples"></a>Anzahl der Trainingsbeispiele
Hier werden die Inhalte für [sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) dargestellt:

        SELECT COUNT(*) FROM criteo.criteo_train;

Dies ergibt:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Sie können auch den folgenden Befehl in der Eingabeaufforderung im Hive-Verzeichnis „/bin“ ausgeben:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Anzahl der Testbeispiele in den beiden Test-DataSets
Nun zählen Sie die Anzahl der Beispiele in den beiden Testdatasets. Hier werden die Inhalte für [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) dargestellt:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Dies ergibt:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Wie immer können Sie das Skript zudem über die Hive-Eingabeaufforderung „/bin“ aufrufen, indem wir diesen Befehl ausgeben:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Abschließend untersuchen Sie die Anzahl der Testbeispiele im Testdataset für „day\_23“.

Der hierfür verwendete Befehl ähnelt dem soeben angezeigten (siehe [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Dies ergibt:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Bezeichnungsverteilung im Trainings-DataSet
Die Bezeichnungsverteilung im Trainings-DataSet ist von Interesse. Hierzu führen Sie die Inhalte von [sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql) an:

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Dies ergibt die Bezeichnungsverteilung:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Beachten Sie, dass der Prozentsatz der positiven Bezeichnungen etwa 3,3 % beträgt (in Übereinstimmung mit dem ursprünglichen DataSet).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogrammverteilungen für einige numerische Variablen des Trainings-DataSets
Mit der nativen Funktion „histogram\_numeric“ von Hive können Sie herausfinden, wie die Verteilung numerischer Variablen aussieht. Hier sehen Sie die Inhalte von [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Dadurch ergibt sich Folgendes:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

Die Kombination von Seitenansicht und Auflösung in Hive ermöglicht anstelle der üblichen Liste eine SQL-ähnliche Ausgabe. Beachten Sie, dass in dieser Tabelle die erste Spalte dem Lagerplatzmittelwert und die zweite der Lagerplatzhäufigkeit entspricht.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Ungefähre Prozentwerte für einige numerische Variablen des Trainings-DataSets
Im Zusammenhang mit numerischen Variablen ist auch die Berechnung der ungefähren Prozentwerte interessant. Dies wird von der systemeigenen Hive-Funktion „percentile\_approx“ übernommen. Die Inhalte von [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) lauten:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Dies ergibt:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Die Verteilung der Prozentwerte in der Regel hängt eng mit der Histogrammverteilung beliebiger numerischer Variablen zusammen.         

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Ermitteln der Anzahl der eindeutigen Werte für bestimmte Kategoriespalten im Trainings-DataSet
Im weiteren Verlauf unserer Datenuntersuchung ermitteln Sie nun für einige Kategoriespalten die Anzahl der übernommenen eindeutigen Werte. Führen Sie hierzu die Inhalte von [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql) an:

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Dies ergibt:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Beachten Sie, dass „Col15“ über 19 Millionen eindeutige Werte verfügt! Mit systemeigenen Techniken wie z. B. „one-hot-encoding“ können derartige Mengen an Kategorievariablen nicht sinnvoll codiert werden. Die leistungsfähige, zuverlässige Technik [Lernen durch Anzahl](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) erläutert und veranschaulicht den effizienten Umgang mit diesem Problem.

Prüfen Sie zum Schluss die Anzahl von eindeutigen Werten für einige weitere Kategoriespalten. Die Inhalte von [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) lauten:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Dies ergibt:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Mit Ausnahme von „Col20“ enthalten alle Spalten viele eindeutige Werte.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Zusammentreffende Anzahlen von Kategorievariablenpaaren im Trainings-DataSet

Auch zusammentreffende Anzahlen von Kategorievariablenpaaren sind von Interesse. Dies kann mit dem Code in [sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql) ermittelt werden:

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Kehren Sie die Anzahlwerte anhand des Auftretens um, und prüfen Sie in diesem Fall die ersten 15. Dies ergibt:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Komprimieren von DataSets für Azure Machine Learning
Nachdem Sie die DataSets untersucht und aufgezeigt haben, wie Sie diese Art der Untersuchung für beliebige Variablen (einschließlich Kombinationen) durchführen können, komprimieren Sie die Datasets, damit Sie in Azure Machine Learning Modelle erstellen können. Denken Sie an unser Hauptthema: Sie sagen anhand von Beispielattributen (Funktionswerte von „Col2“ bis „Col40“) vorher, ob „Col1“ den Wert „0“ (kein Klick) oder „1“ (Klick) ergibt.

Um Ihre Trainings- und Testdatasets auf 1% der ursprünglichen Größe zu komprimieren, verwenden Sie die systemeigene Hive-Funktion „RAND()“. Dies wird vom folgenden Skript für das Trainingsdataset übernommen: [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql):

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Dies ergibt:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

Das Skript [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) gilt für die Testdaten für "day\_22":

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Dies ergibt:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Und abschließend verfügen wir für die Testdaten für „day\_23“ über das Skript [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql):

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Dies ergibt:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Hiermit können Sie die komprimierten Trainings- und Testdatasets nun zum Erstellen von Modellen in Azure Machine Learning einsetzen.

Bevor Sie in Azure Machine Learning fortfahren, müssen Sie eine letzte wichtige Komponente in Bezug auf die Zahlentabelle beachten. In den nächsten Unterabschnitt wird die Zahlentabelle ausführlich erläutert.

## <a name="count"></a> Eine kurze Erläuterung der Zahlentabelle
Wie Sie gesehen haben, sind mehrere Kategorievariablen äußerst umfangreich. In dieser exemplarischen Vorgehensweise wird die leistungsfähige Technik [Lernen durch Anzahl](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) vorgestellt, mit der diese Variablen effizient und stabil codiert werden. Weitere Informationen zu dieser Technik finden Sie über den angegebenen Link.

[!NOTE]
>Der Schwerpunkt dieser exemplarischen Vorgehensweise liegt auf der Verwendung von Zahlentabellen zum Erstellen kompakter Darstellungen umfangreicher Kategoriefunktionen. Dies ist nicht die einzige Möglichkeit, Kategoriefunktionen zu codieren. Interessierte Benutzer können sich über anderen Verfahren wie [One-hot-encoding](http://en.wikipedia.org/wiki/One-hot) und [Feature hashing](http://en.wikipedia.org/wiki/Feature_hashing) (beide Artikel in englischer Sprache) informieren.
>

Verwenden Sie zum Erstellen von Zahlentabellen aus den Zahlendaten die Daten im Ordner „raw/count“. Im Abschnitt „Modellierung“ erfahren Sie, wie diese Zahlentabellen für Kategoriefunktionen neu erstellt oder vorgefertigte Zahlentabellen für Untersuchungen eingesetzt werden. Im folgenden bedeutet „vordefinierte Zahlentabellen“, dass die bereitgestellten Zahlentabellen verwendet werden. Im nächsten Abschnitt finden Sie ausführliche Anweisungen für den Zugriff auf diese Tabellen.

## <a name="aml"></a> Erstellen eines Modells mit Azure Machine Learning
Beim Erstellen von Modellen in Azure Machine Learning führen wir diese Schritte aus:

1. [Abrufen der Daten aus Hive-Tabellen in Azure Machine Learning](#step1)
2. [Erstellen des Experiments: Bereinigen der Daten und Ausstatten mit Funktionen mithilfe von Zahlentabellen](#step2)
3. [Erstellen, Trainieren und Bewerten des Modells](#step3)
4. [Bewerten des Modells](#step4)
5. [Veröffentlichen des Modells als Webdienst](#step5)

Nun können Sie Modelle in Azure Machine Learning Studio erstellen. Unsere komprimierten Daten wurden als Hive-Tabellen im Cluster gespeichert. Verwenden Sie zum Lesen dieser Daten das **Import Data**-Modul von Azure Machine Learning. Im Folgenden finden Sie die Anmeldeinformationen für den Zugriff auf das Speicherkonto für diesen Cluster.

### <a name="step1"></a> Schritt 1: Abrufen von Daten aus den Hive-Tabellen in Azure Machine Learning mit dem „Import Data“-Modul und Auswählen für ein Computerexperiment
Starten Sie durch Auswählen von **+NEW** -> **EXPERIMENT** -> **Blank Experiment**. Suchen Sie dann über das Feld **Suche** oben links nach „Daten importieren“. Legen Sie das Modul **Daten importieren** durch Drag &amp; Drop auf der Experimentcanvas ab (der mittlere Teil des Bildschirms), um das Modul für den Datenzugriff zu verwenden.

So sieht **Import Data** beim Abrufen von Daten aus der Hive-Tabelle aus:

![„Import Data“ ruft Daten ab](./media/hive-criteo-walkthrough/i3zRaoj.png)

Für das Modul **Daten importieren** sind die Werte der in der Grafik enthaltenen Parameter nur Beispiele für Werte, die Sie bereitstellen müssen. Im Folgenden finden Sie allgemeine Anleitungen zum Ausfüllen des Parametersatzes für das Modul **Daten importieren** .

1. Auswählen von „Hive-Abfrage“ als **Datenquelle**
2. Im Feld **Hive database query** reicht „SELECT * FROM <Name_Ihrer_\_Datenbank\_.Name_Ihrer\_Tabelle\_> -“ aus.
3. **Hcatalog-Server-URI**: Wenn Ihr Cluster „abc“ ist, lautet dieser einfach: https://abc.azurehdinsight.net
4. **Hadoop user account name**: Der bei der Bereitstellung des Clusters ausgewählte Benutzername. (NICHT der Benutzername für den Remotezugriff!)
5. **Hadoop-Benutzerkontokennwort**: Das Kennwort für den bei der Bereitstellung des Clusters ausgewählten Benutzernamen. (NICHT das Kennwort für den Remotezugriff!)
6. **Speicherort der Ausgabedaten**: Wählen Sie „Azure“ aus.
7. **Azure-Speicherkontoname**: Das dem Cluster zugeordnete Speicherkonto
8. **Azure-Speicherkontoschlüssel**: Der dem Cluster zugeordnete Speicherschlüssel
9. **Azure-Containername**: Wenn der Clustername „abc“ ist, gilt in der Regel einfach „abc“.

Sobald **Import Data** das Abrufen von Daten beendet (das grüne Häkchen für das Modul wird angezeigt), speichern Sie diese Daten als DataSet (mit einem Namen Ihrer Wahl). Dies sieht folgendermaßen aus:

![„Import Data“ speichert Daten](./media/hive-criteo-walkthrough/oxM73Np.png)

Klicken Sie mit der rechten Maustaste auf den Ausgabeport des **Import Data** -Moduls. Es werden die Optionen **Save as dataset** und **Visualize** angezeigt. Wenn die Option **Visualisieren** ausgewählt wurde, werden 100 Datenzeilen sowie rechts eine für einige zusammenfassende Statistiken nützliche Leiste angezeigt. Um Daten zu speichern, wählen Sie einfach **Als Dataset speichern** aus, und befolgen Sie die Anweisungen.

Um das gespeicherte DataSet für die Verwendung in einem Machine Learning-Experiment zu verwenden, suchen Sie die DataSets mithilfe des nachfolgend angezeigten Felds **Suche** . Geben Sie dann einfach teilweise den Namen ein, den Sie dem DataSets gegeben haben, um darauf zuzugreifen, und ziehen es in den Hauptbereich. Durch das Ziehen in den Hauptbereich wird es zur Verwendung für die Machine Learning-Modellierung ausgewählt.

![Ziehen des Datasets in den Hauptbereich](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Dies gilt sowohl für Trainings- als auch Test-DataSets. Achten Sie außerdem darauf, den Datenbanknamen und die Tabellennamen zu verwenden, die Sie für diesen Zweck angegeben haben. Die in der Abbildung verwendeten Werte dienen lediglich zur Veranschaulichung.\**\
> 
> 

### <a name="step2"></a> Schritt 2: Erstellen eines einfachen Experiments in Azure Machine Learning, um Klicks/keine Klicks vorherzusagen
Unser Azure ML-Experiment sieht wie folgt aus:

![Machine Learning-Experiment](./media/hive-criteo-walkthrough/xRpVfrY.png)

Prüfen Sie nun die Hauptkomponenten dieses Experiments. Ziehen Sie zuerst unsere gespeicherten Trainings- und Testdatasets in den Experimentbereich.

#### <a name="clean-missing-data"></a>Fehlende Daten bereinigen
Das anpassbare **Clean Missing Data**-Modul bereinigt fehlende Daten. Die Methode wird vom Benutzer festgelegt. In diesem Modul sehen Sie dies:

![Fehlende Daten bereinigen](./media/hive-criteo-walkthrough/0ycXod6.png)

Ersetzen Sie alle fehlenden Werte durch „0“. Es gibt weitere Optionen, die mithilfe der Dropdownlisten im Modul angezeigt werden.

#### <a name="feature-engineering-on-the-data"></a>Funktionsverarbeitung der Daten
Für einige kategorische Features von großen DataSets können Millionen von eindeutigen Werten vorhanden sein. Die Verwendung naiver Methoden wie One-Hot-Codierung für die Darstellung solcher hochdimensionaler Funktionen ist völlig unmöglich. In dieser exemplarischen Vorgehensweise wird demonstriert, wie in Azure Machine Learning integrierte Zählfunktionen zum Generieren von kompakten Darstellungen dieser hochdimensionalen kategorischen Variablen verwendet werden. Das Endergebnis ist eine geringere Modellgröße, kürzere Trainingszeiten und Leistungsdaten, die mit denen anderer Techniken vergleichbar sind.

##### <a name="building-counting-transforms"></a>Erstellen von Zähltransformationen
Zum Erstellen von Zählfunktionen verwenden Sie das **Build Counting Transform**-Modul in Azure Machine Learning. Das Modul sieht wie folgt aus:

![Build Counting Transform-Modul](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![Build Counting Transform-Modul](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT] 
> Geben Sie im Feld **Count columns** die Spalten ein, die Sie zählen möchten. In der Regel handelt es sich dabei (wie bereits erwähnt) um hochdimensionale kategorische Spalten. Bedenken Sie, dass das Criteo-DataSet über 26 Kategoriespalten verfügt: von Col15 bis Col40. Führen Sie für alle eine Zählung durch, und geben Sie die Indizes (von 15 bis 40, durch Kommas getrennt, wie gezeigt) an.
> 

Zur Verwendung des Moduls im MapReduce-Modus (geeignet für große DataSets) benötigen Sie Zugriff auf einen HDInsight Hadoop-Cluster (dazu kann auch der zum Durchsuchen von Funktionen genutzte Cluster verwendet werden) und die Anmeldeinformationen. Die vorigen Abbildungen veranschaulichen die ausgefüllten Werte (ersetzen Sie die Beispielwerte durch die entsprechenden Werte für Ihren Anwendungsfall).

![Modulparameter](./media/hive-criteo-walkthrough/05IqySf.png)

In der Abbildung oben wird das Eingeben des Eingabe-Blob-Speicherorts gezeigt. Dieser Speicherort enthält die Daten, die zum Erstellen von Zähltabellen reserviert sind.

Speichern Sie die Transformation, wenn die Ausführung des Moduls abgeschlossen ist. Klicken Sie dazu mit der rechten Maustaste auf das Modul, und wählen Sie die Option **Als Transformation speichern**:

![Option „Als Transformation speichern“](./media/hive-criteo-walkthrough/IcVgvHR.png)

In unserer oben gezeigten Experimentarchitektur entspricht das DataSet „ytransform2“ genau einer gespeicherten Zähltransformation. Für den Rest dieses Experiments wird angenommen, dass der Reader ein **Build Counting Transform**-Modul für einige Daten zum Generieren von Zahlen verwendet und mit diesen dann Zählfunktionen für die Trainings- und Testdatasets generiert.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Auswählen der Zählfunktionen, die in Trainings- und Test-DataSets aufgenommen werden
Sobald eine Zähltransformation bereitsteht kann der Benutzer auswählen, welche Funktionen mithilfe des **Modify Count Table Parameters**-Moduls in seine Trainings- und Testdatasets aufgenommen werden. Dieses Modul wird der Vollständigkeit halber hier angezeigt. Aus Gründen der Einfachheit verwenden Sie es jedoch nicht in unserem Experiment.

![Modify Count Table Parameters](./media/hive-criteo-walkthrough/PfCHkVg.png)

In diesem Fall gesehen müssen die LoggOdds verwendet werden, und die „back off“-Spalte wird ignoriert. Sie können auch Parameter festlegen, z.B. den Schwellenwert für Papierkorb, die Anzahl der hinzuzufügenden vorhergehenden Pseudobeispiele für die Glättung, und ob Laplace-Rauschen verwendet wird. Dabei handelt es sich um erweiterte Funktionen, und es ist darauf hinzuweisen, dass die Standardwerte ein guter Ausgangspunkt für Benutzer sind, die noch nicht mit dieser Art von Funktionsgenerierung vertraut sind.

##### <a name="data-transformation-before-generating-the-count-features"></a>Datentransformation vor dem Generieren der Zählfunktionen
Jetzt liegt der Fokus auf einem wichtigen Aspekt der Transformation unserer Trainings- und Testdaten vor dem tatsächlichen Generieren der Zählfunktionen. Beachten Sie, dass zwei **Execute R Script**-Module verwendet werden, bevor die Zähltransformation auf unsere Daten angewendet werden.

![Execute R Script-Module](./media/hive-criteo-walkthrough/aF59wbc.png)

Hier ist das erste R-Skript:

![Erstes R-Skript](./media/hive-criteo-walkthrough/3hkIoMx.png)

Dieses R-Skript benennt unsere Spalten in „Col1“ bis „Col40“ um, da die Zähltransformation Namen dieses Formats erwartet.

Im zweiten R-Skript gleicht es die Verteilung zwischen positiven und negativen Klassen (Klasse 1 bzw. 0) durch die Verkleinerung der negativen Klasse aus. Dieser Vorgang wird anhand dieses R-Skripts veranschaulicht:

![Zweites R-Skript](./media/hive-criteo-walkthrough/91wvcwN.png)

In diesem einfachen R-Skript wird „pos\_neg\_ratio“ verwendet, um den Ausgleich zwischen den positiven und den negativen Klassen festzulegen. Dies ist wichtig, da das Verringern der Diskrepanz zwischen Klassen in der Regel Leistungsvorteile bei Klassifizierungsproblemen mit sich bringt, wenn die Klassenverteilung verzerrt ist (beachten Sie, dass in diesem Fall 3,3% positive und 96,7% negative Klassen vorhanden sind).

##### <a name="applying-the-count-transformation-on-our-data"></a>Anwenden der Zähltransformation auf unsere Daten
Schließlich können Sie das Modul **Apply Transformation** zum Anwenden der Zähltransformationen auf unsere Trainings- und Testdatasets verwenden. Dieses Modul nimmt die gespeicherte Zähltransformation als eine Eingabe und die Trainings- oder Test-DataSets als die andere Eingabe auf und gibt Daten mit Zählfunktionen zurück. Dies wird hier gezeigt:

![Apply Transformation-Modul](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Ein Auszug des Erscheinungsbilds der Zählfunktionen
Es ist hilfreich zu sehen, wie die Zählfunktionen in unserem Fall aussehen. Hier sehen Sie einen Auszug dieser Funktion:

![Zählfunktionen](./media/hive-criteo-walkthrough/FO1nNfw.png)

In diesem Ausschnitt wird gezeigt, dass Sie die Spalten, für die Sie Zählungen durchgeführt haben, neben den relevanten Backoffs auch die Zahlen und logarithmischen Wahrscheinlichkeiten erhalten.

Mit diesen transformierten Datasets können Sie nun ein Azure Machine Learning-Modell erstellen. Die Vorgehensweise wird im nächsten Abschnitt gezeigt.

### <a name="step3"></a> Schritt 3: Erstellen, Trainieren und Bewerten des Modells

#### <a name="choice-of-learner"></a>Auswahl des Lerners
Zunächst müssen Sie einen Lerner auswählen. Verwenden Sie eine verstärkte Entscheidungsstruktur mit zwei Klassen als Lerner. Dies sind die Standardoptionen für diesen Lerner:

![Two-Class Boosted Decision Tree-Parameter](./media/hive-criteo-walkthrough/bH3ST2z.png)

Wählen Sie die Standardwerte für das Experiment. Die Standardwerte sind in der Regel aussagekräftig und eine gute Möglichkeit zum schnellen Erzielen einer Leistungsbasis. Sie können die Leistung verbessern, indem Sie Parameter auswählen, sobald Sie eine Basislinie haben.

#### <a name="train-the-model"></a>Modelltraining
Für das Training rufen Sie einfach ein **Train Model**-Modul auf. Bei den zwei Eingaben handelt es sich um die  verstärkte Entscheidungsstruktur mit zwei Klassen und unser Trainings-DataSet. Dies wird hier gezeigt:

![Train Model-Modul](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Bewertung des Modells
Sobald Sie über ein Trainingsmodell verfügen, können Sie das Testdataset und dessen Leistung bewerten. Verwenden Sie dazu das nachfolgend gezeigte **Score Model**-Modul mit einem **Evaluate Model**-Modul:

![Modul Modell bewerten](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> Schritt 4: Bewerten des Modells
Nun müssen Sie die Modelleistung analysieren. Wenn bei zwei Klassen (binäre) Klassifizierungsprobleme auftreten, eignet sich AUC gewöhnlich als Maßstab. Ordnen Sie zum Visualisieren das **Score Model**-Modul einem **Evaluate Model**-Modul zu. Wenn Sie im Modul **Evaluate Model** auf **Visualize** klicken, ergibt dies ungefähr folgende Grafik:

!["Evaluate"-Modul für BDT-Modell](./media/hive-criteo-walkthrough/0Tl0cdg.png)

Bei binären (oder zweiklassigen) Klassifizierungsproblemen, eignet sich AUC (Area Under Curve, Bereich unter der Kurve) als guter Maßstab für die Vorhersagegenauigkeit. Im folgenden Abschnitt finden Sie die Ergebnisse dieses Modells für unser Testdataset. Um diese zu erhalten, klicken mit der rechten Maustaste auf den Ausgangsport des Moduls **Evaluate Model** und anschließend auf **Visualize**.

!["Visualize Evaluate Model"-Modul](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> Schritt 5: Veröffentlichen des Modells als Webdienst
Die Möglichkeit zum Veröffentlichen eines Azure Machine Learning-Modells als Webdienst ohne hohen Aufwand ist eine nützliche Funktion, um es allgemein verfügbar zu machen. Sobald dies erfolgt ist, kann jeder mit den Daten, für die Vorhersagen benötigt werden, Aufrufe an den Webdienst durchführen. Der Webdienst nutzt dann das Modell für das Zurückgeben dieser Vorhersagen.

Speichern Sie dazu unser Trainingsmodell zuerst als „Trained Model“-Objekt. Dazu klicken wir mit der rechten Maustaste auf das Modul **Train Model**. Anschließend wählen wir die Option **Save as Trained Model** aus.

Erstellen Sie als Nächstes Eingabe- und Ausgabeports für unseren Webdienst:

* ein Eingabeport nimmt Daten im gleichen Format auf wie die Daten, für die Sie Vorhersagen benötigen.
* ein Ausgabeport gibt die bezeichneten Beschriftungen und zugehörigen Wahrscheinlichkeiten aus.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Auswählen einiger Datenzeilen für den Eingangsanschluss
Die Verwendung eines **Apply SQL Transformation** -Moduls ist eine komfortable Methode zum Auswählen von nur 10 Zeilen, die als Eingabeportdaten fungieren. Wählen Sie mithilfe der hier gezeigten SQL-Abfrage nur diese Datenzeilen für unseren Eingabeport aus:

![Eingangsportdaten](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webdienst
Jetzt können Sie ein kleines Experiment ausführen und in unserem Webdienst veröffentlichen.

#### <a name="generate-input-data-for-webservice"></a>Generieren von Eingabedaten für den Webdienst
Da die Zahlentabelle groß ist, wählen Sie einige Zeilen mit Testdaten aus, und generieren Sie mit den Zählfunktionen Ausgabedaten. Diese können als Eingabedatenformat für unseren Webdienst dienen. Dies wird hier gezeigt:

![BDT-Eingangsdaten erstellen](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Für das Eingabedatenformat verwenden Sie die AUSGABE des **Count Featurizer**-Moduls. Nach Abschluss der Ausführung dieses Experiments speichern Sie die Ausgabe des **Count Featurizer** -Moduls als DataSet. Dieses DataSet wird für die Eingabedaten des Webdiensts verwendet.
> 
> 

#### <a name="scoring-experiment-for-publishing-webservice"></a>Abstimmung des Experiments für den Veröffentlichungswebdienst
Zuerst wird die Darstellung angezeigt. Die grundlegende Struktur ist ein **Score Model**-Modul, das unser Trainingsmodellobjekt und einige Zeilen der zuvor mit dem **Count Featurizer**-Modul generierten Eingabedaten akzeptiert. Verwenden Sie „Select Columns in Dataset“, um die „Scored Labels“ sowie die „Scored Probabilities“ auszublenden.

![Select Columns in Dataset](./media/hive-criteo-walkthrough/kRHrIbe.png)

Beachten Sie, dass das **Select Columns in Dataset** -Modul zum „Filtern“ von Daten aus einem DataSet verwendet werden kann. Den Inhalt sehen Sie hier:

![Filtern mit dem „Select Columns in Dataset“-Modul](./media/hive-criteo-walkthrough/oVUJC9K.png)

Um die blauen E/A-Ports zu erhalten, klicken Sie einfach unten rechts auf **Webdienst vorbereiten** . Wenn wir dieses Experiment ausführen, können wir zudem den Webdienst veröffentlichen, indem wir unten rechts auf dieses Symbol **PREPARE WEBSERVICE** klicken:

![PREPARE WEBSERVICE](./media/hive-criteo-walkthrough/WO0nens.png)

Nach dem Veröffentlichen des der Webdienstes werden Sie auf eine Seite weitergeleitet, die folgendermaßen aussieht:

![Dashboard des Webdiensts](./media/hive-criteo-walkthrough/YKzxAA5.png)

Auf der linken Seite befinden sich zwei Links zu Webdiensten:

* Der **REQUEST/RESPONSE**-Dienst (RRS) für einzelne Vorhersagen, der in diesem Workshop verwendet wurde.
* Der **BATCH EXECUTION** -Dienst (BES) wird für Stapelvorhersagen verwendet und setzt voraus, dass sich die Eingabedaten für Vorhersagen in einem Azure-BLOB-Speicher befinden.

Wenn Sie auf den Link **REQUEST/RESPONSE** klicken, gelangen Sie zu einer Seite mit vorprogrammiertem Code in C#, Python und R. Dieser Code kann problemlos für Aufrufe an den Webdienst verwendet werden. Beachten Sie, dass der API-Schlüssel auf dieser Seite für die Authentifizierung verwendet werden muss.

Es empfiehlt sich, diesen Python-Code in eine neue Zelle des IPython-Notizbuchs zu kopieren.

Hier sehen Sie einen Python-Codeabschnitt mit dem richtigen API-Schlüssel.

![Python-Code](./media/hive-criteo-walkthrough/f8N4L4g.png)

Beachten Sie, dass der Standard-API-Schlüssel durch den API-Schlüssel unseres Webdienstes ersetzt wurde. Wenn Sie in einem IPython-Notizbuch für diese Zelle auf **Ausführen** klicken, wird folgende Antwort zurückgegeben:

![IPython-Antwort](./media/hive-criteo-walkthrough/KSxmia2.png)

Für die beiden angefragten Testbeispiele (im JSON-Framework oder Python-Skript) erhalten Sie Antworten im Format „Bewertete Bezeichnungen, bewertete Wahrscheinlichkeiten“. In diesem Fall wurden die Standardwerte des vorab erstellten Codes ausgewählt (Nullen für alle numerischen Spalten und die Zeichenfolge „value“ für alle Kategoriespalten).

Damit sind wir am Ende unser exemplarischen Vorgehensweise zum Behandeln umfangreicher Datasets mit Azure Machine Learning angekommen. Sie haben mit einem Terabyte an Daten begonnen, ein Vorhersagemodell erstellt und dieses als Webdienst in der Cloud bereitgestellt.

