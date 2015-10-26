<properties pageTitle="Tutorial: Azure Active Directory-Integration mit Jive | Microsoft Azure" description="Hier erfahren Sie, wie Sie Jive mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit Jive
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=330042), um Feedback abzugeben. Weitere Informationen finden Sie unter [Bewährte Methoden zur Verwaltung der Verbesserungen des Anwendungszugriffs für Azure Active Directory](http://go.microsoft.com/fwlink/?LinkId=329963).
  
In diesem Tutorial wird die Integration von Azure und Jive erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Jive-Mandanten
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Jive
2.  Konfigurieren der Benutzerbereitstellung

##Aktivieren der Anwendungsintegration für Jive
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Jive aktivieren.

###So aktivieren Sie die Anwendungsintegration für Jive

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jive-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-jive-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie zum Öffnen des **Anwendungskatalogs** auf **App hinzufügen** und anschließend auf **Eine Anwendung hinzufügen, die mein Unternehmen verwendet**.

    ![Was möchten Sie tun?](./media/active-directory-saas-jive-tutorial/IC700995.png "Was möchten Sie tun?")

5.  Geben Sie im **Suchfeld** das Wort **Jive** ein.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701001.png "Jive")

6.  Wählen Sie im Ergebnisbereich **Jive** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701005.png "Jive")
##Konfigurieren der Benutzerbereitstellung
  
In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Jive aktivieren. Im Rahmen dieses Verfahrens werden Sie zur Angabe eines Benutzersicherheitstokens aufgefordert, das Sie von „Jive.com“ anfordern müssen.
  
Der folgende Screenshot zeigt ein Beispiel für das entsprechende Dialogfeld in Azure AD:

![Benutzerbereitstellung konfigurieren](./media/active-directory-saas-jive-tutorial/IC698794.png "Benutzerbereitstellung konfigurieren")

###So konfigurieren Sie die Benutzerbereitstellung

1.  Klicken Sie im Azure-Verwaltungsportal auf der Anwendungsintegrationsseite für **Jive** auf **Benutzerbereitstellung konfigurieren**, um das Dialogfeld **Benutzerbereitstellung konfigurieren** zu öffnen.

2.  Legen Sie auf der Seite **Geben Sie Ihre Jive-Anmeldeinformationen ein, um die automatische Benutzerbereitstellung zu aktivieren** die folgenden Konfigurationseinstellungen fest:

    1.  Geben Sie im Textfeld für den **Jive-Admin-Benutzernamen** den Namen eines Jive-Kontos ein, dem das Profil **Systemadministrator** in „Jive.com“ zugewiesen ist.

    2.  Geben Sie im Feld **Jive-Administratorkennwort** das Kennwort für das Konto ein.

    3.  Geben Sie im Textfeld **Jive-Mandanten-URL** die URL des Jive-Mandanten ein.

        >[AZURE.NOTE]Die Jive-Mandanten-URL ist die URL, die von Ihrer Organisation zum Anmelden bei Jive verwendet wird. In der Regel hat die URL folgendes Format: **www.<organisation>.jive.com**.

    4.  Klicken Sie auf **Überprüfen**, um die Konfiguration zu überprüfen.

    5.  Klicken Sie auf **Weiter**, um die Bestätigungsseite zu öffnen.

3.  Klicken Sie auf der Bestätigungsseite auf das Häkchen, um die Konfiguration zu speichern.
  
Sie können nun ein Testkonto erstellen und nach 10 Minuten überprüfen, ob das Konto mit „Jive.com“ synchronisiert wurde.

<!---HONumber=Oct15_HO3-->