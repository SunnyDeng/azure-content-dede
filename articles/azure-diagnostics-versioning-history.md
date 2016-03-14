<properties
	pageTitle="Azure-Diagnose – Versionsverlauf"
	description="Erläuterung der Änderungen in den verschiedenen Versionen von Azure-Diagnose, die in den verschiedenen Versionen der Microsoft Azure SDKs enthalten sind."
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="robb"/>


# Microsoft Azure-Diagnose – Versionsverlauf

Sie kennen Azure-Diagnose noch nicht? Informationen hierzu finden Sie in der [Übersicht über Azure-Diagnose](azure-diagnostics.md).

Jede Version von Azure SDK wird in der Regel mit einer neuen Version von Azure-Diagnose bereitgestellt. In der folgenden Tabelle werden die Versionen von Azure SDK und Azure-Diagnose beschrieben, die mit den jeweiligen SDK-Versionen zur Verfügung gestellt werden.



Azure SDK-Version | Version von Azure-Diagnose | Modell
--- | --- | ---
1\.x | 1\.0 | Plug-In
2\.0 bis 2.4| 1,0 | "
2\.5 | 1\.2 | Erweiterung
2\.6 | 1\.3 | "
2\.7 | 1\.4 | "
2\.8 | 1\.5 | "


Die neueste Version ist 1.5, die im Lieferumfang von Azure SDK 2.8 enthalten ist. Die Version der Azure-Diagnoseerweiterung, die mit dem SDK geliefert wird, wird nur für lokale Emulatorszenarien verwendet. Alle bereitgestellten Anwendungen verwenden automatisch die neueste Version, wenn sie in Azure ausgeführt werden. Dies geschieht unabhängig davon, mit welcher SDK-Version die jeweilige Anwendung erstellt wurde. Wenn Sie jedoch nicht das neueste Azure SDK installieren, verfügen Sie eventuell nicht über alle Tools, mit denen Sie die neuen Funktionen nutzen können.

Verschiedene Funktionen und Änderungen werden im Folgenden beschrieben.

## Azure SDK 2.8
In Azure SDK 2.8 wurde die Möglichkeit hinzugefügt, Diagnosedaten an [Application Insights](./application-insights/app-insights-cloudservices.md) zu senden. Dies vereinfacht die Diagnose von Problemen sowohl in Ihrer gesamten Anwendung als auch auf System- und Infrastrukturebene.

## Änderungen an Azure 2.6-Diagnose

Für Azure SDK 2.6-Clouddienstprojekte in Visual Studio wurden die folgenden Änderungen vorgenommen. (Diese Änderungen gelten auch für spätere Versionen des Azure SDK.)

- Der lokale Emulator unterstützt jetzt die Diagnose. Dies bedeutet, dass Sie Diagnosedaten erfassen und sicherstellen können, dass Ihre Anwendung die richtigen Ablaufverfolgungen erstellt, während Sie Ihre Entwicklung und Tests in Visual Studio durchführen. Die Verbindungszeichenfolge `UseDevelopmentStorage=true` aktiviert das Sammeln von Diagnosedaten, während Sie Ihr Clouddienstprojekt in Visual Studio mithilfe des Azure-Speicheremulators ausführen. Alle Diagnosedaten werden im Speicherkonto (Development Storage) gesammelt.

- Die Verbindungszeichenfolge für das Diagnosespeicherkonto (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) wird erneut in der Dienstkonfigurationsdatei (.cscfg) gespeichert. In Azure SDK 2.5 wurde das Diagnosespeicherkonto in der Datei "diagnostics.wadcfgx" angegeben.

Es gibt einige wichtige Unterschiede zwischen der Funktion der Verbindungszeichenfolge in Azure SDK 2.4 und früher und in Azure SDK 2.6 und später.

- Bis Azure SDK 2.4 wurde die Verbindungszeichenfolge vom Diagnose-Plug-In als Laufzeit verwendet, um Informationen zum Speicherkonto für die Übertragung der Diagnoseprotokolle abzurufen.

- Ab Azure SDK 2.6 wird die Diagnoseverbindungszeichenfolge von Visual Studio während der Veröffentlichung zum Konfigurieren der Diagnoseerweiterung mit den entsprechenden Speicherkontoinformationen verwendet. Mit der Verbindungszeichenfolge können Sie die verschiedenen Speicherkonten für verschiedene Dienstkonfigurationen definieren, die Visual Studio beim Veröffentlichen verwendet. Da das Diagnose-Plug-In (nach Azure SDK 2.5) nicht mehr verfügbar ist, kann jedoch die CSCFG-Datei allein die Diagnoseerweiterung nicht aktivieren. Sie müssen die Erweiterung mithilfe von Tools wie Visual Studio oder PowerShell separat aktivieren.

- Um die Konfiguration der Diagnoseerweiterung mit PowerShell zu vereinfachen, enthält die Paketausgabe von Visual Studio auch die öffentliche Konfigurations-XML für die Diagnoseerweiterung jeder Rolle. Visual Studio verwendet die Diagnoseverbindungszeichenfolge zum Ausfüllen der Speicherkontoinformationen, die in der öffentlichen Konfiguration vorhanden sind. Die öffentlichen Konfigurationsdateien werden im Extensions-Ordner erstellt und haben das folgende Namensgebungsmuster: "PaaSDiagnostics.<RoleName>. PubConfig.xml". Dieses Muster kann von allen PowerShell-basierten Bereitstellungen verwendet werden, um die einzelnen Konfigurationen einer Rolle zuzuordnen.

- Die Verbindungszeichenfolge in der CSCFG-Datei wird auch im Azure-Portal verwendet, um auf die Diagnosedaten zuzugreifen, sodass sie in der Registerkarte **Überwachung** angezeigt werden kann. Die Verbindungszeichenfolge ist erforderlich, um den Dienst so zu konfigurieren, dass ausführliche Überwachungsdaten im Portal angezeigt werden.

### Migrieren von Projekten zu Azure SDK 2.6 und höher

Wenn beim Migrieren von Azure SDK 2.5 zu Azure SDK 2.6 oder höher in der WADCFGX-Datei ein Diagnosespeicherkonto angegeben ist, dann bleibt der Speicherort unverändert. Um die Flexibilität, die sich aus der Verwendung verschiedener Speicherkonten für unterschiedliche Speicherkonfigurationen ergibt, nutzen zu können, müssen Sie Ihrem Projekt die Verbindungszeichenfolge manuell hinzufügen. Wenn Sie ein Projekt aus Azure SDK 2.4 oder früher zu Azure SDK 2.6 migrieren, werden die Diagnoseverbindungszeichenfolgen beibehalten. Beachten Sie jedoch die Änderungen im Hinblick auf die Handhabung von Verbindungszeichenfolgen in Azure SDK 2.6, wie im vorherigen Abschnitt beschrieben.

### Bestimmung des Diagnosespeicherkontos durch Visual Studio

- Wenn eine Diagnoseverbindungszeichenfolge in der CSCFG-Datei angegeben ist, wird es von Visual Studio beim Veröffentlichen und beim Generieren der öffentlichen XML-Konfigurationsdateien beim Verpacken zur Konfiguration der Diagnoseerweiterung verwendet.

- Wenn keine Diagnoseverbindungszeichenfolge in der CSCFG-Datei angegeben ist, verwendet Visual Studio beim Veröffentlichen und beim Generieren der öffentlichen XML-Konfigurationsdateien beim Verpacken wieder das in der WADCFGX-Datei angegebene Speicherkonto zur Konfiguration der Diagnoseerweiterung.

- Die Diagnoseverbindungszeichenfolge in der CSCFG-Datei hat Vorrang vor dem in der WADCFGSX-Datei angegebenen Speicherkonto. Wenn eine Diagnoseverbindungszeichenfolge in der CSCFG-Datei angegeben ist, verwendet Visual Studio diese und ignoriert das Speicherkonto in der WADCFGX-Datei.

### Wozu dient das Kontrollkästchen "Verbindungszeichenfolgen für Entwicklungsspeicher...aktualisieren"?

Das Kontrollkästchen **Verbindungszeichenfolgen für Entwicklungsspeicher zur Diagnose und zum Zwischenspeichern beim Veröffentlichen in Microsoft Azure mit Anmeldeinformationen für Microsoft Azure-Speicherkonto aktualisieren** ist eine bequeme Möglichkeit, um alle Verbindungszeichenfolgen für ein Entwicklungsspeicherkonto mit dem Azure-Speicherkonto zu aktualisieren, das während der Veröffentlichung angegeben wird.

Beispiel: Sie aktivieren dieses Kontrollkästchen, und die Diagnoseverbindungszeichenfolge gibt `UseDevelopmentStorage=true` an. Wenn Sie das Projekt in Azure veröffentlichen, aktualisiert Visual Studio automatisch die Diagnoseverbindungszeichenfolge mit dem Speicherkonto, das Sie im Webpublishing-Assistenten angegeben haben. Wenn jedoch ein echtes Speicherkonto als Diagnoseverbindungszeichenfolge angegeben wurde, wird stattdessen dieses Konto verwendet.

## Unterschiede zwischen Diagnosefunktionen in Azure SDK 2.4 und früher und Azure SDK 2.5 und später

Wenn Sie für Ihr Projekt ein Upgrade von Azure SDK 2.4 auf Azure SDK 2.5 oder höher durchführen, sollten Sie die folgenden Diagnosefunktionsunterschiede beachten.

- **Konfigurations-APIs sind veraltet** – In Azure SDK 2.4 oder früheren Versionen ist eine programmgesteuerte Konfiguration der Diagnose verfügbar; diese Funktion ist jedoch in Azure SDK 2.5 und höher veraltet. Wenn die Diagnosekonfiguration derzeit im Code definiert ist, müssen Sie diese Einstellungen im Projekt von Grund auf neu konfigurieren, damit die Diagnose weiterhin funktioniert. Die Diagnosekonfigurationsdatei für Azure SDK 2.4 ist "diagnostics.wadcfg"; in Azure SDK 2.5 oder höher hat sie den Namen "diagnostics.wadcfgx".

- **Diagnose für Clouddienstanwendungen kann nur auf Rollenebene, nicht auf der Instanzebene konfiguriert werden.**

- **Jedes Mal, wenn Sie Ihre App bereitstellen, wird die Diagnosekonfiguration aktualisiert** – Dies kann Paritätsprobleme verursachen, wenn Sie die Diagnosekonfiguration im Server-Explorer ändern und anschließend die App erneut bereitstellen.

- **In Azure SDK 2.5 und höher werden Absturzabbilder in der Diagnosekonfigurationsdatei und nicht im Code konfiguriert** – Wenn Sie Absturzabbilder im Code konfiguriert haben, müssen Sie die Konfiguration manuell vom Code in die Konfigurationsdatei übertragen, da die Absturzabbilder während der Migration zu Azure SDK 2.6 nicht übertragen werden.

<!---HONumber=AcomDC_0302_2016-->