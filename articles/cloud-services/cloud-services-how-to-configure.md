<properties 
	pageTitle="Konfigurieren eines Clouddiensts | Microsoft Azure" 
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
	ms.date="09/22/2015"
	ms.author="adegeo"/>




# Konfigurieren von Clouddiensten

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-configure.md)
- [Azure Preview Portal](cloud-services-how-to-configure-portal.md)

Im Azure-Verwaltungsportal können Sie die am häufigsten für einen Clouddienst verwendeten Einstellungen konfigurieren. Wenn Sie die Konfigurationsdateien jedoch direkt aktualisieren möchten, laden Sie eine zu aktualisierende Konfigurationsdatei herunter, laden Sie anschließend die aktualisierte Datei hoch, und aktualisieren Sie den Clouddienst mit den Konfigurationsänderungen. In beiden Fällen wird die aktualisierte Konfiguration an alle Rolleninstanzen übermittelt.

Das Azure-Verwaltungsportal ermögicht Ihnen auch das [Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services](cloud-services-role-enable-remote-desktop.md)

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


## Nächste Schritte

* Weitere Informationen zum [Bereitstellen eines Clouddiensts](cloud-services-how-to-create-deploy.md)
* [Konfigurieren eines benutzerdefinierten Domänennamens](cloud-services-custom-domain-name.md)
* [Verwalten Ihres Clouddiensts](cloud-services-how-to-manage.md)
* [Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services](cloud-services-role-enable-remote-desktop.md)
* Konfigurieren von [SSL-Zertifikaten](cloud-services-configure-ssl-certificate.md)

<!---HONumber=Oct15_HO3-->