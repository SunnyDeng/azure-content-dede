<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Work.com | Microsoft Azure" description="Hier erfahren Sie, wie Sie Work.com mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Work.com
  
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=529836), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Work.com erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Work.com-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die AAD-Benutzer, denen Sie den Zugriff auf Work.com zugewiesen haben, mittels einmaliger Anmeldung auf der Work.com-Unternehmenswebsite bei der Anwendung anmelden \(durch den Dienstanbieter initiierte Anmeldung\). Alternativ können sie den Zugriffsbereich nutzen \(siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)\).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Work.com
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-work-com-tutorial/IC794105.png "Szenario")

##Aktivieren der Anwendungsintegration für Work.com
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Work.com aktivieren.

###So aktivieren Sie die Anwendungsintegration für Work.com

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-work-com-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-work-com-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-work-com-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Work.com** ein.

    ![Anwendungskatalog](./media/active-directory-saas-work-com-tutorial/IC794106.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Work.com** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Work.com zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie ein Zertifikat auf Work.com hochladen.

>[AZURE.NOTE]Um einmaliges Anmelden zu konfigurieren, müssen Sie einen benutzerdefinierten Domänennamen von Work.com einrichten. Sie müssen mindestens einen Domänennamen definieren, testen und dann in Ihrer gesamten Organisation bereitstellen.

###So konfigurieren Sie einmaliges Anmelden

1.  Melden Sie sich bei Ihrem Work.com-Mandanten als Administrator an.

2.  Wechseln Sie zu **Setup**.

    ![Einrichtung](./media/active-directory-saas-work-com-tutorial/IC794108.png "Einrichtung")

3.  Klicken Sie im linken Navigationsbereich im Abschnitt **Verwalten ** auf **Domänenverwaltung**, um den entsprechenden Abschnitt zu erweitern, und klicken Sie anschließend auf **Meine Domäne**, um die Seite **Meine Domäne** zu öffnen.

    ![Meine Domäne](./media/active-directory-saas-work-com-tutorial/IC767825.png "Meine Domäne")

4.  Um zu überprüfen, ob Ihre Domäne richtig eingerichtet wurde, stellen Sie sicher, dass sich die Domäne in „**Schritt 4 – bereitgestellt für Benutzer**“ befindet und die Domäneneinstellungen korrekt sind.

    ![Domäne für Benutzer bereitgestellt](./media/active-directory-saas-work-com-tutorial/IC784377.png "Domäne für Benutzer bereitgestellt")

5.  Melden Sie sich in einem anderen Webbrowserfenster beim Azure-Portal an.

6.  Klicken Sie auf der Anwendungsintegrationsseite für **Work.com** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-work-com-tutorial/IC794109.png "Einmaliges Anmelden konfigurieren")

7.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Work.com anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-work-com-tutorial/IC794110.png "Einmaliges Anmelden konfigurieren")

8.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Work.com-Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei Work.com verwenden \(z. B. **http://company.my.salesforce.com*”), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-work-com-tutorial/IC794111.png "App-URL konfigurieren")

9.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Work.com** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-work-com-tutorial/IC794112.png "Einmaliges Anmelden konfigurieren")

10. Melden Sie sich bei Ihrem Work.com-Mandanten an.

11. Wechseln Sie zu **Setup**.

    ![Einrichtung](./media/active-directory-saas-work-com-tutorial/IC794108.png "Einrichtung")

12. Erweitern Sie das Menü **Sicherheitskontrollen**, und klicken Sie dann auf **Einstellungen für einmaliges Anmelden**.

    ![Einstellungen für einmaliges Anmelden](./media/active-directory-saas-work-com-tutorial/IC794113.png "Einstellungen für einmaliges Anmelden")

13. Führen Sie auf der Dialogfeldseite **Einstellungen für einmaliges Anmelden** die folgenden Schritte aus:

    ![SAML aktiviert](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML aktiviert")

    1.  Wählen Sie **SAML aktiviert**.
    2.  Klicken Sie auf **Neu**.

14. Führen Sie im Abschnitt **Einstellungen für einmalige Anmelden für SAML** die folgenden Schritte aus:

    ![SAML-Einstellung für einmaliges Anmelden](./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML-Einstellung für einmaliges Anmelden")

    1.  Geben Sie im Textfeld **Name** einen Namen für die Konfiguration ein.  

        >[AZURE.NOTE]Bei Eingabe eines Werts für **Name** wird automatisch das Textfeld **API-Name** gefüllt.

    2.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Work.com** den Wert für **Aussteller-URL**, und fügen Sie ihn in das Textfeld **Aussteller** ein.
    3.  Klicken Sie auf **Durchsuchen**, um das heruntergeladene Zertifikat hochzuladen.
    4.  Geben Sie ****https://salesforce-work.com** in das Textfeld **Entitäts-ID** ein.
5.  Wählen Sie für **SAML-Identitätstyp** die Option **Assertion enthält die Verbund-ID aus dem Benutzerobjekt** aus.
    6.  Wählen Sie für **Speicherort der SAML-Identität** die Option **Identität ist im NameIdentifier-Element der Subject-Anweisung enthalten** aus.
    7.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Work.com** den Wert der **Remoteanmelde-URL**, und fügen Sie ihn in das Textfeld **Anmelde-URL des Identitätsanbieters** ein.
    8.  Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Work.com** den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Abmelde-URL des Identitätsanbieters** ein.
    9.  Wählen Sie für **Vom Dienstanbieter initiierte Anforderungsbindung** die Option **HTTP Post**.
    10. Klicken Sie auf **Speichern**.

15. Klicken Sie im linken Navigationsbereich Ihres Work.com-Portals auf **Domänenverwaltung**, um den entsprechenden Abschnitt zu erweitern, und klicken Sie anschließend auf **Meine Domäne**, um die Seite **Meine Domäne** zu öffnen.

    ![Meine Domäne](./media/active-directory-saas-work-com-tutorial/IC794115.png "Meine Domäne")

16. Klicken Sie auf der Seite **Meine Domäne** im Abschnitt **Anmeldeseitenbranding** auf **Bearbeiten**.

    ![Anmeldeseitenbranding](./media/active-directory-saas-work-com-tutorial/IC767826.png "Anmeldeseitenbranding")

17. Im Abschnitt **Authentifizierungsdienst** der Seite **Anmeldeseitenbranding** wird der Name Ihrer **SAML-SSO-Einstellungen** angezeigt. Wählen Sie den Namen aus, und klicken Sie auf **Speichern**.

    ![Anmeldeseitenbranding](./media/active-directory-saas-work-com-tutorial/IC784366.png "Anmeldeseitenbranding")

18. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-work-com-tutorial/IC794116.png "Einmaliges Anmelden konfigurieren")

##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure Active Directory-Benutzer anmelden können, müssen sie in Work.com bereitgestellt werden. Im Fall von Work.com ist die Bereitstellung eine manuelle Aufgabe.

###So konfigurieren Sie die Benutzerbereitstellung

1.  Melden Sie sich bei der Work.com-Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Setup**.

    ![Einrichtung](./media/active-directory-saas-work-com-tutorial/IC794108.png "Einrichtung")

3.  Navigieren Sie zu **Benutzer verwalten \> Benutzer**.

    ![Benutzer verwalten](./media/active-directory-saas-work-com-tutorial/IC784369.png "Benutzer verwalten")

4.  Klicken Sie auf **Neuer Benutzer**.

    ![Alle Benutzer](./media/active-directory-saas-work-com-tutorial/IC794117.png "Alle Benutzer")

5.  Führen Sie im Abschnitt „Benutzer bearbeiten“ die folgenden Schritte aus:

    ![Benutzer bearbeiten](./media/active-directory-saas-work-com-tutorial/IC794118.png "Benutzer bearbeiten")

    1.  Geben Sie die Attribute **Nachname**, **Alias**, **E-Mail**, **Benutzername** und **Spitzname** eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Wählen Sie **Rolle**, **Benutzerlizenz** und **Profil**.
    3.  Klicken Sie auf **Speichern**.  

        >[AZURE.NOTE]Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von Work.com-Benutzerkonten oder mithilfe der von Work.com bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Work.com Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für Work.com auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-work-com-tutorial/IC794119.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-work-com-tutorial/IC767830.png "Ja")
  
Nach 10 Minuten können Sie überprüfen, ob das Konto mit Work.com synchronisiert wurde.
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->