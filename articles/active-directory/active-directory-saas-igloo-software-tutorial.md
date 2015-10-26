<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Igloo Software | Microsoft Azure" description="Hier erfahren Sie, wie Sie Igloo Software mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Igloo Software
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=526509), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Igloo Software erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein [Igloo Software](http://www.igloosoftware.com/)-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Igloo Software zugewiesen haben, mittels einmaliger Anmeldung auf der Igloo Software-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Igloo Software
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Szenario")
##Aktivieren der Anwendungsintegration für Igloo Software
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Igloo Software aktivieren.

###So aktivieren Sie die Anwendungsintegration für Igloo Software

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** **Igloo Software** ein.

    ![Anwendungskatalog](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Igloo Software** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Igloo Software zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei in Ihren Central Desktop-Mandanten hochladen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Igloo Software** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Igloo Software anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD – einmaliges Anmelden")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Igloo Software-Anmelde-URL** die URL im Format „**https://company.igloocommunities.com/?signin*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Igloo Software** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Igloo Software-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zur **Systemsteuerung**.

    ![Systemsteuerung](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Systemsteuerung")

7.  Klicken Sie auf der Registerkarte **Mitgliedschaft** auf **Anmeldeeinstellungen**.

    ![Anmeldeeinstellungen](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Anmeldeeinstellungen")

8.  Klicken Sie im Abschnitt für die SAML-Konfiguration auf **SAML-Authentifizierung konfigurieren**.

    ![SAML-Konfiguration](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "SAML-Konfiguration")

9.  Führen Sie im Abschnitt **Allgemeine Konfiguration** die folgenden Schritte aus:

    ![Allgemeine Konfiguration](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "Allgemeine Konfiguration")

    1.  Geben Sie im Textfeld **Verbindungsname** einen Namen für die Konfiguration ein.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Igloo Software** den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **ldP-Anmelde-URL** ein.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Igloo Software** den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **ldP-Abmelde-URL** ein.
    4.  Wählen Sie für **Abmeldeantwort und HTTP-Anforderungstyp** die Option **POST** aus.
    5.  Erstellen Sie aus dem heruntergeladenen Zertifikat eine Textdatei.
        
		>[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    6.  Entfernen Sie die erste Zeile (**) und die letzte Zeile (**) aus der Textdateiversion des Zertifikats, kopieren Sie den restlichen Text des Zertifikats, und fügen Sie ihn in das Textfeld **Öffentliches Zertifikat** ein.

10. Führen Sie unter **Antwort- und Authentifizierungskonfiguration** die folgenden Schritte aus:

    ![Antwort- und Authentifizierungskonfiguration](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Antwort- und Authentifizierungskonfiguration")

    1.  Wählen Sie für **Identitätsanbieter** die Option **Microsoft ADFS** aus.
    2.  Wählen Sie für **Bezeichnertyp** die Option **E-Mail-Adresse** aus.
    3.  Geben Sie **emailaddress** in das Textfeld **E-Mail-Attribut** ein.
    4.  Geben Sie **givenname** in das Textfeld **Vornamen-Attribut** ein.
    5.  Geben Sie **surname** in das Textfeld **Nachnamen-Attribut** ein.

11. Führen Sie die folgenden Schritte aus, um die Konfiguration abzuschließen:

    ![Benutzererstellung beim Anmelden](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Benutzererstellung beim Anmelden")

    1.  Wählen Sie für **Benutzererstellung beim Anmelden** die Option **Bei der Anmeldung neuen Benutzer in der Website erstellen** aus.
    2.  Wählen Sie für **Anmeldeeinstellungen** die Option **SAML-Schaltfläche auf dem Anmeldebildschirm verwenden** aus.
    3.  Klicken Sie auf **Speichern**.

12. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Für das Konfigurieren der Benutzerbereitstellung in Igloo Software steht kein Aktionselement zur Verfügung. Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Igloo Software anzumelden, überprüft Igloo Software, ob der Benutzer vorhanden ist. Ist noch kein Benutzerkonto verfügbar, wird es von Igloo Software automatisch erstellt.
##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Igloo Software Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Igloo Software** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->