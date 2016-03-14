<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Zoom | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Zoom mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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
    ms.date="02/29/2016" 
    ms.author="jeedes" />

#Tutorial: Azure Active Directory-Integration mit Zoom
  
In diesem Tutorial wird die Integration von Azure und Zoom erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Zoom-Mandanten
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Zoom zugewiesen haben, mittels einmaliger Anmeldung auf der Zoom-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Zoom
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-zoom-tutorial/IC784693.png "Szenario")

##Aktivieren der Anwendungsintegration für Zoom
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Zoom aktivieren.

###So aktivieren Sie die Anwendungsintegration für Zoom:

1.  Klicken Sie im klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-zoom-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-zoom-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-zoom-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** **Zoom** ein.

    ![Anwendungskatalog](./media/active-directory-saas-zoom-tutorial/IC784694.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Zoom** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Zoom](./media/active-directory-saas-zoom-tutorial/IC784695.png "Zoom")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Zoom zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (Konvertieren eines binären Zertifikats in eine Textdatei; in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **Zoom** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoom-tutorial/IC784696.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Zoom anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoom-tutorial/IC784697.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Zoom-Anmelde-URL** die URL im Format „**http://company.zoom.us*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-zoom-tutorial/IC784698.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Zoom** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoom-tutorial/IC784699.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Zoom-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf die Registerkarte **Einmaliges Anmelden**.

    ![Einmaliges Anmelden](./media/active-directory-saas-zoom-tutorial/IC784700.png "Einmaliges Anmelden")

7.  Klicken Sie auf die Registerkarte **Sicherheitskontrollen**, und navigieren Sie dann zu den Einstellungen für **Einmaliges Anmelden**.

8.  Führen Sie im Abschnitt „Einmaliges Anmelden“ die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/active-directory-saas-zoom-tutorial/IC784701.png "Einmaliges Anmelden")

    1.  Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Zoom** den Wert für **Dienst-URL für einmaliges Anmelden**, und fügen Sie ihn in das Textfeld **URL der Anmeldeseite** ein.
    2.  Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Zoom** den Wert für **Dienst-URL für einmaliges Abmelden**, und fügen Sie ihn in das Textfeld **URL der Abmeldeseite** ein.
    3.  Erstellen Sie eine **Base64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    4.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat des Identitätsanbieters** ein.
    5.  Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Zoom** den Wert für **Aussteller-URL**, und fügen Sie ihn in das Textfeld **Aussteller** ein.
    6.  Klicken Sie auf **Speichern**.

9.  Wählen Sie im klassischen Azure-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zoom-tutorial/IC784702.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Zoom anmelden können, müssen sie in Zoom bereitgestellt werden. Im Fall von Zoom ist die Bereitstellung eine manuelle Aufgabe.

###So stellen Sie Benutzerkonten bereit

1.  Melden Sie sich bei der **Zoom**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie auf die Registerkarte **Kontenverwaltung** und anschließend auf **Benutzerverwaltung**.

3.  Klicken Sie im Abschnitt „Benutzerverwaltung“ auf **Benutzer hinzufügen**.

    ![Benutzerverwaltung](./media/active-directory-saas-zoom-tutorial/IC784703.png "Benutzerverwaltung")

4.  Führen Sie auf der Seite **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Hinzufügen von Benutzern](./media/active-directory-saas-zoom-tutorial/IC784704.png "Hinzufügen von Benutzern")

    1.  Wählen Sie als **Benutzertyp** die Option **Basic**.
    2.  Geben Sie im Textfeld **E-Mails** die E-Mail-Adresse eines gültigen AAD-Benutzerkontos ein, das Sie bereitstellen möchten.
    3.  Klicken Sie auf **Hinzufügen**.

>[AZURE.NOTE] Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Zoom-Benutzerkonten oder mithilfe der von Zoom bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Zoom Benutzer zu:

1.  Erstellen Sie im klassischen Azure-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Zoom** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-zoom-tutorial/IC784705.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-zoom-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0302_2016-->