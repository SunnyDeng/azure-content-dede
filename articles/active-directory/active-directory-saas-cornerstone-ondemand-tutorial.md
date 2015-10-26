<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Cornerstone OnDemand | Microsoft Azure" description="Hier erfahren Sie, wie Sie Cornerstone OnDemand mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Cornerstone OnDemand
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=526246), um Feedback abzugeben.

In diesem Tutorial wird die Integration von Azure und Cornerstone OnDemand erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Cornerstone OnDemand-Mandanten

Nach Abschluss dieses Tutorials können sich die Cornerstone OnDemand zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer Cornerstone OnDemand-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können Sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Cornerstone OnDemand
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Szenario")
##Aktivieren der Anwendungsintegration für Cornerstone OnDemand

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Cornerstone OnDemand aktivieren.

###So aktivieren Sie die Anwendungsintegration für Cornerstone OnDemand

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** **cornerstone ondemand** ein.

    ![Anwendungskatalog](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Cornerstone OnDemand** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Cornerstone OnDemand zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Cornerstone OnDemand** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Aktivieren der einmaligen Anmeldung](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Aktivieren der einmaligen Anmeldung")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Cornerstone OnDemand anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Microsoft Azure AD – einmaliges Anmelden](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD – einmaliges Anmelden")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Cornerstone OnDemand-Anmelde-URL** die URL im Format „**http://company.csod.com*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Cornerstone OnDemand** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Einmaliges Anmelden konfigurieren")

5.  Senden Sie Folgendes an das Supportteam von Cornerstone OnDemand:

    1.  Das heruntergeladene Zertifikat
    2.  Den Wert der **Remoteanmelde-URL**
    3.  Den Wert der **Remoteabmelde-URL**

    >[AZURE.NOTE]Einmaliges Anmelden muss vom Supportteam von Cornerstone OnDemand konfiguriert werden. Sie erhalten eine Benachrichtigung vom Supportteam, wenn die Konfiguration abgeschlossen ist.

6.  Wählen Sie die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Um Azure AD-Benutzern die Anmeldung bei Cornerstone OnDemand zu ermöglichen, müssen sie in Cornerstone OnDemand bereitgestellt werden. Im Fall von Cornerstone OnDemand ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Senden Sie die Informationen (z. B. Name und E-Mail) für den Azure AD-Benutzer, den Sie bereitstellen möchten, an das Supportteam von Cornerstone OnDemand.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Cornerstone OnDemand-Benutzerkonten oder mithilfe der von Cornerstone OnDemand bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Cornerstone OnDemand Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Cornerstone OnDemand** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Benutzer zuweisen](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Benutzer zuweisen")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->