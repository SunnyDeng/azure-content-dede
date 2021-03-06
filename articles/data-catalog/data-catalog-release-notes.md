<properties
   pageTitle="Versionshinweise für Azure Data Catalog"
   description="Versionshinweise für die öffentliche Vorschauversion von Azure Data Catalog vom 28. August 2015"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="11/06/2015"
   ms.author="maroche"/>

# Versionshinweise für Azure Data Catalog


## Hinweise für die Azure Data Catalog-Version vom 6. November 2015

### Beim Start des Tools für die Datenquellenregistrierung können Fehler auftreten

Beim Starten des Tools **Azure Data Catalog** für die Datenquellenregistrierung vom **Azure Data Catalog-Portal** aus erhalten Benutzer, die bereits eine ältere Version des Tools installiert haben, möglicherweise diese Meldung: "Die Anwendung kann nicht gestartet werden. Wenden Sie sich an den Hersteller der Anwendung."

Benutzer, die diese Nachricht erhalten, sollten in der Windows-Systemsteuerung die vorhandene Anwendung "Azure Data Catalog" entfernen, und das Tool anschließend erneut vom **Azure Data Catalog-Portal** aus starten.

## Hinweise für die Azure Data Catalog-Version vom 13. Juli 2015

### Registrieren bei Oracle Database und Herstellen einer Verbindung

Beim Herstellen einer Verbindung mit Oracle Database-Datenquellen müssen Benutzer die richtigen Oracle-Treiber installieren, die der Bitanzahl (32 Bit oder 64 Bit) der verwendeten Software entsprechen.

-	Beim Registrieren von Oracle-Datenquellen auf einem 32-Bit-Windows-Computer werden die 32-Bit-Oracle-Treiber verwendet.
-	Beim Registrieren von Oracle-Datenquellen auf einem Computer mit 64-Bit-Windows werden die 64-Bit-Oracle-Treiber verwendet.
-	Beim Verbinden mit Oracle-Datenquellen mithilfe von Excel auf einem Computer mit der 32-Bit-Version von Microsoft Office (auch unter 64-Bit-Windows) werden die 32-Bit-Oracle-Treiber verwendet.
-	Beim Verbinden mit Oracle-Datenquellen mithilfe von Excel auf einem Computer mit der 64-Bit-Version von Microsoft Office werden die 64-Bit-Oracle-Treiber verwendet.

### Registrieren bei SQL Server Reporting Services und Herstellen einer Verbindung

Die Unterstützung von SQL Server Reporting Services (SSRS)-Datenquellen ist in der ersten Vorschauversion von Azure Data Catalog auf Server im systemeigenen Modus beschränkt. Die Unterstützung für SSRS im SharePoint-Modus wird in einer späteren Version implementiert.

### Öffnen von Datenassets in Excel

Beim Öffnen von Datenassets aus dem **Azure Data Catalog-Portal** in Microsoft Excel wird möglicherweise ein Dialogfeld mit einem **Sicherheitshinweis für Microsoft Excel** angezeigt. Hierbei handelt es sich um einen Standardvorgang. Benutzer können **Aktivieren** auswählen, um den Vorgang fortzusetzen.

Weitere Informationen finden Sie unter [Aktivieren oder Deaktivieren von Sicherheitswarnungen zu Verknüpfungen und Dateien von verdächtigen Websites](https://support.office.com/de-DE/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### Fehlende Blob- und UDT-Spalten in der Vorschau

Wenn Sie Tabellen und Sichten registrieren, die Spalten für Blob- (Binary Large Object) und benutzerdefinierte Datentypen (user-defined data type, UDT) enthalten, und Sie die Vorschau für die Datenassets hinzufügen möchten, werden diese Spalten in der Vorschau nicht berücksichtigt.

### Proxy- und Richtlinienkonfiguration und Datenquellenregistrierung

Es kann vorkommen, dass sich Benutzer beim Azure Data Catalog-Portal anmelden können, jedoch eine Fehlermeldung erhalten, die ihre Anmeldung beim Tool zum Registrieren von Datenquellen verhindert.

Für dieses Problem sind zwei Ursachen möglich:

**Ursache 1: Konfiguration der Active Directory-Verbunddienste** Das Tool zum Registrieren von Datenquellen verwendet die Formularauthentifizierung, um Benutzeranmeldungen anhand von Active Directory zu überprüfen. Für eine erfolgreiche Anmeldung muss die Formularauthentifizierung durch einen Active Directory-Administrator in der globalen Authentifizierungsrichtlinie aktiviert sein.

In manchen Fällen tritt dieser Fehler möglicherweise nur dann auf, wenn der Benutzer im Unternehmensnetzwerk ist oder wenn der Benutzer von außerhalb des Unternehmensnetzwerks eine Verbindung herstellt. Mit der globalen Authentifizierungsrichtlinie können separate Authentifizierungsmethoden für Intranet- und Extranetverbindungen aktiviert werden. Anmeldefehler können auftreten, wenn die Formularauthentifizierung nicht für das Netzwerk aktiviert ist, aus dem der Benutzer eine Verbindung herstellt.

Weitere Informationen finden Sie unter [Konfigurieren von Authentifizierungsrichtlinien](https://technet.microsoft.com/de-DE/library/dn486781.aspx).

**Ursache 2: Netzwerkproxykonfiguration** Wenn das Unternehmensnetzwerk einen Proxyserver verwendet, kann das Registrierungstool möglicherweise aufgrund des Proxys keine Verbindung zu Azure Active Directory herstellen. Um die Funktion des Registrierungstools sicherzustellen, können Benutzer die Konfigurationsdatei bearbeiten , indem sie der Datei diesen Abschnitt hinzufügen:


	  <system.net>
	    <defaultProxy useDefaultCredentials="true" enabled="true">
	      <proxy usesystemdefault="True"
	                      proxyaddress="http://<your corporate network proxy url>"
	                      bypassonlocal="True"/>
	    </defaultProxy>
	  </system.net>


Starten Sie das Registrierungstool, um die Datei "RegistrationTool.exe.config" zu suchen, und öffnen Sie den Windows Task-Manager. Klicken Sie im Task-Manager auf der Registerkarte "Details" mit der rechten Maustaste auf "RegistrationTool.exe", und wählen Sie im Popupmenü "Dateispeicherort öffnen" aus.

<!---HONumber=Nov15_HO3-->