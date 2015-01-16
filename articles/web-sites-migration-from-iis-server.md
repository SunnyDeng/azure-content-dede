<properties urlDisplayName="Migrate from IIS to Azure Websites with Migration Assistant" pageTitle="Migrieren von IIS-Websites zu Azure-Websites mit Migration Assistant" metaKeywords="Azure-Websites, Migration, migrieren, IIS" description="Zeigt, wie Sie mithilfe des Assistenten für die Migration von Azure-Websites vorhandene IIS-Websites schnell zu Azure-Websites migrieren können." metaCanonical="" services="web-sites" documentationCenter="" title="Migrate your IIS Websites to Azure Websites using the Migration Assistant" authors="cephalin,anwestg"  solutions="" writer="cephalin" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="cephalin" />

# Migrieren von IIS-Websites zu Azure-Websites mit Migration Assistant
Vorhandene Websites, die auf Internet Information Service (IIS) 6 oder höher ausgeführt werden, können problemlos zu Azure-Websites migriert werden. [Azure Websites Migration Assistant](https://www.movemetothecloud.net/) kann die IIS-Server-Installation analysieren, identifizieren, welche Websites zu Azure-Websites migriert werden, alle Elemente markieren, die nicht migriert werden können oder auf der Plattform nicht unterstützt werden, und dann die Websites und zugeordneten Datenbanken nach Azure migrieren.

>[WACOM.NOTE] Der Support für Windows Server 2003 läuft am 14. Juli 2015 aus. Wenn Sie derzeit Websites auf einem IIS-Server unter Windows Server 2003 haben, bietet Azure-Websites eine risikoarme, kostengünstige und wenig aufwändige Möglichkeit, Ihre Websites online zu halten. Azure Websites Migration Assistant kann Sie bei der Automatisierung des Migrationsprozesses unterstützen. 

## Während der Kompatibilitätsanalyse überprüfte Elemente
Azure Websites Migration Assistant erstellt einen Bereitschaftsbericht, um alle möglichen Probleme oder Hindernisse zu identifizieren, die eine erfolgreiche Migration von lokalen IIS zu Azure-Websites verhindern könnten. Einige der wichtigsten zu berücksichtigenden Elemente sind:

-	Portbindungen - Azure-Websites unterstützen nur Port 80 für HTTP und 443 für HTTPS-Datenverkehr. Andere Portkonfigurationen werden ignoriert, und der Datenverkehr wird auf 80 oder 443 weitergeleitet.
-	Authentifizierung - Azure-Websites unterstützt standardmäßig anonyme Authentifizierung und Formularauthentifizierung, sofern von einer Anwendung angegeben. Windows-Authentifizierung kann nur durch die Integration in Azure Active Directory und ADFS verwendet werden. Alle anderen Formen der Authentifizierung, z. B. Standardauthentifizierung, werden zurzeit nicht unterstützt. 
-	Globaler Assemblycache (GAC) - der GAC wird in Azure-Websites nicht unterstützt. Wenn Ihre Anwendung Assemblys referenziert, die Sie in der Regel im globalen Assemblycache bereitstellen, müssen Sie in den Ordner "bin" der Anwendung in Azure-Websites bereitstellen. 
-	IIS5-Kompatibilitätsmodus - Dies wird in Azure-Websites nicht unterstützt. 
-	Anwendungspools - In Azure-Websites werden jede Website und deren untergeordneten Anwendungen im selben Anwendungspool ausgeführt. Wenn Ihre Website mehrere untergeordnete Anwendungen hat, die mehrere Anwendungspools verwenden, konsolidieren Sie diese in einem einzigen Anwendungspool mit gemeinsamen Einstellungen, oder migrieren Sie jede Anwendung zu einer getrennten Website.
-	COM-Komponenten - Azure-Websites lässt keine Registrierung von COM-Komponenten auf der Plattform zu. Wenn Ihre Websites oder Anwendungen COM-Komponenten verwenden, müssen Sie sie in verwaltetem Code umschreiben und mit der Website oder Anwendung bereitstellen.
-	ISAPI-Filter - Azure-Websites unterstützt die Verwendung der ISAPI-Filter. Sie müssen die folgenden Schritte ausführen:
	-	Stellen Sie die DLLs für Ihre Website bereit. 
	-	Registrieren Sie die DLLs mit [Web.config](http://www.iis.net/configreference/system.webserver/isapifilters)
	-	Platzieren Sie eine Datei "applicationHost.xdt" im Stammverzeichnis der Website mit dem folgenden Inhalt:

			?xml version="1.0"?
			<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
			<configSections>
			    <sectionGroup name="system.webServer">
			      <section name="isapiFilters" xdt:Transform="SetAttributes(overrideModeDefault)" overrideModeDefault="Allow" />
			    </sectionGroup>
			  </configSections>
			</configuration>

		Weitere Beispiele zur Verwendung von XML-Dokumenttransformationen auf Ihrer Website finden Sie unter [Transformieren einer Microsoft Azure-Website](http://blogs.msdn.com/b/waws/archive/2014/06/17/transform-your-microsoft-azure-web-site.aspx).

-	Andere Komponenten wie z. B. SharePoint, FrontPage-Servererweiterungen (FPSE), FTP oder SSL-Zertifikate werden nicht migriert.

## Verwenden des Azure Websites Migration Assistant
In diesem Abschnitt werden Sie schrittweise durch ein Beispiel zum Migrieren einiger Websites geführt, die eine SQL Server-Datenbank verwenden und auf einem lokalen Computer mit Windows Server 2003 R2 (IIS 6.0) ausgeführt werden:

1.	Navigieren Sie auf dem IIS-Server oder Client-Computer zu [https://www.movemetothecloud.net/](https://www.movemetothecloud.net/) 

	![](./media/web-sites-migration-from-iis-server/migration-tool-homepage.png)

2.	Installieren Sie Azure Websites Migration Assistant, indem Sie auf die Schaltfläche **Dedicated IIS Server** klicken. Weitere Optionen werden in der nahen Zukunft bereitgestellt. 
4.	Klicken Sie auf **Install Tool**, um Azure Websites Migration Assistant auf Ihrem Computer zu installieren.

	![](./media/web-sites-migration-from-iis-server/install-page.png)

	>[WACOM.NOTE] Sie können auch auf **Download for offline install** klicken, um eine ZIP-Datei herunterzuladen und auf Servern ohne Internetverbindung zu installieren. Oder klicken Sie auf **Upload an existing migration readiness report**, eine erweiterte Option, die zusammen mit einem vorhandenen Migrationsbereitschaftsbericht ausgeführt wird, den Sie zuvor erstellt haben (wird weiter unten erläutert).

5.	Klicken Sie im Bildschirm **Application Install** auf **Install**, um die Installation auf Ihrem Computer vorzunehmen. Bei Bedarf werden auch die entsprechenden Abhängigkeiten wie Web Deploy, DacFX und IIS installiert. 

	![](./media/web-sites-migration-from-iis-server/install-progress.png)

	Nach der Installation wird Azure Websites Migration Assistant automatisch gestartet.
  
6.	Wählen Sie **Migrate sites and databases from a remote server to Azure**. Geben Sie die Administratoranmeldeinformationen für den Remoteserver an, und klicken Sie auf **Continue**. 

	![](./media/web-sites-migration-from-iis-server/migrate-from-remote.png)

Sie können natürlich auch vom lokalen Server aus migrieren. Die Remoteoption ist nützlich, wenn Sie Websites von einem IIS-Produktionsserver migrieren möchten.
 
	Das Migrationstool prüft an diesem Punkt die IIS-Server-Konfiguration, z. B. Websites, Anwendungen, Anwendungspools und Abhängigkeiten, um für die Migration geeignete Websites zu erkennen. 

8.	Der folgende Screenshot zeigt drei Websites - **Default Web Site**, **TimeTracker** und **CommerceNet4**. Alle haben eine zugeordnete Datenbank, die migriert werden soll. Wählen Sie alle Websites aus, die Sie bewerten möchten, und klicken Sie dann auf **Next**.

	![](./media/web-sites-migration-from-iis-server/select-migration-candidates.png)
 
9.	Klicken Sie auf **Upload**, um den Bereitschaftsbericht hochzuladen. Wenn Sie auf **save file locally** klicken, können Sie das Migrationstool später erneut ausführen und den gespeicherten Bereitschaftsbericht wie zuvor beschrieben hochladen.

	![](./media/web-sites-migration-from-iis-server/upload-readiness-report.png)
 
Nachdem Sie den Bereitschaftsbericht hochgeladen haben, führt Azure eine Bereitschaftsanalyse durch und zeigt die Ergebnisse an. Lesen Sie die Bewertungsdetails für jede Website, und stellen Sie sicher, dass Sie alle Probleme verstanden oder behoben haben, bevor Sie fortfahren. 
 
	![](./media/web-sites-migration-from-iis-server/readiness-assessment.png)

12.	Klicken Sie auf **Begin Migration**, um die Migration zu starten. Sie werden zu Azure umgeleitet, um sich bei Ihrem Konto anzumelden. Es ist wichtig, dass Sie sich mit einem Konto anmelden, das über ein aktives Azure-Abonnement verfügt. Wenn Sie nicht über ein Azure-Konto verfügen, können Sie sich hier für eine kostenlose Testversion anmelden. 

13.	Wählen Sie das Mandantenkonto, das Azure-Abonnement und die Region aus, die für die migrierten Azure-Websites und Datenbanken verwendet werden sollen, und klicken Sie dann auf **Start Migration**. Sie können die zu migrierenden Websites später auswählen.

	![](./media/web-sites-migration-from-iis-server/choose-tenant-account.png)

14.	Auf dem nächsten Bildschirm können Sie die Standardeinstellungen für die Migration ändern, wie z. B.:

	- Verwenden einer vorhandenen Azure SQL-Datenbank oder Erstellen einer neuen Azure SQL-Datenbank und Konfigurieren der Anmeldeinformationen
	- Auswählen der zu migrierenden Websites
	- Definieren von Namen für die Azure-Websites und deren verknüpften SQL-Datenbanken
	- Anpassen der globalen Einstellungen und Einstellungen auf Websiteebene

	Der folgende Screenshot zeigt alle Websites, die für die Migration ausgewählt wurden, mit ihren Standardeinstellungen.

	![](./media/web-sites-migration-from-iis-server/migration-settings.png)

	>[WACOM.NOTE] Über das Kontrollkästchen **Enable Azure Active Directory** der benutzerdefinierten Einstellungen wird die Azure-Website in [Azure Active Directory](http://azure.microsoft.com/en-us/documentation/articles/active-directory-whatis/) integriert (das **Standardverzeichnis**). Weitere Informationen zur Synchronisierung von Azure Active Directory mit Ihrem lokalen Active Directory finden Sie unter [Verzeichnisintegration](http://msdn.microsoft.com/library/jj573653).

16.	 Nachdem Sie alle gewünschten Änderungen vorgenommen haben, klicken Sie auf **Create**, um den Migrationsvorgang zu starten. Das Migrationstool erstellt die Azure SQL-Datenbank und Azure-Website und veröffentlicht die Inhalte der Websites und Datenbanken. Der Migrationsstatus wird im Migrationstools klar angezeigt, und am Ende sehen Sie eine Zusammenfassung mit Einzelheiten zu den migrierten Websites, dem Erfolg sowie Links zu den neu erstellten Azure-Websites. 

	Tritt während der Migration ein Fehler auf, so weist das Migrationstool klar auf den Fehler hin und setzt die Änderungen zurück. Sie können einen Fehlerbericht auch direkt an das Entwicklungsteam senden, indem Sie auf die Schaltfläche **Send Error Report** klicken. Die erfasste Fehleraufrufliste und ein Nachrichtentextkörper werden mitgeschickt. 

	![](./media/web-sites-migration-from-iis-server/migration-error-report.png)

	Wenn die Migration erfolgreich und ohne Fehler verlaufen ist, können Sie auch auf die Schaltfläche **Give Feedback** klicken, um direktes Feedback zu geben. 
 
20.	Klicken Sie auf die Links auf den Azure-Websites, und stellen Sie sicher, dass die Migration erfolgreich ist.


