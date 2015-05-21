<properties
   pageTitle="Mit RemoteApp jede Anwendung auf jedem Gerät ausführen"
   description="Erfahren Sie, wie Sie mithilfe von RemoteApp jede Anwendung für Benutzer freigeben können."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="04/14/2015"
   ms.author="elizapo"/>

# Mit RemoteApp jede Anwendung auf jedem Gerät ausführen

In diesem Lernprogramm erfahren Sie, wie eine Windows-Anwendung überall auf jedem Gerät ausgeführt werden kann – und zwar genau jetzt. Egal, ob Internet Explorer 6, eine vor zehn Jahren geschriebene benutzerdefinierte Anwendung oder eine Office-App – Ihre Benutzer sind für diese Anwendungen nicht länger an ein bestimmtes Betriebssystem \(wie Windows XP\) gebunden.

Mithilfe von RemoteApp können Ihre Benutzer auch ihre eigenen Android- oder Apple-Geräte verwenden und die gleiche Nutzererfahrung wie mit Windows \(oder Windows Phones\) machen. Dies geschieht durch Hosten der Windows-Anwendung in einer Sammlung virtueller Windows-Computer in Azure, auf die Benutzer überall zugreifen können, wo eine Internetverbindung besteht. Lassen Sie uns anfangen.

In diesem Lernprogramm teilen wir Access mit allen Benutzern. Da Access eine Datenbank ist und die Datenbank hilfreich sein soll, unternehmen wir einige zusätzliche Schritte, um Benutzern den Zugriff auf die Access-Datenfreigabe zu ermöglichen.

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]


## Erstellen einer RemoteApp-Sammlung

Zunächst erstellen Sie eine Sammlung. Die Sammlung fungiert als Container für Ihre Anwendungen und Ihre Benutzer. Jede Sammlung basiert auf einem Image – Sie können Ihr eigenes erstellen oder eines verwenden, das in Ihrem Abonnement zur Verfügung gestellt wird. Für dieses Lernprogramm verwenden wir das Office 2013-Testimage. Es enthält die Anwendung, die wir gemeinsam nutzen möchten.

1. Scrollen Sie im Azure-Verwaltungsportal im linken Navigationsbereich nach unten, bis Azure RemoteApp angezeigt wird. Öffnen Sie die Seite.
2. Klicken Sie auf **Erstellen einer RemoteApp-Sammlung**.
3. Klicken Sie auf **Schnellerfassung** und geben Sie einen Namen für die Sammlung ein.
4. Wählen Sie die Region, die Sie verwenden möchten, um Ihre Sammlung zu erstellen. Wählen Sie für optimale Ergebnisse die Region, die dem Standort geografisch am nächsten ist, an dem die Benutzer auf die Anwendung zugreifen. In diesem Lernprogramm befinden sich die Benutzer beispielsweise in Redmond, Washington. Die nächstgelegene Azure-Region ist **West US**.
5. Wählen Sie den Abrechnungsplan aus, den Sie verwenden möchten. Der grundlegende Abrechnungsplan sieht 16 Benutzer auf einer großen Azure-VM voraus, während der standardmäßige Abrechnungsplan 10 Benutzer auf eine große Azure-VM vorsieht. Als allgemeines Beispiel funktioniert der Standardplan gut bei einem Dateneintrags-Workflow. Bei einer Produktivitätsanwendung wie Office sollten Sie den Standardplan nehmen. 
6. Wählen Sie abschließend das Office 2013 Professional-Image. Dieses Image enthält Office 2013-Anwendungen.  
7. Klicken Sie nun auf **RemoteApp-Sammlung erstellen**.

![Erstellen einer Cloudsammlung über RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Das Erstellen Ihrer Sammlung wird gestartet. Dies kann bis zu einer Stunde dauern.

Sie nun können Ihre Benutzer hinzufügen.

## Die Anwendung für Benutzer freigeben

Nachdem Ihre Sammlung erfolgreich erstellt wurde, ist es Zeit, Access für Benutzer zu veröffentlichen und die Benutzer hinzuzufügen, die Zugriff haben sollen.

Wenn Sie während der Sammlungserstellung vom Azure-RemoteApp-Knoten weg navigiert sind, finden Sie den Weg dorthin von der Azure-Homepage wieder zurück.

1. Klicken Sie auf **RemoteApp** im linken Navigationsbereich.
2. Klicken Sie auf die zuvor erstellte Sammlung, um zusätzliche Optionen zu erhalten, und konfigurieren Sie die Sammlung. ![Eine neue RemoteApp-Cloudsammlung](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. Auf der Registerkarte **Veröffentlichen** klicken Sie auf **Veröffentlichen** unten auf dem Bildschirm, und klicken Sie dann auf **Startmenüprogramme veröffentlichen**. ![Veröffentlichen Sie ein RemoteApp-Programm](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Wählen Sie die Anwendungen aus, die Sie aus der Liste veröffentlichen möchten. Für unsere Zwecke haben wir Access ausgewählt. Klicken Sie auf **Fertig stellen**. Warten Sie, bis die Veröffentlichung der Anwendungen abgeschlossen ist. ![Access in RemoteApp veröffentlichen](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Gehen Sie nach der Veröffentlichung der Anwendung weiter zur Registerkarte **Benutzerzugriff**, um alle Benutzer hinzuzufügen, die Zugriff auf Ihre Anwendungen benötigen. Geben Sie Benutzernamen \(E-Mail-Adresse\) für Ihre Benutzer ein und klicken Sie dann auf **Speichern**. ![Hinzufügen von Benutzern in RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. Jetzt können Sie Ihren Benutzern mitteilen, wie sie auf die neuen Anwendungen zugreifen können. Dazu senden Sie Ihren Benutzern eine E-Mail, die auf die Download-URL für den Remotedesktop-Client verweist. ![Die Download-URL des Clients für RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## Konfigurieren Sie den Zugriff auf Access

Einige Anwendungen benötigen eine zusätzliche Konfiguration, nachdem Sie sie über RemoteApp bereitgestellt haben. Wir werden speziell für Access eine Dateifreigabe auf Azure erstellen, auf die jeder Benutzer zugreifen kann. \(Wenn Sie dies nicht tun möchten, können Sie eine [Hybrid-Sammlung](remoteapp-create-hybrid-deployment.md) [ anstelle unserer Cloud-Sammlung] erstellen, mit der die Benutzer Zugriff auf Dateien und Informationen in Ihrem lokalen Netzwerk erhalten.\) Anschließend sollen die Benutzer dem Azure-Dateisystem ein lokales Laufwerk auf ihrem Computer zuordnen.

Den ersten Teil führen Sie als Administrator aus. Dann müssen Ihre Benutzer einige Schritte durchführen.

1. Legen Sie los, indem Sie die Befehlszeilenschnittstelle \(cmd.exe\) veröffentlichen. Wählen Sie in der Registerkarte **Veröffentlichen** **Cmd** aus und klicken Sie dann auf **Veröffentlichen \> Programm mit Pfad veröffentlichen**.
2. Geben Sie den Namen der Anwendung und den Pfad ein. Verwenden Sie zu diesem Zweck "File Explorer" als Name und "% SYSTEMDRIVE%\\windows\\explorer.exe" als Pfad. ![Veröffentlichen Sie die Datei cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. Nun müssen Sie ein Azure-[Speicherkonto](storage-create-storage-account.md) erstellen. Wir haben unseres "accessstorage" genannt. Wählen Sie einen Namen, der für Sie von Bedeutung ist \(es kann nur ein "accessstorage" geben\). ![Unser Azure-Speicherkonto.](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Kehren Sie zurück zu Ihrem Dashboard, sodass Sie den Pfad zu Ihrem Speicherort \(Endpunkt\) abrufen können. Da Sie diesen gleich benötigen, kopieren Sie ihn irgendwo hin. ![Der Speicherkontopfad](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. Nachdem das Speicherkonto erstellt wurde, benötigen Sie als Nächstes den primären Zugriffsschlüssel. Klicken Sie auf **Zugriffstasten verwalten** und kopieren Sie dann den primären Zugriffsschlüssel.
6. Nun legen Sie den Kontext des Speicherkontos fest und erstellen eine neue Dateifreigabe für Access. Führen Sie die folgenden Cmdlets in einem Windows PowerShell-Fenster mit erhöhten Rechten aus:
   
        $ctx=New-AzureStorageContext <account name> <account key>
    	$s = New-AzureStorageShare <share name> -Context $ctx

	Für uns sind dies die Cmdlets, die wir ausführen:
    
	    $ctx=New-AzureStorageContext accessstorage <key>
    	$s = New-AzureStorageShare <share name> -Context $ctx


Jetzt ist der Benutzer an der Reihe. Erstens müssen Ihre Benutzer einen [RemoteApp-Client](remoteapp-clients.md) installieren. Als Nächstes müssen die Benutzer ein Laufwerk von ihrem Konto zu dieser von Ihnen erstellten Azure-Dateifreigabe zuordnen und ihre Access-Dateien hinzufügen. So wird es gemacht:

1. Greifen Sie im RemoteApp-Client auf die veröffentlichten Anwendungen zu. Starten Sie das cmd.exe-Programm.
2. Führen Sie den folgenden Befehl zum Zuordnen eines Laufwerks von Ihrem Computer zu der Dateifreigabe aus: net use z: \\<accountname>.file.core.windows.net\<share name\> /u:<user name> <account key>
1. Starten Sie jetzt die Datei-Explorer-Anwendung von RemoteApp. Kopieren Sie alle Access-Dateien, die Sie in der freigegebenen Anwendung für die Dateifreigabe verwenden möchten. ![Access-Dateien in eine Azure-Freigabe einstellen](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Öffnen Sie zum Schluss Access, und öffnen Sie dann die Datenbank, die Sie soeben freigegeben haben. Ihre ausgeführten Access-Daten sollten nun in der Cloud angezeigt werden. ![Eine echte Access-Datenbank, die in der Cloud ausgeführt wird](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Sie können Access jetzt auf jedem Ihrer Geräte benutzen - Sie müssen nur sicherstellen, dass Sie einen RemoteApp-Client installiert haben.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Nun, da Sie eine Sammlung erstellen können, versuchen Sie das Erstellen einer [Sammlung, die Office 365 verwendet](remoteapp-tutorial-o365anywhere.md). Oder Sie erstellen eine [Hybrid-Sammlung](remoteapp-create-hybrid-deployment.md), die auf Ihr lokales Netzwerk zugreifen kann.

<!--Image references-->

<!--HONumber=52-->
