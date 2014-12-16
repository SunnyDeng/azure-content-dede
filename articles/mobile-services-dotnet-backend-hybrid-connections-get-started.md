<properties urlDisplayName="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" pageTitle="Verbinden eines mobilen Azure-Diensts mit einem lokalen SQL Server über Hybridverbindungen - Azure Mobile Services" metaKeywords="" description="Learn how to connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" metaCanonical="" services="" documentationCenter="Mobile" title="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" authors="mahender" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/11/2014" ms.author="mahender" />
  
# Verbinden eines mobilen Azure-Diensts mit einem lokalen SQL Server über Hybridverbindungen 

Wenn ein Unternehmen die Cloud einführt, müssen oft bestimmte Bestände lokal erhalten werden, ob aus technischen, rechtlichen oder Sicherheitsgründen. Mit Mobile Services können Sie schnell und einfach eine in der Cloud gehostete Mobilitätsebene auf diese Bestände aufsetzen und lokal vor Ort über sichere Hybridverbindungen auf die Bestände zugreifen. Unterstützt werden unter anderem alle Ressourcen, die auf einem statischen TCP-Port laufen, inklusive Microsoft SQL Server, MySQL, HTTP Web-APIs und die meisten selbst entwickelten Webdienste. 

In diesem Lernprogramm erfahren Sie, wie Sie einen mobilen .NET Back-End-Dienst so anpassen können, dass er eine lokale SQL Server-Datenbank anstatt der standardmäßig mit Ihrem Dienst bereitgestellten Azure SQL-Datenbank verwendet. Obwohl Hybridverbindungen für mobile JavaScript Back-End-Dienste unterstützt werden, behandelt dieses Thema nur mobile .NET Back-End-Dienste.

In diesem Thema werden die grundlegenden Schritte erläutert:

1. [Voraussetzungen](#Prerequisites)
2. [Installieren von SQL Server Express, Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank](#InstallSQL)
3. [Erstellen einer Hybridverbindung](#CreateHC)
4. [Installieren des lokalen Hybrid Connection Manager, um die Verbindung zu vervollständigen](#InstallHCM)
5. [Anpassen eines Mobile Service, um die Verbindung zu verwenden](#CreateService)

<a name="Prerequisites"></a>
##Voraussetzungen##
Zum Durchführen des Lernprogramms benötigen Sie folgende Produkte: Alle Produkte sind als kostenlose Versionen verfügbar, sodass Sie kostenlos mit der Entwicklung für Azure beginnen können.

- **Visual Studio 2013** - Zum Herunterladen einer kostenlosen Testversion von Visual Studio 2013 besuchen Sie [Visual Studio Downloads](http://www.visualstudio.com/downloads/download-visual-studio-vs). Installieren Sie diese Version, bevor Sie fortfahren.

- **SQL Server 2014 Express with Tools** - Laden Sie Microsoft SQL Server Express kostenlos von der [Microsoft Web Platform Datenbank-Seite](http://www.microsoft.com/web/platform/database.aspx) herunter. Wählen Sie die Version **Express** (nicht LocalDB). Die Version **Express with Tools** enthält das Produkt SQL Server Management Studio, das Sie in diesem Lernprogramm verwenden werden.

Außerdem benötigen Sie einen lokalen Computer, der sich über Hybridverbindungen mit Azure verbinden wird. Dieser Computer muss die folgenden Kriterien erfüllen:

- Verbindung mit Azure über Port 5671 möglich
- Verbindung zu *Hostname*:*Portnummer* der lokalen Ressource, mit der Sie sich verbinden möchten, ist möglich. Die Ressource muss nicht unbedingt auf demselben Computer gehostet sein. 

<a name="InstallSQL"></a>
## Installieren von SQL Server Express, Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank

Um eine lokale SQL Server- oder SQL Server Express-Datenbank mit einer Hybridverbindung verwenden zu können, muss TCP/IP an einem statischen Port aktiviert werden. Standardinstanzen von SQL Server verwenden den statischen Port 1433, benannte Instanzen dagegen nicht. 

Detaillierte Anweisungen zum Konfigurieren von SQL Server, um die oben genannten Bedingungen zu erfüllen, finden Sie unter [Installieren von SQL Server Express, Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank](/de-de/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL). Wenn Sie SQL Server bereits in einer Konfiguration und Umgebung installiert haben, welche die oben beschriebenen Kriterien erfüllen, können Sie gleich mit [Erstellen einer lokalen SQL Server-Datenbank](/de-de/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB) beginnen. 

Dieses Lernprogramm geht davon aus, dass der Name der Datenbank **OnPremisesDB** ist, die Datenbank auf Port **1433** läuft und der Hostname des Computers **onPremisesServer** ist.

<a name="CreateHC"></a>
## Erstellen einer Hybridverbindung
1. Melden Sie sich auf lokalen Computer beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409) an.

2. Klicken Sie unten im Navigationsbereich auf **+NEU** und anschließend auf **App-Dienste**, BizTalk Service und auf Benutzerdefinierte Erstellung.

	![Create BizTalk Service][CreateBTS]

3. Geben Sie einen Namen für den **BizTalk Service** ein, und wählen Sie eine **Edition** aus. 

	![Configure new BizTalk Service][ConfigureBTS]

	Dieses Lernprogramm verwendet **mobile1**. Sie müssen einen eindeutigen Namen für Ihren neuen BizTalk Service angeben.

4. Nach der Erstellung des BizTalk Service wählen Sie die Registerkarte **Hybridverbindungen** aus, und klicken Sie auf **Hinzufügen**.

	![Add Hybrid Connection][AddHC]

	Daraufhin wird eine neue Hybridverbindung erstellt.

5. Geben Sie einen **Namen** und einen **Hostnamen** für Ihre Hybridverbindung ein, und legen Sie **Port** auf `1433` fest. 
  
	![Configure Hybrid Connection][ConfigureHC]

		Der Hostname ist der Name des lokalen Servers. Konfigurieren Sie die Hybridverbindung für den Zugriff auf SQL Server unter dem Port 1433.

6. Nach der Erstellung wird die neue Verbindung in der folgenden Tabelle angezeigt. 
 
	![Hybrid Connection successfully created][HCCreated]
	
	Der Status der neuen Verbindung wird als **Lokale Einrichtung unvollständig** angezeigt.

Nun müssen Sie den Hybrid Connection Manager auf dem lokalen Computer installieren.

<a name="InstallHCM"></a>
## Installieren des lokalen Hybrid Connection Manager, um die Verbindung zu vervollständigen

Über den Hybrid Connection Manager kann sich Ihr lokaler Computer mit Azure verbinden und TCP-Datenverkehr weiterleiten. Sie müssen diese Software auf dem lokalen Computer installieren, auf den Sie von Azure aus zugreifen möchten.

1. Wählen Sie die soeben erstellte Verbindung aus und klicken Sie in der unteren Leiste auf **Lokale Einrichtung**.

	![On-Premises Setup][DownloadHCM]

4. Klicken Sie auf **Installieren und konfigurieren**.

	![Install Hybrid Connection Manager][InstallHCM]

	Dabei wird eine angepasste Instanz des Connection Managers installiert, die bereits für die soeben erstellte Hybridverbindung vorkonfiguriert ist.

3. Schließen Sie die restlichen Einrichtungsschritte für den Connection Manager ab.

	![Hybrid Connection Manager setup][HCMSetup]

	Nach Abschluss der Installation wird der Status der Hybridverbindung als **1 Instanz verbunden** angezeigt. Unter Umständen müssen Sie Ihren Browser aktualisieren und einige Minuten abwarten. Die lokale Einrichtung ist nun abgeschlossen.

	![Hybrid Connection connected][HCConnected]

<a name="CreateService"></a>
## Anpassen eines Mobile Service, um die Verbindung zu verwenden
### Zuordnen der Hybridverbindung zum Dienst
1. Wählen Sie auf der Registerkarte **Mobile Services** im Portal einen vorhandenen mobilen Dienst aus, oder erstellen Sie einen neuen. 

	>[WACOM.NOTE]Wählen Sie unbedingt entweder einen Dienst aus, der mit dem .NET Back-End erstellt wurde, oder erstellen Sie einen neuen mobilen .NET Back-End-Dienst. Informationen zum Erstellen von mobilen .NET Back-End-Diensten finden Sie unter [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/) 

2. Suchen Sie in der Registerkarte **Konfigurieren** für Ihren mobilen Dienst den Bereich **Hybridverbindungen**, und klicken Sie auf **Hybridverbindung hinzufügen**.

	![Associate Hybrid Connection][AssociateHC]

3. Wählen Sie die soeben auf der Registerkarte "BizTalk Services" erstellte Hybridverbindung aus, und klicken Sie auf  **OK**. 

	![Pick associated Hybrid Connection][PickHC]

Der mobile Dienst ist nun zu der neuen Hybridverbindung zugeordnet.

### Ändern des Diensts, um die lokale Verbindungszeichenfolge zu verwenden
Zuletzt müssen wir eine Anwendungseinstellung erstellen, um die Verbindungszeichenfolge für unseren lokalen SQL Server zu speichern. Anschließend müssen wir den mobilen Dienst ändern, um die neue Verbindungszeichenfolge zu verwenden. 

1. Fügen Sie auf der Registerkarte **Konfigurieren** unter **Verbindungszeichenfolgen** eine neue Verbindungszeichenfolge mit dem Namen `OnPremisesDatabase` und einem Wert wie `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}` hinzu.

	![Connection string for on-premises database][ConnectionString]

	Ersetzen Sie `{password}` durch das sichere Kennwort für Ihre lokale Datenbank.

2. Klicken Sie auf **Speichern**, um die Hybridverbindung und die soeben erstellte Verbindungszeichenfolge zu speichern.

3. Öffnen Sie in Visual Studio 2013 das Projekt, in dem Ihr mobiler .NET-basierter Dienst definiert wird. 

	Informationen zum Herunterladen Ihres .NET Back-End-Projekts finden Sie unter [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/).
 
4. Erweitern Sie im Projektmappen-Explorer den Ordner **Modelle**, und öffnen Sie die Datenmodelldatei, deren Name mit *Context.cs* endet.

6. Fügen Sie anstelle des **DbContext**-Instanzkonstruktors den folgenden Ausschnitt ein:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDatabase")
            {
            }
        
            // snipped
        }

	Der Dienst verwendet jetzt die neue in Azure definierte Hybridverbindungszeichenfolge.

5. Veröffentlichen Sie Ihre Änderungen und rufen Sie mit einer Client-Anwendung, die mit dem mobilen Dienst verbunden ist, einige Operationen auf, um Datenänderungen zu generieren.

6. Öffnen Sie SQL Management Studio auf dem lokalen Computer, auf dem SQL Server ausgeführt wird, und erweitern Sie im Objekt-Explorer die Datenbank **OnPremisesDB** und anschließend den Knoten **Tabellen**. 

9. Klicken Sie mit der rechten Maustaste auf die Tabelle **hybridService1.TodoItems**, und wählen Sie **Top 1000 Zeilen auswählen** aus, um die Ergebnisse anzuzeigen.

	![SQL Management Studio][SMS]

Die Änderungen aus Ihrer App wurden von Ihrem mobilen Dienst in Ihre lokale Datenbank übertragen.

##Weitere Informationen##
 
+ [Website zu Hybridverbindungen](http://azure.microsoft.com/de-de/services/biztalk-services/)
+ [Überblick über Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274)
+ [BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Konfigurieren" und "Hybridverbindungen"](http://azure.microsoft.com/de-de/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

<!-- IMAGES -->
 
[CreateBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
[ConfigureBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
[AddHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
[ConfigureHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
[HCCreated]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
[InstallHCM]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
[HCMSetup]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
[HCConnected]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
[AssociateHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
[PickHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
[ConnectionString]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
[SMS]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
[DownloadHCM]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png
