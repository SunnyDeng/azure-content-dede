<properties 
	pageTitle="Konfigurieren eines Clouddiensts – Azure" 
	description="Hier erfahren Sie, wie Sie Clouddienste in Azure konfigurieren. Hier erfahren Sie, wie Sie die Konfiguration für Clouddienste aktualisieren und Remotezugriff auf Rolleninstanzen konfigurieren." 
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
	ms.date="06/29/2015"
	ms.author="adegeo"/>




# Konfigurieren von Clouddiensten

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-configure.md)
- [Azure Preview Portal](cloud-services-how-to-configure-portal.md)

Im Azure-Verwaltungsportal können Sie die am häufigsten für einen Clouddienst verwendeten Einstellungen konfigurieren. Wenn Sie die Konfigurationsdateien jedoch direkt aktualisieren möchten, laden Sie eine zu aktualisierende Konfigurationsdatei herunter, laden Sie anschließend die aktualisierte Datei hoch, und aktualisieren Sie den Clouddienst mit den Konfigurationsänderungen. In beiden Fällen wird die aktualisierte Konfiguration an alle Rolleninstanzen übermittelt.

Sie können auch eine Remotedesktopverbindung zu einer oder allen Rollen aktivieren, die in Ihrem Clouddienst ausgeführt werden. Mit Remotedesktop können Sie auf den Desktop Ihrer Anwendung zugreifen, während diese ausgeführt wird, und Probleme diagnostizieren und beheben. Sie können eine Remotedesktopverbindung zu Ihrer Rolle auch aktivieren, wenn Sie während der Entwicklung der Anwendung die Servicedefinitionsdatei (.csdef) für Remotedesktop nicht konfiguriert haben. Die Anwendung muss nicht erneut bereitgestellt werden, um eine Remotedesktopverbindung zu aktivieren.

Während der Konfigurationsupdates kann Azure nur dann eine Dienstverfügbarkeit von 99,95 Prozent sicherstellen, wenn Sie mindestens zwei Rolleninstanzen für jede Rolle haben. In diesem Fall kann ein virtueller Computer Clientanforderungen verarbeiten, während der andere aktualisiert wird. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/).

## Ändern eines Clouddiensts

1. Klicken Sie im [Azure-Portal](http://manage.windowsazure.com/) auf **Cloud Services**, auf den Namen des Clouddiensts und dann auf **Konfigurieren**.

    ![Konfigurationsseite](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    Auf der Seite **Konfigurieren** können Sie die Überwachung konfigurieren, Rolleneinstellungen aktualisieren sowie das Gast-Betriebssystem und die Familie für Rolleninstanzen wählen.

2. Legen Sie in **Überwachung** die Überwachungsstufe auf "Ausführlich" oder "Minimal" fest, und konfigurieren Sie die Diagnoseverbindungszeichenfolgen, die für eine ausführliche Überwachung benötigt werden.

3. Bei Dienstrollen (gruppiert nach Rolle) können Sie die folgenden Einstellungen aktualisieren:
    
    >**Einstellungen** Ändern Sie die Werte verschiedener Konfigurationseinstellungen, die in den *ConfigurationSettings*-Elementen der Dienstkonfigurationsdatei (.cscfg) festgelegt sind.
    >
    >**Zertifikate** Ändern Sie den Zertifikatfingerabdruck, der bei der SSL-Verschlüsselung für eine Rolle verwendet wird. Um ein Zertifikat zu ändern, müssen Sie das neue Zertifikat zuerst hochladen (auf der Seite **Zertifikate**). Aktualisieren Sie anschließend den Fingerabdruck in der Zertifikatzeichenfolge, die in den Rolleneinstellungen angezeigt wird.

4. Unter **Betriebssystem** können Sie die Betriebssystemfamilie oder -version für Rolleninstanzen ändern, oder wählen Sie **Automatisch**, um automatische Updates der aktuellen Betriebssystemversion zu ermöglichen. Die Betriebssystemeinstellungen gelten für Webrollen und Workerrollen, betreffen virtuelle Computer jedoch nicht.

    Während der Bereitstellung wird die aktuelle Betriebssystemversion auf allen Rolleninstanzen installiert, und die Betriebssysteme werden standardmäßig automatisch aktualisiert.
    
    Wenn Ihr Clouddienst wegen Kompatibilitätsanforderungen Ihres Codes unter einem anderen Betriebssystem ausgeführt werden muss, können Sie eine Betriebssystemfamilie und -version wählen. Wenn Sie eine bestimmte Betriebssystemversion wählen, werden automatische Betriebssystemupdates für den Clouddienst außer Kraft gesetzt. Sie müssen dann sicherstellen, dass die Betriebssysteme Updates erhalten.
    
    Wenn Sie alle Kompatibilitätsprobleme lösen, die Ihre Anwendungen mit der aktuellen Betriebssystemversion haben, können Sie automatische Updates des Betriebssystems aktivieren, indem Sie für die Betriebssystemversion **Automatisch** einstellen.
    
    ![Betriebssystemeinstellungen](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Um die Konfigurationseinstellungen zu speichern und an die Rolleninstanzen zu übertragen, klicken Sie auf **Speichern**. (Klicken Sie auf **Verwerfen**, um die Änderungen abzubrechen.) **Speichern** und **Verwerfen** werden der Befehlsleiste hinzugefügt, nachdem Sie eine Einstellung geändert haben.

## Aktualisieren einer Clouddienst-Konfigurationsdatei

1. Laden Sie eine Clouddienst-Konfigurationsdatei (CSCFG) mit der aktuellen Konfiguration herunter. Klicken Sie auf der Seite **Konfigurieren** des Clouddiensts auf **Herunterladen**. Klicken Sie danach auf **Speichern**, oder klicken Sie auf **Speichern als**, um die Datei zu speichern.

2. Laden Sie nach dem Update der Dienstkonfigurationsdatei die Konfigurationsupdates hoch, und wenden Sie sie an:

    1. Klicken Sie auf der Seite **Konfigurieren** auf **Hochladen**.
    
        ![Konfiguration hochladen](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. Klicken Sie unter **Konfigurationsdatei** auf **Durchsuchen**, um die aktualisierte CSCFG-Datei auszuwählen.
    
    3. Wenn Ihr Clouddienst Rollen enthält, die über nur eine Instanz verfügen, aktivieren Sie das Kontrollkästchen **Konfiguration auch dann anwenden, wenn mindestens eine Rolle nur eine einzelne Instanz enthält**, um die Konfigurationsupdates zu aktivieren, damit die Rollen fortgesetzt werden können.
    
        Nur wenn Sie für jede Rolle mindestens zwei Instanzen definieren, kann Azure während der Updates der Dienstkonfiguration eine Verfügbarkeit des Clouddiensts von mindestens 99,95 Prozent garantieren. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/).
    
    4. Klicken Sie auf **OK** (Häkchen).


## Konfigurieren des Remotezugriffs auf Rolleninstanzen

Mit Remotedesktop können Sie auf den Desktop einer Rolle zugreifen, die in Azure ausgeführt wird. Mithilfe einer Remotedesktopverbindung können Sie Probleme mit Ihrer Anwendung diagnostizieren und beheben, während diese ausgeführt wird. Sie können eine Remotedesktopverbindung in der Rolle während der Anwendungsentwicklung oder nach der Bereitstellung der Anwendung in Azure (während die Rolle ausgeführt wird) aktivieren. Zur Aktivierung einer Remotedesktopverbindung in einer aktiven Rolle über das Verwaltungsportal muss die Anwendung nicht neu bereitgestellt werden. Um die Remotedesktopverbindung zu authentifizieren, können Sie ein zuvor hochgeladenes Zertifikat verwenden oder ein neues Zertifikat erstellen.

Auf der Seite **Konfigurieren** des Clouddiensts können Sie Remotedesktop aktivieren oder das lokale Administratorkonto oder -kennwort, das zum Herstellen einer Verbindung mit den virtuellen Computern verwendet wird, das bei der Authentifizierung verwendete Zertifikat oder das Ablaufdatum ändern.

### Konfigurieren des Remotezugriffs in der Dienstdefinitionsdatei

Fügen Sie der Dienstdefinitionsdatei (.csdef) **Import**-Elemente hinzu, um die Module RemoteAccess und RemoteForwarder in das Dienstmodell zu importieren. Wenn diese Module vorhanden sind, fügt Azure die Konfigurationseinstellungen für Remotedesktop zur Dienstkonfigurationsdatei hinzu. Um die Remotedesktopkonfiguration abzuschließen, müssen Sie ein Zertifikat in Azure importieren und das Zertifikat in der Dienstkonfigurationsdatei angeben. Weitere Informationen finden Sie unter [Einrichten einer Remotedesktopverbindung für eine Rolle in Windows Azure][].

### Aktivieren oder Ändern des Remotezugriffs für Rolleninstanzen im Verwaltungsportal

1. Klicken Sie auf **Cloud Services** und dann auf den Namen des Clouddiensts, und klicken Sie dann auf **Konfigurieren**.

2. Klicken Sie auf **Remote**.
    
    ![Clouddienste remote](./media/cloud-services-how-to-configure/CloudServices_Remote.png)
    
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
    
    1. Klicken Sie auf **Instanzen**, um die Seite **Instanzen** zu öffnen.
    
    2. Wählen Sie eine Rolleninstanz aus, in der Remotedesktop konfiguriert ist.
    
    3. Klicken Sie auf **Verbinden**, und folgen Sie den Anweisungen, um den Desktop zu öffnen.
    
    4. Klicken Sie auf **Öffnen** und dann auf **Verbinden**, um die Remotedesktopverbindung zu starten.

### Deaktivieren des Remotezugriffs für Rolleninstanzen im Verwaltungsportal

1. Klicken Sie auf **Cloud Services** und dann auf den Namen des Clouddiensts, und klicken Sie dann auf **Konfigurieren**.

2. Klicken Sie auf **Remote**.

3. Wählen Sie unter **Rollen** die Rolle aus, die aktualisiert werden soll, oder wählen Sie **Alle** für alle Rollen.

4. Deaktivieren Sie das Kontrollkästchen **Remotedesktop aktivieren**.

5. Klicken Sie auf **OK** (Häkchen).

[Einrichten einer Remotedesktopverbindung für eine Rolle in Windows Azure]: https://msdn.microsoft.com/library/azure/hh124107.aspx

[Verwenden von Remotedesktop mit Azure-Rollen]: https://msdn.microsoft.com/library/azure/gg443832.aspx
			
 

<!---HONumber=August15_HO6-->