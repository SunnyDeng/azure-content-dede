<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Kudos | Microsoft Azure" description="Hier erfahren Sie, wie Sie Kudos mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Kudos
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=528191), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Kudos erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Kudos-Mandanten
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Kudos zugewiesen haben, mittels einmaliger Anmeldung auf der Kudos-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Kudos
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-kudos-tutorial/IC787799.png "Szenario")
##Aktivieren der Anwendungsintegration für Kudos
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Kudos aktivieren.

###So aktivieren Sie die Anwendungsintegration für Kudos

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-kudos-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-kudos-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-kudos-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Kudos** ein.

    ![Anwendungskatalog](./media/active-directory-saas-kudos-tutorial/IC787800.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Kudos** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Kudos](./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudos")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Kudos zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache) weitere Informationen.

###Führen Sie zum Konfigurieren des einmaligen Anmeldens die folgenden Schritte aus:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Kudos** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kudos-tutorial/IC787802.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Kudos anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kudos-tutorial/IC787803.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld **Kudos-Anmelde-URL** die URL im Format „**https://company.kudosnow.com*"“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-kudos-tutorial/IC787804.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Kudos** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kudos-tutorial/IC787805.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Kudos-Unternehmenswebsite als Administrator an.

6.  Klicken Sie im oberen Menü auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-kudos-tutorial/IC787806.png "Einstellungen")

7.  Klicken Sie auf **Integrationen > SSO**.

8.  Führen Sie im Abschnitt **SSO** die folgenden Schritte aus:

    ![SSO](./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")

    1.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Kudos** den Wert für **Dienst-URL für einmaliges Anmelden**, und fügen Sie ihn ins Textfeld **Anmelde-URL** ein.
2.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

        >[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

    3.  Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **X.509-Zertifikat** ein.
    4.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Kudos** den Wert für **Dienst-URL für einmaliges Abmelden**, und fügen Sie ihn im Textfeld **Abmelde-URL** ein.
5.  Geben Sie im Textfeld **Ihre Kudos-URL** den Namen Ihres Unternehmens ein.
    6.  Klicken Sie auf **Speichern**.

9.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kudos-tutorial/IC787808.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Kudos anmelden können, müssen sie in Kudos bereitgestellt werden. Im Fall von Kudos ist die Bereitstellung eine manuelle Aufgabe.

###Führen Sie zum Bereitstellen von Benutzerkonten die folgenden Schritte aus:

1.  Melden Sie sich bei Ihrer **Kudos**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie im oberen Menü auf **Einstellungen**.

    ![Einstellungen](./media/active-directory-saas-kudos-tutorial/IC787806.png "Einstellungen")

3.  Klicken Sie auf **Benutzeradministrator**.

4.  Klicken Sie auf die Registerkarte **Benutzer** und anschließend auf **Benutzer hinzufügen**.

    ![Benutzeradministrator](./media/active-directory-saas-kudos-tutorial/IC787809.png "Benutzeradministrator")

5.  Führen Sie im Abschnitt **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/active-directory-saas-kudos-tutorial/IC787810.png "Benutzer hinzufügen")

    1.  Geben Sie **Vorname**, **Nachname**, **E-Mail** und weitere Details eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Benutzer erstellen**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von Kudos-Benutzerkonten oder mithilfe der von Kudos bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Kudos Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Kudos** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-kudos-tutorial/IC787811.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-kudos-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->