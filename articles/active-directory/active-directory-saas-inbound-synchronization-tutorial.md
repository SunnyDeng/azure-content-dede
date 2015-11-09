<properties 
    pageTitle="Tutorial: Konfigurieren von Workday für eingehende Synchronisierung | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Workday mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Tutorial: Konfigurieren von Workday für eingehende Synchronisierung
>[AZURE.NOTE]Azure Active Directory (AD) Premium steht für Kunden in China zur Verfügung, die mit der weltweit verfügbaren Instanz von Azure AD arbeiten. Azure AD Premium wird derzeit im von 21Vianet in China betriebenen Microsoft Azure-Dienst nicht unterstützt.

In diesem Tutorial werden die Schritte erläutert, die Sie in Workday und Microsoft Azure AD ausführen müssen, um Personen aus Workday in Microsoft Azure AD zu importieren. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement  
-   Einen Mandanten in Workday  

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Workday  
2.  Erstellen eines Integrationssystembenutzers  
3.  Erstellen einer Sicherheitsgruppe  
4.  Zuweisen des Integrationssystembenutzers zur Sicherheitsgruppe  
5.  Konfigurieren von Sicherheitsgruppenoptionen  
6.  Aktivieren von Sicherheitsrichtlinienänderungen  
7.  Konfigurieren des Benutzerimports in Microsoft Azure AD  

##Aktivieren der Anwendungsintegration für Workday

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Workday aktivieren.

###So aktivieren Sie die Anwendungsintegration für Workday

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.    

	![Active Directory](./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

	![Anwendungen](./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie zum Öffnen des **Anwendungskatalogs** auf **App hinzufügen** und anschließend auf **Eine Anwendung hinzufügen, die mein Unternehmen verwendet**.

	![Was möchten Sie tun?](./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "Was möchten Sie tun?")

5.  Geben Sie im **Suchfeld** den Suchbegriff **Workday** ein.

	![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "Workday")

6.  Wählen Sie im Ergebnisbereich **Workday** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

	![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "Workday")

##Erstellen eines Integrationssystembenutzers

1.  Geben Sie in der **Workday-Workbench** den Suchbegriff **Benutzer erstellen** in das Suchfeld ein, und klicken Sie dann auf den Link **Integrationssystembenutzer erstellen**.     

	![Benutzer erstellen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "Benutzer erstellen")

2.  Führen Sie die Aufgabe „Integrationssystembenutzer erstellen“ aus, indem Sie einen Benutzernamen und ein Kennwort für einen neuen Integrationssystembenutzer angeben. Lassen Sie das Kontrollkästchen „Bei der nächsten Anmeldung neues Kennwort anfordern“ deaktiviert. Dieser Benutzer meldet sich programmgesteuert an. Übernehmen Sie für „Sitzungstimeout in Minuten“ den Standardwert 0. Diese Einstellung verhindert, dass Sitzungen des Benutzers vorzeitig beendet werden.

	![Integrationssystembenutzer erstellen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "Integrationssystembenutzer erstellen")

##Erstellen einer Sicherheitsgruppe

Für das in diesem Tutorial beschriebene Szenario müssen Sie eine uneingeschränkte Integrationssystem-Sicherheitsgruppe erstellen und den Benutzer dieser Gruppe zuweisen.

1.  Geben Sie „Sicherheitsgruppe erstellen“ in das Suchfeld ein, und klicken Sie dann auf den Link „Sicherheitsgruppe erstellen“.     

	![Sicherheitsgruppe erstellen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "Sicherheitsgruppe erstellen")

2.  Führen Sie die Aufgabe „Sicherheitsgruppe erstellen“ aus. Wählen Sie in der Dropdownliste „Typ der Mandantensicherheitsgruppe“ die Option „Integrationssystem-Sicherheitsgruppe – uneingeschränkt“ aus, um eine Sicherheitsgruppe zu erstellen, der Mitglieder explizit hinzugefügt werden.

	![Sicherheitsgruppe erstellen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "Sicherheitsgruppe erstellen")

##Zuweisen des Integrationssystembenutzers zur Sicherheitsgruppe

1.  Geben Sie „Sicherheitsgruppe bearbeiten“ in das Suchfeld ein, und klicken Sie dann auf den Link **Sicherheitsgruppe bearbeiten**.     

	![Sicherheitsgruppe bearbeiten](./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "Sicherheitsgruppe bearbeiten")

2.  Suchen Sie anhand des Namens nach der neuen Integrationssicherheitsgruppe, und wählen Sie sie aus.

	![Sicherheitsgruppe bearbeiten](./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "Sicherheitsgruppe bearbeiten")

3.  Fügen Sie den neuen Integrationssystembenutzer der neuen Sicherheitsgruppe hinzu.

	![Systemsicherheitsgruppe](./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "Systemsicherheitsgruppe")

##Konfigurieren von Sicherheitsgruppenoptionen

In diesem Schritt gewähren Sie der neuen Sicherheitsgruppe Berechtigungen für Get- und Put-Vorgänge für die durch die folgenden Domänensicherheitsrichtlinien geschützten Objekte:

-   Externe Kontobereitstellung  
-   Mitarbeiterdaten: öffentliche Mitarbeiterberichte  
-   Mitarbeiterdaten: alle Positionen  
-   Mitarbeiterdaten: aktuelle Personalinformationen  
-   Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil  

&nbsp;

1.  Geben Sie „Domänensicherheitsrichtlinien“ in das Suchfeld ein, und klicken Sie dann auf den Link „Domänensicherheitsrichtlinien für Funktionsbereich“.     

	![Domänensicherheitsrichtlinien](./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "Domänensicherheitsrichtlinien")

2.  Suchen Sie nach „System“, und wählen Sie den Funktionsbereich „System“ aus. Klicken Sie auf die Schaltfläche „OK“.

	![Domänensicherheitsrichtlinien](./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "Domänensicherheitsrichtlinien")

3.  Erweitern Sie in der Liste der Sicherheitsrichtlinien für den Funktionsbereich „System“ den Eintrag „Sicherheitsverwaltung“, und wählen Sie die Domänensicherheitsrichtlinie „Externe Kontobereitstellung“ aus.

	![Domänensicherheitsrichtlinien](./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "Domänensicherheitsrichtlinien")

4.  Klicken Sie auf die Schaltfläche „Berechtigungen bearbeiten“, und fügen Sie die neue Sicherheitsgruppe auf dem Bildschirm „Berechtigungen bearbeiten“ zur Liste der Sicherheitsgruppen mit Berechtigungen für die Get- und Put-Integration hinzu.

	![Berechtigung bearbeiten](./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "Berechtigung bearbeiten")

5.  Wiederholen Sie Schritt 1 oben, um zum Bildschirm für die Auswahl der Funktionsbereiche zurückzukehren. Suchen Sie nach „Personal“, wählen Sie den Funktionsbereich „Personal“ aus, und klicken Sie auf die Schaltfläche „OK“.

	![Domänensicherheitsrichtlinien](./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "Domänensicherheitsrichtlinien")

6.  Erweitern Sie in der Liste der Sicherheitsrichtlinien für den Funktionsbereich „Personal“ den Eintrag „Mitarbeiterdaten: Personal“, und wiederholen Sie Schritt 4 für die folgenden Sicherheitsrichtlinien:

	-   Mitarbeiterdaten: öffentliche Mitarbeiterberichte  
    -   Mitarbeiterdaten: alle Positionen  
    -   Mitarbeiterdaten: aktuelle Personalinformationen  
    -   Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil    

	![Domänensicherheitsrichtlinien](./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "Domänensicherheitsrichtlinien")

##Aktivieren von Sicherheitsrichtlinienänderungen

1.  Geben Sie „aktivieren“ in das Suchfeld ein, und klicken Sie dann auf den Link „Ausstehende Sicherheitsrichtlinienänderungen aktivieren“.    

	![Aktivieren](./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "Aktivieren")

2.   Geben Sie zum Ausführen der Aufgabe „Ausstehende Sicherheitsrichtlinienänderungen aktivieren“ zunächst einen Kommentar für Überwachungszwecke ein, und klicken Sie dann auf die Schaltfläche „OK“.

	![Ausstehende Sicherheitsrichtlinienänderungen aktivieren](./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "Ausstehende Sicherheitsrichtlinienänderungen aktivieren")

3.  Führen Sie die Aufgabe auf dem nächsten Bildschirm durch, indem Sie das Kontrollkästchen „Bestätigen“ aktivieren und auf „OK“ klicken.

	![Ausstehende Sicherheitsrichtlinienänderungen aktivieren](./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "Ausstehende Sicherheitsrichtlinienänderungen aktivieren")

##Konfigurieren des Benutzerimports in Microsoft Azure AD

In diesem Abschnitt wird beschrieben, wie Sie Microsoft Azure AD zum Importieren von Personen aus Workday konfigurieren.

###So konfigurieren Sie den Benutzerimport in Microsoft Azure AD

1.  Klicken Sie auf der Anwendungsintegrationsseite für **Workday** auf **Benutzerimport konfigurieren**, um das Dialogfeld **Bereitstellung konfigurieren** zu öffnen.    

2.  Führen Sie auf der Seite **Einstellungen und Administrator-Anmeldeinformationen** die folgenden Schritte aus, und klicken Sie anschließend auf „Weiter“:

	![Einstellungen und Administrator-Anmeldeinformationen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "Einstellungen und Administrator-Anmeldeinformationen")

	1.  Geben Sie im Textfeld für den **Workday-Admin-Benutzernamen** den Namen des Benutzers ein, den Sie im Abschnitt [Erstellen eines Integrationssystembenutzers](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) erstellt haben.    
    2.  Geben Sie im Textfeld für das **Workday-Admin-Kennwort** das Kennwort des Benutzers ein, den Sie im Abschnitt [Erstellen eines Integrationssystembenutzers](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) erstellt haben.    
    3.  Geben Sie im Textfeld für die **Workday-Mandanten-URL** die URL Ihres Workday-Mandanten ein.    

3.  Klicken Sie auf der Seite **Verbindung testen** auf **Test starten**, um die Konnektivität zu überprüfen, und klicken Sie anschließend auf **Weiter**.

	![Verbindung testen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "Verbindung testen")

4.  Klicken Sie auf der Seite **Bereitstellungsoptionen** auf **Weiter**.

	![Bereitstellungsoptionen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "Bereitstellungsoptionen")

5.  Klicken Sie im Dialogfeld **Bereitstellung beginnen** auf **Abschließen**.

	![Bereitstellung beginnen](./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "Bereitstellung beginnen")

Jetzt können Sie zum Abschnitt **Benutzer** wechseln und überprüfen, ob der Workday-Benutzer importiert wurde.

<!---HONumber=Nov15_HO1-->