---
title: Kopieren von Daten aus und nach Dynamics CRM oder Dynamics 365 mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie mithilfe einer Kopieraktivität in eine Data Factory-Pipeline Daten aus Microsoft Dynamics CRM oder Microsoft Dynamics 365 in unterstützte Senkendatenspeicher oder aus unterstützten Quelldatenspeichern nach Dynamics CRM oder Dynamics 365 kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: dc0b01e23ebb2695fd0365f054b3cacd2573f3c6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/16/2018
---
# <a name="copy-data-from-and-to-dynamics-365-or-dynamics-crm-by-using-azure-data-factory"></a>Kopieren von Daten aus und nach Dynamics CRM oder Dynamics 365 mithilfe von Azure Data Factory

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus und nach Microsoft Dynamics 365 oder Dynamics CRM zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

> [!NOTE]
> Dieser Artikel bezieht sich auf Version 2 von Data Factory, die zurzeit als Vorschau verfügbar ist. Wenn Sie die Data Factory-Version 1 verwenden, die allgemein verfügbar ist, lesen Sie [Verschieben von Daten mit der Kopieraktivität](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus Dynamics 365 oder Dynamics CRM in jeden unterstützten Senkendatenspeicher kopieren. Zudem können Sie Daten aus jedem unterstützten Quelldatenspeicher in Dynamics 365 oder Dynamics CRM kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser Dynamics-Connector unterstützt insbesondere folgende Dynamics-Versionen und -Authentifizierungstypen. (IFD ist die Abkürzung für „Internet Facing Deployment“ [Bereitstellung mit Internetzugriff].)

| Dynamics-Versionen | Authentifizierungstypen | Beispiele für verknüpfte Dienste |
|:--- |:--- |:--- |
| Dynamics 365 (online) <br> Dynamics CRM Online | Office 365 | [Dynamics Online und Office 365-Authentifizierung](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 lokal mit IFD <br> Dynamics CRM 2016 lokal mit IFD <br> Dynamics CRM 2015 lokal mit IFD | IFD | [Dynamics lokal mit IFD und IFD-Authentifizierung](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Für Dynamics 365 werden insbesondere die folgenden Anwendungstypen unterstützt:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Andere Anwendungstypen wie z.B. Finance & Operations, Talent usw. werden nicht unterstützt.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Dynamics verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Die folgenden Eigenschaften werden für den mit Dynamics verknüpften Dienst unterstützt.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 und Dynamics CRM Online

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **Dynamics** festgelegt werden. | Ja |
| deploymentType | Der Bereitstellungstyp der Dynamics-Instanz. Für Dynamics Online muss der Typ **Online** lauten. | Ja |
| organizationName | Der Organisationsname der Dynamics-Instanz. | Nein. Er sollte angegeben werden, wenn dem Benutzer mehr als eine Dynamics-Instanz zugeordnet ist. |
| authenticationType | Der Authentifizierungstyp für die Herstellung der Verbindung mit dem Dynamics-Server. Geben Sie für Dynamics Online **Office 365** an. | Ja |
| username | Geben Sie den Benutzernamen für die Herstellung der Verbindung mit Dynamics an. | Ja |
| password | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für „username“ angegeben haben. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein für die Quelle. Ja für die Senke, wenn der mit der Quelle verknüpfte Dienst keine Integration Runtime aufweist. |

>[!IMPORTANT]
>Beim Kopieren von Daten nach Dynamics kann die standardmäßige Azure Integration Runtime nicht zum Ausführen des Kopiervorgangs verwendet werden. Anders gesagt: Wenn für den mit der Quelle verknüpften Dienst keine Integration Runtime festgelegt wurde, [erstellen Sie explizit eine Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) mit einem Speicherort in der Nähe Ihrer Dynamics-Instanz. Ordnen Sie die Runtime in dem mit Dynamics verknüpften Dienst wie im folgenden Beispiel zu.

**Beispiel: Dynamics Online mithilfe der Office 365-Authentifizierung**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 und Dynamics CRM lokal mit IFD

*Die zusätzlichen Eigenschaften im Vergleich zu Dynamics Online lauten „hostName“ und „port“.*

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **Dynamics** festgelegt werden. | Ja |
| deploymentType | Der Bereitstellungstyp der Dynamics-Instanz. Muss für Dynamics lokal mit IFD **OnPremisesWithIfd** lauten.| Ja |
| hostName | Der Hostname des lokalen Dynamics-Servers. | Ja |
| port | Der Port des lokalen Dynamics-Servers. | Nein (Standard = 443) |
| organizationName | Der Organisationsname der Dynamics-Instanz. | Ja |
| authenticationType | Der Authentifizierungstyp für die Herstellung der Verbindung mit dem Dynamics-Server. Geben Sie für Dynamics lokal mit IFD **Ifd** an. | Ja |
| username | Geben Sie den Benutzernamen für die Herstellung der Verbindung mit Dynamics an. | Ja |
| password | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für „username“ angegeben haben. Sie können dieses Feld optional als SecureString markieren, um es sicher in ADF zu speichern, oder dieses Kennwort in Azure Key Vault speichern und von dort von der Kopieraktivität abrufen lassen, wenn Datenkopiervorgänge durchgeführt werden. Weitere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Quelle: Nein, Senke: Ja |

>[!IMPORTANT]
>Um Daten nach Dynamics zu kopieren, [erstellen Sie explizit eine Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) mit einem Speicherort in der Nähe Ihrer Dynamics-Instanz. Ordnen Sie die Runtime im verknüpften Dienst wie im folgenden Beispiel zu.

**Beispiel: Dynamics lokal mit IFD mit IFD-Authentifizierung**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Dynamics-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus und nach Dynamics die type-Eigenschaft des Datasets auf **DynamicsEntity** fest. Die folgenden Eigenschaften werden unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf **DynamicsEntity** festgelegt werden. |Ja |
| entityName | Der logische Name der abzurufenden Entität. | Nein für die Quelle (wenn „query“ in der Aktivitätsquelle angegeben ist), Ja für die Senke. |

> [!IMPORTANT]
>- Wenn Sie Daten aus Dynamics kopieren, ist im Dynamics-Dataset der Abschnitt „structure“ erforderlich. Darin werden Spaltenname und Datentyp für die Dynamics-Daten definiert, die Sie kopieren möchten. Weitere Informationen finden Sie unter [Datasetstruktur](concepts-datasets-linked-services.md#dataset-structure) und [Datentypzuordnung für Dynamics](#data-type-mapping-for-dynamics).
>- Beim Kopieren von Daten nach Dynamics ist der Abschnitt „structure“ im Dynamics-Dataset optional. Welche Spalten kopiert werden sollen, wird vom Quelldatenschema bestimmt. Wenn es sich bei Ihrer Quelle um eine CSV-Datei ohne Header handelt, geben Sie im Eingabedataset die „structure“ mit Spaltenname und Datentyp an. Diese werden den Feldern in der CSV-Datei nacheinander in der entsprechenden Reihenfolge zugeordnet.

**Beispiel:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von den Quell- und Senkentypen für Dynamics unterstützt werden.

### <a name="dynamics-as-a-source-type"></a>Dynamics als Quelltyp

Legen Sie zum Kopieren von Daten aus Dynamics den Quelltyp in der Kopieraktivität auf **DynamicsSource** fest. Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **DynamicsSource** festgelegt werden. | Ja |
| query | FetchXML ist eine proprietäre Abfragesprache, die in Dynamics (online und lokal) verwendet wird. Siehe folgendes Beispiel. Weitere Informationen finden Sie unter [Erstellen von Abfragen mit FeachXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | Nein (wenn „entityName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>FetchXML-Beispielabfrage

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics als Senkentyp

Legen Sie zum Kopieren von Daten zu Dynamics den Senkentyp in der Kopieraktivität auf **DynamicsSink** fest. Die folgenden Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf **DynamicsSink** festgelegt werden. | Ja |
| writeBehavior | Das Schreibverhalten des Vorgangs.<br/>Der zulässige Wert ist **Upsert**. | Ja |
| writeBatchSize | Die Zeilenanzahl der Daten, die in jedem Batch in Dynamics geschrieben werden. | Nein (Standard = 10) |
| ignoreNullValues | Gibt an, ob NULL-Werte von Eingabedaten (außer Schlüsselfeldern) während des Schreibvorgangs ignoriert werden sollen.<br/>Zulässige Werte sind **true** und **false**.<br>- **true**: Lässt die Daten im Zielobjekt unverändert, wenn Sie einen upsert- oder update-Vorgang ausführen. Fügt beim Ausführen eines insert-Vorgangs einen definierten Standardwert ein.<br/>- **false**: Aktualisiert die Daten im Zielobjekt auf NULL, wenn Sie einen upsert- oder update-Vorgang ausführen. Fügt beim Ausführen eines insert-Vorgangs einen NULL-Wert ein. | Nein (Standardwert ist „false“) |

>[!NOTE]
>Sowohl für die Senke „writeBatchSize“ als auch für die Kopieraktivität [parallelCopies](copy-activity-performance.md#parallel-copy) für die Dynamics-Senke lautet der Standardwert 10. Daher werden 100 Datensätze gleichzeitig an Dynamics übermittelt.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Datentypzuordnung für Dynamics

Beim Kopieren von Daten aus Dynamics werden die folgenden Zuordnungen von Dynamics-Datentypen zu Data Factory-Zwischendatentypen verwendet. Informationen dazu, wie die Kopieraktivität das Quellschema und den Datentyp zur Senke zuordnet, finden Sie unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md).

Konfigurieren Sie anhand der folgenden Zuordnungstabelle den entsprechenden Data Factory-Datentyp in der Datasetstruktur auf Grundlage des Dynamics-Quelldatentyps.

| Dynamics-Datentyp | Data Factory-Zwischendatentyp | Als Quelle unterstützt | Als Senke unterstützt |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolescher Wert | ✓ | ✓ |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | DECIMAL | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Zeichenfolge | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | |
| AttributeType.ManagedProperty | Boolescher Wert | ✓ | |
| AttributeType.Memo | Zeichenfolge | ✓ | ✓ |
| AttributeType.Money | DECIMAL | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Zeichenfolge | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Die Dynamics-Datentypen „AttributeType.CalendarRules“ und „AttributeType.PartyList“ werden nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
