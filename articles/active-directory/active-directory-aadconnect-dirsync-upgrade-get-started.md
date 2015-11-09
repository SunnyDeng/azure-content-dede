<properties
   pageTitle="Microsoft Azure AD Connect – Upgrade vom Windows Azure AD-Synchronisierungstool (DirSync) | Microsoft Azure"
   description="Informationen Sie zum Aktualisieren von DirSync auf Azure AD Connect. Dieser Artikel beschreibt die Schritte zur Aktualisierung Ihres aktuellen Windows Azure AD-Synchronisierungstools (DirSync) auf Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/26/2015"
   ms.author="shoatman;billmath"/>

# Aktualisieren des Windows Azure Active Directory-Synchronisierungstools (DirSync) auf Azure Active Directory Connect (Azure AD Connect)

Die folgende Dokumentation hilft Ihnen beim Upgrade Ihrer vorhandenen DirSync-Installation auf Azure AD Connect.

## Verwandte Dokumentation
Wenn Sie die Dokumentation zum [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md) nicht gelesen haben, finden Sie in der folgenden Tabelle Links zu verwandten Themen. Sie müssen die ersten beiden fett hervorgehobenen Themen gelesen haben, bevor Sie mit dem Upgrade von DirSync beginnen.

| Thema | |
| --------- | --------- |
| **Azure AD Connect herunterladen** | [Azure AD Connect herunterladen](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **Hardware und Voraussetzungen** | [Azure AD Connect: Hardware und Voraussetzungen](active-directory-aadconnect-prerequisites.md) |
| **Für die Installation verwendete Konten** | [Weitere Informationen zu Azure AD Connect-Konten und -Berechtigungen](active-directory-aadconnect-accounts-permissions.md) |

## Upgrade von DirSync
Je nach Ihrer aktuellen DirSync-Bereitstellung gibt es unterschiedliche Optionen für das Upgrade. Falls die erwartete Upgradezeit weniger als drei Stunden beträgt, lautet die Empfehlung, ein direktes Upgrade durchzuführen. Wenn die erwartete Upgradezeit mehr als drei Stunden beträgt, empfehlen wir Ihnen die Durchführung einer parallelen Bereitstellung auf einem anderen Server. Die Schätzung lautet, dass das Upgrade länger als drei Stunden dauert, wenn Sie über mehr als 50.000 Objekte verfügen.

| Szenario | |
| ---- | ---- |
| [Direktes Upgrade](#in-place-upgrade) | Die ist die bevorzugte Option, wenn das Upgrade voraussichtlich weniger als drei Stunden in Anspruch nimmt. |
| [Parallele Bereitstellung](#parallel-deployment) | Die ist die bevorzugte Option, wenn das Upgrade voraussichtlich mehr als drei Stunden in Anspruch nimmt. |

>[AZURE.NOTE]Wenn Sie ein Upgrade von DirSync auf Azure AD Connect planen, sollten Sie DirSync nicht selbst deinstallieren, bevor Sie das Upgrade durchführen. Azure AD Connect liest und migriert die Konfiguration aus DirSync und führt die Deinstallation nach der Untersuchung des Servers durch.

**Direktes Upgrade**

Die erwartete Zeit zum Durchführen des Upgrades wird vom Assistenten angezeigt. Diese Schätzung basiert auf der Annahme, dass ein Upgrade für eine Datenbank mit 50.000 Objekten (Benutzern, Kontakten und Gruppen) drei Stunden dauert. Azure AD Connect analysiert Ihre aktuellen DirSync-Einstellungen und empfiehlt ein direktes Upgrade, sofern die Anzahl von Objekten in der Datenbank unter 50.000 liegt. Wenn Sie den Vorgang fortsetzen, werden Ihre aktuellen Einstellungen während des Upgrades automatisch angewendet, und Ihr Server setzt die aktive Synchronisierung automatisch fort.

Falls Sie eine Konfigurationsmigration und eine parallele Bereitstellung durchführen möchten, können Sie die Empfehlung des direkten Upgrades ignorieren. Beispielsweise können Sie die Gelegenheit nutzen, um die Hardware und das Betriebssystem zu aktualisieren. Weitere Informationen finden Sie im Abschnitt [Parallele Bereitstellung](#parallel-deployment).

**Parallele Bereitstellung**

Eine parallele Bereitstellung wird empfohlen, wenn Sie über mehr als 50.000 Objekte verfügen. So vermeiden Sie Verzögerungen des Betriebs für Ihre Benutzer. Bei der Azure AD Connect-Installation wird die erwartete Ausfallzeit in Verbindung mit dem Upgrade geschätzt, aber wenn Sie DirSync in der Vergangenheit bereits aktualisiert haben, ist Ihre eigene Erfahrung vermutlich ausschlaggebender.

### Unterstützte zu aktualisierende DirSync-Konfigurationen
Die folgenden Konfigurationsänderungen werden für DirSync unterstützt und sind Teil des Upgrades:

- Filterung von Domänen und Organisationseinheiten
- Alternative ID (UPN)
- Kennwortsynchronisierung und Exchange-Hybrideinstellungen
- Gesamtstruktur-/Domäneneinstellungen und Azure AD-Einstellungen

Die folgenden Änderungen können im Rahmen des Upgrades nicht aktualisiert werden. Wenn Sie eine oder mehrere dieser Änderungen vorgenommen haben, wird das Upgrade blockiert. In diesen Fällen wird empfohlen, einen neuen Azure AD Connect-Server im [Stagingmodus](active-directory-aadconnectsync-operations.md#staging-mode) zu installieren und die alte DirSync- und die neue Azure AD Connect-Konfiguration zu überprüfen.

- Nicht unterstützte DirSync-Änderungen, z. B. entfernte Attribute und die Verwendung einer benutzerdefinierten Erweiterungs-DLL
- Filterung basierend auf Benutzerattributen

Die Kennwörter, die von DirSync für die Dienstkonten verwendet werden, können nicht abgerufen werden und werden nicht migriert. Diese Kennwörter werden während des Upgrades zurückgesetzt.

### Allgemeine Schritte des Upgrades von DirSync auf Azure AD Connect

1. Willkommen bei Azure AD Connect
2. Analyse der aktuellen DirSync-Konfiguration
3. Sammeln des globalen Azure AD-Administratorkennworts
4. Sammeln der Anmeldeinformationen für ein Enterprise-Administratorkonto (nur während der Installation von Azure AD Connect verwendet)
5. Installation von Azure AD Connect
    * Deinstallation von DirSync
	* Installieren von Azure AD Connect
	* Optionales Starten der Synchronisierung

Zusätzliche Schritte sind in folgenden Fällen erforderlich:

* Sie verwenden derzeit eine vollständige SQL Server-Version (lokal oder remote)
* Sie verfügen über mehr als 50.000 Objekte für die Synchronisierung

## Direktes Upgrade

1. Starten Sie den Azure AD Connect-Installer (MSI).
2. Lesen Sie die Lizenzbedingungen und den Datenschutzhinweis, und stimmen Sie diesen zu. ![Willkommen bei Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. Klicken Sie auf „Weiter“, um die Analyse der vorhandenen DirSync-Installation zu starten. ![Analysieren der vorhandenen Installation der Verzeichnissynchronisierung](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. Nach Abschluss der Analyse erhalten Sie Empfehlungen dazu, wie Sie fortfahren.  
    - Wenn Sie SQL Server Express verwenden und über weniger als 50.000 Objekte verfügen, wird der folgende Bildschirm angezeigt: ![Abgeschlossene Analyse bereit für die Aktualisierung von DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
    - Falls Sie eine SQL Server-Version für DirSync verwenden, wird stattdessen diese Seite angezeigt: ![Abgeschlossene Analyse bereit für die Aktualisierung von DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)<BR/> Die Informationen zum vorhandenen SQL Server-Datenbankserver, der von DirSync verwendet wird, werden angezeigt. Führen Sie bei Bedarf die entsprechenden Anpassungen durch. Klicken Sie auf **Weiter**, um die Installation fortzusetzen.
    - Fall Sie über mehr als 50.000 Objekte verfügen, wird stattdessen dieser Bildschirm angezeigt: ![Abgeschlossene Analyse bereit für die Aktualisierung von DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)<BR/> Aktivieren Sie zum Fortsetzen eines direkten Upgrades das Kontrollkästchen neben dieser Meldung: **DirSync-Upgrade auf diesem Computer fortsetzen.** Wenn Sie stattdessen eine [parallele Bereitstellung](#parallel-deployment) durchführen möchten, müssen Sie die DirSync-Konfigurationseinstellungen exportieren und auf den neuen Server verschieben.
5. Geben Sie das derzeit verwendete Kennwort für das Konto ein, um eine Verbindung zu Azure AD herzustellen. Dies muss das Konto sein, das momentan von DirSync verwendet wird. ![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)
6. Geben Sie ein Enterprise-Administratorkonto für Active Directory ein. ![Geben Sie Ihre ADDS-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Sie können nun mit der Konfiguration loslegen. Wenn Sie auf **Upgrade** klicken, wird DirSync deinstalliert und Azure AD Connect konfiguriert und die Synchronisierung gestartet. ![Bereit für Konfiguration](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)


## Parallele Bereitstellung

### Exportieren der DirSync-Konfiguration
**Parallele Bereitstellung mit mehr als 50.000 Objekten**

Wenn Sie über mehr als 50.000 Objekte verfügen, wird von der Azure AD Connect-Installation eine parallele Bereitstellung empfohlen.

Ein Bildschirm ähnlich dem folgenden wird angezeigt:

![Analyse abgeschlossen](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Wenn Sie die parallele Bereitstellung fortsetzen möchten, müssen Sie die folgenden Schritte ausführen:

- Klicken Sie auf die Schaltfläche **Einstellungen exportieren**. Bei der Installation von Azure AD Connect auf einem separaten Server werden diese Einstellungen importiert, um Einstellungen von Ihrem aktuellen DirSync zur neuen Azure AD Connect-Installation zu migrieren.

Sobald Sie Ihre Einstellungen erfolgreich exportiert haben, können Sie den Azure AD Connect-Assistenten auf dem DirSync-Server beenden. Fahren Sie mit dem nächsten Schritt zum [Installieren von Azure AD Connect auf einem separaten Server](#installation-of-azure-ad-connect-on-separate-server) fort.

**Parallele Bereitstellung mit weniger als 50.000 Objekten**

Gehen Sie wie folgt vor, falls Sie über weniger als 50.000 Objekte verfügen, aber trotzdem eine parallele Bereitstellung durchführen möchten:

1. Führen Sie den Azure AD Connect-Installer aus (MSI).
2. Wenn der Bildschirm **Willkommen bei Azure AD Connect** angezeigt wird, beenden Sie den Installations-Assistenten, indem Sie auf das „X“ oben rechts im Fenster klicken.
3. Öffnen Sie eine Eingabeaufforderung.
4. Führen Sie aus dem Installationsordner von Azure AD Connect (Standard: C:\\Programme\\Microsoft Azure Active Directory Connect) den folgenden Befehl aus: `AzureADConnect.exe /ForceExport`.
5. Klicken Sie auf die Schaltfläche **Einstellungen exportieren**. Bei der Installation von Azure AD Connect auf einem separaten Server werden diese Einstellungen importiert, um Einstellungen von Ihrem aktuellen DirSync zur neuen Azure AD Connect-Installation zu migrieren.

![Analyse abgeschlossen](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

Sobald Sie Ihre Einstellungen erfolgreich exportiert haben, können Sie den Azure AD Connect-Assistenten auf dem DirSync-Server beenden. Fahren Sie mit dem nächsten Schritt zum [Installieren von Azure AD Connect auf einem separaten Server](#installation-of-azure-ad-connect-on-separate-server) fort.

### Installieren von Azure AD Connect auf einem separaten Server

Bei der Installation von Azure AD Connect auf einem neuen Server wird davon ausgegangen, dass Sie Azure AD Connect neu installieren möchten. Da Sie die DirSync-Konfiguration verwenden möchten, müssen Sie einige zusätzliche Schritte ausführen:

1. Führen Sie den Azure AD Connect-Installer aus (MSI).
2. Wenn der Bildschirm **Willkommen bei Azure AD Connect** angezeigt wird, beenden Sie den Installations-Assistenten, indem Sie auf das „X“ oben rechts im Fenster klicken.
3. Öffnen Sie eine Eingabeaufforderung.
4. Führen Sie aus dem Installationsordner von Azure AD Connect (Standard: C:\\Programme\\Microsoft Azure Active Directory Connect) den folgenden Befehl aus: `AzureADConnect.exe /migrate`. Der Installations-Assistent von Azure AD Connect wird gestartet, und der folgende Bildschirm wird angezeigt: ![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. Wählen Sie die Datei mit den Einstellungen, die von Ihrer DirSync-Installation exportiert wurde.
6. Konfigurieren Sie alle erweiterten Optionen, einschließlich:
    - Einen benutzerdefinierten Speicherort für Azure AD Connect.
	- Eine vorhandene Instanz von SQL Server (Standard: Azure AD Connect installiert SQL Server 2012 Express). Verwenden Sie nicht dieselbe Datenbankinstanz wie für Ihren DirSync-Server.
	- Ein Dienstkonto für die Verbindung zu SQL Server (wenn Ihre SQL Server-Datenbank remotegesteuert ist, muss dieses Konto ein Domänendienstkonto sein). Auf diesem Bildschirm werden die folgenden Optionen angezeigt: ![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. Klicken Sie auf **Weiter**.
8. Lassen Sie auf der Seite **Bereit zur Konfiguration** das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, sobald die Konfiguration abgeschlossen wurde** aktiviert. Der Server befindet sich im [Stagingmodus](active-directory-aadconnectsync-operations.md#staging-mode), sodass Änderungen derzeit nicht nach Azure AD exportiert werden.
9. Klicken Sie auf **Installieren**.

>[AZURE.NOTE]Die Synchronisierung zwischen Windows Server Active Directory und Azure Active Directory wird gestartet, es werden aber keine Änderungen an Azure AD exportiert. Nur ein Synchronisierungstool kann jeweils aktiv Änderungen exportieren. Dies wird als [Stagingmodus](active-directory-aadconnectsync-operations.md#staging-mode) bezeichnet.

### Überprüfen, ob Azure AD Connect bereit für den Beginn der Synchronisierung ist

Um sicherzustellen, dass Azure AD Connect bereit für die Übernahme von DirSync ist, müssen Sie im Startmenü in der Gruppe **Azure AD Connect** die Option **Synchronisierungsdienst-Manager** öffnen.

In der Anwendung müssen Sie die Registerkarte **Vorgänge** anzeigen. In dieser Registerkarte überprüfen Sie, dass die folgenden Vorgänge abgeschlossen wurden:

- Import auf den AD-Connector
- Import auf den Azure AD-Connector
- Vollständige Synchronisierung auf dem AD-Connector
- Vollständige Synchronisierung auf dem Azure AD-Connector

Sehen Sie sich die Ergebnisse dieser Vorgänge an, und stellen Sie sicher, dass keine Fehler vorhanden sind.

Wenn Sie prüfen möchten, welche Änderungen nach Azure AD exportiert werden, können Sie die Informationen zum Überprüfen der Konfiguration unter [Stagingmodus](active-directory-aadconnectsync-operations.md#staging-mode) nachlesen. Nehmen Sie die erforderlichen Konfigurationsänderungen vor, bis nichts Unerwartetes mehr angezeigt wird.

Wenn nach Abschluss dieser vier Vorgänge keine Fehler mehr vorhanden und Sie mit den zu exportierenden Änderungen zufrieden sind, können Sie DirSync deinstallieren und die Azure AD Connect-Synchronisierung aktivieren. Führen Sie die beiden nächsten Schritte aus, um die Migration abzuschließen.

### Deinstallieren von DirSync (alter Server)

- Suchen Sie unter **Programme und Funktionen** nach **Microsoft Azure Active Directory-Synchronisierungstool**.
- Deinstallieren Sie das **Microsoft Azure Active Directory-Synchronisierungstool**.
- Beachten Sie, dass die Deinstallation bis zu 15 Minuten dauern kann.

Wenn DirSync deinstalliert wird, ist kein aktiver Server vorhanden, der den Export nach Azure AD durchführt. Der nächste Schritt muss abgeschlossen werden, bevor Änderungen in der lokalen Active Directory-Instanz weiter mit Azure AD synchronisiert werden.

### Aktivieren von Azure AD Connect (neuer Server)
Wenn Sie Azure AD Connect nach Abschluss der Installation wieder öffnen, können Sie weitere Konfigurationsänderungen vornehmen. Starten Sie **Azure AD Connect** über das Startmenü oder über die Verknüpfung auf dem Desktop. Achten Sie darauf, die MSI-Datei für die Installation nicht erneut auszuführen.

Daraufhin sollte Folgendes angezeigt werden:

![Zusätzliche Aufgaben](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

- Wählen Sie **Stagingmodus konfigurieren**.
- Deaktivieren Sie den Stagingmodus durch Klicken auf das Kontrollkästchen **Stagingmodus aktiviert**.

![Geben Sie Ihre Azure AD-Anmeldeinformationen ein.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

- Klicken Sie auf die Schaltfläche **Weiter**.
- Klicken Sie auf der Bestätigungsseite auf die Schaltfläche **Installieren**.

Azure AD Connect ist jetzt der aktive Server.

## Nächste Schritte
Nachdem Sie Azure AD Connect installiert haben, können Sie [die Installation überprüfen und Lizenzen zuweisen](active-directory-aadconnect-whats-next.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Nov15_HO1-->