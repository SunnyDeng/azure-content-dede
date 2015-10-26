<properties pageTitle="Tutorial: Azure Active Directory-Integration mit FM:Systems | Microsoft Azure" description="Hier erfahren Sie, wie Sie FM:Systems mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit FM:Systems
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=534787), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und FM:Systems erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein FM:Systems-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie FM:Systems zugewiesen haben, mittels einmaliger Anmeldung auf der FM:Systems-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für FM:Systems
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Szenario")
##Aktivieren der Anwendungsintegration für FM:Systems
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für FM:Systems aktivieren.

###So aktivieren Sie die Anwendungsintegration für FM:Systems

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **FM:Systems** ein.

    ![Anwendungskatalog](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **FM:Systems** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![FM: Systems](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM: Systems")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei FM:Systems zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **FM:Systems** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei FM:Systems anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Einmaliges Anmelden konfigurieren")

3.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "App-URL konfigurieren")

    1.  Geben Sie im Textfeld für die **FM:Systems-Anmelde-URL** Ihre FM:Systems-**Antwort-URL** ein (z. B. „**https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).

        >[AZURE.WARNING]Sie erhalten diesen Wert vom Supportteam von FM:Systems.

    2.  Klicken Sie auf **Weiter**.

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für FM:Systems** auf **Metadaten herunterladen**, und speichern Sie die Metadaten auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Einmaliges Anmelden konfigurieren")

5.  Senden Sie die heruntergeladene Metadatendatei an das Supportteam von FM:Systems.

    >[AZURE.NOTE]Die eigentliche SSO-Konfiguration muss vom Supportteam von FM:Systems durchgeführt werden. Sie erhalten eine Benachrichtigung, wenn SSO für Ihr Abonnement aktiviert wurde.

6.  Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei FM:Systems anmelden können, müssen sie in FM:Systems bereitgestellt werden. Im Fall von FM:Systems ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich in einem anderen Webbrowserfenster bei der FM:Systems-Unternehmenswebsite als Administrator an.

2.  Navigieren Sie zu **Systemadministration > Sicherheit verwalten > Benutzer > Benutzerliste**.

    ![Systemadministration](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Systemadministration")

3.  Klicken Sie auf **Neuen Benutzer erstellen**.

    ![Neuen Benutzer erstellen](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Neuen Benutzer erstellen")

4.  Führen Sie im Abschnitt **Benutzer erstellen** die folgenden Schritte aus:

    ![Benutzer erstellen](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Benutzer erstellen")

    1.  Geben Sie den Benutzernamen, das Kennwort, die Bestätigung des Kennworts, die E-Mail-Adresse und die Mitarbeiterkennung eines gültigen Azure Active Directory-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Weiter**.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von FM:Systems-Benutzerkonten oder mithilfe der von FM:Systems bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie FM:Systems Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **FM:Systems** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->