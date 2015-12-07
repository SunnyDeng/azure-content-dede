<properties
   pageTitle="Erstellen eines FQDN für einen virtuellen Computer im Azure-Vorschauportal | Microsoft Azure"
   description="Enthält Informationen zum Erstellen eines vollqualifizierten Domänennamens (FQDN) für einen Ressourcen-Manager-basierten virtuellen Computer im Azure-Vorschauportal."
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/21/2015"
   ms.author="dkshir"/>

# Erstellen eines vollständig qualifizierten Domänennamens im Azure-Vorschauportal

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.


Beim Erstellen eines virtuellen Computers im [Azure-Vorschauportal](https://portal.azure.com) mit dem Bereitstellungsmodell **Ressourcen-Manager** erstellt das Portal eine öffentliche IP als Ressource für den virtuellen Computer. Mit dieser IP-Adresse können Sie per Remotezugriff auf den virtuellen Computer zugreifen. Das Portal erstellt standardmäßig aber keinen [vollqualifizierten Domänennamen](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN). Da es einfacher ist, sich anstelle einer IP-Adresse einen FQDN zu merken und zu verwenden, wird in diesem Artikel beschrieben, wie Sie ihn dem virtuellen Computer hinzufügen.

Im Artikel wird vorausgesetzt, dass Sie sich an Ihrem Abonnement im Portal angemeldet und mit dem **Ressourcen-Manager** einen virtuellen Computer mit den verfügbaren Images erstellt haben. Führen Sie die folgenden Schritte aus, nachdem die Ausführung des virtuellen Computers begonnen hat:

1.  Zeigen Sie die Einstellungen des virtuellen Computers im Portal an, und klicken Sie auf die öffentliche IP-Adresse.

    ![IP-Ressource suchen](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  Heben Sie die Zuordnung der öffentlichen IP zum virtuellen Computer auf (**Zuordnung aufheben**). Beachten Sie, dass noch kein Domänenname angezeigt wird. Nach dem Klicken auf die Schaltfläche **Ja** dauert es unter Umständen einige Sekunden, bevor das Aufheben der Zuordnung abgeschlossen ist.

    ![Zuordnung von IP-Ressource aufheben](media/virtual-machines-create-fqdn-on-portal/dissociateIP.PNG)

    Wir ordnen diese öffentliche IP dem virtuellen Computer nach den folgenden Schritten zu. Wenn die öffentliche IP eine _Dynamische öffentliche IP_ ist, geht die IPv4-Adresse verloren, und nach dem Konfigurieren des FQDN wird eine neue Adresse zugewiesen.

3.  Nachdem die Schaltfläche **Zuordnung aufheben** ausgegraut wurde, klicken Sie auf den Abschnitt **Alle Einstellungen** der öffentlichen IP und öffnen die Registerkarte **Konfiguration**. Geben Sie den gewünschten DNS-Namen ein. **Speichern** Sie diese Konfiguration.

    ![DNS-Name eingeben](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

4.  Wechseln Sie zurück zum Blatt mit dem virtuellen Computer im Portal, und klicken Sie für den virtuellen Computer auf **Alle Einstellungen**. Öffnen Sie die Registerkarte **Netzwerkschnittstellen**, und klicken Sie auf die Netzwerkschnittstellenressource, die diesem virtuellen Computer zugeordnet ist. Das Blatt **Netzwerkschnittstelle** wird im Portal geöffnet.

    ![Netzwerkschnittstelle öffnen](media/virtual-machines-create-fqdn-on-portal/openNetworkInterface.PNG)

5.  Beachten Sie, dass das Feld **Öffentliche IP-Adresse** für die Netzwerkschnittstelle leer ist. Klicken Sie für diese Netzwerkschnittstelle auf den Abschnitt **Alle Einstellungen**, und öffnen Sie die Registerkarte **IP-Adressen**. Klicken Sie auf dem Blatt **IP-Adressen** für das Feld **Öffentliche IP-Adresse** auf **Aktiviert**. Wählen Sie die Registerkarte **IP-Adresse – Erforderliche Einstellungen konfigurieren**, und wählen Sie die Standard-IP aus, deren Zuordnung Sie eben aufgehoben haben. Klicken Sie auf **Speichern**. Das erneute Hinzufügen der IP-Ressource kann einige Minuten dauern.

    ![IP-Ressource konfigurieren](media/virtual-machines-create-fqdn-on-portal/configureIP.PNG)

6.  Schließen Sie alle anderen Blätter, und wechseln Sie zurück zum Blatt **Virtueller Computer**. Klicken Sie in den Einstellungen auf die öffentliche IP-Adressressource. Beachten Sie, dass im Blatt mit der öffentlichen IP jetzt der gewünschte FQDN als **DNS-Name** angezeigt wird.

    ![FQDN wird erstellt](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)

    Sie können jetzt eine Remoteverbindung mit dem virtuellen Computer herstellen, indem Sie diesen DNS-Namen verwenden. Verwenden Sie beispielsweise `SSH adminuser@testdnslabel.eastus.cloudapp.azure.com` beim Herstellen einer Verbindung mit einem virtuellen Linux-Computer, der über den vollqualifizierten Domänennamen `testdnslabel.eastus.cloudapp.azure.com` und den Benutzernamen `adminuser` verfügt.

<!---HONumber=AcomDC_1125_2015-->