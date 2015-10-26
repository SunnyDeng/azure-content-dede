<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Coupa | Microsoft Azure" description="Hier erfahren Sie, wie Sie Coupa mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Coupa
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=529806), um Feedback abzugeben.

In diesem Tutorial wird die Integration von Azure und Coupa erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Coupa-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Coupa zugewiesen haben, mittels einmaliger Anmeldung über den Zugriffsbereich anmelden (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Coupa
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-coupa-tutorial/IC791897.png "Szenario")
##Aktivieren der Anwendungsintegration für Coupa

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Coupa aktivieren.

###So aktivieren Sie die Anwendungsintegration für Coupa

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-coupa-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-coupa-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-coupa-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **coupa** ein.

    ![Anwendungskatalog](./media/active-directory-saas-coupa-tutorial/IC791898.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Coupa** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
##Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Coupa zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für Coupa müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Melden Sie sich bei der Coupa-Unternehmenswebsite als Administrator an.

2.  Navigieren Sie zu **Einrichtung > Sicherheitskontrollen **.

    ![Sicherheitskontrollen](./media/active-directory-saas-coupa-tutorial/IC791900.png "Sicherheitskontrollen")

3.  Klicken Sie auf **SP-Metadaten herunterladen und importieren**, um die Coupa-Metadatendatei auf Ihren Computer herunterzuladen.

    ![Coupa-SP-Metadaten](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa-SP-Metadaten")

4.  Melden Sie sich in einem anderen Browserfenster beim Azure Active Directory-Portal an.

5.  Klicken Sie auf der Anwendungsintegrationsseite für **Coupa** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-coupa-tutorial/IC791902.png "Einmaliges Anmelden konfigurieren")

6.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Coupa anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-coupa-tutorial/IC791903.png "Einmaliges Anmelden konfigurieren")

7.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-coupa-tutorial/IC791904.png "App-URL konfigurieren")

    1.  Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei Ihrer Coupa-Anwendung verwenden (z. B. „*http://company.Coupa.com*”).
    2.  Öffnen Sie die heruntergeladene Coupa-Metadatendatei, und kopieren Sie den Wert von **AssertionConsumerService index/URL**.
    3.  Fügen Sie im Textfeld für die **Coupa-Antwort-URL** den Wert von **AssertionConsumerService index/URL** ein.
    4.  Klicken Sie auf **Weiter**.

8.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Coupa** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-coupa-tutorial/IC791905.png "Einmaliges Anmelden konfigurieren")

9.  Navigieren Sie auf der Coupa-Unternehmenswebsite zu **Einrichtung > Sicherheitskontrollen**.

    ![Sicherheitskontrollen](./media/active-directory-saas-coupa-tutorial/IC791900.png "Sicherheitskontrollen")

10. Führen Sie im Abschnitt **Mit Coupa-Anmeldeinformationen anmelden** die folgenden Schritte aus:

    ![Mit Coupa-Anmeldeinformationen anmelden](./media/active-directory-saas-coupa-tutorial/IC791906.png "Mit Coupa-Anmeldeinformationen anmelden")

    1.  Wählen Sie **Mit SAML anmelden** aus.
    2.  Klicken Sie auf **Durchsuchen**, um die heruntergeladene Azure Active Directory-Metadatendatei hochzuladen.
    3.  Klicken Sie auf **Speichern**.

11. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-coupa-tutorial/IC791907.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung

Damit sich Azure AD-Benutzer bei Coupa anmelden können, müssen sie in Coupa bereitgestellt werden. Im Fall von Coupa ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der **Coupa**-Unternehmenswebsite als Administrator an.

2.  Klicken Sie im oberen Menü auf **Einrichtung** und dann auf **Benutzer**.

    ![Benutzer](./media/active-directory-saas-coupa-tutorial/IC791908.png "Benutzer")

3.  Klicken Sie auf **Erstellen**.

    ![Benutzer erstellen](./media/active-directory-saas-coupa-tutorial/IC791909.png "Benutzer erstellen")

4.  Führen Sie im Abschnitt **Benutzer erstellen** die folgenden Schritte aus:

    ![Benutzerdetails](./media/active-directory-saas-coupa-tutorial/IC791910.png "Benutzerdetails")

    1.  Geben Sie die Attribute **Benutzername**, **Vorname**, **Nachname**, **ID für einmaliges Anmelden** und **E-Mail** eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Erstellen**.

    >[AZURE.NOTE]Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Coupa-Benutzerkonten oder mithilfe der von Coupa bereitgestellten APIs erstellen.

##Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Coupa Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Coupa** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-coupa-tutorial/IC791911.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-coupa-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->