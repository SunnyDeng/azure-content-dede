<properties
	pageTitle="Erstellen und Bereitstellen eines Clouddiensts| Microsoft Azure"
	description="Hier erfahren Sie, wie Sie einen Clouddienst mithilfe der Schnellerfassung in Azure erstellen und bereitstellen. In diesen Beispielen wird das Azure-Portal verwendet."
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/15/2016"
	ms.author="adegeo"/>




# Erstellen und Bereitstellen eines Clouddiensts

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-create-deploy-portal.md)
- [Azure classic portal](cloud-services-how-to-create-deploy.md)

Das Azure-Portal bietet zwei Methoden zum Erstellen und Bereitstellen eines Clouddiensts: *Schnellerfassung* und *Benutzerdefiniert erstellen*.

In diesem Thema wird erläutert, wie Sie die Schnellerfassungsmethode zum Erstellen eines neuen Clouddiensts und dann **Hochladen** verwenden, um ein Clouddienstpaket in Azure hochzuladen und bereitzustellen. Wenn Sie diese Methode verwenden, werden im Azure-Portal praktische Links zum Erfüllen aller Anforderungen zur Verfügung gestellt. Wenn Sie Ihren Cloud-Dienst bei der Erstellung auch bereitstellen möchten, können Sie beides mithilfe von Benutzerdefinierte Erstellung durchführen.

> [AZURE.NOTE]Wenn Sie Ihren Clouddienst aus Visual Studio Team Services (VSTS) veröffentlichen möchten, verwenden Sie die Schnellerstellung. Richten Sie die VSTS-Veröffentlichung dann über Azur-Schnellstart oder das Dashboard ein. Weitere Informationen finden Sie unter [Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Team Services][TFSTutorialForCloudService] oder in der Hilfe zur **Schnellstart**-Seite.

## Konzepte
Für die Bereitstellung einer Anwendung als Clouddienst in Azure sind drei Komponenten erforderlich:

- **Dienstdefinition:** Die Clouddienst-Definitionsdatei (.csdef) definiert das Dienstmodell einschließlich der Rollenanzahl.

- **Dienstkonfiguration:** Die Clouddienst-Konfigurationsdatei (.cscfg) enthält Konfigurationseinstellungen für den Clouddienst sowie einzelne Rollen, darunter die Anzahl der Rolleninstanzen.

- **Dienstpaket:** Das Dienstpaket (.cspkg) enthält den Anwendungscode, die Konfigurationen und die Dienstdefinitionsdatei.

Weitere Informationen zu diesen Komponenten sowie zum Erstellen eines Pakets finden Sie [hier](cloud-services-model-and-package.md).

## Vorbereiten Ihrer App
Bevor Sie einen Clouddienst bereitstellen können, müssen Sie das Clouddienstpaket (CSPKG) aus dem Anwendungscode und eine Clouddienstkonfigurationsdatei (CSCFG) erstellen. Das Azure-SDK stellt Tools zum Vorbereiten dieser erforderlichen Bereitstellungsdateien bereit. Sie können das SDK auf der Seite [Azure-Downloads](http://azure.microsoft.com/downloads/) in der Sprache herunterladen, in der Sie den Anwendungscode entwickeln möchten.

Drei Clouddienstfunktionen benötigen vor dem Export eines Dienstpakets spezielle Konfigurationen:

- Wenn Sie einen Clouddienst bereitstellen möchten, der Secure Sockets Layer (SSL) für die Datenverschlüsselung verwendet, [konfigurieren Sie die Anwendung für SSL](cloud-services-configure-ssl-certificate-portal.md#modify).

- Wenn Sie Remotedesktopverbindungen zu Rolleninstanzen konfigurieren möchten, [konfigurieren Sie die Rollen](cloud-services-role-enable-remote-desktop.md) für Remotedesktop. Dies kann nur im klassischen Portal durchgeführt werden.

- Wenn Sie die ausführliche Überwachung für den Clouddienst konfigurieren möchten, aktivieren Sie für den Clouddienst die Azure-Diagnose. *Minimale Überwachung* (die Standardüberwachungsstufe) verwendet Leistungsindikatoren, die aus den Hostbetriebssystemen für Rolleninstanzen (virtuelle Computer) erfasst wurden. Bei der *ausführlichen Überwachung* werden zusätzliche Kennzahlen basierend auf Leistungsdaten innerhalb der Rolleninstanzen erfasst, um eine genauere Analyse von Problemen zu ermöglichen, die während der Anwendungsverarbeitung auftreten. Informationen zum Aktivieren der Azure-Diagnose finden Sie unter [Aktivieren der Diagnose in Azure](cloud-services-dotnet-diagnostics.md).

Sie müssen das [Dienstpaket erstellen](cloud-services-model-and-package.md#servicepackagecspkg), um einen Clouddienst mit Bereitstellungen von Webrollen oder Workerrollen zu erstellen.

## Voraussetzungen

- Falls Sie das Azure-SDK noch nicht installiert haben, klicken Sie auf **Azure-SDK installieren**, um die [Azure-Downloadseite](http://azure.microsoft.com/downloads/) zu öffnen. Laden Sie dann das SDK für die Sprache herunter, in der Sie den Code entwickeln möchten. (Dazu haben Sie auch später noch die Möglichkeit.)

- Falls Rolleninstanzen ein Zertifikat erfordern, erstellen Sie die Zertifikate. Clouddienste erfordern eine PFX-Datei mit einem privaten Schlüssel. Sie können die [Zertifikate zu Azure hochladen](), wenn Sie den Clouddienst erstellen und bereitstellen.

- Wenn Sie den Clouddienst für eine Affinitätsgruppe bereitstellen möchten, erstellen Sie die Affinitätsgruppe. Sie können eine Affinitätsgruppe verwenden, um den Clouddienst und andere Azure-Dienste für den gleichen Standort in einer Region bereitzustellen. Sie können die Affinitätsgruppe im Bereich **Netzwerke** des klassischen Azure-Portals auf der Seite **Affinitätsgruppen** erstellen.


## Schritt 3: Erstellen eines Clouddiensts und Hochladen des Bereitstellungspakets

1. Melden Sie sich beim Azure-Portal an.
2. Klicken Sie auf **Neu > Compute**, führen Sie einen Bildlauf nach unten zu **Clouddienst** aus, und klicken Sie darauf.

    ![Clouddienst veröffentlichen](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. Klicken Sie unten auf der angezeigten Informationsseite auf **Erstellen**.
4. Geben Sie auf dem neuen Blatt **Clouddienst** einen Wert für **DNS-Name** ein.
5. Erstellen Sie eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene Ressourcengruppe aus.
6. Wählen Sie einen **Speicherort** aus.
7. Wählen Sie **Paket**, und füllen Sie auf dem Blatt **Paket hochladen** die erforderlichen Felder aus.  

     Wenn eine der Rollen eine einzelne Instanz enthält, stellen Sie sicher, dass **Auch dann bereitstellen, wenn für mindestens eine Rolle nur eine Instanz vorhanden ist.** aktiviert ist.

8. Stellen Sie sicher, dass **Bereitstellung starten** aktiviert ist.
9. Klicken Sie auf **OK**.

    ![Clouddienst veröffentlichen](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## Hochladen eines Zertifikats

Wenn Ihr Bereitstellungspaket [für die Verwendung von Zertifikaten konfiguriert](cloud-services-configure-ssl-certificate-portal.md#modify) wurde, können Sie das Zertifikat jetzt hochladen.

1. Wählen Sie **Zertifikate**. Wählen Sie auf dem Blatt **Zertifikate hinzufügen** die PFX-Datei mit dem SSL-Zertifikat aus, und geben Sie das **Kennwort** für das Zertifikat ein.
2. Klicken Sie auf **Zertifikat anfügen** und anschließend auf dem Blatt **Zertifikate hinzufügen** auf **OK**.
3. Klicken Sie auf dem Blatt **Clouddienst** auf **Erstellen**. Wenn sich die Bereitstellung im Status **Ready** befindet, können Sie mit den nächsten Schritten fortfahren.

    ![Clouddienst veröffentlichen](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## Überprüfen, ob Ihre Bereitstellung erfolgreich abgeschlossen wurde

1. Klicken Sie auf die Instanz des Clouddiensts.

	Der Status sollte anzeigen, dass der Dienst **Ausgeführt** wird.

2. Klicken Sie unter **Essentials** auf die **Website-URL**, um den Clouddienst in einem Webbrowser zu öffnen.

    ![CloudServices\_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## Nächste Schritte

* [Allgemeine Konfiguration Ihres Clouddiensts](cloud-services-how-to-configure-portal.md)
* [Konfigurieren eines benutzerdefinierten Domänennamens](cloud-services-custom-domain-name-portal.md)
* [Verwalten Ihres Clouddiensts](cloud-services-how-to-manage-portal.md)
* Konfigurieren von [SSL-Zertifikaten](cloud-services-configure-ssl-certificate-portal.md)

<!---HONumber=AcomDC_0121_2016-->