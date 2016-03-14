<properties
    pageTitle="Aktivieren des Remotezugriffs für Azure-Bereitstellungen in Eclipse"
    description="Erfahren Sie mehr über das Aktivieren des Remotezugriffs auf Azure-Bereitstellungen mit dem Azure-Toolkit für Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="02/26/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# Aktivieren des Remotezugriffs für Azure-Bereitstellungen in Eclipse #

Zur Problembehandlung bei Bereitstellungen können Sie den Remotezugriff aktivieren und verwenden, um die Verbindung zu dem virtuellen Computer herzustellen, der als Host für die Bereitstellung dient. Die Funktion "Remotezugriff" verwendet RDP (Remote Desktop Protocol). Sie können den Remotezugriff für Ihre Bereitstellung konfigurieren, nachdem Sie sie in Azure veröffentlicht haben. Wenn Sie Eclipse in Verbindung mit einem Windows-Betriebssystem verwenden, können Sie den Remotezugriff auch vor der Veröffentlichung in Azure konfigurieren. Beachten Sie, dass Sie einen Remotedesktopclient benötigen, der mit Ihrem Betriebssystem kompatibel ist, um die Verbindung zum virtuellen Computer Ihrer Bereitstellung in Azure herstellen zu können.

## Aktivieren des Remotezugriffs vor der Bereitstellung in Azure ##

>[AZURE.NOTE] Zum Aktivieren des Remotezugriffs vor der Bereitstellung der Anwendung in Azure müssen Sie Eclipse unter Windows ausführen.

Die folgende Abbildung zeigt das Eigenschaftendialogfeld **Remote Access**, in dem der Remotezugriff aktiviert wird:

![][ic719494]

Es gibt zwei Methoden zum Anzeigen es Eigenschaftendialogfelds **Remote Access**:

* Klicken Sie im Dialogfeld **Publish to Azure** im Abschnitt **Remote Access** auf den Link **Advanced**.
* Öffnen Sie das Dialogfeld **Properties** Ihres Azure-Projekts.

Wenn Sie ein neues Azure-Bereitstellungsprojekt erstellen, ist der Remotezugriff standardmäßig nicht aktiviert. Sie können den Remotezugriff jedoch ganz einfach aktivieren, indem Sie den Benutzernamen und das Kennwort im Dialogfeld **Publish to Azure** angeben. Das Kennwort für den Remotezugriff wird mit X.509-Zertifikaten verschlüsselt. Wenn Sie nicht selbst ein Zertifikat angeben, verwendet die Verschlüsselung ein selbstsigniertes Zertifikat, das zum Lieferumfang des Azure-Plug-Ins für Eclipse gehört. Dieses selbstsignierte Zertifikat befindet sich im Ordner **cert** Ihres Azure-Projekts und ist als öffentliche Zertifikatdatei ("SampleRemoteAccessPublic.cer") und als Zertifikatdatei für den privaten Informationsaustausch (Personal Information Exchange, PFX) ("SampleRemoteAccessPrivate.pfx") gespeichert. Letztere enthält den privaten Schlüssel für das Zertifikat, und das Standardkennwort lautet **Password1**. Da dieses Kennwort jedoch allgemein bekannt ist, sollte das Standardzertifikat nur für Schulungszwecke und nicht in einer Produktionsumgebung verwendet werden. Wenn Sie also Remotesitzungen für andere als Schulungszwecke für Ihre Bereitstellungen aktivieren möchten, sollten Sie im Dialogfeld **Publish to Azure** auf den Link **Advanced** klicken, um ein eigenes Zertifikat anzugeben. Beachten Sie, dass Sie die PFX-Version des Zertifikats auf Ihren im Azure-Verwaltungsportal gehosteten Dienst hochladen müssen, damit Azure das Benutzerkennwort entschlüsseln kann.

Im Rest des Lernprogramms wird gezeigt, wie Sie den Remotezugriff auf ein Azure-Bereitstellungsprojekt aktivieren, das ursprünglich mit deaktiviertem Remotezugriff erstellt wurde. Zum Zwecke dieser Schulung erstellen wir ein neues selbstsigniertes Zertifikat mit einer PFX-Datei und einem Kennwort Ihrer Wahl. Sie haben auch die Möglichkeit, ein Zertifikat von einer Zertifizierungsstelle zu verwenden.

## Aktivieren des Remotezugriffs nach der Bereitstellung in Azure ##

Zum Aktivieren des Remotezugriffs nach der Bereitstellung in Azure gehen Sie folgendermaßen vor:

1. Melden Sie sich mit Ihrem Konto beim Azure-Verwaltungsportal an.
1. Wählen Sie in der Liste **Cloud Services** den von Ihnen bereitgestellten Clouddienst aus.
1. Klicken Sie auf der Seite "Cloud Services" auf den Link **Configure**.
1. Klicken Sie unten auf der Konfigurationsseite auf den Link **Remote**.
1. Wenn das Popupdialogfeld angezeigt wird:
    1. Geben Sie die Rolle an, für die Sie den Remotezugriff aktivieren möchten.
    1. Aktivieren Sie das Kontrollkästchen **Enable Remote Desktop**.
    1. Geben Sie den Benutzernamen mit Kennwort an, für den Sie den Remotezugriff aktivieren möchten.
    1. Wählen Sie das Zertifikat aus.
1. Klicken Sie auf **OK**. 

Es wird eine Meldung angezeigt, die besagt, dass Ihre Konfigurationsänderung verarbeitet wird, was einige Minuten in Anspruch nehmen kann. Folgen Sie nach Abschluss der Konfigurationsänderung den Schritten im Abschnitt **Remote anmelden** weiter unten in diesem Artikel.
	
## Aktivieren des Remotezugriffs in Ihrem Paket ##

* Klicken Sie im Projekt-Explorer in Eclipse mit der rechten Maustaste auf Ihr Azure-Projekt und anschließend auf **Properties**.
* Erweitern Sie im Dialogfeld **Properties** im linken Bereich **Azure**, und klicken Sie dann auf **Remote Access**.
* Vergewissern Sie sich im Dialogfeld **Remote Access **, dass **Enable all roles to accept Remote Desktop Connections with these login credentials** aktiviert ist.
* Geben Sie einen Benutzernamen für die Remotedesktopverbindung an.
* Geben Sie das Kennwort für das Benutzerkonto an, und bestätigen Sie es. Die in diesem Dialogfeld festgelegten Werte für Benutzernamen und Kennwort werden verwendet, wenn Sie eine Remotedesktopverbindung herstellen. (Beachten Sie, dass dieses Kennwort nicht Ihrem PFX-Kennwort entspricht.)
* Geben Sie das Ablaufdatum für das Benutzerkonto ein.
* Klicken Sie auf **New**, um ein neues selbstsigniertes Zertifikat zu erstellen. (Alternativ können Sie mit den Schaltflächen **Workspace** oder **FileSystem** ein Zertifikat aus Ihrem Arbeitsbereich oder Dateisystem auswählen. Zum Zwecke dieses Lernprogramms erstellen wir jedoch ein neues Zertifikat.
* Geben Sie im Dialogfeld **New Certificate** das Kennwort ein, das Sie für Ihre PFX-Datei verwenden, und bestätigen Sie es.
* Übernehmen Sie den für **Name (CN)** bereitgestellten Wert, oder geben Sie einen benutzerdefinierten Wert ein.
* Geben Sie den Pfad und den Dateinamen ein, unter dem das neue Zertifikat in CER-Form gespeichert werden soll. Für diesen und den nächsten Schritt können Sie den Ordner **cert** des Azure-Projekts verwenden, Sie können aber auch einen anderen Speicherort auswählen. Zum Zwecke dieses Lernprogramms verwenden wir **c:\\mycert\\mycert.cer**. (Erstellen Sie den Ordner **c:\\mycert**, bevor Sie fortfahren, oder verwenden Sie auf Wunsch einen vorhandenen Ordner.)
* Geben Sie den Pfad und den Dateinamen ein, unter dem das neue Zertifikat und der zugehörige private Schlüssel in PFX-Form gespeichert werden soll. Zum Zwecke dieses Lernprogramms verwenden wir c:**\\mycert\\mycert.pfx**. Das Dialogfeld **New Certificate** sollte nun ähnlich dem folgenden aussehen (aktualisieren Sie die Ordnerpfade, wenn Sie nicht **c:\\mycert** verwendet haben): ![][ic712275]
* Klicken Sie auf **OK**, um das Dialogfeld **New Certificate** zu schließen.
* Das Dialogfeld **Remote Access** sollte nun etwa wie folgt aussehen: </p> ![][ic719495]
* Klicken Sie auf **OK**, um das Dialogfeld **Remote Access** zu schließen.
	
Erstellen Sie Ihre Anwendung mit dem Buildset für die Bereitstellung in der Cloud neu.

## Remote anmelden ##

Nachdem Ihre Rolleninstanz bereit ist, können Sie sich remote bei dem virtuellen Computer anmelden, der als Host für Ihre Anwendung dient.

* Wenn Sie Eclipse unter Windows verwenden und bei der Bereitstellung in Azure die Option **Start remote desktop on deploy** ausgewählt haben, wird ein Anmeldebildschirm für die Remotedesktopverbindung angezeigt, wenn die Bereitstellung beginnt. Wenn Sie aufgefordert werden, Benutzernamen und Kennwort einzugeben, geben Sie die Werte ein, die Sie für den Remotebenutzer eingegeben haben, damit Sie sich anmelden können.
* Eine weitere Möglichkeit zur Remoteanmeldung besteht über das <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Azure-Verwaltungsportal</a>:
    * Klicken Sie in der Ansicht **Cloud Services** des Azure-Verwaltungsportals auf Ihren Clouddienst, klicken Sie auf **Instances**, klicken Sie auf eine bestimmte Instanz, und klicken Sie dann auf die Schaltfläche **Connect**. Die Schaltfläche **Connect** wird wie folgt in der Befehlsleiste angezeigt: ![][ic659273]
    >[AZURE.NOTE] Wenn Sie sich in einem anderen Betriebssystem als Windows befinden, müssen Sie einen Remotedesktopclient verwenden, der mit Ihre Betriebssystem kompatibel ist, und den Schritten zum Konfigurieren dieses Clients mit dem Einstellungen in der von Ihnen heruntergeladenen RDP-Datei folgen.
    * Nachdem Sie auf die Schaltfläche **Connect** geklickt haben, werden Sie aufgefordert, eine RDP-Datei zu öffnen. Öffnen Sie die Datei, und folgen Sie den Anweisungen. (Sie können diese Datei auch auf dem lokalen Computer speichern und ausführen, indem Sie bei der Remoteanmeldung bei ihrem virtuellen Computer darauf doppelklicken, ohne dass Sie zuvor zum Verwaltungsportal wechseln.)
    * Wenn Sie aufgefordert werden, Benutzernamen und Kennwort einzugeben, geben Sie die Werte ein, die Sie für den Remotebenutzer eingegeben haben, damit Sie sich anmelden können.

## Weitere Informationen ##

[Azure-Toolkit für Eclipse][]

[Creating a Hello World Application for Azure in Eclipse (in englischer Sprache)][]

[Installieren des Azure-Toolkits für Eclipse][]

Weitere Informationen zum Verwenden von Azure mit Java finden Sie im [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure-Toolkit für Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse (in englischer Sprache)]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installieren des Azure-Toolkits für Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png

<!---HONumber=AcomDC_0302_2016-->