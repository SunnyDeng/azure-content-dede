<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Workday | Microsoft Azure" description="Hier erfahren Sie, wie Sie Workday mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Workday
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=330042), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und Workday erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Mandanten in Workday
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Workday
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Konfigurieren der Benutzerbereitstellung

![Szenario](./media/active-directory-saas-workday-tutorial/IC782919.png "Szenario")

##Aktivieren der Anwendungsintegration für Workday
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Workday aktivieren.

###So aktivieren Sie die Anwendungsintegration für Workday

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-workday-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie zum Öffnen des **Anwendungskatalogs** auf **App hinzufügen** und anschließend auf **Eine Anwendung hinzufügen, die mein Unternehmen verwendet**.

    ![Was möchten Sie tun?](./media/active-directory-saas-workday-tutorial/IC700995.png "Was möchten Sie tun?")

5.  Geben Sie im **Suchfeld** das Wort **Workday** ein.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6.  Wählen Sie im Ergebnisbereich **Workday** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Workday zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie ein Base-64-codiertes Zertifikat erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie auf der Anwendungsintegrationsseite für **Workday** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-workday-tutorial/IC782920.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Workday anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-workday-tutorial/IC782921.png "Einmaliges Anmelden konfigurieren")

3.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-workday-tutorial/IC782957.png "App-URL konfigurieren")

    1.  Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei Workday verwenden (z. B. *https://impl.workday.com/\<Mandant>/login-saml2.htmld*).
    2.  Geben Sie im Textfeld **Antwort-URL von Workday** die Antwort-URL von Workday ein (z. B.: *https://impl.workday.com/\<Mandant>/login-saml.htmld*).

        >[AZURE.NOTE]Ihre Antwort-URL muss eine Unterdomäne aufweisen (z. B. www, wd2, wd3, wd3-impl, wd5, wd5-impl). „**http://www.myworkday.com*“ funktioniert beispielsweise, „**http://myworkday.com*“ jedoch nicht.
 
4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Workday** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-workday-tutorial/IC782922.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster auf der Workday-Unternehmenswebsite als Administrator an.

6.  Wechseln Sie zu **Menü > Workbench**.

    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7.  Wechseln Sie zu **Kontoverwaltung**.

    ![Kontoverwaltung](./media/active-directory-saas-workday-tutorial/IC782924.png "Kontoverwaltung")

8.  Wechseln Sie zu **Mandanten-Setup bearbeiten – Sicherheit**.

    ![Mandanten-Sicherheit bearbeiten](./media/active-directory-saas-workday-tutorial/IC782925.png "Mandanten-Sicherheit bearbeiten")

9.  Führen Sie im Abschnitt **Umleitungs-URLs** die folgenden Schritte aus:

    ![Umleitungs-URLs](./media/active-directory-saas-workday-tutorial/IC7829581.png "Umleitungs-URLs")

     9\.1. Klicken Sie auf **Zeile hinzufügen**.

     9\.2. Geben Sie in den Textfeldern **Anmeldung der Umleitungs-URL** und **Mobile Umleitungs-URL** die **Mandanten-URL von Workday** ein, die Sie auf der Seite **App-URL konfigurieren** im Azure-Portal eingegeben haben.
    
     9\.3. Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden bei Workday konfigurieren** die **Dienst-URL für einmaliges Abmelden**, und fügen Sie sie im Textfeld **Abmeldung der Umleitungs-URL** ein.

     9\.4. Geben Sie im Textfeld **Umgebung** den Umgebungsnamen ein.


       >[AZURE.NOTE]Der Wert des Umgebungsattribut ist an den Wert des Mandanten-URI gebunden: >> – Wenn der Domänenname der Workday-Mandanten-URL mit „impl“ beginnt (z. B.: *https://impl.workday.com/\<tenant>/login-saml2.htmld*), muss das **Umgebungsattribut** auf „Implementierung“ festgelegt werden. >Wenn der Domänenname anders beginnt, müssen Sie Workday kontaktieren, um den entsprechenden **Umgebungswert** zu erhalten.

10. Führen Sie im Abschnitt **SAML-Setup** die folgenden Schritte aus:

    ![SAML-Setup](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML-Setup")

     10\.1. Wählen Sie die Option **SAML-Authentifizierung aktivieren**.

     10\.2. Klicken Sie auf **Zeile hinzufügen**.

11. Führen Sie im Abschnitt „SAML-Identitätsanbieter“ die folgenden Schritte aus:

    ![SAML-Identitätsanbieter](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML-Identitätsanbieter")

     11\.1. Geben Sie im Textfeld „Name des Identitätsanbieters“ einen Anbieternamen ein (z. B.: *SPInitiatedSSO*).

     11\.2. Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Workday** den Wert für die **Identitätsanbieter-ID**, und fügen Sie ihn in das Textfeld **Aussteller** ein.

     11\.3. Wählen Sie **Workday-initiierte Abmeldung**.

     11\.4. Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden bei Workday konfigurieren** den Wert von **Dienst-URL für einmaliges Abmelden**, und fügen Sie sie im Textfeld **Abmeldung der Anforderungs-URL** ein.


     11\.3. Klicken Sie auf **Öffentliches Schlüsselzertifikat des Identitätsanbieters**, und klicken Sie dann auf **Erstellen**. <br><br> ![Erstellen](./media/active-directory-saas-workday-tutorial/IC782928.png "Erstellen")

     11\.4. Klicken Sie auf **Öffentlichen x509-Schlüssel erstellen**. <br><br> ![Erstellen](./media/active-directory-saas-workday-tutorial/IC782929.png "Erstellen")

     11\.5. Führen Sie im Abschnitt **Öffentlichen x509-Schlüssel anzeigen** die folgenden Schritte aus: <br><br> ![Öffentlichen x509-Schlüssel anzeigen](./media/active-directory-saas-workday-tutorial/IC782930.png "Öffentlichen x509-Schlüssel anzeigen") <br>

      1.  Geben Sie im Textfeld **Name** einen Namen für das Zertifikat ein (z. B.: *PPE\_SP*).
      2.  Geben Sie im Textfeld **Gültig ab** den „Gültig-ab“-Attributwert des Zertifikats ein.
      3.  Geben Sie im Textfeld **Gültig bis** den „Gültig-bis“-Attributwert des Zertifikats ein.
		
           >[AZURE.NOTE]Sie können die „Gültig ab“- und „Gültig bis“-Daten per Doppelklick aus dem heruntergeladenen Zertifikat abrufen. Die Datumsangaben sind in der Registerkarte **Details** aufgeführt.

      4.  Erstellen Sie eine **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.

		>[AZURE.TIP]Weitere Informationen finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

      5.  Öffnen Sie das Base64-codierte Zertifikat in Notepad und kopieren Sie den Inhalt.
      6.  Fügen Sie in der Textbox **Zertifikat** den Inhalt der Zwischenablage ein.
      7.  Klicken Sie auf **OK**.

12.  Führen Sie die folgenden Schritte aus: <br><br> ![SSO-Konfiguration](./media/active-directory-saas-workday-tutorial/IC7829351111.png "SSO-Konfiguration")

     12\.1. Aktivieren Sie das **private Schlüsselpaar „X. 509“**.

     12\.2. Geben Sie im Textfeld **Dienstanbieter-ID** ****http://www.workday.com** ein.

     12\.3. Wählen Sie **SP-initiierte SAML-Authentifizierung aktivieren**.

     12\.4. Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Workday** den Wert für **Dienst-URL für einmaliges Anmelden**, und fügen Sie ihn in das Textfeld **Dienst-URL des Identitätsanbieters für einmaliges Anmelden** ein.
     
     12\.5 Wählen Sie **SP-initiierte Authentifizierungsanfrage nicht verkleinern**.

     12\.6. Wählen Sie als **Signaturmethode für Authentifizierungsanfragen** **SHA256**. <br><br> ![Signaturmethode für Authentifizierungsanfragen](./media/active-directory-saas-workday-tutorial/IC782932.png "Signaturmethode für Authentifizierungsanfragen") <br><br>
 
     12\.7 Klicken Sie auf **OK**.<br><br> ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. Klicken Sie im Azure AD-Portal auf der Seite **Einmaliges Anmelden bei Workday konfigurieren** auf **Weiter**. <br><br>

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-workday-tutorial/IC782934.png "Einmaliges Anmelden konfigurieren")

13. Klicken Sie auf der Seite **Bestätigung für einmaliges Anmelden** auf **Abschließen**. <br><br>

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-workday-tutorial/IC782935111.png "Einmaliges Anmelden konfigurieren")



##Konfigurieren der Benutzerbereitstellung
  
Um einen in Workday bereitgestellten Testbenutzer zu bekommen, müssen Sie sich an das Workday-Supportteam wenden. Das Workday-Supportteam erstellt den Benutzer für Sie.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Workday Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Workday** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-workday-tutorial/IC782935.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-workday-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->