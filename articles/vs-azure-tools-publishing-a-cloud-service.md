<properties
   pageTitle="Veröffentlichen eines Clouddiensts mit den Azure Tools | Microsoft Azure"
   description="Sie erhalten Informationen dazu, wie Sie Azure-Clouddienstprojekte mit Visual Studio veröffentlichen."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/04/2015"
   ms.author="kempb" />

# Veröffentlichen eines Clouddiensts mit den Azure Tools

Mit den Azure-Tools für Microsoft Visual Studio können Sie Ihre Azure-Anwendung direkt aus Visual Studio veröffentlichen. Visual Studio unterstützt die integrierte Veröffentlichung für die Staging- oder Produktionsumgebung eines Clouddiensts.

Um eine Azure-Anwendung veröffentlichen zu können, müssen Sie über ein Azure-Abonnement verfügen. Sie müssen auch einen Clouddienst und ein Speicherkonto für die Nutzung durch die Anwendung einrichten. Dies können Sie im [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=213885) festlegen.

>[AZURE.IMPORTANT]Beim Veröffentlichen können Sie die Bereitstellungsumgebung für Ihren Clouddienst auswählen. Außerdem müssen Sie ein Speicherkonto auswählen, das zum Speichern des Anwendungspakets für die Bereitstellung verwendet wird. Nach der Bereitstellung wird das Anwendungspaket aus dem Speicherkonto entfernt. Weitere Informationen zum Einrichten dieser Dienste finden Sie unter [Einrichten von Diensten, die zum Veröffentlichen eines Clouddiensts mit Visual Studio erforderlich sind](vs-azure-tools-setting-up-services-required-to-publish-a-cloud-service-from-visual-studio.md).

Wenn Sie eine Azure-Anwendung entwickeln und testen, können Sie Web Deploy verwenden, um Änderungen inkrementell für Ihre Webrollen zu veröffentlichen. Nachdem Sie Ihre Anwendung in einer Bereitstellungsumgebung veröffentlicht haben, können Sie mit Web Deploy Änderungen direkt auf dem virtuellen Computer bereitstellen, auf dem die Webrolle ausgeführt wird. Sie müssen nicht jedes Mal die gesamte Azure-Anwendung verpacken und veröffentlichen, wenn Sie die Webrolle aktualisieren und die Änderungen testen möchten. Mit diesem Ansatz können Sie Ihre Webrollenänderungen in der Cloud zu Testzwecken vorhalten, ohne darauf warten zu müssen, dass Ihre Anwendung in einer Bereitstellungsumgebung veröffentlicht wird.

Verwenden Sie die folgenden Verfahren zum Veröffentlichen der Azure-Anwendung und zum Aktualisieren einer Webrolle mit Web Deploy:

- Veröffentlichen oder Verpacken einer Azure-Anwendung aus Visual Studio

- Aktualisieren einer Webrolle als Teil des Entwicklungs- und Testzyklus

## Veröffentlichen und Verpacken einer Azure-Anwendung in Visual Studio

Wenn Sie Ihre Azure-Anwendung veröffentlichen, können Sie eine der folgenden Aufgaben ausführen:

- Erstellen eines Dienstpakets: Sie können dieses Paket und die Dienstkonfigurationsdatei verwenden, um Ihre Anwendung über das [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=213885) in einer Bereitstellungsumgebung zu veröffentlichen.

- Veröffentlichen Ihres Azure-Projekts in Visual Studio: Verwenden Sie den Veröffentlichungs-Assistenten, um die Anwendung direkt in Azure zu veröffentlichen. Informationen hierzu finden Sie unter [Assistent zur Veröffentlichung einer Azure-Anwendung](vs-azure-tools-publish-azure-application-wizard.md).

### So erstellen Sie ein Dienstpaket in Visual Studio

1. Wenn Sie bereit zum Veröffentlichen der Anwendung sind, öffnen Sie den Projektmappen-Explorer und das Kontextmenü für das Azure-Projekt, in dem die Rollen enthalten sind, und wählen Sie dann die Option „Veröffentlichen“.

1. Führen Sie die folgenden Schritte aus, um nur ein Dienstpaket zu erstellen:

  1. Öffnen Sie das Kontextmenü für das Azure-Projekt, und wählen Sie **Verpacken**.

  1. Wählen Sie im Dialogfeld **Azure-Anwendung verpacken** die Dienstkonfiguration, für die Sie ein Paket erstellen möchten, und wählen Sie anschließend die Buildkonfiguration.

  1. (Optional) Um den Remotedesktop für den Clouddienst nach dem Veröffentlichen zu aktivieren, aktivieren Sie das Kontrollkästchen **Remotedesktop für alle Rollen aktivieren** und klicken dann auf **Einstellungen**, um den Remotedesktop zu konfigurieren. Wenn Sie den Clouddienst nach dem Veröffentlichen debuggen möchten, aktivieren Sie das Remotedebuggen, indem Sie die Option **Remotedebugger für alle Rollen aktivieren** aktivieren.

      Weitere Informationen finden Sie unter [Verwenden von Remotedesktop mit Azure-Rollen](vs-azure-tools-using-remote-desktop-with-azure-roles.md).

  1. Wählen Sie den Link **Verpacken**, um das Paket zu erstellen.

      Im Datei-Explorer wird der Dateispeicherort des neu erstellten Pakets angezeigt. Sie können diesen Speicherort kopieren, damit Sie ihn im Azure-Verwaltungsportal verwenden können.

  1. Um dieses Paket in einer Bereitstellungsumgebung zu veröffentlichen, müssen Sie diesen Speicherort als Paketspeicherort verwenden, wenn Sie mit dem [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=213885)einen Clouddienst erstellen und dieses Paket in einer Umgebung bereitstellen.

1. (Optional) Um den Bereitstellungsprozess abzubrechen, wählen Sie im Kontextmenü für die Position im Aktivitätsprotokoll die Option **Abbrechen und entfernen**. Der Bereitstellungsprozess wird beendet, und die Bereitstellungsumgebung wird aus Azure gelöscht.

    >[AZURE.NOTE]Sie müssen Sie das Azure-Verwaltungsportal verwenden, um diese Umgebung nach der Bereitstellung zu entfernen.

1. (Optional) Nach dem Starten Ihrer Rolleninstanzen wird die Bereitstellungsumgebung in Visual Studio im Server-Explorer automatisch unter dem Knoten **Cloud Services** angezeigt. Hier können Sie den Status der einzelnen Rolleninstanzen anzeigen. Weitere Informationen finden Sie unter [Verwalten von Azure-Ressourcen mit dem Cloud-Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). Die folgende Abbildung zeigt die Rolleninstanzen, während sie sich noch im Zustand der Initialisierung befinden:

    ![VST\_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## Aktualisieren einer Webrolle als Teil des Entwicklungs- und Testzyklus

Wenn die Back-End-Infrastruktur Ihrer App stabil ist, aber die Webrollen eine häufigere Aktualisierung erfordern, können Sie Web Deploy nutzen, um nur eine Webrolle im Projekt zu aktualisieren. Dies ist hilfreich, wenn Sie die Back-End-Workerrollen nicht neu erstellen und bereitstellen möchten oder wenn Sie über mehrere Webrollen verfügen und nur eine der Webrollen aktualisieren möchten.

### Anforderungen

Dies sind die Anforderungen zum Verwenden von Web Deploy für die Aktualisierung Ihrer Webrolle:

- **Nur für Entwicklungs- und Testzwecke:** Die Änderungen werden direkt auf dem virtuellen Computer vorgenommen, auf dem die Webrolle ausgeführt wird. Wenn dieser virtuelle Computer recycelt werden muss, gehen die Änderungen verloren, da das von Ihnen veröffentlichte Originalpaket zum Neuerstellen des virtuellen Computers für die Rolle verwendet wird. Sie müssen die Anwendung erneut veröffentlichen, um die aktuellen Änderungen für die Webrolle zu erhalten.

- **Nur Webrollen können aktualisiert werden:** Workerrollen können nicht aktualisiert werden. Außerdem ist es nicht möglich, das RoleEntryPoint-Element in „web role.cs“ zu aktualisieren.

- **Unterstützt nur eine einzelne Instanz einer Webrolle:** Sie können in Ihrer Bereitstellungsumgebung nicht mehrere Instanzen einer Webrolle nutzen. Mehrere Webrollen jeweils mit einer Instanz werden aber unterstützt.

- **Sie müssen Remotedesktopverbindungen aktivieren:** Dies ist erforderlich, damit für Web Deploy der Benutzer und das Kennwort verwendet werden können, um eine Verbindung mit dem virtuellen Computer herzustellen. Über diese Verbindung werden die Änderungen auf dem Server bereitgestellt, auf dem Internetinformationsdienste (IIS) ausgeführt wird. Außerdem kann es erforderlich sein, eine Verbindung mit dem virtuellen Computer herzustellen, um auf diesem virtuellen Computer ein vertrauenswürdiges Zertifikat für IIS hinzuzufügen. (Dadurch wird sichergestellt, dass die von Web Deploy verwendete Remoteverbindung für IIS sicher ist.)

Beim folgenden Verfahren wird davon ausgegangen, dass Sie den Assistenten **Azure-Anwendung veröffentlichen** verwenden.

### So aktivieren Sie Web Deploy beim Veröffentlichen der Anwendung

1. Sie müssen zuerst Remotedesktopverbindungen konfigurieren, um das Kontrollkästchen **Web Deploy für alle Webrollen aktivieren** aktivieren zu können. Wählen Sie für alle Rollen die Option **Remotedesktop aktivieren**, und geben Sie dann die Anmeldeinformationen an, die zum Herstellen der Remoteverbindung im angezeigten Feld **Remotedesktopkonfiguration** verwendet werden. Weitere Informationen finden Sie unter [Verwenden von Remotedesktop mit Azure-Rollen](vs-azure-tools-remote-desktop-roles.md).

1. Wählen Sie **Web Deploy für alle Webrollen aktivieren**, um Web Deploy für alle Webrollen in Ihrer Anwendung zu aktivieren.

    Als Warnung wird ein gelbes Dreieck angezeigt. Für Web Deploy wird standardmäßig ein nicht vertrauenswürdiges, selbstsigniertes Zertifikat verwendet. Dies wird nicht zum Hochladen von vertraulichen Daten empfohlen. Wenn Sie diesen Prozess für vertrauliche Daten schützen möchten, können Sie ein SSL-Zertifikat hinzufügen, das für Web Deploy-Verbindungen verwendet wird. Dieses Zertifikat muss ein vertrauenswürdiges Zertifikat sein. Informationen zur Vorgehensweise finden Sie im Abschnitt **So machen Sie Web Deploy sicher** weiter unten in diesem Thema.

1. Wählen Sie **Weiter**, um den Bildschirm **Zusammenfassung** anzuzeigen, und dann **Veröffentlichen**, um den Clouddienst bereitzustellen.

    Der Clouddienst wird veröffentlicht. Für den virtuellen Computer, der erstellt wird, sind Remoteverbindungen für IIS aktiviert, damit Web Deploy zum Aktualisieren Ihrer Webrollen verwendet werden kann, ohne dass sie erneut veröffentlicht werden müssen.

    >[AZURE.NOTE]Wenn Sie für eine Webrolle mehr als eine Instanz konfiguriert haben, wird eine Warnmeldung mit folgenden Informationen angezeigt: Jede Webrolle ist im Paket, das zum Veröffentlichen der Anwendung erstellt wird, auf nur eine Instanz beschränkt. Klicken Sie auf OK, um fortzufahren. Wie im Abschnitt „Anforderungen“ beschrieben, können Sie mehr als eine Webrolle verwenden, aber nur eine Instanz jeder Rolle.

### So aktualisieren Sie Ihre Webrolle mit Web Deploy

1. Nehmen Sie zum Verwenden von Web Deploy am Projekt Codeänderungen für alle Webrollen in Visual Studio vor, die Sie veröffentlichen möchten, und klicken Sie in der Projektmappe dann mit der rechten Maustaste auf diesen Projektknoten, und zeigen Sie auf **Veröffentlichen**. Das Dialogfeld **Website veröffentlichen** wird angezeigt.

1. (Optional) Wenn Sie ein vertrauenswürdiges SSL-Zertifikat zur Verwendung durch Remoteverbindungen für IIS hinzugefügt haben, können Sie das Kontrollkästchen **Nicht vertrauenswürdiges Zertifikat zulassen** deaktivieren. Informationen zur Vorgehensweise beim Hinzufügen eines Zertifikats, um Web Deploy sicher zu machen, finden Sie im Abschnitt **So machen Sie Web Deploy sicher** weiter unten in diesem Thema.

1. Zum Verwenden von Web Deploy werden für den Veröffentlichungsmechanismus der Benutzername und das Kennwort benötigt, den bzw. das Sie beim ersten Veröffentlichen des Pakets für die Remotedesktopverbindung einrichten.

  1. Geben Sie unter **Benutzername** den Benutzernamen ein.

  1. Geben Sie unter **Kennwort** das Kennwort ein.

  1. (Optional) Wählen Sie **Kennwort speichern**, wenn Sie dieses Kennwort in diesem Profil speichern möchten.

1. Wählen Sie **Veröffentlichen**, um die Änderungen an der Webrolle zu veröffentlichen.

    In der Statuszeile wird **Veröffentlichungsvorgang wurde gestartet** angezeigt. Wenn die Veröffentlichung abgeschlossen ist, wird **Veröffentlichen erfolgreich** angezeigt. Die Änderungen wurden auf dem virtuellen Computer für die Webrolle bereitgestellt. Sie können die Azure-Anwendung jetzt in der Azure-Umgebung starten, um die Änderungen zu testen.

### So machen Sie Web Deploy sicher

1. Für Web Deploy wird standardmäßig ein nicht vertrauenswürdiges, selbstsigniertes Zertifikat verwendet. Dies wird nicht zum Hochladen von vertraulichen Daten empfohlen. Wenn Sie diesen Prozess für vertrauliche Daten schützen möchten, können Sie ein SSL-Zertifikat hinzufügen, das für Web Deploy-Verbindungen verwendet wird. Dieses Zertifikat muss ein vertrauenswürdiges Zertifikat von einer Zertifizierungsstelle sein.

    Um Web Deploy für jeden virtuellen Computer und jede Webrolle sicher zu machen, müssen Sie das vertrauenswürdige Zertifikat hochladen, das Sie für Web Deploy im [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=213885) verwenden möchten. Dadurch wird sichergestellt, dass das Zertifikat dem virtuellen Computer hinzugefügt wird, der beim Veröffentlichen der Anwendung für die Webrolle erstellt wird.

1. Führen Sie die folgenden Schritte aus, um ein vertrauenswürdiges SSL-Zertifikat für IIS hinzuzufügen, das für Remoteverbindungen verwendet wird:

  1. Klicken Sie zum Herstellen einer Verbindung mit dem virtuellen Computer, auf dem die Webrolle ausgeführt wird, unter **Cloud Explorer** oder **Server-Explorer** auf die Instanz der Webrolle. Wählen Sie anschließend den Befehl **Mithilfe von Remotedesktop verbinden**. Ausführliche Schritte zum Herstellen einer Verbindung mit dem virtuellen Computer finden Sie unter [Verwenden von Remotedesktop mit Azure-Rollen](vs-azure-tools-remote-desktop-roles.md).

      Sie werden im Browser aufgefordert, eine RDP-Datei herunterzuladen.

  1. Öffnen Sie den Verwaltungsdienst im IIS-Manager, um ein SSL-Zertifikat hinzuzufügen. Aktivieren Sie SSL im IIS-Manager, indem Sie im Aktionsbereich den Link **Bindungen** öffnen. Das Dialogfeld **Sitebindung hinzufügen** wird angezeigt. Wählen Sie **Hinzufügen** und in der Dropdownliste **Typ** dann die Option HTTPS. Wählen Sie in der Liste **SSL-Zertifikat** das SSL-Zertifikat aus, das von einer Zertifizierungsstelle signiert wurde und das Sie in das Azure-Verwaltungsportal hochgeladen haben. Weitere Informationen finden Sie unter [Konfigurieren der Verbindungseinstellungen für den Verwaltungsdienst](http://go.microsoft.com/fwlink/?LinkId=215824).

      >[AZURE.NOTE]Wenn Sie ein vertrauenswürdiges SSL-Zertifikat hinzufügen, wird die Warnung mit dem gelben Dreieck im **Veröffentlichungs-Assistenten** nicht mehr angezeigt.

## Einschließen von Dateien in das Dienstpaket

Unter Umständen müssen Sie bestimmte Dateien in Ihr Dienstpaket einschließen, damit diese auf dem virtuellen Computer verfügbar sind, der für eine Rolle erstellt wird. Es kann beispielsweise sein, dass Sie Ihrem Dienstpaket eine EXE- oder MSI-Datei, die von einem Startskript verwendet wird, hinzufügen möchten. Oder Sie möchten eine Assembly hinzufügen, die für ein Webrollen- oder Workerrollenprojekt erforderlich ist. Um Dateien einzuschließen, müssen sie der Projektmappe für Ihre Azure-Anwendung hinzugefügt werden.

### So schließen Sie Dateien in das Dienstpaket ein

1. Führen Sie die folgenden Schritte aus, um einem Dienstpaket eine Assembly hinzuzufügen:

  1. Öffnen Sie im **Projektmappen-Explorer** den Projektknoten für das Projekt, für das die referenzierte Assembly fehlt.

  1. Um die Assembly dem Projekt hinzuzufügen, öffnen Sie das Kontextmenü für den Ordner **Verweise** und wählen dann **Verweis hinzufügen**. Das Dialogfeld „Verweis hinzufügen“ wird angezeigt.

  1. Wählen Sie den Verweis, den Sie hinzufügen möchten, und dann die Schaltfläche **OK**.

      Der Verweis wird der Liste unter dem Ordner **Verweise** hinzugefügt.

  1. Öffnen Sie das Kontextmenü für die Assembly, die Sie hinzugefügt haben, und wählen Sie **Eigenschaften**. Das Fenster **Eigenschaften** wird angezeigt.

      Um diese Assembly in das Dienstpaket einzuschließen, wählen Sie in der Liste **Lokale Kopie** die Option **Wahr**.

1. Öffnen Sie im **Projektmappen-Explorer** den Projektknoten für das Projekt, für das die referenzierte Assembly fehlt.

1. Um die Assembly dem Projekt hinzuzufügen, öffnen Sie das Kontextmenü für den Ordner **Verweise** und wählen dann **Verweis hinzufügen**. Das Dialogfeld **Verweis hinzufügen** wird angezeigt.

1. Wählen Sie den Verweis, den Sie hinzufügen möchten, und dann die Schaltfläche **OK**.

    Der Verweis wird der Liste unter dem Ordner **Verweise** hinzugefügt.

1. Öffnen Sie das Kontextmenü für die Assembly, die Sie hinzugefügt haben, und wählen Sie **Eigenschaften**. Das Fenster „Eigenschaften“ wird angezeigt.

1. Um diese Assembly in das Dienstpaket einzuschließen, wählen Sie in der Liste **Lokale Kopie** die Option **Wahr**.

1. Zum Einschließen von Dateien in das Dienstpaket, die Ihrem Webrollenprojekt hinzugefügt wurden, öffnen Sie das Kontextmenü für die Datei und wählen dann **Eigenschaften**. Wählen Sie im Fenster **Eigenschaften** im Listenfeld **Buildvorgang** die Option **Inhalt**.

1. Zum Einschließen von Dateien in das Dienstpaket, die Ihrem Workerrollenprojekt hinzugefügt wurden, öffnen Sie das Kontextmenü für die Datei und wählen dann **Eigenschaften**. Wählen Sie im Fenster **Eigenschaften** im Listenfeld **In Ausgabeverzeichnis kopieren** die Option **Kopieren, wenn neuer**.

## Nächste Schritte

Weitere Informationen zum Veröffentlichen in Azure aus Visual Studio finden Sie unter [Assistent zur Veröffentlichung einer Azure-Anwendung](vs-azure-tools-publish-azure-application-wizard.md).

<!---HONumber=Sept15_HO3-->