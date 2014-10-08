<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# Erstellen einer Cloud-Bereitstellung einer RemoteApp

Es existieren zwei Bereitstellungsarten für RemoteApps:

-   Cloud: Die Bereitstellung erfolgt vollständig in der Cloud mithilfe der Option **Schnellerfassung** im Azure-Verwaltungsportal.
-   Hybrid: Verwendet ein virtuelles Netzwerk für lokalen Zugriff und wird mithilfe der Optionen **Mit VPN erstellen** im Verwaltungsportal erstellt.

Dieses Lernprogramm beschreibt Schritte bei der Erstellung einer Cloud-Bereitstellung. Es sind vier Schritte notwendig:

1.  Erstellen eines RemoteApp-Diensts.
2.  Optionale Konfiguration der Verzeichnissynchronisierung. RemoteApp benötigt diese Funktion, um Benutzer, Gruppen, Kontakte und Kennwörter aus Ihrem lokalen Active Directory in Ihren Active Directory-Mandanten in Azure zu synchronisieren.
3.  Veröffentlichen von RemoteApp-Programmen.
4.  Konfigurieren des Benutzerzugriffs.

**Bevor Sie anfangen**

Sie benötigen Folgendes, bevor Sie den Dienst erstellen könne:

-   Registrieren Sie sich für die Vorschau von RemoteApp. Sie können sich unter [][]<http://azure.microsoft.com/en-us/services/remoteapp/></a> registrieren.
-   Sammeln Sie Informationen über die Benutzer und Gruppen, denen Sie Zugriff gewähren möchten. Dabei handelt es sich entweder um Microsoft-Konten oder Active Directory-Organisations-Konten für Benutzer oder Gruppen.
-   Wir setzen voraus, dass Sie entweder die im Rahmen Ihres Abonnements bereitgestellte Abbildvorlage verwenden oder dass Sie Ihre gewünschte Abbildvorlage bereits hochgeladen haben. Falls Sie eine andere Abbildvorlage hochladen möchten, können Sie dies auf der Seite für Abbildvorlagen tun. Klicken Sie auf **Abbildvorlage hochladen** und folgen Sie den Schritten im Assistenten.

## **Schritt 1: Erstellen eines RemoteApp-Diensts**

1.  Navigieren Sie im [Azure-Verwaltungsportal][] zur RemoteApp-Seite.
2.  Klicken Sie auf **Neu \> Schnellerfassung**.

3.  Geben Sie einen Namen für Ihren Dienst ein und wählen Sie eine Region aus.
4.  Wählen Sie das Abonnement aus, das Sie für die Erstellung des Diensts verwenden möchten.
5.  Wählen Sie die Vorlage für den Dienst aus.

    **Tipp:** Ihr RemoteApp-Abonnement enthält bereits eine Abbildvorlage mit Office 2013-Programmen, von denen einige bereits veröffentlicht sind (z. B. Word) und andere bereit für die Veröffentlichung sind.

6.  Klicken Sie auf **RemoteApp-Dienst erstellen**.

    **Wichtig:** Die Bereitstellung Ihres Diensts kann bis zu 30 Minuten in Anspruch nehmen.

Nachdem Ihr RemoteApp-Dienst erstellt wurde, öffnen Sie die **Schnellstart**-Seite für RemoteApp, um die Einrichtung fortzusetzen.

## **Schritt 2: Konfigurieren der Active Directory-Verzeichnissynchronisierung (optional)**

Falls Sie Active Directory verwenden möchten, benötigt RemoteApp Verzeichnissynchronisierung zwischen Azure Active Directory und Ihrem lokalen Active Directory, um Benutzer, Gruppen, Kontakte und Kennwörter mit Ihrem Active Directory-Mandanten in Azure zu synchronisieren. Unter [Roadmap für Verzeichnissynchronisierung][] finden Sie Informationen zur Planung und ausführliche Schritte.

## **Schritt 3: Veröffentlichen von RemoteApp-Programmen**

Ein RemoteApp-Programm ist die App bzw. das Programm, das Sie Ihren Benutzern bereitstellen. Es befindet sich in der Abbildvorlage, die Sie für den Dienst hochgeladen haben. Wenn ein Benutzer auf ein RemoteApp-Programm zugreift, scheint das Programm in der lokalen Umgebung zu laufen, obwohl es tatsächlich in Azure ausgeführt wird.

Bevor Ihre Benutzer RemoteApp-Programme verwenden können, müssen Sie diese für den Endbenutzer-Feed veröffentlichen, eine Liste von Programmen, die Ihre Benutzer über das Azure-Portal erreichen können.

Sie können mehrere Programme in Ihrem RemoteApp-Dienst veröffentlichen. Klicken Sie in der Seite für RemoteApp-Programme auf **Veröffentlichen**, um ein Programm hinzuzufügen. Die Veröffentlichung erfolgt entweder über das Startmenü der Abbildvorlage oder durch Angabe des Pfads für das Programm in der Abbildvorlage. Wählen Sie für die Veröffentlichung über das Startmenü das zu veröffentlichende Programm aus. Wenn Sie stattdessen den Pfad angeben möchten, geben Sie einen Namen für das Programm und den Pfad ein, unter dem das Programm in der Abbildvorlage installiert ist.

## **Schritt 4: Konfigurieren des Benutzerzugriffs**

Sie haben Ihren RemoteApp-Dienst erstellt und müssen nun Benutzer und Gruppen hinzufügen, denen Sie Ihre Remoteressourcen zur Verfügung stellen möchten. Falls Sie Active Directory verwenden, müssen die entsprechenden Benutzer oder Gruppen in demjenigen Active Directory-Mandanten existieren, der dem Abonnement zugeordnet ist, mit dem Sie diesen RemoteApp-Dienst erstellt haben.

1.  Klicken Sie auf der Seite Schnellstart auf **Benutzerzugriff konfigurieren**.
2.  Geben Sie das Organisations-Konto, den Gruppennamen (aus Active Directory) oder das Microsoft-Konto ein, dem Sie Zugriff gewähren möchten.

    Stellen Sie für Benutzer sicher, dass Sie das Format "<user@domain.com>" verwenden. Geben Sie für Gruppen den Gruppennamen ein.

3.  Validieren Sie die Benutzer bzw. Gruppen und klicken Sie auf **Speichern**.

## Nächste Schritte

Geschafft - Sie haben Ihre RemoteApp-Cloud-Bereitstellung erfolgreich erstellt und bereitgestellt. Ihre Benutzer können nun den Remotedesktop-Client herunterladen und installieren. Sie finden die URL für den Client in der RemoteApp-Schnellstartseite. Anschließend können sich die Benutzer in Azure anmelden und die von Ihnen veröffentlichten RemoteApp-Programme verwenden.

  []: http://azure.microsoft.com/en-us/services/remoteapp/
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Roadmap für Verzeichnissynchronisierung]: http://msdn.microsoft.com/en-us/library/azure/hh967642.aspx
