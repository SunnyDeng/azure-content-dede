<properties
	pageTitle="Vorschau von Azure Active Directory-Domänendienste: erste Schritte | Microsoft Azure"
	description="Erste Schritte mit Azure Active Directory-Domänendienste"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="maheshu"/>

# Azure AD-Domänendienste *(Vorschau)* – erste Schritte

## Schritt 3: Aktivieren von Azure AD-Domänendienste
Mit diesem Schritt können Sie Azure AD-Domänendienste für Ihr Verzeichnis aktivieren. Führen Sie die folgenden Konfigurationsschritte aus, um Azure AD-Domänendienste für Ihr Verzeichnis zu aktivieren.

1. Navigieren Sie zum **Azure-Verwaltungsportal** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Wählen Sie den Knoten **Active Directory** im linken Bereich aus.
3. Wählen Sie den Azure AD-Mandanten (Verzeichnis) aus, für den Sie Azure AD-Domänendienste aktivieren möchten.

    ![Auswählen des Azure AD-Verzeichnisses](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Klicken Sie auf die Registerkarte **Konfigurieren**.

    ![Registerkarte "Konfigurieren" des Verzeichnisses](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Scrollen Sie hinunter zum Abschnitt **Domänendienste**.

    ![Konfigurationsabschnitt von Domänendienste](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Schalten Sie die Option **Domänendienste für dieses Verzeichnis aktivieren** auf **JA**. Es werden einige weitere Konfigurationsoptionen für Azure AD-Domänendienste auf der Seite angezeigt.

    ![Aktivieren von Domänendienste](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE]Wenn Sie Azure AD-Domänendienste für Ihren Mandanten aktivieren, generiert und speichert Azure AD die erforderlichen Hashwerte für die Kerberos- und NTLM-Anmeldeinformationen zum Authentifizieren von Benutzern.

7. Geben Sie einen Wert für **DNS-Domänenname der Domänendienste** an.
   - Standardmäßig ist der Standarddomänenname des Verzeichnisses (d. h. mit dem Domänensuffix **.onmicrosoft.com**) ausgewählt.
   - Im Dropdownmenü werden alle Domänen aufgeführt, die für Ihr Azure AD-Verzeichnis konfiguriert wurden, einschließlich überprüfter und nicht überprüfter Domänen, die Sie auf der Registerkarte "Domänen" konfigurieren.
   - Darüber hinaus können Sie in dieser bearbeitbaren Dropdownliste auch einen benutzerdefinierten Domänennamen eingeben.

8. Im nächsten Schritt wird ein virtuelles Netzwerk ausgewählt, in dem Azure AD-Domänendienste verfügbar sein soll. Wählen Sie das soeben erstellte virtuelle Netzwerk in der Dropdownliste mit der Bezeichnung **Domänendienste mit diesem virtuellen Netzwerk verbinden** aus.
9. Wenn Sie die obigen Optionen ausgewählt haben, klicken Sie im Aufgabenbereich am unteren Rand der Seite auf "Speichern", um Azure AD-Domänendienste zu aktivieren.
10. Auf der Seite wird der Status "Ausstehend" angezeigt, während Azure AD-Domänendienste für das Verzeichnis aktiviert wird.

    ![Aktivieren von Domänendienste – Status "Ausstehend"](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE]Azure AD-Domänendienste bietet eine hohe Verfügbarkeit für Ihre verwaltete Domäne. Wenn Sie Azure AD-Domänendienste für Ihre Domäne erstmalig aktivieren, werden die IP-Adressen, unter denen Domänendienste im virtuellen Netzwerk verfügbar ist, nacheinander angezeigt. Die zweite IP-Adresse wird angezeigt, sobald der Dienst hohe Verfügbarkeit für Ihre Domäne aktiviert. Wenn hohe Verfügbarkeit für Ihre Domäne konfiguriert und aktiv ist, sollten im Abschnitt **Domänendienste** der Registerkarte **Konfigurieren** zwei IP-Adressen angezeigt werden.

11. Nach ungefähr 20 bis 30 Minuten wird im Feld **IP-Adresse** auf der Seite **Konfigurieren** die erste IP-Adresse angezeigt, unter der Domänendienste in Ihrem virtuellen Netzwerk verfügbar ist.

    ![Domänendienste aktiviert – erste IP-Adresse bereitgestellt](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

12. Wenn hohe Verfügbarkeit für Ihre Domäne funktionsfähig ist, werden auf der Seite zwei IP-Adressen angezeigt. Dies sind die IP-Adressen, unter denen Azure AD-Domänendienste im ausgewählten virtuellen Netzwerk verfügbar ist. Notieren Sie sich diese IP-Adressen, damit Sie die DNS-Einstellungen für Ihr virtuelles Netzwerk aktualisieren können. Mit diesem Schritt können virtuelle Computer im virtuellen Netzwerk eine Verbindung mit der Domäne für Vorgänge wie Domänenbeitritt herstellen.

    ![Domänendienste aktiviert – beide IP-Adressen bereitgestellt](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

  >[AZURE.NOTE]Abhängig von der Größe Ihres Azure AD-Verzeichnisses (Anzahl der Benutzer, Gruppen usw.) dauert es eine Weile, bis der Inhalt des Verzeichnisses in Azure AD-Domänendienste verfügbar ist. Diese Synchronisation erfolgt im Hintergrund. Bei großen Verzeichnissen mit Zehntausenden von Objekten kann es möglicherweise ein oder zwei Tage dauern, bis alle Benutzer, Gruppenmitgliedschaften und Anmeldeinformationen synchronisiert wurden und in Azure AD-Domänendienste verfügbar sind.


---
[**Nächster Schritt – Aktualisieren der DNS-Einstellungen für das virtuelle Azure-Netzwerk**](active-directory-ds-getting-started-dns.md)

<!---HONumber=Oct15_HO3-->