<properties linkid="manage-services-how-to-configure-a-cloud-service" urlDisplayName="How to configure" pageTitle="How to configure a cloud service - Azure" metaKeywords="Configuring cloud services" description="Learn how to configure cloud services in Azure. Learn to update the cloud service configuration and configure remote access to role instances." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Configure Cloud Services" authors="davidmu" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="davidmu" />

# <span id="configurecloudservice"></span></a>Konfigurieren von Cloud-Diensten

[WACOM.INCLUDE [Haftungsausschluss](../includes/disclaimer.md)]

Im Azure-Verwaltungsportal können Sie die am häufigsten für einen Cloud-Dienst verwendeten Einstellungen konfigurieren. Wenn Sie die Konfigurationsdateien jedoch direkt aktualisieren möchten, laden Sie eine zu aktualisierende Konfigurationsdatei herunter, laden Sie anschließend die aktualisierte Datei hoch, und aktualisieren Sie den Cloud-Dienst mit den Konfigurationsänderungen. In beiden Fällen wird die aktualisierte Konfiguration an alle Rolleninstanzen übermittelt.

Sie können auch eine Remotedesktopverbindung zu einer oder allen Rollen aktivieren, die in Ihrem Cloud-Dienst ausgeführt werden. Mit Remotedesktop können Sie auf den Desktop Ihrer Anwendung zugreifen, während diese ausgeführt wird, und Probleme diagnostizieren und beheben. Sie können eine Remotedesktopverbindung zu Ihrer Rolle auch aktivieren, wenn Sie während der Entwicklung der Anwendung die Servicedefinitionsdatei (.csdef) für Remotedesktop nicht konfiguriert haben. Die Anwendung muss nicht erneut bereitgestellt werden, um eine Remotedesktopverbindung zu aktivieren.

Während der Konfigurationsupdates kann Azure nur dann eine Dienstverfügbarkeit von 99,95 Prozent sicherstellen, wenn Sie mindestens zwei Rolleninstanzen (virtuelle Computer) für jede Rolle haben. In diesem Fall kann ein virtueller Computer Clientanforderungen verarbeiten, während der andere aktualisiert wird. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel][Vereinbarungen zum Servicelevel].

## Inhaltsverzeichnis

-   [Gewusst wie: Aktualisieren der Konfiguration des Cloud-Diensts][Gewusst wie: Aktualisieren der Konfiguration des Cloud-Diensts]
-   [Gewusst wie: Konfigurieren des Remotezugriffs auf Rolleninstanzen][Gewusst wie: Konfigurieren des Remotezugriffs auf Rolleninstanzen]

## <span id="update"></span></a>Gewusst wie: Aktualisieren der Konfiguration des Cloud-Diensts

1.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] auf **Cloud-Dienste**. Klicken Sie anschließend auf den Namen des Cloud-Diensts, um das Dashboard zu öffnen.

2.  Klicken Sie auf **Konfigurieren**.

    Auf der Seite **Konfigurieren** können Sie die Überwachung konfigurieren, Rolleneinstellungen aktualisieren sowie das Gast-Betriebssystem und die Familie für Rolleninstanzen (virtuelle Computer) wählen.

    ![Konfigurationsseite][Konfigurationsseite]

3.  Legen Sie in den Überwachungseinstellungen die Überwachungsstufe auf "Ausführlich" oder "Minimal" fest, und konfigurieren Sie die Diagnoseverbindungszeichenfolgen, die für eine ausführliche Überwachung benötigt werden. Anweisungen hierzu finden Sie unter [Überwachen von Cloud-Diensten][Überwachen von Cloud-Diensten].

4.  Bei Dienstrollen (gruppiert nach Rolle) können Sie die folgenden Einstellungen aktualisieren:

    > -   **Einstellungen** Ändern Sie die Werte verschiedener Konfigurationseinstellungen, die in den *ConfigurationSettings*-Elementen der Dienstkonfigurationsdatei (.cscfg) festgelegt sind.

    > -   **Zertifikate** Ändern Sie den Zertifikatfingerabdruck, der bei der SSL-Verschlüsselung für eine Rolle verwendet wird. Um ein Zertifikat zu ändern, müssen Sie das neue Zertifikat zuerst hochladen (auf der Seite **Zertifikate**). Aktualisieren Sie anschließend den Fingerabdruck in der Zertifikatzeichenfolge, die in den Rolleneinstellungen angezeigt wird.

5.  In den **Betriebssystemeinstellungen** können Sie die Betriebssystemfamilie oder -version für Rolleninstanzen (virtuelle Computer) ändern, oder wählen Sie **Automatisch**, um automatische Updates der aktuellen Betriebssystemversion wieder aufzunehmen. Die Betriebssystemeinstellungen gelten für Webrollen und Workerrollen, haben jedoch keine Auswirkung auf Rollen virtueller Computer, die gehosteten Diensten im vorherigen Azure-Verwaltungsportal hinzugefügt wurden.

    Wenn Sie einen neuen Cloud-Dienst bereitstellen, können Sie als Betriebssystem entweder Windows Server 2008 R2, Windows Server 2008 mit Servicepack 2 (SP2) oder Windows Server 2012 wählen. Während der Bereitstellung wird die aktuelle Betriebssystemversion auf allen Rolleninstanzen installiert, und die Betriebssysteme werden standardmäßig automatisch aktualisiert.

    Wenn Ihr Cloud-Dienst wegen Kompatibilitätsanforderungen Ihres Codes unter einem anderen Betriebssystem ausgeführt werden muss, können Sie eine Betriebssystemfamilie und -version wählen. Wenn Sie eine bestimmte Betriebssystemversion wählen, werden automatische Betriebssystemupdates für den Cloud-Dienst außer Kraft gesetzt. Sie müssen dann sicherstellen, dass die Betriebssysteme Updates erhalten.

    Wenn Sie alle Kompatibilitätsprobleme lösen, die Ihre Anwendungen mit der aktuellen Betriebssystemversion haben, können Sie automatische Updates des Betriebssystems wieder aufnehmen, indem Sie für die Betriebssystemversion **Automatisch** einstellen.

    ![Betriebssystemeinstellungen][Betriebssystemeinstellungen]

6.  Um die Konfigurationseinstellungen zu speichern und an die Rolleninstanzen zu übertragen, klicken Sie auf **Speichern**. (Klicken Sie auf **Verwerfen**, um die Änderungen abzubrechen.) **Speichern** und **Verwerfen** werden der Befehlsleiste hinzugefügt, nachdem Sie eine Einstellung geändert haben.

### Manuelles Aktualisieren einer Cloud-Dienstkonfigurationsdatei

1.  Laden Sie eine Cloud-Dienstkonfigurationsdatei (CSCFG) mit der aktuellen Konfiguration herunter. Klicken Sie auf der Seite **Konfigurieren** des Cloud-Diensts auf **Herunterladen**. Klicken Sie danach auf **Speichern**, oder klicken Sie auf **Speichern als**, um die Datei zu speichern.

2.  Laden Sie nach dem Update der Dienstkonfigurationsdatei die Konfigurationsupdates hoch, und wenden Sie sie an:

    a. Klicken Sie auf der Seite **Konfigurieren** auf **Hochladen**.

    **Neue Konfigurationsdatei hochladen** wird geöffnet.

    ![Konfiguration hochladen][Konfiguration hochladen]

    b. Klicken Sie unter **Konfigurationsdatei** auf **Durchsuchen**, um die aktualisierte CSCFG-Datei auszuwählen.

    c. Wenn Ihr Cloud-Dienst Rollen enthält, die nur eine Instanz haben, aktivieren Sie das Kontrollkästchen **Apply configuration even if one or more roles contain a single instance**, um die Konfigurationsupdates zu aktivieren, damit die Rollen fortgesetzt werden können.

    Nur wenn Sie für jede Rolle mindestens zwei Instanzen definieren, kann Azure während der Updates der Dienstkonfiguration eine Verfügbarkeit des Cloud-Diensts von mindestens 99,95 Prozent garantieren. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel][1].

    d. Klicken Sie auf "OK" (Häkchen).

## <span id="remoteaccess"></span></a>Gewusst wie: Konfigurieren des Remotezugriffs auf Rolleninstanzen

Mit Remotedesktop können Sie auf den Desktop einer Rolle zugreifen, die in Azure ausgeführt wird. Mithilfe einer Remotedesktopverbindung können Sie Probleme mit Ihrer Anwendung diagnostizieren und beheben, während diese ausgeführt wird. Sie können eine Remotedesktopverbindung in der Rolle während der Anwendungsentwicklung oder nach der Bereitstellung der Anwendung in Azure (während die Rolle ausgeführt wird) aktivieren. Zur Aktivierung einer Remotedesktopverbindung in einer aktiven Rolle über das Verwaltungsportal muss die Anwendung nicht neu bereitgestellt werden. Um die Remotedesktopverbindung zu authentifizieren, können Sie ein zuvor hochgeladenes Zertifikat verwenden oder ein neues Zertifikat erstellen.

Auf der Seite **Konfigurieren** des Cloud-Diensts können Sie Remotedesktop aktivieren oder das lokale Administratorkonto oder -kennwort, das zum Herstellen einer Verbindung mit den virtuellen Computern verwendet wird, das bei der Authentifizierung verwendete Zertifikat oder das Ablaufdatum ändern.

<div class="dev-callout"> 
<b>Hinweis</b> 
<p>Wenn der Cloud-Dienst mindestens zwei verbundene virtuelle Windows Server-Computer umfasst, m&uuml;ssen Sie den Remotezugriff nicht konfigurieren, da diese virtuellen Computer automatisch f&uuml;r Remotedesktop konfiguriert werden.</p> 
</div>

### Konfigurieren des Remotezugriffs in der Dienstdefinitionsdatei

Fügen Sie der Dienstdefinitionsdatei (.csdef) **Import**-Elemente hinzu, um die Module RemoteAccess und RemoteForwarder in das Dienstmodell zu importieren. Wenn diese Module vorhanden sind, fügt Azure die Konfigurationseinstellungen für Remotedesktop zur Dienstkonfigurationsdatei hinzu. Um die Remotedesktopkonfiguration abzuschließen, müssen Sie ein Zertifikat in Azure importieren und das Zertifikat in der Dienstkonfigurationsdatei angeben. Weitere Informationen finden Sie unter [Einrichten einer Remotedesktopverbindung für eine Rolle in Windows Azure][Einrichten einer Remotedesktopverbindung für eine Rolle in Windows Azure].

### Aktivieren oder Ändern des Remotezugriffs für Rolleninstanzen im Verwaltungsportal

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, und klicken Sie auf **Cloud-Dienste**. Klicken Sie anschließend auf den Namen des Cloud-Diensts, um das Dashboard zu öffnen.

2.  Öffnen Sie die Seite **Konfigurieren** des Cloud-Diensts, und klicken Sie auf **Remote**.

    Unter **Remotedesktop konfigurieren** werden die Einstellungen (falls vorhanden) angezeigt, die der Dienstkonfigurationsdatei beim Bereitstellen des Cloud-Diensts hinzugefügt wurden (siehe unten).

    ![Cloud-Dienste remote][Cloud-Dienste remote]

> [WACOM.NOTE]
> **Warnung:** Alle Rolleninstanzen werden neu gestartet, wenn Sie Remotedesktop erstmals aktivieren und auf OK (Häkchen) klicken. Um einen Neustart zu verhindern, muss in der Rolle das Zertifikat installiert sein, mit dem das Kennwort verschlüsselt wird. Ist kein Zertifikat installiert, sehen Sie die folgende Option: ![CloudServices\_CreateNewCertDropDown][CloudServices\_CreateNewCertDropDown]

    To prevent a restart, install a certificate and then return to this dialog (see [Using Remote Desktop with Azure Roles][Using Remote Desktop with Azure Roles] for more information). If you choose an existing certificate, then a configuration update will be sent to all the instances in the role.

1.  Wählen Sie unter **Rollen** die Dienstrolle aus, die aktualisiert werden soll, oder wählen Sie **Alle** für alle Rollen.

2.  Nehmen Sie die folgenden Änderungen vor:

-   Um Remotedesktop zu aktivieren, aktivieren Sie das Kontrollkästchen **Remotedesktop aktivieren**. Um Remotedesktop zu deaktivieren, deaktivieren Sie das Kontrollkästchen.

-   Erstellen Sie ein Konto, das für Remotedesktopverbindungen mit den Rolleninstanzen verwendet wird.

-   Aktualisieren Sie das Kennwort für das bestehende Konto.

-   Wählen Sie ein hochgeladenes Zertifikat für die Authentifizierung aus (laden Sie das Zertifikat mit **Hochladen** auf der Seite **Zertifikate** hoch), oder erstellen Sie ein neues Zertifikat.

-   Ändern Sie das Ablaufdatum für die Remotedesktopkonfiguration.

1.  Wenn Sie die Konfigurationsupdates beendet haben, klicken Sie auf OK (Häkchen).

2.  So verbinden Sie eine Rolleninstanz:

    a. Klicken Sie auf **Instanzen**, um die Seite **Instanzen** zu öffnen.

    b. Wählen Sie eine Rolleninstanz aus, in der Remotedesktop konfiguriert ist.

    c. Klicken Sie auf **Verbinden**, und folgen Sie den Anweisungen, um den Desktop des virtuellen Computers zu öffnen.

    d. Klicken Sie auf **Öffnen** und dann auf **Verbinden**, um die Remotedesktopverbindung zu starten.

### Deaktivieren des Remotezugriffs für Rolleninstanzen im Verwaltungsportal

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, und klicken Sie auf **Cloud-Dienste**. Klicken Sie anschließend auf den Namen des Cloud-Diensts, um das Dashboard zu öffnen.

2.  Öffnen Sie die Seite **Konfigurieren** des Cloud-Diensts, und klicken Sie auf **Remote**.

3.  Wählen Sie unter **Rollen** die Dienstrolle aus, die aktualisiert werden soll, oder wählen Sie **Alle** für alle Rollen.

4.  Deaktivieren Sie das Kontrollkästchen **Remotedesktop aktivieren**.

5.  Klicken Sie auf "OK" (Häkchen).

  [Haftungsausschluss]: ../includes/disclaimer.md
  [Vereinbarungen zum Servicelevel]: https://www.windowsazure.com/de-de/support/legal/sla/
  [Gewusst wie: Aktualisieren der Konfiguration des Cloud-Diensts]: #update
  [Gewusst wie: Konfigurieren des Remotezugriffs auf Rolleninstanzen]: #remoteaccess
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com/
  [Konfigurationsseite]: ./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png
  [Überwachen von Cloud-Diensten]: ../how-to-monitor-a-cloud-service/
  [Betriebssystemeinstellungen]: ./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png
  [Konfiguration hochladen]: ./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png
  [1]: http://www.windowsazure.com/de-de/support/legal/sla/
  [Einrichten einer Remotedesktopverbindung für eine Rolle in Windows Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/hh124107.aspx
  [Cloud-Dienste remote]: ./media/cloud-services-how-to-configure/CloudServices_Remote.png
