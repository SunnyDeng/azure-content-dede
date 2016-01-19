<properties
   pageTitle="SQL Azure mit Azure RemoteApp | Microsoft Azure"
   description="Erfahren Sie, wie Sie SQL Azure mit Azure RemoteApp verwenden."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="01/07/2016"
   ms.author="elizapo"/>

# SQL Azure mit Azure RemoteApp

Wenn Kunden sich für das Hosten ihrer Windows-Anwendungen in der Cloud mit Azure RemoteApp entscheiden, möchten sie meist auch ihre Daten, z. B. SQL-Server, zur Cloud migrieren, um eine vollständige Cloudbereitstellung zu realisieren. Dies ermöglicht eine umfassende Lösung mit Hosting in der Cloud, auf die per Azure RemoteApp jederzeit und mit jedem Gerät von jedem Ort aus zugegriffen werden kann. Unten sind Links und Referenzen sowie Anleitungen zur Unterstützung bei diesem Prozess angegeben.

## Migrieren von SQL-Daten

Beginnen Sie mit [Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank](sql-database-cloud-migrate.md). In diesem Artikel wird die Vorgehensweise beschrieben.

## Konfigurieren von Azure RemoteApp
Hosten Sie Ihre Windows-Anwendung in Azure RemoteApp. Unten ist eine grobe Schritt-für-Schritt-Anleitung angegeben:

1.     Erstellen Sie die [Azure RemoteApp-Vorlagen-VM](remoteapp-imageoptions.md). 
2.     Installieren Sie die erforderliche Anwendung auf der virtuellen Maschine.
3.     Konfigurieren Sie die Anwendung so, dass eine Verbindung mit der SQL-Datenbank hergestellt wird, und vergewissern Sie sich, dass sie funktioniert.
4.     Führen Sie Sysprep für die VM durch, und fahren Sie sie herunter. Erfassen Sie sie als Image für die Verwendung mit Azure. **Hinweis:** Sie müssen darauf achten, dass die Anwendung die Informationen für die Datenbankverbindung über den Sysprep-Prozess beibehalten kann. Falls die Anwendung die Informationen für die Datenbankverbindung nicht beibehalten kann, müssen Sie sich unter Umständen an den Anbieter der Anwendung wenden und erfragen, wie die Verbindungszeichenfolge angegeben werden kann.
5.     Importieren Sie das benutzerdefinierte Image in Ihre Azure RemoteApp-Bibliothek, indem Sie den richtigen geografischen Standort auswählen, unter dem sich Ihre SQL Azure-Bereitstellung befindet. 
6.     Stellen Sie eine RemoteApp-Sammlung in demselben Rechenzentrum wie Ihre SQL Azure-Bereitstellung bereit, indem Sie die oben angegebene Vorlage verwenden und die Anwendung veröffentlichen. Durch das Bereitstellen von Azure RemoteApp in demselben Rechenzentrum wie Ihre SQL Azure-Bereitstellung können Sie sicherstellen, dass die höchsten Verbindungsgeschwindigkeiten erreicht werden und die Latenz verringert wird. 

## Aspekte der App- und SQL-Konfiguration:
Beim Verwenden von Azure SQL mit RemoteApp sind einige Punkte zu beachten:

Lesen Sie sich [Konfigurieren einer Firewall für die Azure SQL-Datenbank](sql-database-firewall-configure.md) durch. Ein Auszug aus dem Artikel lautet: „Anfänglich ist jeglicher Zugriff auf den Azure SQL-Datenbankserver durch die Firewall blockiert. Damit der Azure SQL-Datenbankserver verwendet werden kann, müssen Sie zum klassischen Portal wechseln und eine oder mehrere Firewallregeln auf Serverebene festlegen, die den Zugriff auf den Azure SQL-Datenbankserver ermöglichen. Geben Sie mithilfe der Firewallregeln an, welche IP-Adressbereiche aus dem Internet zulässig sind und ob Azure-Anwendungen versuchen können, eine Verbindung mit dem Azure SQL-Datenbankserver herzustellen.“

Wenn ein Computer versucht, eine Verbindung mit dem Datenbankserver über das Internet herzustellen, prüft die Firewall auch die Ursprungs-IP-Adresse der Anforderung anhand sämtlicher Firewallregeln auf Serverebene und (falls erforderlich) auf Datenbankebene. „Liegt die IP-Adresse der Anforderung innerhalb eines der in den Firewallregeln auf Serverebene angegebenen Bereiche, wird die Verbindung mit dem Azure SQL-Datenbankserver gewährt.“ Daher können wir IP-Bereiche verwenden, nicht nur einzelne IP-Quelladressen.

Befolgen Sie die Schritt-für-Schritt-Anleitung unter [Konfigurieren der Firewalleinstellungen für SQL-Datenbank mit dem Azure-Portal](sql-database-configure-firewall-settings.md), um den IP-Bereich anzugeben. Geben Sie beim Konfigurieren der Regeln für die SQL-Firewall den IP-Bereich des Subnetzes an, der für die Azure RemoteApp-Sammlung angegeben wurde. Die ARA-Server sollen dadurch auch dann eine Verbindung mit der SQL-DB herstellen können, wenn sie über dynamisch zugewiesene IP-Adressen verfügen.

## Problembehandlung
Falls die Verwendung einer unter Azure RemoteApp gehosteten Clientanwendung mit Verbindung mit einer SQL-Datenbank, die unter Azure oder lokal gehostet wird, langsam ist, kann dies verschiedene Gründe haben.

- Die Netzwerklatenz von Ihrem Gerät zu Azure ist hoch. Wechseln Sie zur besten und schnellsten Netzwerkverbindung, um die höchstmögliche Leistung zu erzielen. Verwenden Sie [azurespeed.com](http://azurespeed.com/) als allgemeines Tool zum Testen der Latenz Ihrer Geräte gegenüber dem Azure-Rechenzentrum.  
- Die unter Azure RemoteApp gehostete Client-App ist ausgelastet. Die Leistung verbessert sich, wenn Sie einen anderen Abrechnungsplan auswählen, z. B. Premium-Abrechnung. Ein anderer Trick zur Überwachung Ihrer Anwendung ist das Verfolgen der Verbrauchswerte: Drücken Sie während einer aktiven Sitzung die Tastenkombination STRG+ALT+ENDE, um den SAS-Bildschirm zu starten. Wählen Sie den Task-Manager aus, und beobachten Sie die Ressourcenauslastung für Ihre App.
- Der SQL-Server ist ausgelastet oder nicht optimiert. Führen Sie die Schritte der SQL-Anleitung für die Problembehandlung aus. 

<!---HONumber=AcomDC_0114_2016-->