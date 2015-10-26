<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Jobscience | Microsoft Azure" description="Hier erfahren Sie, wie Sie Jobscience mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Jobscience
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=526255), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Jobscience erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Jobscience-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Jobscience zugewiesen haben, mittels einmaliger Anmeldung auf der Jobscience-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Jobscience
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-jobscience-tutorial/IC784341.png "Szenario")
##Aktivieren der Anwendungsintegration für Jobscience
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Jobscience aktivieren.

###So aktivieren Sie die Anwendungsintegration für Jobscience

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-jobscience-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-jobscience-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-jobscience-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Jobscience** ein.

    ![Anwendungskatalog](./media/active-directory-saas-jobscience-tutorial/IC784342.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Jobscience** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Jobscience zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für Jobscience müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Melden Sie sich bei der Jobscience-Unternehmenswebsite als Administrator an.

2.  Navigieren Sie zu **Einrichtung**.

    ![Einrichtung](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Einrichtung")

3.  Klicken Sie im linken Navigationsbereich im Abschnitt **Verwalten ** auf **Domänenverwaltung**, um den entsprechenden Abschnitt zu erweitern, und klicken Sie anschließend auf **Meine Domäne**, um die Seite **Meine Domäne** zu öffnen.

    ![Meine Domäne](./media/active-directory-saas-jobscience-tutorial/IC767825.png "Meine Domäne")

4.  Um zu überprüfen, ob Ihre Domäne richtig eingerichtet wurde, stellen Sie sicher, dass sich die Domäne in „**Schritt 4 – bereitgestellt für Benutzer**“ befindet und die Domäneneinstellungen korrekt sind.

    ![Domäne für Benutzer bereitgestellt](./media/active-directory-saas-jobscience-tutorial/IC784377.png "Domäne für Benutzer bereitgestellt")

5.  Melden Sie sich in einem anderen Webbrowserfenster beim Azure-Portal an.

6.  Klicken Sie auf der Anwendungsintegrationsseite für **Jobscience** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Einmaliges Anmelden konfigurieren")

7.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Jobscience anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Einmaliges Anmelden konfigurieren")

8.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Jobscience-Anmelde-URL** die URL im Format „**http://company.my.salesforce.com*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-jobscience-tutorial/IC784362.png "App-URL konfigurieren")

9.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Jobscience** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Einmaliges Anmelden konfigurieren")

10. Klicken Sie auf der Jobscience-Unternehmenswebsite auf **Sicherheitskontrollen** und anschließend auf **Einstellungen für einmaliges Anmelden**.

    ![Sicherheitskontrollen](./media/active-directory-saas-jobscience-tutorial/IC784364.png "Sicherheitskontrollen")

11. Führen Sie im Abschnitt **Einstellungen für einmaliges Anmelden** die folgenden Schritte aus:

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Einstellungen für einmaliges Anmelden")

    1.  Wählen Sie **SAML aktiviert** aus.
    2.  Klicken Sie auf **Neu**.

12. Führen Sie im Dialogfeld **Einstellungen für einmaliges Anmelden mit SAML – Bearbeiten** die folgenden Schritte aus:

    ![SAML-Einstellung für einmaliges Anmelden](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML-Einstellung für einmaliges Anmelden")

    1.  Geben Sie im Textfeld **Name** einen Namen für die Konfiguration ein.
    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Jobscience** den Wert der **Aussteller-URL**, und fügen Sie ihn in das Textfeld **Aussteller** ein.
    3.  Geben Sie ****https://salesforce-jobscience.com** in das Textfeld **Entitäts-ID** ein.
4.  Klicken Sie auf **Durchsuchen**, um Ihr Azure AD-Zertifikat hochzuladen.
    5.  Wählen Sie für **SAML-Identitätstyp** die Option **Assertion enthält die Verbund-ID aus dem Benutzerobjekt** aus.
    6.  Wählen Sie für **Speicherort der SAML-Identität** die Option **Identität ist im NameIdentifier-Element der Subject-Anweisung enthalten** aus.
    7.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Jobscience** den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Anmelde-URL des Identitätsanbieters** ein.
    8.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Jobscience** den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Abmelde-URL des Identitätsanbieters** ein.
    9.  Klicken Sie auf **Speichern**.

13. Klicken Sie im linken Navigationsbereich im Abschnitt **Verwalten ** auf **Domänenverwaltung**, um den entsprechenden Abschnitt zu erweitern, und klicken Sie anschließend auf **Meine Domäne**, um die Seite **Meine Domäne** zu öffnen.

    ![Meine Domäne](./media/active-directory-saas-jobscience-tutorial/IC767825.png "Meine Domäne")

14. Klicken Sie auf der Seite **Meine Domäne** im Abschnitt **Anmeldeseitenbranding** auf **Bearbeiten**.

    ![Anmeldeseitenbranding](./media/active-directory-saas-jobscience-tutorial/IC767826.png "Anmeldeseitenbranding")

15. Im Abschnitt **Authentifizierungsdienst** der Seite **Anmeldeseitenbranding** wird der Name Ihrer **SAML-SSO-Einstellungen** angezeigt. Wählen Sie den Namen aus, und klicken Sie auf **Speichern**.

    ![Anmeldeseitenbranding](./media/active-directory-saas-jobscience-tutorial/IC784366.png "Anmeldeseitenbranding")

16. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Einmaliges Anmelden konfigurieren")
  
Klicken Sie zum Abrufen der Anmelde-URL für vom Dienstanbieter initiiertes einmaliges Anmelden im Menüabschnitt **Sicherheitskontrollen** auf **Einstellungen für einmaliges Anmelden**.

![Sicherheitskontrollen](./media/active-directory-saas-jobscience-tutorial/IC784368.png "Sicherheitskontrollen")
  
Klicken Sie auf das SSO-Profil, das Sie im obigen Schritt erstellt haben. Auf dieser Seite wird die SSO-URL für Ihr Unternehmen angezeigt (z. B. **https://companyname.my.salesforce.com?so=companyid*).
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Jobscience anmelden können, müssen sie in Jobscience bereitgestellt werden. Im Fall von Jobscience ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der **Jobscience**-Unternehmenswebsite als Administrator an.

2.  Navigieren Sie zu „Einrichtung“.

    ![Einrichtung](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Einrichtung")

3.  Navigieren Sie zu **Benutzer verwalten > Benutzer**.

    ![Benutzer](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Benutzer")

4.  Klicken Sie auf **Neuer Benutzer**.

    ![Alle Benutzer](./media/active-directory-saas-jobscience-tutorial/IC784370.png "Alle Benutzer")

5.  Führen Sie im Dialogfeld **Benutzer bearbeiten** die folgenden Schritte aus:

    ![Benutzer bearbeiten](./media/active-directory-saas-jobscience-tutorial/IC784371.png "Benutzer bearbeiten")

    1.  Geben Sie den Vornamen, den Nachnamen, den Alias, die E-Mail-Adresse, den Benutzernamen und den Spitznamen des Azure AD-Benutzers, den Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie auf **Speichern**.

    >[AZURE.NOTE]Der Besitzer des Azure AD-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Jobscience-Benutzerkonten oder mithilfe der von Jobscience bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Jobscience Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Jobscience** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-jobscience-tutorial/IC784372.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->