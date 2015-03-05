<properties 
	pageTitle="Festlegen von Endpunkten auf einen virtuellen Computer in Azure" 
	description="Erfahren Sie, wie Sie die Kommunikation mit einem virtuellen Computer in Azure einrichten." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kathydav"/>

#Einrichten von Endpunkten für einen virtuellen Computer

Alle virtuellen Computer, die Sie in Azure erstellen, können automatisch über einen privaten Netzwerkkanal mit anderen virtuellen Computern im gleichen Clouddienst oder virtuellen Netzwerk kommunizieren. Computer im Internet oder anderen virtuellen Netzwerken erfordern jedoch von den Endpunkten, dass sie den eingehenden Netzwerkdatenverkehr auf einen virtuellen Computer leiten.

Beim Erstellen eines virtuellen Computers in Azure-Verwaltungsportal werden die Endpunkte für Remotedesktop und Windows PowerShell-Remoting Secure Shell (SSH) automatisch erstellt. Sie können zusätzliche Endpunkte beim Erstellen des virtuellen Computers oder später nach Bedarf konfigurieren. 

Jeder Endpunkt verfügt über einen öffentlichen und einen privaten Port.

- Der öffentliche Port wird von Azure-Lastenausgleich verwendet, um eingehenden Datenverkehr an den virtuellen Computer aus dem Internet zu überwachen. 
- Der private Port wird vom virtuellen Computer zum Überwachen von eingehendem Datenverkehr verwendet, in der Regel für eine Anwendung oder ein Dienst, der auf dem virtuellen Computer ausgeführt wird.

Standardwerte für die IP-Protokoll und TCP- oder UDP-Ports für bekannte Netzwerk Protokolle werden bereitgestellt, wenn Sie mit dem Verwaltungsportal Endpunkte erstellen. Bei benutzerdefinierten Endpunkten müssen Sie das richtige IP-Protokoll (TCP oder UDP) und die öffentlichen und privaten Ports angeben. Um nach dem Zufallsprinzip eingehenden Datenverkehr auf mehrere virtuelle Computer zu verteilen, müssen Sie einen Lastenausgleich, bestehend aus mehreren Endpunkten, erstellen.

Nachdem Sie einen Endpunkt erstellen, können Sie eine Zugriffssteuerungsliste (ACL) zum Definieren von Regeln erstellen, die eingehenden Datenverkehr an den öffentlichen Port des Endpunkts auf Grundlage der Quell-IP-Adresse zulassen oder verweigern. Wenn der virtuelle Computer in Windows Azure ist, sollten Sie stattdessen Netzwerk-Sicherheitsgruppen verwenden. Weitere Informationen finden Sie unter [zu Netzwerk-Sicherheitsgruppen](https://msdn.microsoft.com/library/azure/dn848316.aspx).

**Wichtig**: Firewall-Konfiguration für virtuelle Computer in Azure erfolgt automatisch für Remote Desktop und Secure Shell (SSH) und in den meisten Fällen für Windows PowerShell-Remoting zugeordnete Ports. Für Ports, die für alle anderen Endpunkte angegeben werden, wird keine Konfiguration der Firewall des virtuellen Computers automatisch durchgeführt. Wenn Sie einen Endpunkt für den virtuellen Computer erstellen, müssen Sie sicherstellen, dass die Firewall des virtuellen Computers außerdem den Datenverkehr für das Protokoll und den privaten Port entsprechend der Endpunktkonfiguration ermöglicht.

##Erstellen eines Endpunkts

1.	Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an, falls noch nicht geschehen.
2.	Klicken Sie auf **virtuelle Maschinen**, und klicken Sie dann auf den Namen des virtuellen Computers, den Sie konfigurieren möchten.
3.	Klicken Sie auf **Endpunkte**. Die Seite "Endpunkte" listet alle aktuellen Endpunkte für den virtuellen Computer auf.

	![Endpoints](./media/virtual-machines-set-up-endpoints/endpointswindows.png)
 
4.	Klicken Sie in der Taskleiste auf **hinzufügen**. 
5.	Wählen Sie auf der Seite **Einer virtuellen Maschine einen Endpunkt hinzufügen** den Typ des Endpunkts aus. 

	- Wenn Sie einen neuen Endpunkt erstellen, der nicht Teil des einen Lastenausgleichs oder das erste Element in einem neuen Satz mit Lastenausgleich ist, wählen Sie **einen eigenständigen Endpunkt hinzufügen** und klicken Sie dann auf den Pfeil nach links.
	- Wählen Sie andernfalls **Einen Endpunkt zu einem vorhandenen Satz mit Lastenausgleich hinzufügen**, wählen Sie den Namen des Satzes mit Lastenausgleich aus und klicken Sie dann auf den Pfeil nach links. Geben Sie auf der Seite **Geben Sie die Details des Endpunkts** einen Namen für den Endpunkt in **Name** an und klicken Sie dann auf das Häkchen, um den Endpunkt zu erstellen.

6.	Geben Sie auf der Seite **Details des Endpunkts festlegen** einen Namen für den Endpunkt in **Name** an. Sie können auch den Namen eines Protokolls aus der Liste auswählen, die Anfangswerte für **Protokoll**, **öffentlicher Port** und **privater Port** eintragen wird.
7.	Für einen benutzerdefinierten Endpunkt in **Protokoll**, wählen Sie entweder **TCP** oder **UDP**.
8.	Für angepasste Ports in **öffentlicher Port**, geben Sie die Portnummer für den eingehenden Datenverkehr aus dem Internet ein. Geben Sie in **privater Port** die Nummer des Ports an, auf dem der virtuelle Computer überwacht. Diese Portnummern dürfen sich unterscheiden. Stellen Sie sicher, dass die Firewall auf dem virtuellen Computer konfiguriert wurde, um den Datenverkehr für das Protokoll (in Schritt 7) und den privaten Port zu ermöglichen.
9.	Wenn dieser Endpunkt die erste Instanz in eine Gruppe mit Lastenausgleich sein soll, klicken Sie auf **Eine Gruppe mit Lastenausgleich erstellen** und klicken Sie dann auf den Pfeil nach rechts. Geben Sie auf der Seite **Den Lastenausgleich konfigurieren** einen Lastenausgleichsnamen, ein Prüfpunkt-Protokoll und einen Port sowie das Prüfpunkt-Intervall und die Anzahl gesendeter Prüfpunkten an. Der Azure-Lastenausgleich sendet Prüfpunkte an die virtuellen Computer in einem Satz mit Lastenausgleich, um deren Verfügbarkeit zu überwachen. Der Azure-Lastenausgleich leitet keinen Datenverkehr zu virtuellen Maschinen weiter, die nicht auf die Überprüfung reagieren. Klicken Sie auf den Pfeil nach rechts.
10.	Aktivieren Sie das Kontrollkästchen, um den Endpunkt zu erstellen.

Der Endpunkt wird jetzt auf der Seite Endpunkte aufgelistet.

![Endpoint creation successful](./media/virtual-machines-set-up-endpoints/endpointwindowsnew.png)
 
PowerShell-Konfiguration finden Sie unter [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

##Verwaltung der ACL für einen Endpunkt

Zum Definieren der Computer, die Datenverkehr senden können, kann die ACL für einen Endpunkt Datenverkehr anhand der IP-Quelladresse einschränken. Gehen Sie folgendermaßen vor, um eine ACL an einem Endpunkt hinzuzufügen, zu modifizieren oder zu entfernen.

> [AZURE.NOTE] Wenn der Endpunkt Teil eines Satzes mit Lastenausgleich ist, werden alle Änderungen, die Sie an der ACL oder an einem Endpunkt vornehmen, auf alle Endpunkte in diesem Satz angewendet.

Ist der virtuelle Computer in einem virtuellen Netzwerk in Azure, sollten Sie stattdessen die Netzwerk-Sicherheitsgruppen verwenden. Weitere Informationen finden Sie unter [zu Netzwerk-Sicherheitsgruppen](https://msdn.microsoft.com/library/azure/dn848316.aspx).


1.	Melden Sie sich beim Azure-Verwaltungsportal an, falls noch nicht geschehen.
2.	Klicken Sie auf **virtuelle Maschinen**, und klicken Sie dann auf den Namen des virtuellen Computers, den Sie konfigurieren möchten.
3.	Klicken Sie auf **Endpunkte**. Auf der Seite "Endpunkte" werden alle Endpunkte für den virtuellen Computer aufgelistet.

    ![ACL list](./media/virtual-machines-set-up-endpoints/EndpointsShowsDefaultEndpointsForVM.png)
 
4.	Wählen Sie den entsprechenden Endpunkt aus der Liste. 
5.	Klicken Sie in der Taskleiste auf **ACL verwalten**. Das **Specify ACL Details** Dialogfeld wird angezeigt.

    ![Specify ACL details](./media/virtual-machines-set-up-endpoints/EndpointACLdetails.png)
 
6.	Verwenden Sie die Zeilen in der Liste zum Hinzufügen, Löschen oder Bearbeiten von Regeln für eine ACL und Änderung ihrer Reihenfolge. Der **Remotesubnetz**-Wert ist ein IP-Adressbereich für eingehenden Datenverkehr aus dem Internet, den der Azure-Lastenausgleich zulassen oder verweigern wird, abhängig von der Quell-IP-Adresse des Datenverkehrs. Sie müssen den IP-Adressbereich im CIDR-Format, auch bekannt als Präfix Adressformat, angeben. Ein Beispiel ist 131.107.0.0/16. 

Sie können Regeln verwenden, um nur Verkehr von bestimmten Computern zuzulassen, die Ihren Computern im Internet entsprechen  oder um Datenverkehr von bestimmten, bekannten Adressbereichen zu verweigern.

Die Regeln werden der Reihe nach, beginnend mit der ersten und endend mit der letzten Regel, bewertet. Dies bedeutet, dass die Regeln gemäß der Restriktivität geordnet werden sollen. Beispiele und Weitere Informationen finden Sie unter [über Network Access Control Lists,](http://go.microsoft.com/fwlink/p/?linkid=303816&clcid=0x409).

PowerShell-Konfiguration von ACLs finden Sie unter [Verwalten von Zugriffssteuerungslisten (ACLs) für Endpunkte mithilfe von PowerShell](https://msdn.microsoft.com/library/azure/dn376543.aspx).

##Zusätzliche Ressourcen

[Lastenausgleich virtueller Computer](http://www.windowsazure.com/manage/windows/common-tasks/how-to-load-balance-virtual-machines/)

[Informationen zu Netzwerk-Zugriffssteuerungslisten](http://go.microsoft.com/fwlink/p/?linkid=303816&clcid=0x409)

[Informationen zu Netzwerk-Sicherheitsgruppen](https://msdn.microsoft.com/library/azure/dn848316.aspx)



<!--HONumber=45--> 
