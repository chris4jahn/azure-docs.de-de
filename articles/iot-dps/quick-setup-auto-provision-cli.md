---
title: "Einrichten der Gerätebereitstellung mithilfe der Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation"
description: 'Azure-Schnellstartanleitung: Einrichten des Azure IoT Hub Device Provisioning-Diensts mithilfe der Azure-Befehlszeilenschnittstelle'
services: iot-dps
keywords: 
author: JimacoMS2
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 09b212a5abe2ebb9c123f3adcbdfa59390d16c10
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2018
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Einrichten des IoT Hub Device Provisioning-Diensts mithilfe der Azure-Befehlszeilenschnittstelle

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle eine IoT Hub-Instanz und einen IoT Hub Device Provisioning-Dienst erstellen und die beiden Dienste miteinander verknüpfen. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

> [!IMPORTANT]
> Sowohl die IoT Hub-Instanz als auch der Bereitstellungsdienst, die bzw. den Sie im Rahmen dieser Schnellstartanleitung erstellen, ist öffentlich als DNS-Endpunkt ermittelbar. Vermeiden Sie daher die Verwendung vertraulicher Informationen, falls Sie die Namen für diese Ressourcen ändern.
>


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Im folgenden Beispiel wird am Standort *westus* eine Ressourcengruppe mit dem Namen *my-sample-resource-group* erstellt.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Die Ressourcengruppe wird in diesem Beispiel am Standort „USA, Westen“ erstellt. Mit dem Befehl `az account list-locations -o table` können Sie eine Liste der verfügbaren Standorte anzeigen.
>
>

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

Erstellen Sie mithilfe des Befehls [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) eine IoT Hub-Instanz. 

Im folgenden Beispiel wird am Standort *westus* eine IoT Hub-Instanz mit dem Namen *my-sample-hub* erstellt.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>Erstellen eines Bereitstellungsdiensts

Erstellen Sie mithilfe des Befehls [az iot dps create](/cli/azure/iot/dps#az_iot_dps_create) einen Bereitstellungsdienst. 

Im folgenden Beispiel wird am Standort *westus* ein Bereitstellungsdienst mit dem Namen *my-sample-dps* erstellt.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Der Bereitstellungsdienst wird in diesem Beispiel am Standort „USA, Westen“ erstellt. Sie können eine Liste mit den verfügbaren Standorten anzeigen. Führen Sie hierzu entweder den Befehl `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` aus, oder navigieren Sie zur Seite [Azure-Status](https://azure.microsoft.com/status/), und suchen Sie nach „Device Provisioning-Dienst“. In Befehlen können Standorte entweder als einzelnes Wort oder mit mehreren Wörtern (beispielsweise „westus“, „West US“, „WEST US“ usw.) angegeben werden. Die Groß-/Kleinschreibung spielt hierbei keine Rolle. Wenn Sie den Standort in einem Format mit mehreren Wörtern angeben, müssen Sie den Wert in Anführungszeichen einschließen, wie im folgenden Beispiel zu sehen: `-- location "West US"`.
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>Abrufen der Verbindungszeichenfolge für die IoT Hub-Instanz

Die Verbindungszeichenfolge Ihrer IoT Hub-Instanz wird benötigt, um sie mit dem Device Provisioning-Dienst zu verknüpfen. Rufen Sie mithilfe des Befehls [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string) die Verbindungszeichenfolge ab, und verwenden Sie die Ausgabe des Befehls, um eine Variable festzulegen, die später beim Verknüpfen der beiden Ressourcen verwendet wird. 

Im folgenden Beispiel wird die Variable *hubConnectionString* auf den Wert der Verbindungszeichenfolge für den primären Schlüssel für die Richtlinie *iothubowner* des Hubs festgelegt. Sie können eine andere Richtlinie mit dem Parameter `--policy-name` angeben. Der Befehl verwendet die Optionen [query](/cli/azure/query-azure-cli) und [output](/cli/azure/format-output-azure-cli#tsv-output-format) der Azure-Befehlszeilenschnittstelle, um die Verbindungszeichenfolge aus der Befehlsausgabe zu extrahieren.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Sie können den Befehl `echo` verwenden, um die Verbindungszeichenfolge anzuzeigen.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Diese beiden Befehle eignen sich für einen unter Bash ausgeführten Host. Bei Verwendung einer lokalen Windows-/Befehlsshell oder eines PowerShell-Hosts müssen Sie die Befehle an die korrekte Syntax für die jeweilige Umgebung anpassen.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Verknüpfen der IoT Hub-Instanz und des Bereitstellungsdiensts

Verknüpfen Sie die IoT Hub-Instanz und Ihren Bereitstellungsdienst mithilfe des Befehls [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create). 

Im folgenden Beispiel wird eine IoT Hub-Instanz namens *my-sample-hub* am Standort *westus* mit einem Device Provisioning-Dienst namens *my-sample-dps* verknüpft. Dabei wird die Verbindungszeichenfolge für *my-sample-hub* verwendet, die im vorherigen Schritt in der Variablen *hubConnectionString* gespeichert wurde.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>Überprüfen des Bereitstellungsdiensts

Rufen Sie mithilfe des Befehls [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show) die Details Ihres Bereitstellungsdiensts ab.

Im folgenden Beispiel werden die Details eines Bereitstellungsdiensts namens *my-sample-dps* abgerufen. Die verknüpfte IoT Hub-Instanz wird in der Sammlung *properties.iotHubs* angezeigt.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstartanleitungen in dieser Sammlung bauen auf dieser Schnellstartanleitung auf. Wenn Sie planen, mit den nachfolgenden Schnellstarts oder Tutorials fortzufahren, sollten Sie die in diesem Schnellstart erstellten Ressourcen nicht bereinigen. Wenn Sie nicht fortfahren möchten, können Sie die folgenden Befehle verwenden, um den Bereitstellungsdienst, die IoT Hub-Instanz oder die Ressourcengruppe und alle dazugehörigen Ressourcen zu löschen.

Führen Sie zum Löschen des Bereitstellungsdiensts den Befehl [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete) aus:

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Führen Sie zum Löschen der IoT Hub-Instanz den Befehl [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete) aus:

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Führen Sie zum Löschen einer Ressourcengruppe und all ihrer Ressourcen den Befehl [az group delete](/cli/azure/group#az_group_delete) aus:

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen IoT Hub und eine Instanz des Device Provisioning-Diensts bereitgestellt und die beiden Ressourcen verknüpft. Sie können mit dem Schnellstart zur Erstellung eines simulierten Geräts fortfahren, um zu erfahren, wie Sie diese Einrichtung zum Bereitstellen eines simulierten Geräts verwenden.

> [!div class="nextstepaction"]
> [Schnellstart zum Erstellen eines simulierten Geräts](./quick-create-simulated-device.md)

