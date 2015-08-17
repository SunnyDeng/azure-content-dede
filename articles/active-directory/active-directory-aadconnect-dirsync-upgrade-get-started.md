<properties 
   pageTitle="Microsoft Azure AD Connect – Upgrade vom Windows Azure AD-Synchronisierungstool (DirSync)" 
   description="Informationen Sie zum Aktualisieren von DirSync auf Azure AD Connect. Dieser Artikel beschreibt die Schritte zur Aktualisierung Ihres aktuellen Windows Azure AD-Synchronisierungstools (DirSync) auf Azure AD Connect." 
   services="active-directory" 
   documentationCenter="" 
   authors="shoatman" 
   manager="terrylanfear" 
   editor="billmath"/>

<tags 
   ms.service="active-directory" 
   ms.workload="identity" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="05/26/2015" 
   ms.author="shoatman"/>

# Aktualisieren des Windows Azure Active Directory-Synchronisierungstools (DirSync) auf Azure Active Directory Connect

Die folgende Dokumentation hilft Ihnen beim Upgrade Ihrer vorhandenen DirSync-Installation auf Azure AD Connect

## Azure AD Connect herunterladen

Für Ihre ersten Schritte mit Azure AD Connect können Sie die neuste Version über den folgenden Link herunterladen: [Download Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) (in englischer Sprache)


## Vor der Installation von Azure AD Connect
Vor der Installation von Azure AD Connect und dem Upgrade von Dir Sync gibt es einige Dinge, die Sie benötigen.

- Das Kennwort des vorhandenen globalen Administratorkontos für Ihre Azure AD-Instanz (beim Installieren werden Sie an das Konto erinnert)
- Ein Enterprise-Administratorkonto für Ihr lokales Active Directory
- Optional: Wenn Sie DirSync für eine Vollversion von SQL Server konfiguriert haben, die Informationen für diese Datenbank-Instanz.

### Parallele Bereitstellung

Wenn Sie derzeit mehr als 50 K-Objekte synchronisieren, dann erhalten Sie die Option zur Ausführung einer parallelen Bereitstellung. Die parallele Bereitstellung erfordert einen separaten Server oder einer Reihe von Servern (wenn Sie einen separaten Server für SQL Server benötigen). Der Vorteil der parallelen Bereitstellung ist die Vermeidung von Ausfallzeiten bei der Synchronisierung. Bei der Azure AD Connect-Installation wird die erwartete Ausfallzeit geschätzt, aber wenn Sie DirSync in der Vergangenheit bereits aktualisiert haben, dann ist Ihre eigene Erfahrung vermutlich ausschlaggebender.

## Installieren von Azure AD Connect

Azure AD Connect herunterladen und auf Ihren vorhandenen DirSync-Server kopieren.

1. Navigieren Sie zu AzureADConnect.msi und doppelklicken Sie darauf
2. Gehen Sie nun Schritt für Schritt durch den Assistenten

Bei einem direkten Upgrade gibt es folgende Schritte auf oberer Ebene:

1. Willkommen bei Azure AD Connect
2. Analyse der aktuellen DirSync-Konfiguration
3. Sammeln des globalen Azure AD-Administratorkennworts
4. Sammeln der Anmeldeinformationen für ein Enterprise-Administratorkonto (nur während der Installation von Azure AD Connect verwendet)
5. Installation von AAD Connect
    * Deinstallation von DirSync
	* Installieren Sie AAD Connect
	* Optionales Starten der Synchronisierung

Zusatzschritte/-informationen sind erforderlich, wenn:

* Sie derzeit einen vollständigen SQL Server verwenden (lokal oder Remote)
* Sie mehr als 50K-Objekte im Bereich für die Synchronisierung haben

## Direkte Aktualisierung – weniger als 50 K-Objekte – SQL Express (Exemplarische Vorgehensweise)

0. Starten Sie den Azure AD Connect-Installer (MSI)

1. Lesen Sie die Lizenzbedingungen und den Datenschutzhinweis und stimmen Sie diesen zu.

![Willkommen bei Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)

2. Klicken Sie auf „Weiter“ für die Analyse der vorhandenen DirSync-Installation

![Analysieren der vorhandenen Installation der Verzeichnissynchronisierung](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)

3. Nach Abschluss der Analyse machen wir Empfehlungen zum Fortfahren. In diesem Szenario (weniger als 50K-Objekte mit SQL Express) wird der folgende Bildschirm angezeigt.

![Abgeschlossene Analyse bereit für die Aktualisierung von DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)

4. Geben Sie das derzeit verwendete Kennwort für das Konto ein, um eine Verbindung zu Azure AD herzustellen.

![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)

5. Geben Sie ein Enterprise-Administratorkonto für Active Directory ein.

![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)

6. Sie können nun mit der Konfiguration loslegen. Wenn Sie auf „Weiter“ klicken, wird DirSync deinstalliert und Azure AD Connect konfiguriert und die Synchronisierung gestartet.  

![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)


## Direktes Upgrade – mehr als 50K-Objekte
Bei Schritt 3 sehen Sie eine andere Meldung, wenn Sie mehr als 50 K-Objekte im Bereich für die Synchronisierung haben. Ein Bildschirm ähnlich dem folgenden wird angezeigt:

![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

In diesem Fall empfehlen wir eine parallele Aktualisierung auf einem separaten Server. Der Grund für unsere Empfehlung ist, dass abhängig von der Größe Ihrer Organisation ein direktes Upgrade Auswirkungen auf Ihre Vereinbarungen zum Servicelevel mit Ihrem Unternehmen in Bezug darauf haben kann, wie schnell Änderungen im lokalen Active Directory in Azure AD angezeigt werden / Office 365. Wir schätzen ab, wie lange die erste Synchronisierung mit Azure AD Connect dauern könnte. Wie bereits erwähnt ist Ihre eigene Erfahrung mit der ursprünglichen Installation von DirSync oder Upgrades auf DirSync wahrscheinlich der beste Indikator.

Die parallele Bereitstellung erfordert einen separaten Server oder mehrere Server (wenn Sie SQL Server auf einem separaten Server von Azure AD Connect ausführen müssen). Aus diesem Grund ist es völlig angemessen, eine direkte Aktualisierung zu erwägen, wenn sie so geplant werden kann, dass Auswirkungen innerhalb Ihrer Organisation vermieden werden.

Für ein direktes Upgrade klicken Sie auf das Kontrollkästchen neben der Nachricht: "Mit der Aktualisierung von DirSync auf diesem Computer fortfahren".

## Direktes Upgrade - Vollständiger SQL Server

Bei Schritt 3 sehen Sie eine andere Meldung, wenn bei der DirSync-Installation eine lokale oder Remote-Vollversion von SQL Server verwendet wird. Ein Bildschirm ähnlich dem folgenden wird angezeigt:

![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)

Die Informationen zum vorhandenen SQL Server-Datenbankserver, der von DirSync verwendet wird, werden Ihnen angezeigt. Falls nötig, führen Sie die entsprechenden Anpassungen durch. Klicken Sie auf "Weiter", um die Installation fortzusetzen.

## Parallele Bereitstellung – mehr als 50K-Objekte

Wenn Sie bei Schritt 3 mehr als 50 K-Objekte haben, empfiehlt die Azure AD Connect-Installation eine parallele Bereitstellung. Informationen, ob eine direkte oder parallele Bereitstellung für Azure AD Connect ausgewählt werden sollte, finden Sie unter "Integrierte Aktualisierung - mehr als 50 K-Objekte". Ein Bildschirm ähnlich dem folgenden wird angezeigt:

![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Wenn Sie die parallele Bereitstellung fortsetzen möchten, müssen Sie die folgenden Schritte ausführen:

- Klicken Sie auf die Schaltfläche „Einstellungen exportieren“. Bei der Installation von Azure AD Connect auf einem separaten Server werden diese Einstellungen zum Migrieren von Einstellungen von Ihrem aktuellen DirSync auf Ihre neue AAD Connect-Installation importiert.

Sobald Sie Ihre Einstellungen erfolgreich exportiert haben, können Sie den Azure AD Connect-Assistenten auf dem DirSync-Server beenden.

### Installation von Azure AD Connect auf einem separaten Server

Bei der Installation von Azure AD Connect auf einem neuen Server wird DirSync nicht automatisch gefunden und es wird davon ausgegangen, dass Sie Azure AD Connect neu installieren möchten. Hier gibt es einige besondere Schritte:

1. Führen Sie den Azure AD Connect-Installer aus (MSI)
2. Wenn der Bildschirm "Willkommen bei Azure AD Connect" angezeigt wird, beenden Sie den Assistenten durch Klicken auf das "X" in der oberen rechten Ecke des Fensters.
3. Öffnen Sie eine Eingabeaufforderung.
4. Führen Sie aus dem Installationsordner von Azure AD Connect (Standard: C:\\Program Files\\Microsoft Azure Active Directory Connect) den folgenden Befehl aus:
    * AzureADConnect.exe /migrate

Azure AD Connect bietet Ihnen die folgende Benutzeroberfläche:

![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)

5. Wählen Sie die Datei mit den Einstellungen, die von Ihrer DirSync-Installation exportiert wurde.
6. Konfigurieren Sie alle erweiterten Optionen, einschließlich:
    * Einen benutzerdefinierten Speicherort für Azure AD Connect
	* Eine vorhandene Instanz von SQL Server (Standard: Azure AD Connect installiert SQL Server 2012 Express)
	* Ein Dienstkonto für die Verbindung zu SQL Server (wenn Ihre SQL Server-Datenbank remotegesteuert ist, dann muss dieses Konto ein Domänendienstkonto sein)

Diese Optionen finden Sie in der folgenden Benutzeroberfläche:

![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)

7. Klicken Sie auf Weiter. 
8. Belassen Sie auf der Seite "Bereit zur Konfiguration" das Kontrollkästchen "Start the synchronization process as soon as the configuration completes" aktiviert.[AZURE.NOTE]Die Synchronisierung zwischen Windows Server Active Directory und Azure Active Directory wird gestartet, es werden aber keine Änderungen an Azure AD exportiert. Nur ein Synchronisierungstool kann jeweils aktiv Änderungen exportieren.
9. Klicken Sie auf "Installieren".

[AZURE.NOTE]Deaktivieren Sie das Kontrollkästchen „Synchronisierung starten“, um sicherzustellen, dass das noch immer installierte und laufende DirSync und Azure AD Connect nicht zur gleichen Zeit an AAD zu schreiben versuchen.

### Überprüfen Sie, ob Azure AD Connect bereit für die Synchronisierung ist.

Um zu bestimmen, ob Azure AD Connect für die Übernahme von DirSync bereits ist, müssen Sie den Synchronization Service Manager von Azure AD Connect öffnen. Die Suche mit "Synchronisierung" im Startmenü von Windows zeigt diese Anwendung an.

In der Anwendung gehen Sie auf die Registerkarte "Vorgänge". In dieser Registerkarte überprüfen Sie, dass die folgenden Vorgänge abgeschlossen wurden:

- Import auf dem AD-Verwaltungs-Agenten
- Import auf dem Azure AD-Verwaltungs-Agenten
- Vollständige Synchronisierung auf dem AD-Verwaltungs-Agenten
- Vollständige Synchronisierung auf dem Azure AD-Verwaltungs-Agenten

Sobald diese vier Vorgänge abgeschlossen sind, können Sie DirSync deinstallieren und die Azure AD Connect-Synchronisierung aktivieren.

### Deinstallieren von DirSync (alter Server)

- Suchen Sie in "Programme hinzufügen oder entfernen" nach dem "Windows Azure Active Directory-Synchronisierungstool"
- Deinstallieren Sie das „Windows Azure Active Directory-Synchronisierungstool“

### Öffnen Sie Azure AD Connect (neuer Server)
Nach der Installation öffnen Sie Azure AD Connect erneut für die Konfiguration. Öffnen Sie Azure AD Connect.

Daraufhin sollte Folgendes angezeigt werden:

![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

* Wählen Sie "Stagingmodus konfigurieren".
    * Das Aktualisieren von DirSync mit den exportierten Einstellungen versetzt Azure AD Connect automatisch in den Stagingmodus. Stagingmodus bedeutet im Grunde, dass die Synchronisierung innerhalb von Azure AD Connect stattfindet, Änderungen aber nicht nach Azure AD oder AD exportiert werden.
* Deaktivieren Sie den Stagingmodus durch Klicken auf das Kontrollkästchen "Stagingmodus aktiviert".

![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

* Klicken Sie auf die Schaltfläche "Installieren".

Herzlichen Glückwunsch, Sie haben Azure AD Connect erfolgreich mithilfe der parallelen Bereitstellung migriert.

## Azure AD Connect unterstützende Komponenten

Folgendes ist eine Liste der erforderlichen und unterstützenden Komponenten, die Azure AD Connect auf dem Server installiert, auf dem Azure AD Connect eingerichtet ist. Diese Liste ist für eine einfache Expressinstallation. Wenn Sie einen anderen SQL Server auf der Installationsseite für Synchronisierungsdienste verwenden möchten, werden die unten aufgeführten SQL Server 2012-Komponenten nicht installiert.

- Forefront Identity Manager – Azure Active Directory Connector
- Microsoft SQL Server 2012 – Befehlszeilenprogramme
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Azure Active Directory-Modul für Windows PowerShell
- Microsoft Online Services-Anmeldeassistent für IT-Experten
- Microsoft Visual C++ 2013 Redistributionspaket


**Zusätzliche Ressourcen**

* [Verwenden Sie Ihre lokale Identitätsinfrastruktur in der Cloud](active-directory-aadconnect.md)
* [Funktionsweise von Azure AD Connect](active-directory-aadconnect-how-it-works.md)
* [Wie geht es weiter mit Azure AD Connect?](active-directory-aadconnect-whats-next.md)
* [Weitere Informationen](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect auf MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=August15_HO6-->