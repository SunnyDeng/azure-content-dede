<properties title="Erstellen einer Cloud-Bereitstellung einer RemoteApp" pageTitle="Erstellen einer Cloud-Bereitstellung einer RemoteApp" description="Erfahren Sie, wie Sie eine Bereitstellung einer RemoteApp erstellen, mit der Daten in der Azure-Cloud gespeichert werden." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/05/2014" ms.author="elizapo" ms.manager="kathyw" />

# Erstellen einer Cloud-Bereitstellung einer RemoteApp

Es existieren zwei Bereitstellungsarten für RemoteApps:

-   Cloud: Die Bereitstellung erfolgt vollständig in der Cloud mithilfe der Option **Schnellerfassung** im Azure-Verwaltungsportal.
-   Hybrid: Verwendet ein virtuelles Netzwerk für lokalen Zugriff und wird mithilfe der Optionen **Mit VPN erstellen** im Verwaltungsportal erstellt.

Dieses Lernprogramm beschreibt Schritte bei der Erstellung einer Cloud-Bereitstellung. Es sind vier Schritte notwendig:

1.  Erstellen eines RemoteApp-Dienstes.
2.  Optionale Konfiguration der Verzeichnissynchronisierung. Dies ist für die Synchronisierung von Benutzern, Gruppen, Kontakten und Kennwörtern vom lokalen Active Directory zum Azure Active Directory-Mandanten erforderlich.
3.  Veröffentlichen von RemoteApp-Programmen.
4.  Konfigurieren des Benutzerzugriffs.

**Voraussetzungen**

Bevor Sie mit der Erstellung des Dienstes beginnen, führen Sie Folgendes aus:

-   Registrieren Sie sich für die Vorschau von RemoteApp. Sie können sich unter [][]<http://azure.microsoft.com/de-de/services/remoteapp/></a> registrieren.
-   Sammeln Sie Informationen über die Benutzer und Gruppen, denen Sie Zugriff gewähren möchten. Dies können Informationen zu Microsoft-Konten oder Active Directory-Geschäftskonten für Benutzer oder Gruppen sein.
-   Wir setzen voraus, dass Sie entweder die im Rahmen Ihres Abonnements bereitgestellte Abbildvorlage verwenden oder dass Sie Ihre gewünschte Abbildvorlage bereits hochgeladen haben. Falls Sie eine andere Abbildvorlage hochladen möchten, können Sie dies auf der Seite für Abbildvorlagen tun. Klicken Sie auf **Abbildvorlage hochladen** und folgen Sie den Schritten im Assistenten.
-   Möchten Sie benutzerdefinierte Apps oder LOB-Programme bereitstellen? Erstellen Sie ein neues [benutzerdefiniertes Vorlagenimage][benutzerdefiniertes Vorlagenimage], und verwenden Sie es in der Cloudbereitstellung.

## **Schritt 1: Erstellen eines RemoteApp-Diensts**

1.  Navigieren Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] zur RemoteApp-Seite.
2.  Klicken Sie auf **Neu \> Schnellerfassung**.

3.  Geben Sie einen Namen für Ihren Dienst ein und wählen Sie eine Region aus.
4.  Wählen Sie das Abonnement aus, das Sie für die Erstellung des Diensts verwenden möchten.
5.  Wählen Sie die Vorlage für den Dienst aus.

    **Tipp:** Ihr RemoteApp-Abonnement enthält bereits eine Abbildvorlage mit Office 2013-Programmen, von denen einige bereits veröffentlicht sind (z. B. Word) und andere bereit für die Veröffentlichung sind. Sie können auch ein neues [benutzerdefiniertes Vorlagenimage][benutzerdefiniertes Vorlagenimage] erstellen und es in der Cloudbereitstellung verwenden.

6.  Klicken Sie auf **RemoteApp-Dienst erstellen**.

    **Wichtig:** Die Bereitstellung Ihres Diensts kann bis zu 30 Minuten in Anspruch nehmen.

Gehen Sie nach dem Erstellen des RemoteApp-Dienstes zur RemoteApp-Seite **Schnellstart**, um mit der Einrichtung fortzufahren.

## **Schritt 2: Konfigurieren der Active Directory-Verzeichnissynchronisierung (optional)**

Falls Sie Active Directory verwenden möchten, benötigt RemoteApp Verzeichnissynchronisierung zwischen Azure Active Directory und Ihrem lokalen Active Directory, um Benutzer, Gruppen, Kontakte und Kennwörter mit Ihrem Active Directory-Mandanten in Azure zu synchronisieren. Informationen zur Planung und eine detaillierte Anleitung finden Sie unter [Fahrplan zur Verzeichnissynchronisierung][Fahrplan zur Verzeichnissynchronisierung].

## **Schritt 3: Veröffentlichen von RemoteApp-Programmen**

Ein RemoteApp-Programm ist die App oder das Programm, das Sie den Benutzern zur Verfügung stellen. Es befindet sich im Vorlagenimage, das Sie für den Dienst hochgeladen haben. Wenn ein Benutzer auf das RemoteApp-Programm zugreift, scheint dieses Programm in der lokalen Umgebung des Benutzers zu laufen, tatsächlich wird es aber in Azure ausgeführt.

Bevor ein Benutzer aber auf das RemoteApp-Programm zugreifen kann, muss es im Endbenutzer-Feed – einer Liste verfügbarer Programme, auf die der Benutzer über das Azure-Portal zugreift – veröffentlicht werden.

Sie können in Ihrem RemoteApp-Dienst mehrere Programme veröffentlichen. Um ein Programm hinzuzufügen, klicken Sie auf der Seite mit den RemoteApp-Programmen auf **Veröffentlichen**. Sie können das Programm vom Startmenü des Vorlagenimage aus veröffentlichen oder den Pfad zum Programm im Vorlagenimage angeben. Wenn Sie das Programm vom Startmenü aus veröffentlichen möchten, wählen Sie das zu veröffentlichende Programm aus. Wenn Sie den Pfad angeben möchten, geben Sie den Namen des Programms sowie den Pfad an, unter dem das Programm im Vorlagenimage installiert ist.

## **Schritt 4: Konfigurieren des Benutzerzugriffs**

Nach dem Erstellen des RemoteApp-Dienstes müssen Sie nun die Benutzer und Gruppen hinzufügen, die Zugriff auf die Remoteressourcen haben sollen. Falls Sie Active Directory verwenden, müssen die entsprechenden Benutzer oder Gruppen in demjenigen Active Directory-Mandanten existieren, der dem Abonnement zugeordnet ist, mit dem Sie diesen RemoteApp-Dienst erstellt haben.

1.  Klicken Sie auf der Seite "Schnellstart" auf **Benutzerzugriff konfigurieren**.
2.  Geben Sie das Geschäftskonto, den Gruppennamen (aus Active Directory) oder das Microsoft-Konto ein, dem Sie den Zugriff erteilen möchten.

    Verwenden Sie für Benutzer das Format “<user@domain.com>”. Für Gruppen geben Sie den Gruppennamen ein.

3.  Sobald die Benutzer oder Gruppen überprüft wurden, klicken Sie auf **Speichern**.

## Nächste Schritte

Geschafft - Sie haben Ihre RemoteApp-Cloud-Bereitstellung erfolgreich erstellt und bereitgestellt. Als Nächstes müssen die Benutzer den Remotedesktopclient herunterladen und installieren. Die URL für den Client finden Sie auf der RemoteApp-Schnellstartseite. Die Benutzer müssen sich nun beim Client anmelden und auf die veröffentlichten RemoteApp-Programme zugreifen.

  []: http://azure.microsoft.com/de-de/services/remoteapp/
  [benutzerdefiniertes Vorlagenimage]: http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-custom-image/
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Fahrplan zur Verzeichnissynchronisierung]: http://msdn.microsoft.com/de-de/library/azure/hh967642.aspx
