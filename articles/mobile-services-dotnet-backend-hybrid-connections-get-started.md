<properties linkid="mobile-services-dotnet-backend-hybrid-connections-get-started" urlDisplayName="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" pageTitle="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections - Azure Mobile Services" metaKeywords="" description="Learn how to connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" metaCanonical="" services="" documentationCenter="Mobile" title="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# Verbinden eines mobilen Azure-Diensts mit einem lokalen SQL Server über Hybridverbindungen

Wenn ein Unternehmen die Cloud einführt, müssen oft bestimmte Bestände lokal erhalten werden, ob aus technischen, rechtlichen oder Sicherheitsgründen. Mit Mobile Services können Sie schnell und einfach eine in der Cloud gehostete Mobilitätsebene auf diese Bestände aufsetzen und lokal vor Ort über sichere Hybridverbindungen auf die Bestände zugreifen. Unterstützt werden unter anderem alle Ressourcen, die auf einem statischen TCP-Port laufen, inklusive Microsoft SQL Server, MySQL, HTTP Web-APIs und die meisten selbst entwickelten Webdienste.

In diesem Lernprogramm erfahren Sie, wie Sie einen mobilen .NET Back-End-Dienst so anpassen können, dass er eine lokale SQL Server-Datenbank anstatt der standardmäßig mit Ihrem Dienst bereitgestellten Azure SQL-Datenbank verwendet. Obwohl Hybridverbindungen für mobile JavaScript Back-End-Dienste unterstützt werden, behandelt dieses Thema nur mobile .NET Back-End-Dienste.

In diesem Thema werden die grundlegenden Schritte erläutert:

1.  [Voraussetzungen][]
2.  [Installieren von SQL Server Express, Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank][]
3.  [Erstellen einer Hybridverbindung][]
4.  [Installieren des lokalen Hybrid Connection Manager, um die Verbindung zu vervollständigen][]
5.  [Anpassen eines Mobile Service, um die Verbindung zu verwenden][]

<a name="Prerequisites"></a>

## Voraussetzungen

Zum Durchführen des Lernprogramms benötigen Sie folgende Produkte: Alle Produkte sind als kostenlose Versionen verfügbar, sodass Sie kostenlos mit der Entwicklung für Azure beginnen können.

-   **Visual Studio 2013** - Zum Herunterladen einer kostenlosen Testversion von Visual Studio 2013 besuchen Sie [Visual Studio Downloads][]. Installieren Sie diese Version, bevor Sie fortfahren.

-   **SQL Server 2014 Express with Tools** - Laden Sie Microsoft SQL Server Express kostenlos von der [Microsoft Web Platform Datenbank-Seite][] herunter. Wählen Sie die Version **Express** (nicht LocalDB). Die Version **Express with Tools** enthält das Produkt SQL Server Management Studio, das Sie in diesem Lernprogramm verwenden werden.

Außerdem benötigen Sie einen lokalen Computer, der sich über Hybridverbindungen mit Azure verbinden wird. Dieser Computer muss die folgenden Kriterien erfüllen:

-   Verbindung mit Azure über Port 5671 möglich
-   Verbindung zu *Hostname*:*Portnummer* der lokalen Ressource, mit der Sie sich verbinden möchten, ist möglich. Die Ressource muss nicht unbedingt auf demselben Computer gehostet sein.

<a name="InstallSQL"></a>

## Installieren von SQL Server Express, Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank

Um eine lokale SQL Server- oder SQL Server Express-Datenbank mit einer Hybridverbindung verwenden zu können, muss TCP/IP an einem statischen Port aktiviert werden. Standardinstanzen von SQL Server verwenden den statischen Port 1433, benannte Instanzen dagegen nicht.

Detaillierte Anweisung zum Konfigurieren von SQL Server, um die oben genannten Bedingungen zu erfüllen, finden Sie unter [Installieren von SQL Server Express, Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank][1]. Wenn Sie SQL Server bereits in einer Konfiguration und Umgebung installiert haben, welche die oben beschriebenen Kriterien erfüllen, können Sie gleich mit [Erstellen einer lokalen SQL Server-Datenbank][] beginnen.

Dieses Lernprogramm geht davon aus, dass der Name der Datenbank **OnPremisesDB** ist, die Datenbank auf Port **1433** läuft und der Hostname des Computers **onPremisesServer** ist.

<a name="CreateHC"></a>

## Erstellen einer Hybridverbindung

1.  Melden Sie sich auf lokalen Computer beim [Azure-Verwaltungsportal][] an.

2.  Klicken Sie unten im Navigationsbereich auf **+NEU** und anschließend auf **App-Dienste**, **BizTalk Service** und auf **Benutzerdefinierte Erstellung**.

    ![BizTalk Service erstellen][]

3.  Geben Sie einen Namen für den **BizTalk Service** ein und wählen Sie eine **Edition** aus.

    ![Neuen BizTalk Service konfigurieren][]

    Dieses Lernprogramm verwendet **mobile1**. Sie müssen einen eindeutigen Namen für Ihren neuen BizTalk Service angeben.

4.  Nach der Erstellung des BizTalk Service wählen Sie die Registerkarte **Hybridverbindungen** aus und klicken Sie auf **Hinzufügen**.

    ![Hybridverbindung hinzufügen][]

    Daraufhin wird eine neue Hybridverbindung erstellt.

5.  Geben Sie einen **Namen** und **Hostnamen** für Ihre Hybridverbindung ein und konfigurieren Sie den **Port** auf `1433`.

    ![Hybridverbindung konfigurieren][]

    Der Hostname ist der Name des lokalen Servers. Konfigurieren Sie die Hybridverbindung für den Zugriff auf SQL Server unter dem Port 1433.

6.  Nach der Erstellung wird die neue Verbindung in der folgenden Tabelle angezeigt.

    ![Hybridverbindung erfolgreich erstellt][]

    Der Status der neuen Verbindung wird als **Lokale Einrichtung unvollständig** angezeigt.

Nun müssen Sie den Hybrid Connection Manager auf dem lokalen Computer installieren.

<a name="InstallHCM"></a>

## Installieren des lokalen Hybrid Connection Manager, um die Verbindung zu vervollständigen

Über den Hybrid Connection Manager kann sich Ihr lokaler Computer mit Azure verbinden und TCP-Datenverkehr weiterleiten. Sie müssen diese Software auf dem lokalen Computer installieren, auf den Sie von Azure aus zugreifen möchten.

1.  Wählen Sie die soeben erstellte Verbindung aus und klicken Sie in der unteren Leiste auf **Lokale Einrichtung**.

    ![Lokale Einrichtung][]

2.  Klicken Sie auf **Installieren und Konfigurieren**.

    ![Hybrid Connection Manager installieren][]

    Dabei wird eine angepasste Instanz des Connection Managers installiert, die bereits für die soeben erstellte Hybridverbindung vorkonfiguriert ist.

3.  Schließen Sie die restlichen Einrichtungsschritte für den Connection Manager ab.

    ![Hybrid Connection Manager einrichten][]

    Nach Abschluss der Installation wird der Status der Hybridverbindung als **1 Instanz verbunden** angezeigt. Unter Umständen müssen Sie Ihren Browser aktualisieren und einige Minuten abwarten. Die lokale Einrichtung ist nun abgeschlossen.

    ![Hybridverbindung verbunden][]

<a name="CreateService"></a>

## Anpassen eines Mobile Service, um die Verbindung zu verwenden

### Zuordnen der Hybridverbindung zum Dienst

1.  Wählen Sie in der Registerkarte **Mobile Services** im Portal einen existierenden Mobile Service aus oder erstellen Sie einen neuen.

    > [WACOM.NOTE]Wählen Sie unbedingt entweder einen Dienst aus, der mit dem .NET Back-End erstellt wurde oder erstellen Sie einen neuen mobilen .NET Back-End-Dienst. Informationen zum Erstellen von mobilen .NET Back-End-Diensten finden Sie unter [Erste Schritte mit Mobile Services][]

2.  Suchen Sie in der Registerkarte **Konfigurieren** für Ihren mobilen Dienst den Bereich **Hybridverbindungen** und klicken Sie auf **Hybridverbindung hinzufügen**.

    ![Hybridverbindung zuordnen][]

3.  Wählen Sie die soeben in der Registerkarte BizTalk Services erstellte Hybridverbindung aus und klicken Sie auf **OK**.

    ![Zugeordnete Hybridverbindung auswählen][]

Der mobile Dienst ist nun zu der neuen Hybridverbindung zugeordnet.

### Ändern des Diensts, um die lokale Verbindungszeichenfolge zu verwenden

Zuletzt müssen wir eine Anwendungseinstellung erstellen, um die Verbindungszeichenfolge für unseren lokalen SQL Server zu speichern. Anschließend müssen wir den mobilen Dienst ändern, um die neue Verbindungszeichenfolge zu verwenden.

1.  Fügen Sie in der Registerkarte **Konfigurieren** unter **App-Einstellungen** eine neue App-Einstellung mit dem Namen `onPremisesDatabase` und einem Wert ähnlich `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}` hinzu.

    ![App-Einstellung für die Verbindungszeichenfolge][]

    Ersetzen Sie `{password}` durch das sichere Kennwort für Ihre lokale Datenbank.

2.  Klicken Sie auf **Speichern**, um die Hybridverbindung und die neu erstellte App-Einstellung zu speichern.

3.  Öffnen Sie in Visual Studio 2013 das Projekt, in dem Ihr mobiler .NET-basierter Dienst definiert wird.

    Informationen zum Herunterladen Ihres .NET Back-End-Projekts finden Sie unter [Erste Schritte mit Mobile Services][].

4.  Erweitern Sie im Projektmappen-Explorer den Ordner **Models** und öffnen Sie die Datenmodelldatei, deren Name mit *Context.cs* endet.

5.  Fügen Sie anstelle des **DbContext**-Instanzkonstruktors den folgenden Ausschnitt ein:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base(ConfigurationManager.AppSettings["onPremisesDatabase"])
            {
            }

            // snipped
        }

    Der Dienst verwendet nun die neue Hybridverbindungszeichenfolge, die in den App-Einstellungen in Azure definiert ist.

6.  Veröffentlichen Sie Ihre Änderungen und rufen Sie mit einer Client-Anwendung, die mit dem mobilen Dienst verbunden ist, einige Operationen auf, um Datenänderungen zu generieren.

7.  Öffnen Sie SQL Management Studio auf dem lokalen Computer, auf dem SQL Server ausgeführt wird, und erweitern Sie im Objekt-Explorer die Datenbank **OnPremisesDB** und anschließend den Knoten **Tabellen**.

8.  Klicken Sie mit der rechten Maustaste auf die Tabelle **hybridService1.TodoItems** und wählen Sie **Top 1000 Zeilen auswählen** aus, um die Ergebnisse anzuzeigen.

    ![SQL Management Studio][]

Die Änderungen aus Ihrer App wurden von Ihrem mobilen Dienst in Ihre lokale Datenbank übertragen.

## Weitere Informationen

-   [Website zu Hybridverbindungen][]
-   [Überblick über Hybridverbindungen][]
-   [BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Konfigurieren" und "Hybridverbindungen"][]

<!-- IMAGES -->

  [Voraussetzungen]: #Prerequisites
  [Installieren von SQL Server Express, Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank]: #InstallSQL
  [Erstellen einer Hybridverbindung]: #CreateHC
  [Installieren des lokalen Hybrid Connection Manager, um die Verbindung zu vervollständigen]: #InstallHCM
  [Anpassen eines Mobile Service, um die Verbindung zu verwenden]: #CreateService
  [Visual Studio Downloads]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [Microsoft Web Platform Datenbank-Seite]: http://www.microsoft.com/web/platform/database.aspx
  [1]: /en-us/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL
  [Erstellen einer lokalen SQL Server-Datenbank]: /en-us/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB
  [Azure-Verwaltungsportal]: http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409
  [BizTalk Service erstellen]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
  [Neuen BizTalk Service konfigurieren]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
  [Hybridverbindung hinzufügen]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
  [Hybridverbindung konfigurieren]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
  [Hybridverbindung erfolgreich erstellt]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
  [Lokale Einrichtung]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png
  [Hybrid Connection Manager installieren]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
  [Hybrid Connection Manager einrichten]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
  [Hybridverbindung verbunden]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
  [Erste Schritte mit Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Hybridverbindung zuordnen]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
  [Zugeordnete Hybridverbindung auswählen]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
  [App-Einstellung für die Verbindungszeichenfolge]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
  [SQL Management Studio]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
  [Website zu Hybridverbindungen]: http://azure.microsoft.com/en-us/services/biztalk-services/
  [Überblick über Hybridverbindungen]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Konfigurieren" und "Hybridverbindungen"]: http://azure.microsoft.com/en-us/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
