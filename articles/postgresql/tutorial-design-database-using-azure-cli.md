---
title: 'Tutorial: Entwerfen einer Azure Database for PostgreSQL-Instanz mithilfe der Azure CLI'
description: In diesem Tutorial wird veranschaulicht, wie Sie Ihren ersten Azure Database for PostgreSQL-Server mit der Azure CLI erstellen, konfigurieren und abfragen.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 02/28/2018
ms.openlocfilehash: 56425ec7ccb1d6629b82db6683a02a57ab9999b4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-design-an-azure-database-for-postgresql-using-azure-cli"></a>Tutorial: Entwerfen einer Azure Database for PostgreSQL-Instanz mithilfe der Azure CLI 
In diesem Tutorial verwenden Sie die Azure CLI (Befehlszeilenschnittstelle) und andere Hilfsprogramme, um zu lernen, wie Sie Folgendes ausführen:
> [!div class="checklist"]
> * Erstellen einer Azure-Datenbank für PostgreSQL-Server
> * Konfigurieren der Serverfirewall
> * Erstellen einer Datenbank mit dem Hilfsprogramm [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)
> * Laden von Beispieldaten
> * Abfragen von Daten
> * Aktualisieren von Daten
> * Wiederherstellen von Daten

Sie können Azure Cloud Shell im Browser verwenden oder [Azure CLI 2.0 auf dem lokalen Computer installieren]( /cli/azure/install-azure-cli), um die Befehle in diesem Tutorial auszuführen.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli) Informationen dazu. 

Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource vorhanden ist oder in Rechnung gestellt wird. Wählen Sie eine bestimmte Abonnement-ID unter Ihrem Konto mit dem Befehl [az account set](/cli/azure/account#az_account_set) aus.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az_group_create) eine [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und als Gruppe verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myresourcegroup` am Standort `westus` erstellt.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="add-the-extension"></a>Hinzufügen der Erweiterung
Fügen Sie die aktualisierte Azure Database for PostgreSQL-Verwaltungserweiterung mit dem folgenden Befehl hinzu:
```azurecli-interactive
az extension add --name rdbms
``` 

Überprüfen Sie, ob die richtige Version der Erweiterung installiert ist. 
```azurecli-interactive
az extension list
```

Die JSON-Ausgabe sollte Folgendes enthalten: 
```json
{
    "extensionType": "whl",
    "name": "rdbms",
    "version": "0.0.3"
}
```

Wird Version 0.0.3 nicht zurückgegeben, führen Sie den folgenden Befehl aus, um die Erweiterung zu aktualisieren: 
```azurecli-interactive
az extension update --name rdbms
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Erstellen einer Azure-Datenbank für PostgreSQL-Server
Erstellen Sie mit dem Befehl [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) eine [Azure-Datenbank für PostgreSQL-Server](overview.md). Ein Server enthält eine Gruppe von Datenbanken, die als Gruppe verwaltet werden. 

Im folgenden Beispiel wird ein Server mit dem Namen `mydemoserver` in der Ressourcengruppe `myresourcegroup` mit dem Serveradministrator-Anmeldenamen `myadmin` erstellt. Der Name eines Servers wird dem DNS-Namen zugeordnet und muss deshalb in Azure global eindeutig sein. Ersetzen Sie das `<server_admin_password>` durch einen eigenen Wert. Es ist ein Gen 4-Server vom Typ „Allgemein“ mit zwei virtuellen Kernen.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!IMPORTANT]
> Der hier angegebene Benutzername und das Kennwort für den Serveradministrator sind erforderlich, um später in diesem Schnellstart die Anmeldung am Server und bei den zugehörigen Datenbanken auszuführen. Behalten Sie diese Angaben im Kopf, oder notieren Sie sie zur späteren Verwendung.

Standardmäßig wird die **postgres**-Datenbank unter dem Server erstellt. Die [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-Datenbank ist eine Standarddatenbank für die Verwendung durch Benutzer, Hilfsprogramme und Drittanbieteranwendungen. 


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurieren einer Firewallregel auf Serverebene

Erstellen Sie mit dem Befehl [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) eine Azure-PostgreSQL-Firewallregel auf Serverebene. Eine Firewallregel auf Serverebene ermöglicht einer externen Anwendung wie z.B. [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) oder [PgAdmin](https://www.pgadmin.org/), über die Firewall des Azure-PostgreSQL-Diensts eine Verbindung mit Ihrem Server herzustellen. 

Sie können eine Firewallregel festlegen, die einen Bereich von IP-Adressen abdeckt, mit denen Verbindungen aus dem Netzwerk hergestellt werden können. Im folgenden Beispiel wird mit [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) die Firewallregel `AllowAllIps` erstellt, die Verbindungen von beliebigen IP-Adressen ermöglicht. Verwenden Sie 0.0.0.0 als IP-Startadresse und 255.255.255.255 als Endadresse, wenn Sie alle IP-Adressen öffnen möchten.

Zum Einschränken des Zugriffs auf Ihren Azure PostgreSQL-Server ausschließlich auf Ihr Netzwerk können Sie die Firewallregel so festlegen, dass nur der IP-Adressbereich Ihres Unternehmensnetzwerks abgedeckt wird.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Der Azure-PostgreSQL-Server kommuniziert über Port 5432. Wenn Sie eine Verbindung aus einem Unternehmensnetzwerk heraus herstellen, wird der ausgehende Datenverkehr über Port 5432 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. Ihre IT-Abteilung muss Port 5432 öffnen, damit Sie eine Verbindung mit Ihrem Azure SQL-Datenbankserver herstellen können.
>

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Das Ergebnis liegt im JSON-Format vor. Notieren Sie sich die Werte für **administratorLogin** und **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"

}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Herstellen einer Verbindung mit der Azure-Datenbank für die PostgreSQL-Datenbank mit psql
Wenn auf Ihrem Clientcomputer PostgreSQL installiert ist, können Sie mit einer lokalen Instanz von [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) oder der Azure-Cloudkonsole eine Verbindung mit einem Azure-PostgreSQL-Server herstellen. Wir stellen jetzt mit dem Befehlszeilen-Hilfsprogramm psql eine Verbindung mit der Azure-Datenbank für PostgreSQL-Server her.

1. Führen Sie den folgenden psql-Befehl aus, um eine Verbindung mit einer Azure Database for PostgreSQL-Datenbank herzustellen:
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Mit dem folgenden Befehl wird beispielsweise mit den Zugriffsanmeldeinformationen eine Verbindung mit der Standarddatenbank **postgres** auf Ihrem PostgreSQL-Server **mydemoserver.postgres.database.azure.com** hergestellt. Geben Sie das `<server_admin_password>` ein, das Sie bei der Aufforderung zur Kennworteingabe ausgewählt haben.
  
  ```azurecli-interactive
psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver ---dbname=postgres
```

2.  Sobald Sie mit dem Server verbunden sind, erstellen Sie an der Eingabeaufforderung eine leere Datenbank:
```sql
CREATE DATABASE mypgsqldb;
```

3.  Führen Sie an der Eingabeaufforderung den folgenden Befehl zum Wechseln der Verbindung zur neu erstellten Datenbank **mypgsqldb** aus:
```sql
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>Erstellen von Tabellen in der Datenbank
Da Sie nun wissen, wie Sie eine Verbindung mit Azure Database for PostgreSQL herstellen, können Sie einige grundlegende Aufgaben ausführen:

Zunächst erstellen Sie eine Tabelle und füllen sie mit einigen Daten auf. Erstellen Sie beispielsweise eine Tabelle, die Bestandsinformationen nachverfolgt:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Um die neu erstellte Tabelle in der Liste der Tabellen anzuzeigen, geben Sie Folgendes ein:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Laden von Daten in die Tabelle
Die Tabelle ist jetzt erstellt. Fügen Sie einige Daten ein. Führen Sie im geöffneten Eingabeaufforderungsfenster die folgende Abfrage zum Einfügen einiger Datenzeilen aus:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Die Tabelle, die Sie zuvor erstellt haben, enthält nun zwei hinzugefügte Zeilen mit Beispieldaten.

## <a name="query-and-update-the-data-in-the-tables"></a>Abfragen und Aktualisieren der Daten in den Tabellen
Führen Sie die folgende Abfrage aus, um Informationen aus der Bestandstabelle abzurufen: 
```sql
SELECT * FROM inventory;
```

Sie können die Daten in der Bestandstabelle auch aktualisieren:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Die aktualisierten Werte werden angezeigt, wenn Sie die Daten abrufen:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Wiederherstellen eines früheren Zustands einer Datenbank
Stellen Sie sich vor, Sie haben versehentlich eine Tabelle gelöscht. Dies ist eine Situation, in der Sie die Datenbank nicht einfach wiederherstellen können. Mit Azure Database for PostgreSQL können Sie eine beliebige Zeitpunktsicherung Ihres Servers (abhängig vom konfigurierten Aufbewahrungszeitraum für Sicherungen) auf einem neuen Server wiederherstellen. Sie können diesen neuen Server zur Wiederherstellung gelöschter Daten verwenden. 

Der folgende Befehl stellt den Beispielserver zu einem Zeitpunkt wieder her, der vor dem Hinzufügen der Tabelle liegt:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

Für den Befehl `az postgres server restore` sind folgende Parameter erforderlich:
| Einstellung | Empfohlener Wert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der sich der Quellserver befindet.  |
| name | mydemoserver-restored | Der Name des neuen Servers, der durch den Befehl „restore“ erstellt wird. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Wählen Sie einen Zeitpunkt für die Wiederherstellung aus. Datum und Zeit müssen innerhalb des Aufbewahrungszeitraums für Sicherungen des Quellservers liegen. Verwenden Sie das Datums- und Zeitformat nach ISO 8601. Beispielsweise können Sie Ihre eigene lokale Zeitzone wie etwa `2017-04-13T05:59:00-08:00` oder das UTC-Format Zulu `2017-04-13T13:59:00Z` verwenden. |
| source-server | mydemoserver | Der Name oder die ID des Quellservers, über den die Wiederherstellung durchgeführt wird. |

Bei der Wiederherstellung eines Servers zu einem früheren Zeitpunkt wird ein neuer Server erstellt. Dabei wird der ursprüngliche Server zu dem von Ihnen festgelegten Zeitpunkt kopiert. Die Werte zum Standort und Tarif des wiederhergestellten Servers sind mit denen des Quellservers identisch.

Der Befehl ist synchron und gibt nach der Wiederherstellung des Servers Daten zurück. Sobald die Wiederherstellung abgeschlossen ist, suchen Sie nach dem neuen Server, der erstellt wurde. Stellen Sie sicher, dass die Daten wie erwartet wiederhergestellt wurden.


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie die Verwendung der Azure CLI (Befehlszeilenschnittstelle) und anderer Hilfsprogramme zu folgenden Zwecken gelernt:
> [!div class="checklist"]
> * Erstellen einer Azure-Datenbank für PostgreSQL-Server
> * Konfigurieren der Serverfirewall
> * Erstellen einer Datenbank mit dem Hilfsprogramm [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)
> * Laden von Beispieldaten
> * Abfragen von Daten
> * Aktualisieren von Daten
> * Wiederherstellen von Daten

Um als Nächstes zu erfahren, wie Sie das Azure-Portal verwenden, um ähnliche Aufgaben auszuführen, absolvieren Sie dieses Tutorial: [Entwerfen Ihrer ersten Azure-Datenbank für PostgreSQL mithilfe des Azure-Portals](tutorial-design-database-using-azure-portal.md).
