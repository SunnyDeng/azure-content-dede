<properties urlDisplayName="How to configure" pageTitle="Konfigurieren von Cloud-Diensten - Azure" metaKeywords="Konfigurieren von Cloud-Diensten" description="Hier erfahren Sie, wie Sie Cloud-Dienste in Azure konfigurieren. Hier erfahren Sie, wie Sie die Konfiguration für Cloud-Dienste aktualisieren und den Remotezugriff auf Rolleninstanzen konfigurieren." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Configure Cloud Services" authors="davidmu" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/21/2014" ms.author="davidmu" />




<h1><a id="configurecloudservice"></a>Konfigurieren von Cloud-Diensten</h1>

Im Azure-Verwaltungsportal können Sie die am häufigsten für einen Cloud-Dienst verwendeten Einstellungen konfigurieren. Wenn Sie die Konfigurationsdateien jedoch direkt aktualisieren möchten, laden Sie eine zu aktualisierende Konfigurationsdatei herunter, laden Sie anschließend die aktualisierte Datei hoch, und aktualisieren Sie den Cloud-Dienst mit den Konfigurationsänderungen. In beiden Fällen wird die aktualisierte Konfiguration an alle Rolleninstanzen übermittelt.

Sie können auch eine Remotedesktopverbindung zu einer oder allen Rollen aktivieren, die in Ihrem Cloud-Dienst ausgeführt werden.  Mit Remotedesktop können Sie auf den Desktop Ihrer Anwendung zugreifen, während diese ausgeführt wird, und Probleme diagnostizieren und beheben.  Sie können eine Remotedesktopverbindung zu Ihrer Rolle auch aktivieren, wenn Sie während der Entwicklung der Anwendung die Servicedefinitionsdatei (.csdef) für Remotedesktop nicht konfiguriert haben.  Die Anwendung muss nicht erneut bereitgestellt werden, um eine Remotedesktopverbindung zu aktivieren.

Während der Konfigurationsupdates kann Azure nur dann eine Dienstverfügbarkeit von 99,95 Prozent sicherstellen, wenn Sie mindestens zwei Rolleninstanzen für jede Rolle haben. In diesem Fall kann ein virtueller Computer Clientanforderungen verarbeiten, während der andere aktualisiert wird. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](https://www.windowsazure.com/de-de/support/legal/sla/).

##Inhaltsverzeichnis

* [Gewusst wie: Aktualisieren der Konfiguration des Cloud-Diensts](#update)
* [Gewusst wie: Konfigurieren des Remotezugriffs auf Rolleninstanzen](#remoteaccess)


<h2><a id="update"></a>Gewusst wie: Aktualisieren der Konfiguration des Cloud-Diensts</h2>

1. Klicken Sie im [Azure-Verwaltungsportal](http://manage.windowsazure.com/) auf **Cloud-Dienste** und dann auf den Namen des Cloud-Diensts, und klicken Sie dann auf **Konfigurieren**.

	![Configuration Page](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
	
	Auf der Seite **Konfigurieren** können Sie die Überwachung konfigurieren, Rolleneinstellungen aktualisieren sowie das Gast-Betriebssystem und die Familie für Rolleninstanzen wählen. 

2. Legen Sie in **Überwachung** die Überwachungsstufe auf "Ausführlich" oder "Minimal" fest, und konfigurieren Sie die Diagnoseverbindungszeichenfolgen, die für eine ausführliche Überwachung benötigt werden. Anweisungen hierzu finden Sie unter [Überwachen von Cloud-Diensten](../how-to-monitor-a-cloud-service/).


3. Bei Dienstrollen (gruppiert nach Rolle) können Sie die folgenden Einstellungen aktualisieren:

	>- **Einstellungen**: Ändern Sie die Werte verschiedener Konfigurationseinstellungen, die in den *ConfigurationSettings*-Elementen der Dienstkonfigurationsdatei (.cscfg) festgelegt sind.
	
	>- **Zertifikate**: Ändern Sie den Zertifikatfingerabdruck, der bei der SSL-Verschlüsselung für eine Rolle verwendet wird. Um ein Zertifikat zu ändern, müssen Sie das neue Zertifikat zuerst hochladen (auf der Seite **Zertifikate**). Aktualisieren Sie anschließend den Fingerabdruck in der Zertifikatzeichenfolge, die in den Rolleneinstellungen angezeigt wird.

4. Unter **Betriebssystem** können Sie die Betriebssystemfamilie oder -version für Rolleninstanzen ändern, oder wählen Sie **Automatisch** aus, um automatische Updates der aktuellen Betriebssystemversion zu ermöglichen. Die Betriebssystemeinstellungen gelten für Webrollen und Workerrollen, betreffen virtuelle Computer jedoch nicht.

	Während der Bereitstellung wird die aktuelle Betriebssystemversion auf allen Rolleninstanzen installiert, und die Betriebssysteme werden standardmäßig automatisch aktualisiert. 

	Wenn Ihr Cloud-Dienst wegen Kompatibilitätsanforderungen Ihres Codes unter einem anderen Betriebssystem ausgeführt werden muss, können Sie eine Betriebssystemfamilie und -version wählen. Wenn Sie eine bestimmte Betriebssystemversion wählen, werden automatische Betriebssystemupdates für den Cloud-Dienst außer Kraft gesetzt. Sie müssen dann sicherstellen, dass die Betriebssysteme Updates erhalten.

	Wenn Sie alle Kompatibilitätsprobleme lösen, die Ihre Anwendungen mit der aktuellen Betriebssystemversion haben, können Sie automatische Updates des Betriebssystems aktivieren, indem Sie für die Betriebssystemversion **Automatisch** einstellen. 

	![OS Settings](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Um die Konfigurationseinstellungen zu speichern und an die Rolleninstanzen zu übertragen, klicken Sie auf **Speichern**. (Klicken Sie auf **Verwerfen**, um die Änderungen abzubrechen.) **Speichern** und **Verwerfen** werden der Befehlsleiste hinzugefügt, nachdem Sie eine Einstellung geändert haben.

###Manuelles Aktualisieren einer Cloud-Dienstkonfigurationsdatei

1. Laden Sie eine Cloud-Dienstkonfigurationsdatei (CSCFG) mit der aktuellen Konfiguration herunter. Klicken Sie auf der Seite **Konfigurieren** des Cloud-Diensts auf **Herunterladen**. Klicken Sie danach auf **Speichern**, oder klicken Sie auf **Speichern als**, um die Datei zu speichern.

2. Laden Sie nach dem Update der Dienstkonfigurationsdatei die Konfigurationsupdates hoch, und wenden Sie sie an:

	a. Klicken Sie auf der Seite **Konfigurieren** auf **Hochladen**.

	![Upload Configuration](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)

	b. Klicken Sie unter **Konfigurationsdatei** auf **Durchsuchen**, um die aktualisierte CSCFG-Datei auszuwählen.

	c. Wenn Ihr Cloud-Dienst Rollen enthält, die nur eine Instanz haben, aktivieren Sie das Kontrollkästchen **Konfiguration anwenden, auch wenn eine oder mehrere Rollen eine einzelne Instanz enthalten**, um die Konfigurationsupdates zu aktivieren, damit die Rollen fortgesetzt werden können.

	Nur wenn Sie für jede Rolle mindestens zwei Instanzen definieren, kann Azure während der Updates der Dienstkonfiguration eine Verfügbarkeit des Cloud-Diensts von mindestens 99,95 Prozent garantieren. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](http://www.windowsazure.com/de-de/support/legal/sla/).

	d. Klicken Sie auf **OK** (Häkchen). 


<h2><a id="remoteaccess"></a>Gewusst wie: Konfigurieren des Remotezugriffs auf Rolleninstanzen</h2>

Mit Remotedesktop können Sie auf den Desktop einer Rolle zugreifen, die in Azure ausgeführt wird. Mithilfe einer Remotedesktopverbindung können Sie Probleme mit Ihrer Anwendung diagnostizieren und beheben, während diese ausgeführt wird. Sie können eine Remotedesktopverbindung in der Rolle während der Anwendungsentwicklung oder nach der Bereitstellung der Anwendung in Azure (während die Rolle ausgeführt wird) aktivieren.  Zur Aktivierung einer Remotedesktopverbindung in einer aktiven Rolle über das Verwaltungsportal muss die Anwendung nicht neu bereitgestellt werden.  Um die Remotedesktopverbindung zu authentifizieren, können Sie ein zuvor hochgeladenes Zertifikat verwenden oder ein neues Zertifikat erstellen.

Auf der Seite **Konfigurieren** des Cloud-Diensts können Sie Remotedesktop aktivieren oder das lokale Administratorkonto oder -kennwort, das zum Herstellen einer Verbindung mit den virtuellen Computern verwendet wird, das bei der Authentifizierung verwendete Zertifikat oder das Ablaufdatum ändern.

###Konfigurieren des Remotezugriffs in der Dienstdefinitionsdatei

Fügen Sie der Dienstdefinitionsdatei (.csdef) **Import**-Elemente hinzu, um die Module "RemoteAccess" und "RemoteForwarder" in das Dienstmodell zu importieren. Wenn diese Module vorhanden sind, fügt Azure die Konfigurationseinstellungen für Remotedesktop zur Dienstkonfigurationsdatei hinzu. Um die Remotedesktopkonfiguration abzuschließen, müssen Sie ein Zertifikat in Azure importieren und das Zertifikat in der Dienstkonfigurationsdatei angeben. Weitere Informationen finden Sie unter [Einrichten einer Remotedesktopverbindung für eine Rolle in Azure][].

###Aktivieren oder Ändern des Remotezugriffs für Rolleninstanzen im Verwaltungsportal

1. Klicken Sie auf **Cloud-Dienste** und dann auf den Namen des Cloud-Diensts, und klicken Sie anschließend auf **Konfigurieren**.

2. Klicken Sie auf **Remote**.

	 ![Cloud services remote](./media/cloud-services-how-to-configure/CloudServices_Remote.png)

	**Warnung:** Alle Rolleninstanzen werden neu gestartet, wenn Sie Remotedesktop erstmals aktivieren und auf "OK" (Häkchen) klicken. Um einen Neustart zu verhindern, muss in der Rolle das Zertifikat installiert sein, mit dem das Kennwort verschlüsselt wird.

	Um einen Neustart zu verhindern, installieren Sie ein Zertifikat, und kehren Sie dann zu diesem Dialogfeld zurück (weitere Informationen finden Sie unter [Verwenden von Remotedesktop mit Azure-Rollen][]). Wenn Sie ein vorhandenes Zertifikat wählen, wird ein Konfigurationsupdate an alle Instanzen in der Rolle gesendet.

3. Wählen Sie unter **Rollen** die Rolle aus, die aktualisiert werden soll, oder wählen Sie **Alle** für alle Rollen.

4. Nehmen Sie die folgenden Änderungen vor:

	- Um Remotedesktop zu aktivieren, aktivieren Sie das Kontrollkästchen **Remotedesktop aktivieren**. Um Remotedesktop zu deaktivieren, deaktivieren Sie das Kontrollkästchen.

	- Erstellen Sie ein Konto, das für Remotedesktopverbindungen mit den Rolleninstanzen verwendet wird.

	- Aktualisieren Sie das Kennwort für das bestehende Konto.

	- Wählen Sie ein hochgeladenes Zertifikat für die Authentifizierung aus (laden Sie das Zertifikat mit **Hochladen** auf der Seite **Zertifikate** hoch), oder erstellen Sie ein neues Zertifikat. 

	- Ändern Sie das Ablaufdatum für die Remotedesktopkonfiguration.

5. Wenn Sie die Konfigurationsupdates beendet haben, klicken Sie auf **OK** (Häkchen).

6. So verbinden Sie eine Rolleninstanz:

	a. Klicken Sie auf **Instanzen**, um die Seite **Instanzen** zu öffnen.

	b. Wählen Sie eine Rolleninstanz aus, in der Remotedesktop konfiguriert ist.

	c. Klicken Sie auf **Verbinden**, und folgen Sie den Anweisungen, um den Desktop zu öffnen. 

	d. Klicken Sie auf **Öffnen** und dann auf **Verbinden**, um die Remotedesktopverbindung zu starten.

###Deaktivieren des Remotezugriffs für Rolleninstanzen im Verwaltungsportal

1. Klicken Sie auf **Cloud-Dienste** und dann auf den Namen des Cloud-Diensts, und klicken Sie anschließend auf **Konfigurieren**.

2. Klicken Sie auf **Remote**.

3. Wählen Sie unter **Rollen** die Rolle aus, die aktualisiert werden soll, oder wählen Sie **Alle** für alle Rollen.

4. Deaktivieren Sie das Kontrollkästchen **Remotedesktop aktivieren**.

5. Klicken Sie auf **OK** (Häkchen).

[Einrichten einer Remotedesktopverbindung für eine Rolle in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/hh124107.aspx

[Verwendung von Remotedesktop mit Azure-Rollen]: http://msdn.microsoft.com/de-de/library/windowsazure/gg443832.aspx
			

<!--HONumber=35.2-->
