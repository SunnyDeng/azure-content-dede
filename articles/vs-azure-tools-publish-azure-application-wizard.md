<properties 
   pageTitle="Assistent zur Veröffentlichung einer Azure-Anwendung"
	description="Assistent zur Veröffentlichung einer Azure-Anwendung"
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee"/>
<tags 
   ms.service="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/24/2015"
	ms.author="kempb"/>

# Assistent zur Veröffentlichung einer Azure-Anwendung

## Übersicht

Nachdem Sie eine Webanwendung in Visual Studio entwickelt haben, können Sie diese Anwendung mit dem **Assistenten zum Veröffentlichen einer Azure-Anwendung** leichter in einem Azure-Clouddienst veröffentlichen. Im ersten Abschnitt werden die Schritte erläutert, die Sie ausführen müssen, bevor Sie den Assistenten verwenden. In den verbleibenden Abschnitten werden die Features des Assistenten vorgestellt.

>[AZURE.NOTE]In diesem Thema wird die Bereitstellung in Clouddiensten und nicht auf Websites behandelt. Weitere Informationen zum Bereitstellen von Websites finden Sie unter [Bereitstellen einer Azure-Website](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## Voraussetzungen

Bevor Sie Ihre Webanwendung in Azure veröffentlichen können, benötigen Sie ein Microsoft-Konto und ein Azure-Abonnement. Außerdem müssen Sie Ihre Webanwendung einem Azure-Clouddienst zuordnen. Wenn Sie diese Aufgaben bereits erledigt haben, können Sie mit dem nächsten Absatz fortfahren.

1. Verschaffen Sie sich ein Microsoft-Konto und ein Azure-Abonnement. [Hier](https://azure.microsoft.com/pricing/free-trial/) können ein einen Monat lang kostenloses Azure-Abonnement bestellen.

1. Erstellen Sie einen Clouddienst und ein Speicherkonto in Azure. Hierzu können Sie den Server-Explorer in Visual Studio oder das [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=213885) verwenden. Weitere Informationen zum Einrichten Ihrer Azure-Umgebung finden Sie unter [Einrichten von Diensten, die zum Veröffentlichen eines Clouddiensts mit Visual Studio erforderlich sind](vs-azure-tools-publish-azure-application-wizard).

1. Aktivieren Sie Ihre Webanwendung für Azure. Damit Ihre Webanwendung aus Visual Studio in Azure veröffentlicht werden kann, müssen Sie sie einem Azure-Clouddienstprojekt in Visual Studio zuordnen. Wenn Sie das zugeordnete Clouddienstprojekt erstellen möchten, öffnen Sie das Kontextmenü für das Projekt für Ihre Webanwendung, und wählen Sie dann **Konvertieren -> In Azure-Clouddienstprojekt konvertieren** aus.

1. Nachdem Sie das Clouddienstprojekt der Projektmappe hinzugefügt haben, öffnen Sie das Kontextmenü erneut, und wählen Sie dann **Veröffentlichen** aus. Weitere Informationen zum Aktivieren von Anwendungen für Azure finden Sie unter [Vorgehensweise: Migrieren und Veröffentlichen einer Webanwendung in einem Azure-Clouddienst aus Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx).

>[AZURE.NOTE]Sie müssen Visual Studio mit Administratorrechten aufrufen (Als Administrator ausführen).

1. Wenn Sie zum Veröffentlichen der Anwendung bereit sind, öffnen Sie das Kontextmenü für das Azure-Clouddienstprojekt, und wählen Sie dann **Veröffentlichen** aus. In den folgenden Schritten wird der Assistent zum Veröffentlichen der Azure-Anwendung gezeigt.

## Wählen des Abonnements

### So wählen Sie ein Abonnement aus

1. Bevor Sie den Assistenten zum ersten Mal verwenden, müssen Sie sich anmelden. Klicken Sie auf den Link **Anmelden**. Melden Sie sich bei Aufforderung am Azure-Portal an, und geben Sie Ihren Azure-Benutzernamen und Ihr Kennwort an. 

    ![Dies ist einer der Bildschirme des Veröffentlichungs-Assistenten](./media/vs-azure-tools-publish-azure-application-wizard/IC799159.png)

    Die Liste der Abonnements wird mit den Abonnements aufgefüllt, die dem Konto zugeordnet sind. Möglicherweise werden auch Abonnements für alle zuvor importierten Abonnementdateien angezeigt.

1. Wählen Sie in der Liste **Wählen Sie Ihr Abonnement** das Abonnement aus, das für diese Bereitstellung verwendet werden soll.

   Wenn Sie **<Verwalten...>** auswählen, wird das Dialogfeld **Abonnements verwalten** aufgerufen, in dem Sie das gewünschte Abonnement und das Benutzerkonto auswählen können. Auf der Registerkarte **Konten** werden alle Ihre Konten angezeigt. Auf der Registerkarte **Abonnements** werden alle den Konten zugeordneten Abonnements aufgeführt. Sie können auch eine Region auswählen, aus der Sie Azure-Ressourcen verwenden möchten, sowie Zertifikate für Ihr Abonnement im Azure-Portal erstellen oder importieren. Wenn Sie Abonnements über eine Abonnementdatei importiert haben, werden die zugeordneten Zertifikate auf der Registerkarte **Zertifikate** angezeigt. Wenn Sie fertig sind, klicken Sie auf die Schaltfläche **Schließen**.

    ![Manage subscriptions](./media/vs-azure-tools-publish-azure-application-wizard/IC799160.png)

    >[AZURE.NOTE] A subscription file can contain more than one subscription.

1. Klicken Sie zum Fortfahren auf die Schaltfläche **Weiter**. 

    Wenn keine Clouddienste in Ihrem Abonnement vorhanden sind, müssen Sie einen Clouddienst in Azure zum Hosten des Projekts erstellen. Das Dialogfeld **Clouddienst und Speicherkonto erstellen** wird angezeigt.

    Geben Sie einen neuen Namen für den Clouddienst an. Der Name muss in Azure eindeutig sein. Geben Sie dann eine Region oder eine Affinitätsgruppe für ein Datencenter ein, das in Ihrer Nähe oder der Nähe der meisten Ihrer Kunden liegt. Dieser Name wird auch für ein neues Speicherkonto verwendet, das Azure für Ihren Clouddienst erstellt.

1. Ändern Sie die Einstellungen, die Sie für diese Bereitstellung verwenden möchten, und veröffentlichen Sie die Bereitstellung dann über die Schaltfläche **Veröffentlichen**. (Im nächsten Abschnitt werden weitere Details zu den verschiedenen Einstellungen erläutert.) Um die Einstellungen vor der Veröffentlichung zu überprüfen, klicken Sie auf **Weiter**.

    >[AZURE.NOTE]Wenn Sie "Veröffentlichen" in diesem Schritt gewählt haben, können Sie den Status dieser Bereitstellung in Visual Studio überwachen.

Sie können die allgemeinen und erweiterten Einstellungen für eine Bereitstellung mit dem **Assistenten zum Veröffentlichen einer Azure-Anwendung** ändern. Beispielsweise können Sie eine Einstellung wählen, um Ihre Anwendung in einer Testumgebung bereitstellen, bevor Sie sie freigeben. Die folgende Abbildung zeigt die Registerkarte **Allgemeine Einstellungen** für eine Azure-Bereitstellung.

![Allgemeine Einstellungen](./media/vs-azure-tools-publish-azure-application-wizard/IC749013.png)

## Konfigurieren der Einstellungen für die Veröffentlichung

### So konfigurieren Sie die Einstellungen für die Veröffentlichung

1. Führen Sie in der Liste **Clouddienst** einen der folgenden Schritte aus:

   1. Wählen Sie im Dropdownlistenfeld einen vorhandenen Clouddienst aus. Der Standort des Datencenters für den Dienst wird angezeigt. Sie müssen sich diesen Standort notieren und sicherstellen, dass sich der Speicherort des Speicherkontos im selben Datencenter befindet.

    1. Wählen Sie **Neu erstellen**, um einen von Azure gehosteten Clouddienst zu erstellen. Geben Sie im Dialogfeld **Clouddienst erstellen** einen Namen für den Dienst an, und geben Sie dann eine Region oder eine Affinitätsgruppe an, um den Standort des Datencenters festzulegen, in dem der Clouddienst gehostet werden soll. Der Name muss in Azure eindeutig sein.

1. Wählen Sie in der Liste **Umgebung** entweder **Produktion** oder **Staging**. Wählen Sie die Stagingumgebung, wenn Sie die Anwendung in einer Testumgebung bereitstellen möchten. Sie können Ihre Anwendung später in die Produktionsumgebung verlagern.

1. Wählen Sie in der Liste **Buildkonfiguration** entweder **Debuggen** oder **Release**.

1. Wählen Sie in der Liste **Dienstkonfiguration** entweder **Cloud** oder **Lokal**.

    Aktivieren Sie das Kontrollkästchen **Remotedesktop für alle Rollen aktivieren**, wenn Sie eine Remoteverbindung mit dem Dienst herstellen möchten. Diese Option wird in erster Linie für die Problembehandlung verwendet. Ist dieses Kontrollkästchen aktiviert, wird das Dialogfeld **Remotedesktopkonfiguration** angezeigt. Wählen Sie den Link "Einstellungen", um die Konfiguration zu ändern.

    Wählen Sie **Web Deploy für alle Webrollen aktivieren**, um die Webbereitstellung für den Dienst zu aktivieren. Aktivieren Sie Remotedesktop, um dieses Feature zu verwenden. Weitere Informationen finden Sie unter [[Veröffentlichen eines Clouddiensts mit Azure Tools](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx). Weitere Informationen zu Web Deploy finden Sie unter [[Veröffentlichen eines Clouddiensts mit Azure Tools](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx).

1. Wählen Sie die Registerkarte **Erweiterte Einstellungen** aus. Akzeptieren Sie im Feld **Bereitstellungsbezeichnung** den Standardnamen, oder geben Sie einen Namen Ihrer Wahl ein. Um der Bereitstellungsbezeichnung das Datum anzufügen, lassen Sie das Kontrollkästchen aktiviert.

    ![Dritter Bildschirm des Veröffentlichungs-Assistenten](./media/vs-azure-tools-publish-azure-application-wizard/IC749014.png)

1. Wählen Sie in der Liste **Speicherkonto** das Speicherkonto aus, das für diese Bereitstellung verwendet werden soll. Vergleichen Sie die Standorte der Datencenter für den Clouddienst und Ihr Speicherkonto. Im Idealfall sollten diese Standorte identisch sein.

    >[AZURE.NOTE]Das Azure-Speicherkonto speichert das Paket für die Bereitstellung der Anwendung. Nach der Bereitstellung der Anwendung wird das Paket aus dem Speicherkonto entfernt.

1. Aktivieren Sie das Kontrollkästchen **Bereitstellungsaktualisierung**, wenn Sie nur aktualisierte Komponenten bereitstellen möchten. Diese Art der Bereitstellung kann schneller als eine vollständige Bereitstellung erfolgen. Wählen Sie den Link **Einstellungen**, um das Dialogfeld **Bereitstellungsaktualisierungs-Einstellungen** zu öffnen, das in der folgenden Abbildung gezeigt wird.

    ![Bereitstellungseinstellungen](./media/vs-azure-tools-publish-azure-application-wizard/IC617060.png)

    Sie können eine der beiden Optionen für die Bereitstellungsaktualisierung auswählen: inkrementell oder gleichzeitig. Bei der inkrementellen Bereitstellung wird jeweils eine bereitgestellte Instanz aktualisiert, damit die Anwendung online und für Benutzer verfügbar bleibt. Bei der gleichzeitigen Bereitstellung werden alle bereitgestellten Instanzen gleichzeitig aktualisiert. Eine gleichzeitige Aktualisierung nimmt weniger Zeit in Anspruch als das inkrementelle Update. Allerdings steht bei Auswahl dieser Option die Anwendung während des Aktualisierungsvorgangs möglicherweise nicht zur Verfügung.

    Sie sollten das Kontrollkästchen "Falls die Bereitstellung nicht aktualisiert werden kann, vollständige Bereitstellung durchführen" aktivieren, wenn bei einem Fehler der Bereitstellungsaktualisierung die vollständige Bereitstellung automatisch erfolgen soll. Bei einer vollständigen Bereitstellung wird die virtuelle IP-Adresse (VIP) für den Clouddienst zurückgesetzt. Weitere Informationen finden Sie unter [Vorgehensweise: Beibehalten einer konstanten virtuellen IP-Adresse für einen Clouddienst](https://msdn.microsoft.com/library/azure/jj614593.aspx).


1. Zum Debuggen des Diensts aktivieren Sie das Kontrollkästchen **IntelliTrace aktivieren**. Wenn Sie eine **Debugkonfiguration** bereitstellen und Ihren Clouddienst in Azure debuggen möchten, aktivieren Sie das Kontrollkästchen **Remotedebugger für alle Rollen aktivieren**, um die Remotedebuggingdienste bereitzustellen.

2. Aktivieren Sie das Kontrollkästchen **Profilerstellung aktivieren**, und wählen Sie dann den Link **Einstellungen**, um die Profilerstellungsoptionen anzuzeigen und für diese Anwendung ein Profil zu erstellen.


    >[AZURE.NOTE]Sie müssen Visual Studio Ultimate verwenden, um entweder IntelliTrace oder die Profilerstellung für die Ebeneninteraktion zu aktivieren. Beide können nicht gleichzeitig aktiviert werden.

    Weitere Informationen finden Sie unter [Debuggen eines veröffentlichten Clouddiensts mit IntelliTrace](https://msdn.microsoft.com/library/azure/ff683671.aspx) und [Visual Studio und Testen der Leistung eines Clouddiensts](https://msdn.microsoft.com/library/azure/hh369930.aspx).

1. Wählen Sie **Weiter**, um die Zusammenfassungsseite für die Anwendung anzuzeigen.

## Veröffentlichen der Anwendung

1. Sie können auch anhand der Einstellungen, die Sie gewählt haben, ein Veröffentlichungsprofil erstellen. Sie können beispielsweise ein Profil für eine Testumgebung und ein weiteres für die Produktion verwenden. Um dieses Profil zu speichern, klicken Sie auf das Symbol **Speichern**. Der Assistent erstellt das Profil und speichert es im Visual Studio-Projekt. Um den Namen des Profils zu ändern, öffnen Sie die Liste **Zielprofil**, und wählen Sie anschließend **<Verwalten…>** aus.

    ![Zusammenfassungsbildschirm des Veröffentlichungs-Assistenten](./media/vs-azure-tools-publish-azure-application-wizard/IC749015.png)

    >[AZURE.NOTE]Das Veröffentlichungsprofil wird im Projektmappen-Explorer in Visual Studio angezeigt. Die Profileinstellungen werden in eine Datei mit der Erweiterung ".azurePubxml" geschrieben. Einstellungen werden als Attribute der XML-Tags gespeichert.

1. Wählen Sie **Veröffentlichen**, um Ihre Anwendung zu veröffentlichen. Sie können den Prozessstatus im Fenster **Ausgabe** in Visual Studio überwachen.

## Siehe auch

[Einrichten von Diensten, die zum Veröffentlichen eines Clouddiensts aus Visual Studio erforderlich sind](https://msdn.microsoft.com/library/azure/ff683668.aspx)

[Vorgehensweise: Migrieren und Veröffentlichen einer Webanwendung in einem Azure-Clouddienst aus Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx)

[Veröffentlichen eines Clouddiensts mit den Azure Tools](https://msdn.microsoft.com/library/azure/ff683672.aspx)

[Debuggen eines veröffentlichten Clouddiensts mit IntelliTrace und Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx)

[Lokales Testen der Leistung eines Cloud-Diensts](https://msdn.microsoft.com/library/azure/hh369930.aspx)

<!---HONumber=August15_HO9-->