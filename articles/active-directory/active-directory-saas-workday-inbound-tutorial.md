<properties 
    pageTitle="Tutorial: Konfigurieren von Workday für eingehende Synchronisierung | Microsoft Azure" 
    description="Erfahren Sie, wie Workday als Quelle von Identitätsdaten für Azure Active Directory verwendet wird." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />


#Tutorial: Konfigurieren von Workday für eingehende Synchronisierung


In diesem Tutorial werden die Schritte erläutert, die Sie in Workday und Azure AD ausführen müssen, um Personen aus Workday in Azure AD zu importieren.

Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure AD Premium-Abonnement
-   Einen Mandanten in Workday
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1. Aktivieren der Anwendungsintegration für Workday 


2. Erstellen eines Integrationssystembenutzers


3. Erstellen einer Sicherheitsgruppe


4. Zuweisen des Integrationssystembenutzers zur Sicherheitsgruppe


5. Konfigurieren von Sicherheitsgruppenoptionen


6. Aktivieren von Sicherheitsrichtlinienänderungen


7. Konfigurieren des Benutzerimports in Azure AD



##Aktivieren der Anwendungsintegration für Workday
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Workday aktivieren.

###So aktivieren Sie die Anwendungsintegration für Workday

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Anwendung hinzufügen")

  
5. Geben Sie im Suchfeld das Wort **Workday** ein.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Anwendung aus dem Katalog hinzufügen")

6. Wählen Sie im Ergebnisbereich "Workday" aus, und klicken Sie dann auf "Abschließen", um die Anwendung hinzuzufügen.

    ![Anwendungskatalog](./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Anwendungskatalog")





## Erstellen eines Integrationssystembenutzers

1. Geben Sie in der **Workday-Workbench** die Suchzeichenfolge "Benutzer erstellen" in das Suchfeld ein, und klicken Sie dann auf den Link **Integrationssystembenutzer erstellen**. <br><br>![Benutzer erstellen](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Benutzer erstellen")



2. Führen Sie die Aufgabe **Integrationssystembenutzer erstellen** aus, indem Sie einen Benutzernamen und ein Kennwort für einen neuen Integrationssystembenutzer angeben. Lassen Sie das Kontrollkästchen „Bei der nächsten Anmeldung neues Kennwort anfordern“ deaktiviert. Dieser Benutzer meldet sich programmgesteuert an. <br> Übernehmen Sie für "Sitzungstimeout in Minuten" den Standardwert 0. Diese Einstellung verhindert, dass Sitzungen des Benutzers vorzeitig beendet werden. <br><br> ![Integrationssystembenutzer erstellen](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Integrationssystembenutzer erstellen")
 




## Erstellen einer Sicherheitsgruppe

Für das in diesem Tutorial beschriebene Szenario müssen Sie eine uneingeschränkte Integrationssystem-Sicherheitsgruppe erstellen und den Benutzer dieser Gruppe zuweisen.


1. Geben Sie "Sicherheitsgruppe erstellen" in das Suchfeld ein, und klicken Sie dann auf **Sicherheitsgruppe erstellen**. <br><br> ![Sicherheitsgruppe erstellen](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Sicherheitsgruppe erstellen")
 

2. Führen Sie die Aufgabe „Sicherheitsgruppe erstellen“ aus. Wählen Sie in der Dropdownliste "Typ der Mandantensicherheitsgruppe" die Option "Integrationssystem-Sicherheitsgruppe – uneingeschränkt" aus, um eine Sicherheitsgruppe zu erstellen, der Mitglieder explizit hinzugefügt werden. <br><br> ![Sicherheitsgruppe erstellen](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Sicherheitsgruppe erstellen")
 



## Zuweisen des Integrationssystembenutzers zur Sicherheitsgruppe

1. Geben Sie "Sicherheitsgruppe bearbeiten" in das Suchfeld ein, und klicken Sie dann auf **Sicherheitsgruppe bearbeiten**. <br><br> ![Sicherheitsgruppe bearbeiten](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Sicherheitsgruppe bearbeiten")
 
 

2. Suchen Sie anhand des Namens nach der neuen Integrationssicherheitsgruppe, und wählen Sie sie aus. <br><br> ![Sicherheitsgruppe bearbeiten](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Sicherheitsgruppe bearbeiten") Sicherheitsgruppe bearbeiten

3. Fügen Sie den neuen Integrationssystembenutzer der neuen Sicherheitsgruppe hinzu. <br><br> ![Systemsicherheitsgruppe](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Systemsicherheitsgruppe")




## Konfigurieren von Sicherheitsgruppenoptionen

In diesem Schritt gewähren Sie der neuen Sicherheitsgruppe Berechtigungen für **Get**- und **Put**-Vorgänge für die durch die folgenden Domänensicherheitsrichtlinien geschützten Objekte:

- Externe Kontobereitstellung

- Mitarbeiterdaten: öffentliche Mitarbeiterberichte

- Mitarbeiterdaten: alle Positionen

- Mitarbeiterdaten: aktuelle Personalinformationen

- Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil
 
   

1. Geben Sie "Domänensicherheitsrichtlinien" in das Suchfeld ein, und klicken Sie dann auf den Link "Domänensicherheitsrichtlinien für Funktionsbereich". <br><br> ![Domänensicherheitsrichtlinien](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Domänensicherheitsrichtlinien")  
 

2. Suchen Sie nach "System", und wählen Sie den Funktionsbereich **System** aus. Klicken Sie auf **OK**. <br><br> ![Domänensicherheitsrichtlinien](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Domänensicherheitsrichtlinien")


3. Erweitern Sie in der Liste der Sicherheitsrichtlinien für den Funktionsbereich "System" den Eintrag "Sicherheitsverwaltung", und wählen Sie die Domänensicherheitsrichtlinie "Externe Kontobereitstellung" aus. <br><br> ![Domänensicherheitsrichtlinien](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Domänensicherheitsrichtlinien")


4. Klicken Sie auf **Berechtigungen bearbeiten**, und fügen Sie die neue Sicherheitsgruppe auf der Dialogfeldseite **Berechtigungen bearbeiten** zur Liste der Sicherheitsgruppen mit Berechtigungen für die **Get**- und **Put**-Integration hinzu. <br><br> ![Berechtigung bearbeiten](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Berechtigung bearbeiten")

 

5. Wiederholen Sie Schritt 1 oben, um zum Bildschirm für die Auswahl der Funktionsbereiche zurückzukehren. Suchen Sie nach "Personal", wählen Sie den Funktionsbereich "Personal" aus, und klicken Sie auf die Schaltfläche **OK**.<br><br> ![Domänensicherheitsrichtlinien](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Domänensicherheitsrichtlinien")
 

6. Erweitern Sie in der Liste der Sicherheitsrichtlinien für den Funktionsbereich „Personal“ den Eintrag „Mitarbeiterdaten: Personal“, und wiederholen Sie Schritt 4 für die folgenden Sicherheitsrichtlinien:

     - Mitarbeiterdaten: öffentliche Mitarbeiterberichte

     - Mitarbeiterdaten: alle Positionen

     - Mitarbeiterdaten: aktuelle Personalinformationen

     - Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil


<br><br> ![Domänensicherheitsrichtlinien](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Domänensicherheitsrichtlinien")







## Aktivieren von Sicherheitsrichtlinienänderungen


1. Geben Sie "aktivieren" in das Suchfeld ein, und klicken Sie dann auf den Link "Ausstehende Sicherheitsrichtlinienänderungen aktivieren". <br><br> ![Aktivieren](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Aktivieren") 
 

2. Geben Sie zum Ausführen der Aufgabe "Ausstehende Sicherheitsrichtlinienänderungen aktivieren" zunächst einen Kommentar für Überwachungszwecke ein, und klicken Sie dann auf die Schaltfläche **OK**. <br><br> ![Ausstehende Sicherheitsrichtlinienänderungen aktivieren](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Ausstehende Sicherheitsrichtlinienänderungen aktivieren")
 

3. Führen Sie die Aufgabe auf dem nächsten Bildschirm durch, indem Sie das Kontrollkästchen "Bestätigen" aktivieren und auf **OK** klicken. <br><br> ![Ausstehende Sicherheitsrichtlinienänderungen aktivieren](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Ausstehende Sicherheitsrichtlinienänderungen aktivieren")





## Konfigurieren des Benutzerimports in Azure AD

In diesem Abschnitt wird beschrieben, wie Sie Azure AD zum Importieren von Personen aus Workday konfigurieren.


### So konfigurieren Sie den Benutzerimport in Azure AD


1. Klicken Sie auf der Anwendungsintegrationsseite für **Workday** auf **Benutzerimport konfigurieren**, um das Dialogfeld **Bereitstellung konfigurieren** zu öffnen.


2\. Führen Sie auf der Seite **Einstellungen und Administrator-Anmeldeinformationen** die folgenden Schritte aus, und klicken Sie anschließend auf **Weiter**: <br><br> ![Einstellungen und Administrator-Anmeldeinformationen](./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Einstellungen und Administrator-Anmeldeinformationen")
 
     2.1. In the Workday admin user name textbox, type the name of the user you have created in the Creating an integration system user section.

     2.2. In the Workday admin password textbox, type the password of the user you have created in the Creating an integration system user section.

     2.3. In the Workday tenant URL textbox, type the URL or your Workday tenant.


3. Klicken Sie auf der Seite **Verbindung testen** auf **Test starten**, um die Konnektivität zu überprüfen, und klicken Sie anschließend auf **Weiter**. <br><br> ![Verbindung testen](./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Verbindung testen")  
 

4. Klicken Sie auf der Seite **Bereitstellungsoptionen** auf **Weiter**. <br><br> ![Bereitstellungsoptionen](./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Bereitstellungsoptionen")


5. Klicken Sie im Dialogfeld **Bereitstellung beginnen** auf **Abschließen**. <br><br> ![Bereitstellung beginnen](./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Bereitstellung beginnen")
 

Jetzt können Sie zum Abschnitt **Benutzer** wechseln und überprüfen, ob der Workday-Benutzer importiert wurde.



## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0114_2016-->