---
title: "Erstellen und Installieren von P2S-VPN-Clientkonfigurationsdateien für die Azure-Zertifikatauthentifizierung: PowerShell: Azure | Microsoft-Dokumentation"
description: "Erstellen und Installieren von VPN-Clientkonfigurationsdateien unter Windows und Mac OS X für die P2S-Zertifikatauthentifizierung"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: 0ca7b7ca9435d1ba05a2cc0951f5bc88b51bf81b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/22/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-point-to-site-configurations"></a>Erstellen und Installieren von VPN-Clientkonfigurationsdateien für Point-to-Site-Konfigurationen mit nativer Azure-Zertifikatauthentifizierung

VPN-Clientkonfigurationsdateien sind in einer ZIP-Datei enthalten. Konfigurationsdateien enthalten die Einstellungen, die zum Herstellen von P2S-Verbindungen mit nativer Azure-Zertifikatauthentifizierung zwischen einem nativen Windows- oder Mac-IKEv2-VPN-Client und einem VNET erforderlich sind.

### <a name="workflow"></a>P2S-Workflow

  1. Richten Sie das Azure-VPN-Gateway für eine P2S-Verbindung ein.
  2. Erstellen Sie das Stammzertifikat und Clientzertifikate. Laden Sie den öffentlichen Schlüssel des Stammzertifikats in Azure hoch, und installieren Sie die Clientzertifikate auf den Clientgeräten. Die Zertifikate dienen zum Authentifizieren der Benutzer, die eine Verbindung herstellen.
  3. Verwenden Sie die VPN-Clientkonfiguration für die gewünschte Authentifizierungsoption, um den VPN-Client auf dem Windows- oder Mac-Gerät einzurichten. Dadurch können Sie eine P2S-Verbindung mit Azure-VNETs herstellen.

>[!NOTE]
>Clientkonfigurationsdateien gelten spezifisch für die VPN-Konfiguration für das VNET. Wenn nach der Erstellung der VPN-Clientkonfigurationsdateien Änderungen an der P2S-VPN-Konfiguration (beispielsweise am VPN-Protokolltyp oder -Authentifizierungstyp) vorgenommen werden, müssen Sie neue VPN-Clientkonfigurationsdateien für die Benutzergeräte erstellen.
>
>

## <a name="generate"></a>Generieren der VPN-Clientkonfigurationsdateien

Stellen Sie zunächst sicher, dass für alle Benutzer, die eine Verbindung herstellen, ein gültiges Zertifikat auf den Benutzergeräten installiert ist. Weitere Informationen zum Installieren eines Clientzertifikats finden Sie unter [Install a client certificate for Point-to-Site Azure certificate authentication connections](point-to-site-how-to-vpn-client-install-azure-cert.md) (Installieren eines Clientzertifikats für Verbindungen für die P2S-Azure-Zertifikatauthentifizierung).

Sie können Clientkonfigurationsdateien mithilfe von PowerShell oder mithilfe des Azure-Portals erstellen. Mit beiden Methoden wird die gleiche ZIP-Datei zurückgegeben. Entzippen Sie die Datei, um die folgenden Ordner anzuzeigen:

  * **WindowsAmd64** und **WindowsX86**. Diese Ordner enthalten das Windows-32-Bit- bzw. das 64-Bit-Installer-Paket. Das Installer-Paket **WindowsAmd64** gilt nicht nur für Amd, sondern für alle unterstützten 64-Bit-Windows-Clients.
  * **Allgemein**: Dieser Ordner enthält allgemeine Informationen zum Erstellen Ihrer eigenen VPN-Clientkonfiguration. Ignorieren Sie diesen Ordner. Der Ordner „Allgemein“ wird bereitgestellt, wenn für das Gateway IKEv2 oder SSTP und IKEv2 konfiguriert wurde. Wenn nur SSTP konfiguriert ist, ist der Ordner „Allgemein“ nicht vorhanden.

### <a name="zipportal"></a>Erstellen von Dateien über das Azure-Portal

1. Navigieren Sie im Azure-Portal zum Gateway für das virtuelle Netzwerk, mit dem Sie eine Verbindung herstellen möchten.
2. Klicken Sie auf der Seite mit dem Gateway für virtuelle Netzwerke auf **Point-to-Site-Konfiguration**.
3. Klicken Sie oben auf der Seite „Point-to-Site-Konfiguration“ auf **VPN-Client herunterladen**. Es dauert einige Minuten, bis das Clientkonfigurationspaket generiert wird.
4. In Ihrem Browser wird ein Hinweis angezeigt, dass eine Clientkonfigurationsdatei im ZIP-Format verfügbar ist. Sie hat den gleichen Namen wie das Gateway. Entzippen Sie die Datei, um die Ordner anzuzeigen.

### <a name="zipps"></a>Generieren von Dateien mithilfe von PowerShell

1. Beim Generieren von VPN-Clientkonfigurationsdateien ist für „-AuthenticationMethod“ der Wert „EapTls“ angegeben. Erstellen Sie die VPN-Clientkonfigurationsdateien mit dem folgenden Befehl:

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. Kopieren Sie die URL in Ihren Browser, um die ZIP-Datei herunterzuladen. Entzippen Sie dann die Datei, um die Ordner anzuzeigen.

## <a name="installwin"></a>Installieren eines Windows-VPN-Clientkonfigurationspakets

Sie können auf jedem Windows-Clientcomputer das gleiche VPN-Clientkonfigurationspaket verwenden – vorausgesetzt, es handelt sich dabei um die passende Version für die Architektur des jeweiligen Clients. Die Liste mit den unterstützten Clientbetriebssystemen finden Sie im Abschnitt [Point-to-Site-Verbindungen](vpn-gateway-vpn-faq.md#P2S) der häufig gestellten Fragen zu VPN Gateway.

>[!NOTE]
>Sie müssen über Administratorrechte auf dem Windows-Clientcomputer verfügen, von dem Sie eine Verbindung herstellen.
>
>

Führen Sie die folgenden Schritte aus, um den nativen Windows-VPN-Client für die Zertifkatauthentifizierung zu konfigurieren:

1. Wählen Sie die VPN-Clientkonfigurationsdateien, die der Architektur des Windows-Computers entsprechen. Wählen Sie für eine 64-Bit-Prozessorarchitektur das Installer-Paket „VpnClientSetupAmd64“ aus. Wählen Sie für eine 32-Bit-Prozessorarchitektur das Installer-Paket „VpnClientSetupX86“ aus. 
2. Doppelklicken Sie auf das Paket, um es zu installieren. Sollte ein SmartScreen-Popup erscheinen, klicken Sie auf **Weitere Informationen** und anschließend auf **Trotzdem ausführen**.
3. Navigieren Sie auf dem Clientcomputer zu **Netzwerkeinstellungen**, und klicken Sie auf **VPN**. Die VPN-Verbindung zeigt den Namen des virtuellen Netzwerks an, mit dem eine Verbindung hergestellt wird. 
4. Bevor Sie versuchen, eine Verbindung herzustellen, überprüfen Sie, ob Sie auf dem Clientcomputer ein Clientzertifikat installiert haben. Ein Clientzertifikat ist für die Authentifizierung erforderlich, wenn Sie den Typ „native Azure-Zertifikatauthentifizierung“ verwenden. Weitere Informationen zum Generieren von Zertifikaten finden Sie unter [Generieren von Zertifikaten](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Weitere Informationen zum Installieren eines Clientzertifikats finden Sie unter [Installieren eines Clientzertifikats](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>VPN-Clientkonfiguration auf dem Mac (OS X)

Azure bietet für die native Azure-Zertifikatauthentifizierung nicht die MOBILECONFIG-Datei. Sie müssen den IKEv2-VPN-Client auf jedem Mac, der sich mit Azure verbindet, manuell konfigurieren. Der Ordner **Allgemein** enthält alle zum Konfigurieren des Clients notwendigen Informationen. Enthält der Download nicht den Ordner „Allgemein“, wurde als Tunneltyp wahrscheinlich nicht IKEv2 ausgewählt. Wählen Sie IKEv2 aus, und generieren Sie anschließend die ZIP-Datei erneut, um den Ordner „Allgemein“ abzurufen. Der Ordner „Allgemein“ enthält die folgenden Dateien:

* **VpnSettings.xml**. Diese Datei enthält wichtige Einstellungen wie Serveradresse und Tunneltyp. 
* **VpnServerRoot.cer**. Diese Datei enthält das Stammzertifikat, das zum Überprüfen des Azure-VPN-Gateways während der P2S-Verbindungseinrichtung erforderlich ist.

Führen Sie die folgenden Schritte aus, um den nativen VPN-Client auf dem Mac für die Zertifkatauthentifizierung zu konfigurieren. Sie müssen die folgenden Schritte auf jedem Mac ausführen, den Sie mit Azure verbinden:

1. Importieren Sie das Stammzertifikat **VpnServerRoot** auf Ihrem Mac. Hierzu können Sie die Datei auf den Mac kopieren und darauf doppelklicken.  
Klicken Sie zum Importieren auf **Hinzufügen**.

  ![Hinzufügen des Zertifikats](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Durch Doppelklicken auf das Zertifikat wird nicht das Dialogfeld **Hinzufügen** angezeigt. Das Zertifikat wird jedoch im richtigen Speicher installiert. Sie können im Anmeldeschlüsselbund in der Zertifikatkategorie nach dem Zertifikat suchen.
  
2. Stellen Sie sicher, dass Sie ein Clientzertifikat installiert haben, das von dem Stammzertifikat, das Sie beim Konfigurieren der P2S-Einstellungen in Azure hochgeladen haben, ausgestellt wurde. Dies unterscheidet sich von der VPNServerRoot-Datei, die Sie im vorherigen Schritt installiert haben. Ein Clientzertifikat wird für die Authentifizierung verwendet und ist erforderlich. Weitere Informationen zum Generieren von Zertifikaten finden Sie unter [Generieren von Zertifikaten](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Weitere Informationen zum Installieren eines Clientzertifikats finden Sie unter [Installieren eines Clientzertifikats](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Öffnen Sie unter **Network Preferences** (Netzwerkeinstellungen) das Dialogfeld **Netzwerk**, und klicken Sie auf **„+“**, um ein neues VPN-Clientverbindungsprofil für eine P2S-Verbindung mit dem Azure-NET zu erstellen.

  Für **Schnittstelle** ist der Wert „VPN“ und für **VPN-Typ** der Wert „IKEv2“ angegeben. Geben Sie im Feld **Dienstname** einen Namen für das Profil ein, und klicken Sie auf **Erstellen**, um das VPN-Clientverbindungsprofil zu erstellen.

  ![Netzwerk](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Kopieren Sie aus der Datei **VpnSettings.xml** im Ordner **Allgemein** den Tagwert **VpnServer**. Fügen Sie diesen Wert in die Felder **Serveradresse** und **Remote-ID** des Profils ein.

  ![Serverinformationen](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Klicken Sie auf **Authentifizierungseinstellungen**, und wählen Sie **Zertifikat** aus. 

  ![Authentifizierungseinstellungen](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Klicken Sie auf **Auswählen...**, um das Clientzertifikat auszuwählen, das Sie für die Authentifizierung verwenden möchten. Dies ist das Zertifikat, das Sie in Schritt 2 installiert haben.

  ![Zertifikat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. Unter **Choose An Identity** (Identität auswählen) wird eine Liste mit Zertifikaten zur Auswahl angezeigt. Wählen Sie das richtige Zertifikat aus, und klicken Sie auf **Weiter**.

  ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. Geben Sie im Feld **Local ID** (Lokale ID) den Namen des Zertifikats (aus Schritt 6) ein. In diesem Beispiel lautet er „ikev2Client.com“. Klicken Sie auf die Schaltfläche **Übernehmen**, um die Änderungen zu speichern.

  ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Klicken Sie im Dialogfeld **Netzwerk** auf **Übernehmen**, um alle Änderungen zu speichern. Klicken Sie auf **Verbinden**, um die P2S-Verbindung mit dem Azure-VNET zu starten.

## <a name="next-steps"></a>Nächste Schritte

Rufen Sie erneut den Artikel zum [Abschließen Ihrer P2S-Konfiguration](vpn-gateway-howto-point-to-site-rm-ps.md) auf.

Informationen zur P2S-Problembehandlung finden Sie unter [Problembehandlung: Azure Punkt-zu-Standort-Verbindungsprobleme](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).