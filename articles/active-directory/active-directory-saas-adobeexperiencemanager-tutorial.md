---
title: 'Tutorial: Azure Active Directory-Integration mit Adobe Experience Manager | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Adobe Experience Manager konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: c366e314b77cd3344a90826b22b96a45e35b0b4e
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: Azure Active Directory-Integration mit Adobe Experience Manager

In diesem Tutorial erfahren Sie, wie Sie Adobe Experience Manager in Azure Active Directory (Azure AD) integrieren.

Die Integration von Adobe Experience Manager in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Adobe Experience Manager hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Adobe Experience Manager anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Adobe Experience Manager konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Adobe Experience Manager-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Beachten Sie beim Testen der Schritte in diesem Tutorial die folgenden Empfehlungen:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [kostenlose einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptelementen:

1. Hinzufügen von Adobe Experience Manager aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Hinzufügen von Adobe Experience Manager aus dem Katalog
Zum Konfigurieren der Integration von Adobe Experience Manager in Azure AD müssen Sie Adobe Experience Manager aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Adobe Experience Manager aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf das Symbol **Azure Active Directory**. 

    ![Schaltfläche „Azure Active Directory“][1]

2. Navigieren Sie zu **Unternehmensanwendungen ausführen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“][2]
    
3. Wählen Sie oben im Dialogfeld die Schaltfläche **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“][3]

4. Geben Sie im Suchfeld als Suchbegriff **Adobe Experience Manager** ein. Wählen Sie im Ergebnisbereich **Adobe Experience Manager** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Adobe Experience Manager in der Ergebnisliste](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Adobe Experience Manager mithilfe einer Testbenutzerin namens Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Adobe Experience Manager als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Adobe Experience Manager muss eine Verknüpfung eingerichtet werden.

Verwenden Sie in Adobe Experience Manager für **Benutzername** den gleichen Wert wie für **Benutzername** in Azure AD. Damit haben Sie die Verknüpfung zwischen den beiden Benutzern eingerichtet. 

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD bei Adobe Experience Manager zu konfigurieren und zu testen:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on), um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
3. [Erstellen eines Adobe Experience Manager-Testbenutzers](#create-an-adobe-experience-manager-test-user), um eine Entsprechung von Britta Simon in Adobe Experience Manager zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
4. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. [Testen der einmaligen Anmeldung](#test-single-sign-on), um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden bei Ihrer Adobe Experience Manager-Anwendung.

**Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD bei Adobe Experience Manager zu konfigurieren:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Adobe Experience Manager** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden][4]

2. Wählen Sie zum Aktivieren des einmaligen Anmeldens im Dialogfeld **Einmaliges Anmelden** im Dropdownmenü **Modus** die Option **SAML-basierte Anmeldung** aus.
 
    ![Dialogfeld „Einmaliges Anmelden“](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. Führen Sie im Abschnitt **Domäne und URLs für Adobe Experience Manager** die folgenden Schritte aus, wenn Sie die Anwendung im **IdP-Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. Geben Sie im Feld **Bezeichner** einen eindeutigen Wert ein, den Sie auch auf dem AEM-Server definieren. 

    b. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Wenden Sie sich an das [Supportteam von Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html), um diese Werte zu erhalten.
 
4. Aktivieren Sie **Erweiterte URL-Einstellungen anzeigen**. Wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten, führen Sie anschließend die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    Geben Sie im Feld **Anmelde-URL** die URL des Adobe Experience Manager-Servers ein. 

5. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** die Option **Zertifikat (Base64)** aus. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Klicken Sie im Abschnitt „Adobe Experience Manager Configuration“ (Adobe Experience Manager-Konfiguration) auf **Adobe Experience Manager konfigurieren**, um das Fenster „Anmeldung konfigurieren“ zu öffnen. Kopieren Sie die **URL für den SAML-SSO-Dienst**, die **SAML-Entitäts-ID** und die **Abmelde-URL** aus dem Abschnitt „Kurzübersicht“.

    ![Link für Konfigurationsabschnitt](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Wählen Sie **Speichern**aus.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Öffnen Sie das Verwaltungsportal für **Adobe Experience Manager** in einem neuen Browserfenster.

9. Wählen Sie **Settings** (Einstellungen) > **Security** (Sicherheit) > **Users** (Benutzer) aus.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Wählen Sie **Administrator** oder einen anderen relevanten Benutzer aus.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Klicken Sie auf **Account settings** > **Manage TrustStore** (Kontoeinstellungen > TrustStore verwalten).
<<<<<<< HEAD
=======
<<<<<<< HEAD
11. Klicken Sie auf **Account settings** > **Manage TrustStore** (Kontoeinstellungen > TrustStore verwalten).
=======
11. Klicken Sie auf **Account settings** > **Manage TrustStore** (Kontoeinstellungen > Vertrauensspeicher verwalten).
>>>>>>> 6def4612c80a1e9bab4008c57d68ccdbec8d0794
>>>>>>> bb0780f466c4ede2eb00246e1afe01b19bb40688
=======
>>>>>>> ffcb13bb5fc8687cd2f604cdad085442a2a65a08

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. Klicken Sie unter **Add Certificate from CER file** (Zertifikat aus CER-Datei hinzufügen) auf **Select Certificate File** (Zertifikatdatei auswählen). Suchen Sie die Zertifikatsdatei, die Sie bereits aus dem Azure-Portal heruntergeladen haben, und wählen Sie sie aus.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Das Zertifikat wird dem TrustStore hinzugefügt. Notieren Sie den Alias des Zertifikats.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Wählen Sie auf der Seite **Users** (Benutzer) die Option **authentication-service** aus.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Wählen Sie **Account Settings** (Kontoeinstellungen) > **Create/Manage KeyStore** (KeyStore erstellen/verwalten) aus. Erstellen Sie den KeyStore durch Angeben eines Kennworts.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Wechseln Sie zurück zum Verwaltungsbildschirm. Klicken Sie dann auf **Settings** > **Operations** > **Web Console** (Einstellungen > Vorgänge > Webkonsole).

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Dadurch wird die Konfigurationsseite geöffnet.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Suchen Sie **Adobe Granite SAML 2.0 Authentication Handler**. Klicken Sie dann auf das Symbol zum **Hinzufügen**.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Führen Sie auf dieser Seite die folgenden Aktionen aus:

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Geben Sie im Feld **Path** (Pfad) **/** ein.

    b. Fügen Sie im Feld **IDP URL** (IdP-URL) den Wert der **URL für den SAML-SSO-Dienst** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Geben Sie im Feld **IDP Certificate Alias** (Alias des IdP-Zertifikats) den Wert von **Certificate Alias** (Zertifikatalias) ein, den Sie in TrustStore hinzugefügt haben.
<<<<<<< HEAD
=======
<<<<<<< HEAD
    c. Geben Sie im Feld **IDP Certificate Alias** (Alias des IdP-Zertifikats) den Wert von **Certificate Alias** (Zertifikatalias) ein, den Sie in TrustStore hinzugefügt haben.
=======
    c. Geben Sie im Feld **IDP Certificate Alias** (Alias des IdP-Zertifikats) den Wert von **Certificate Alias** (Zertifikatalias) ein, den Sie im Vertrauensspeicher hinzugefügt haben.
>>>>>>> 6def4612c80a1e9bab4008c57d68ccdbec8d0794
>>>>>>> bb0780f466c4ede2eb00246e1afe01b19bb40688
=======
>>>>>>> ffcb13bb5fc8687cd2f604cdad085442a2a65a08

    d. Geben Sie im Feld **Security Provided Entity ID** (Sicherheits-Entitäts-ID) die eindeutige **SAML-Entitäts-ID** ein, die Sie im Azure-Portal konfiguriert haben.

    e. Geben Sie im Feld **Assertion Consumer Service URL** (Assertionsverbraucherdienst-URL) den Wert der **Antwort-URL** ein, den Sie im Azure-Portal konfiguriert haben.

    f. Geben Sie im Feld **Password of Key Store** (Kennwort des Schlüsselspeichers) das **Kennwort** ein, das Sie in KeyStore festgelegt haben.

    g. Geben Sie im Feld **User Attribute ID** (Benutzerattribut-ID) die **Namens-ID** oder eine andere relevante Benutzer-ID ein.

    h. Aktivieren Sie **Autocreate CRX Users** (CRX-Benutzer automatisch erstellen).

    i. Geben Sie im Feld **Logout URL** (Abmelde-URL) den Wert der eindeutigen **Abmelde-URL** ein, den Sie im Azure-Portal abgerufen haben.

    j. Wählen Sie **Speichern**aus.

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen. Nachdem Sie diese App aus dem Bereich **Active Directory** > **Enterprise Applications** hinzugefügt haben, wählen Sie die Registerkarte **Einmaliges Anmelden** aus. Öffnen Sie dann die eingebettete Dokumentation über den Bereich **Konfiguration** am unteren Rand. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

   ![Erstellen eines Azure AD-Testbenutzers][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Wählen Sie im linken Bereich des Azure-Portals die Schaltfläche **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Navigieren Sie zu **Benutzer und Gruppen**, und wählen Sie dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Wählen Sie im oberen Bereich des Dialogfelds **Alle Benutzer** die Option **Hinzufügen** aus, um das Dialogfeld **Benutzer** zu öffnen.

    ![Schaltfläche „Hinzufügen“](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. Führen Sie im Dialogfeld **Benutzer** die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie im Feld **Benutzername** die E-Mail-Adresse des Benutzers Britta Simon ein.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**. Notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    d. Klicken Sie auf **Erstellen**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Erstellen eines Adobe Experience Manager-Testbenutzers

In diesem Abschnitt erstellen Sie in Adobe Experience Manager eine Benutzerin namens Britta Simon. Wenn Sie die Option **Autocreate CRX Users** (CRX-Benutzer automatisch erstellen) aktiviert haben, werden nach der erfolgreichen Authentifizierung automatisch Benutzer erstellt. 

Wenn Sie Benutzer manuell erstellen möchten, wenden Sie sich an das [Adobe Experience Manager-Supportteam](https://helpx.adobe.com/support/experience-manager.html), um die Benutzer auf der Adobe Experience Manager-Plattform hinzuzufügen. 

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf Adobe Experience Manager gewähren.

![Zuweisen der Benutzerrolle][200] 

**Führen Sie die folgenden Schritte aus, um Britta Simon zu Adobe Experience Manager zuzuweisen:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht. Navigieren Sie anschließend zur Verzeichnisansicht, wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen** aus.

    ![Benutzer zuweisen][201] 

2. Wählen Sie in der Anwendungsliste **Adobe Experience Manager** aus.

    ![Adobe Experience Manager-Link in der Anwendungsliste](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“][202]

4. Wählen Sie die Schaltfläche **Hinzufügen** aus. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“][203]

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

6. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Schaltfläche **Zuweisen**.
    
### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Adobe Experience Manager“ klicken, sollten Sie automatisch bei Ihrer Adobe Experience Manager-Anwendung angemeldet werden.

Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png

