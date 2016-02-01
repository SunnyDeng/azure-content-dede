<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Intacct | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Intacct mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Tutorial: Azure Active Directory-Integration mit Intacct
  
In diesem Tutorial wird die Integration von Azure und Intacct erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Intacct-Mandanten
  
Nach Abschluss dieses Tutorials können sich die Intacct zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf der Intacct-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie auch die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) nutzen.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Intacct
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-intacct-tutorial/IC790030.png "Szenario")
##Aktivieren der Anwendungsintegration für Intacct
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Intacct aktivieren.

###So aktivieren Sie die Anwendungsintegration für Intacct

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-intacct-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-intacct-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-intacct-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Intacct** ein.

    ![Anwendungskatalog](./media/active-directory-saas-intacct-tutorial/IC790031.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Intacct** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Intacct zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (Konvertieren eines binären Zertifikats in eine Textdatei; in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Intacct** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-intacct-tutorial/IC790033.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Intacct anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-intacct-tutorial/IC790034.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Intacct-Anmelde-URL** die URL im Format „**https://Intacct.com/company*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-intacct-tutorial/IC790035.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Intacct** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-intacct-tutorial/IC790036.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Intacct-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf die Registerkarte **Unternehmen** und dann auf **Unternehmensinformationen**.

    ![Unternehmen](./media/active-directory-saas-intacct-tutorial/IC790037.png "Unternehmen")

7.  Klicken Sie auf die Registerkarte **Sicherheit** und dann auf **Bearbeiten**.

    ![Sicherheit](./media/active-directory-saas-intacct-tutorial/IC790038.png "Sicherheit")

8.  Führen Sie im Abschnitt **Einmaliges Anmelden (SSO)** die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-intacct-tutorial/IC790039.png "Einmaliges Anmelden")

    1.  Wählen Sie **Einmaliges Anmelden aktivieren** aus.
    2.  Wählen Sie für **Identitätsanbietertyp** die Option **SAML 2.0** aus.
    3.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Intacct** den Wert der **Aussteller-URL**, und fügen Sie ihn in das Textfeld **Aussteller-URL** ein.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Intacct** den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Anmelde-URL** ein.
    5.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.
        
		>[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    6.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat** ein.
    7.  Klicken Sie auf **Speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-intacct-tutorial/IC790040.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Intacct anmelden können, müssen sie in Intacct bereitgestellt werden. Im Fall von Intacct ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei Ihrem**Intacct**-Mandanten an.

2.  Klicken Sie auf die Registerkarte **Unternehmen** und dann auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-intacct-tutorial/IC790041.png "Benutzer")

3.  Klicken Sie auf die Registerkarte **Hinzufügen**.

    ![Hinzufügen](./media/active-directory-saas-intacct-tutorial/IC790042.png "Hinzufügen")

4.  Führen Sie im Abschnitt **Benutzerinformationen** die folgenden Schritte aus:

    ![Benutzerinformationen](./media/active-directory-saas-intacct-tutorial/IC790043.png "Benutzerinformationen")

    1.  Geben Sie die **Benutzer-ID**, den **Nachnamen**, den **Vornamen**, die **E-Mail-Adresse**, den **Titel** und die **Telefonnummer** eines Azure AD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Wählen Sie die **Administratorrechte** eines Azure AD-Kontos aus, das Sie bereitstellen möchten.
    3.  Klicken Sie auf **Speichern**.
        
		>[AZURE.NOTE]Der Besitzer des AAD-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Intacct-Benutzerkonten oder mithilfe der von Intacct bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Intacct Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Intacct** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-intacct-tutorial/IC790044.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-intacct-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->