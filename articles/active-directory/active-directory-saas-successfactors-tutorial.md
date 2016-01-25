<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit SuccessFactors | Microsoft Azure"
    description="Hier erfahren Sie, wie Sie SuccessFactors mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Azure Active Directory-Integration mit SuccessFactors
  
Das Ziel dieses Lernprogramms ist, die Integration von Azure und SuccessFactors im **SP-initiierten SSO-Modus** zu verdeutlichen. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein für SuccessFactor SSO (einmaliges Anmelden) aktiviertes Abonnement im SP-initiierten Modus
  
Nach Abschluss dieses Tutorials können sich die SuccessFactors zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer SuccessFactors-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für SuccessFactors
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-successfactors-tutorial/IC791135.png "Szenario")

##Aktivieren der Anwendungsintegration für SuccessFactors
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für SuccessFactors aktivieren.

###So aktivieren Sie die Anwendungsintegration für SuccessFactors:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-successfactors-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-successfactors-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-successfactors-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-successfactors-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie in das **Suchfeld** **SuccessFactors** ein.

    ![Anwendungskatalog](./media/active-directory-saas-successfactors-tutorial/IC791136.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **SuccessFactors** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![SuccessFactors](./media/active-directory-saas-successfactors-tutorial/IC791137.png "SuccessFactors")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei SuccessFactors zu authentifizieren.
  
Wenn einmaliges Anmelden konfiguriert werden soll, müssen Sie sich an das SuccessFactors-Supportteam wenden.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **SuccessFactors** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-successfactors-tutorial/IC791138.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei SuccessFactors anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-successfactors-tutorial/IC791139.png "Einmaliges Anmelden konfigurieren")

3.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-successfactors-tutorial/IC791140.png "App-URL konfigurieren")

    1.  Geben Sie im Textfeld **SuccessFactors-Anmelde-URL** die URL ein, welche die Benutzer zur Anmeldung bei der SuccessFactors-Anwendung verwenden (z. B. **https://performancemanager4.successfactors.com/sf/home?company=CompanyName&loginMethod=SSO*")).
2.  Geben Sie in das Textfeld **SuccessFactors-Antwort-URL** ****https://performancemanager4.successfactors.com/saml2/SAMLAssertionConsumer?company=CompanyName** ein.

        >[AZURE.NOTE]Dieser Wert ist nur ein temporärer Platzhalter. Sie erhalten den tatsächlichen Wert von Ihrem SuccessFactors Supportteam. Später in diesem Lernprogramm werden Sie Anweisungen für die Kontaktaufnahme mit Ihrem SuccessFactors-Supportteam erhalten. Im Rahmen dieser Konversation erhalten Sie Ihre tatsächliche SuccessFactors-Antwort-URL.

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für SuccessFactors** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-successfactors-tutorial/IC791141.png "Einmaliges Anmelden konfigurieren")

5.  Um das SAML-basierte einmalige Anmelden (SSO) zu konfigurieren, wenden Sie sich an das SuccessFactors-Supportteam. Stellen Sie diesem die folgenden Elemente bereit:

    1.  Das heruntergeladene Zertifikat
    2.  Die Remoteanmelde-URL
    3.  Die Remoteabmelde-URL

    >[AZURE.IMPORTANT]Wenden Sie sich an das SuccessFactors-Supportteam, um den Parameter NameId-Format auf „*Nicht festgelegt*“ festzulegen.

    Das Successfactors-Supportteam sendet Ihnen die richtige **Successfactors-Antwort-URL**, die Sie für das Dialogfeld **App-URL konfigurieren** benötigen.

6.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-successfactors-tutorial/IC791142.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei SuccessFactors anmelden können, müssen sie in SuccessFactors bereitgestellt werden. Im Fall von SuccessFactors ist die Bereitstellung eine manuelle Aufgabe.
  
Um in SuccessFactors erstellte Benutzer abzurufen, müssen Sie sich an das SuccessFactors-Supportteam wenden.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie SuccessFactors Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **SuccessFactors** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-successfactors-tutorial/IC791143.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-successfactors-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->