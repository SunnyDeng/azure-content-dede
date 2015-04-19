<properties 
	pageTitle="Skalieren eines Cloud-Diensts - Azure" 
	description="Hier erfahren Sie, wie Sie einen Cloud-Dienst und verknüpfte Ressourcen in Azure skalieren." 
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
	ms.date="10/21/2014" 
	ms.author="adegeo"/>





# Skalieren einer Anwendung


Auf der Seite "Skalieren" des Azure Verwaltungsportals können Sie Ihre Anwendung manuell skalieren oder Parameter für die automatische Skalierung festlegen. Skaliert werden können Anwendungen, die Webrollen, Workerrollen oder virtuelle Computer ausführen. Sie skalieren eine Anwendung, die Instanzen von Webrollen und Workerrollen ausführt, indem Sie Rolleninstanzen hinzufügen oder entfernen, um die Arbeitslast zu bewältigen.

Bei der Skalierung einer Anwendung, die virtuelle Computer ausführt, werden keine neuen Computer erstellt oder gelöscht, sondern lediglich in einer Verfügbarkeitsgruppe vorhandene Computer aktiviert oder deaktiviert. Sie können festlegen, dass eine Skalierung abhängig von der durchschnittlichen prozentualen CPU-Nutzung oder abhängig von der Anzahl Nachrichten in einer Warteschlange durchgeführt wird.

Folgendes ist allerdings zu beachten, bevor die Skalierung einer Anwendung konfiguriert wird:

- Virtuelle Computer müssen nach der Erstellung zu einer Verfügbarkeitsgruppe hinzugefügt werden, um sie für die Skalierung einer Anwendung verwenden zu können. Die derart in eine Gruppe eingefügten virtuellen Computer können zunächst beliebig aktiviert oder deaktiviert sein. Beim Hochskalieren werden sie allerdings definitiv aktiviert und beim Herunterskalieren deaktiviert. Weitere Information zu virtuellen Computern und Verfügbarkeitsgruppen finden Sie unter [Verwaltung der Verfügbarkeit virtueller Computer](http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/)
- Die Skalierung ist abhängig von der Kernspeichernutzung. Größere Rolleninstanzen oder virtuelle Computer erfordern mehr Kernspeicher. Sie können eine Anwendung nur innerhalb der für Ihr Abonnement geltenden Kernspeichergrenzwerte skalieren. Wenn als Grenzwert für Ihr Abonnement beispielsweise zwanzig Kernspeicher festgelegt sind und Sie eine Anwendung mit zwei mittelgroßen virtuellen Computern ausführen (insgesamt vier Kernspeicher), stehen für das Hochskalieren anderer bereitgestellter Cloud-Dienste in Ihrem Abonnement nur noch sechzehn Kernspeicher zur Verfügung. Für die Skalierung einer Anwendung können nur gleich große virtuelle Computer einer Verfügbarkeitsgruppe verwendet werden. Weitere Informationen über Kernspeichernutzung und Größen von virtuellen Computern finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Microsoft Azure](http://msdn.microsoft.com/library/dn197896.aspx)
- Sie müssen eine Warteschlange anlegen und diese einer Rolle oder einer Verfügbarkeitsgruppe zuweisen, bevor Sie eine Anwendung auf Basis eines Nachrichtenschwellwerts skalieren können. Weitere Informationen finden Sie unter [Verwenden des Warteschlangenspeicherdiensts](http://azure.microsoft.com/develop/net/how-to-guides/queue-service)
- Sie können Ressourcen skalieren, die mit Ihrem Cloud-Dienst verknüpft sind. Weitere Informationen zum Verknüpfen von Ressourcen finden Sie unter [Gewusst wie: Verknüpfen einer Ressource mit einem Cloud-Dienst](http://azure.microsoft.com/manage/services/cloud-services/how-to-manage-a-cloud-service/#linkresources).
- Um die Hochverfügbarkeit Ihrer Anwendung zu gewährleisten, sollte diese mit mindestens zwei Rolleninstanzen bzw. virtuellen Computern bereitgestellt werden. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](https://www.windowsazure.com/de-de/support/legal/sla/).

Folgende Skalierungsaktionen können für einen Cloud-Dienst ausgeführt werden:

- [Manuelles Skalieren einer für die Ausführung von Webrollen und Workerrollen konzipierten Anwendung](#manualscale)
- [Automatisches Skalieren einer für die Ausführung von Webrollen, Workerrollen oder virtuellen Computern konzipierten Anwendung](#autoscale)
- [Skalieren verknüpfter Ressourcen](#scalelink)
- [Planen der Anwendungsskalierung](#schedule)


<h2><a id="manualscale"></a>Manuelles Skalieren einer für die Ausführung von Webrollen und Workerrollen konzipierten Anwendung</h2>

Auf der Skalierungsseite können Sie die Anzahl der in einem Cloud-Dienst laufenden Instanzen manuell erhöhen oder verringern.

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Cloud-Dienste** und dann auf den Namen des Cloud-Diensts, um das Dashboard zu öffnen.

2. Klicken Sie auf **Skalieren**. Die automatische Skalierung ist standardmäßig für alle Rollen deaktiviert, sodass Sie die Anzahl der von Ihrer Anwendung verwendeten Instanzen manuell ändern können.

	![Scale page][manual_scale]

3. Jede Rolle in einem Cloud-Dienst hat einen Regler, über den die Anzahl der verwendbaren Instanzen geändert werden kann. Um eine Rolleninstanz hinzuzufügen, ziehen Sie den Regler nach rechts. Um eine Rolleninstanz zu entfernen, ziehen Sie den Regler nach links.

	![Scale role][slider_role] 


	Sie können die Anzahl der verwendeten Instanzen nur dann erhöhen, wenn ausreichend Kernspeicher für die Unterstützung dieser Instanzen zur Verfügung stehen. Die Farben des Reglers lassen erkennen, wie viele Kernspeicher Ihres Abonnements belegt bzw. noch verfügbar sind:

	- Blau steht für die Kernspeicher, die von der ausgewählten Rolle genutzt werden.
	- Dunkelgrau steht für die Gesamtzahl der Kernspeicher, die von allen Rollen und virtuellen Computern in dem Abonnement genutzt werden.
	- Hellgrau steht für die Kernspeicher, die für die Skalierung zur Verfügung stehen.
	- Pink steht für eine Änderung, die noch nicht gespeichert worden ist.

4. Klicken Sie auf **Speichern**. Je nach Reglerstellung werden Rolleninstanzen hinzugefügt oder entfernt.

<h2><a id="autoscale"></a>Automatisches Skalieren einer für die Ausführung von Webrollen, Workerrollen oder virtuellen Computern konzipierten Anwendung</h2>

Auf der Skalierungsseite können Sie Ihren Cloud-Dienst so konfigurieren, dass die Anzahl der von Ihrer Anwendung verwendeten Instanzen oder virtuellen Computer automatisch erhöht oder verringert wird. Für diese Konfiguration der Skalierung stehen folgende Parameter zur Verfügung:

- [Durchschnittliche CPU-Nutzung](#averagecpu) - Wenn die durchschnittliche prozentuale CPU-Nutzung bestimmte Schwellenwerte über- oder unterschreitet, werden Rolleninstanzen erstellt oder gelöscht oder virtuelle Computer in einer Verfügbarkeitsgruppe aktiviert oder deaktiviert.
- [Warteschlangennachrichten](#queuemessages) - Wenn die Anzahl der Nachrichten in einer Warteschlange bestimmte Schwellenwerte über- oder unterschreitet, werden Rolleninstanzen erstellt oder gelöscht oder virtuelle Computer in einer Verfügbarkeitsgruppe aktiviert oder deaktiviert.

<h3><a id="averagecpu"></a>Durchschnittliche CPU-Nutzung</h3>

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Cloud-Dienste** und dann auf den Namen des Cloud-Diensts, um das Dashboard zu öffnen.
2. Klicken Sie auf **Skalieren**.
3. Führen Sie einen Bildlauf zu dem Abschnitt für die Rolle bzw. Verfügbarkeitsgruppe aus, und klicken Sie dann auf **CPU**. Dann kann Ihre Anwendung basierend auf der durchschnittlichen prozentualen Nutzung von CPU-Ressourcen automatisch skaliert werden.

	![Autoscale on][autoscale_on]

4. Jede Rolle bzw. Verfügbarkeitsgruppe hat einen Regler, über den die Anzahl der verwendbaren Instanzen geändert werden kann. Um die maximale Anzahl verwendbarer Instanzen einzustellen, ziehen Sie den rechten Regler nach rechts. Um die Mindestanzahl verwendbarer Instanzen einzustellen, ziehen Sie den linken Regler nach links.

	**Hinweis:** Auf der Skalierungsseite steht **Instanz** entweder für eine Rolleninstanz oder für eine Instanz eines virtuellen Computers.

	![Instance range][instance_range]

	Die maximale Anzahl Instanzen ist durch die Anzahl der im Abonnement verfügbaren Kernspeicher beschränkt. Die Farben des Reglers lassen erkennen, wie viele Kernspeicher Ihres Abonnements belegt bzw. noch verfügbar sind:

	- Blau steht für die maximale Anzahl der Kernspeicher, die die Rolle nutzen kann.
	- Dunkelgrau steht für die Gesamtzahl der Kernspeicher, die von allen Rollen und virtuellen Computern in dem Abonnement genutzt werden. Wenn dieser Wert die von der Rolle verwendeten Kernspeicher überlappt, ändert sich die Farbe in Dunkelblau.
	- Hellgrau steht für die Kernspeicher, die für die Skalierung zur Verfügung stehen.
	- Pink steht für eine Änderung, die noch nicht gespeichert worden ist.

5. Durch Verschieben eines Reglers wird der Bereich der durchschnittlichen prozentualen CPU-Nutzung eingestellt. Wenn die durchschnittliche prozentuale CPU-Nutzung den Höchstwert überschreitet, werden zusätzliche Rolleninstanzen erstellt bzw. virtuelle Computer aktiviert. Wenn die durchschnittliche prozentuale CPU-Nutzung den Mindestwert unterschreitet, werden Rolleninstanzen gelöscht bzw. virtuelle Computer deaktiviert. Um den Höchstwert der durchschnittlichen prozentualen CPU-Nutzung einzustellen, ziehen Sie den rechten Regler nach rechts. Um den Mindestwert der durchschnittlichen prozentualen CPU-Nutzung einzustellen, ziehen Sie den linken Regler nach links.

	![Target cpu][target_cpu]

6. Sie können die Anzahl der Instanzen festlegen, die beim Hochskalieren Ihrer Anwendung jeweils hinzugefügt bzw. aktiviert werden sollen. Um diese Anzahl zu erhöhen, ziehen Sie den Regler nach rechts. Um sie zu verringern, ziehen Sie den Regler nach links.

	![Scale cpu up][scale_cpuup]

7. Geben Sie die Wartezeit zwischen der letzten Skalierungsaktion und dem nächsten Hochskalieren in Minuten ein. Bei der letzten Skalierungsaktion kann es sich entweder um ein Hoch- oder Herunterskalieren handeln.

	![Up time][scale_uptime]

	Bei der Berechnung der durchschnittlichen prozentualen CPU-Nutzung werden alle Instanzen erfasst. Der Durchschnittswert basiert auf der Nutzung in der jeweils vorhergehenden Stunde. Je nachdem, wie viele Instanzen Ihre Anwendung verwendet, und wenn die festgelegte Wartezeit sehr kurz ist, kann es länger dauern, bis die nächste Skalierungsaktion stattfindet. Das Mindestintervall zwischen Skalierungsaktionen ist fünf Minuten. Skalierungsaktionen können nicht stattfinden, wenn sich eine oder mehrere Instanzen in einem Übergangszustand befinden.

8. Sie können auch die Anzahl der Instanzen festlegen, die beim Herunterskalieren Ihrer Anwendung jeweils gelöscht bzw. deaktiviert werden sollen.  Um diese Anzahl zu erhöhen, ziehen Sie den Regler nach rechts. Um sie zu verringern, ziehen Sie den Regler nach links.

	![Scale cpu down][scale_cpudown]

	Wenn bei Ihrer Anwendung ein plötzlicher Anstieg der CPU-Nutzung auftreten kann, müssen Sie sicherstellen, dass eine ausreichende Mindestanzahl von Instanzen verfügbar ist, um diese Nutzungsspitzen abzufangen.

9. Geben Sie die Wartezeit zwischen der letzten Skalierungsaktion und dem nächsten Herunterskalieren in Minuten ein. Bei der letzten Skalierungsaktion kann es sich entweder um ein Hoch- oder Herunterskalieren handeln.

	![Down time][scale_downtime]

10. Klicken Sie auf **Speichern**. Die Skalierungsaktion kann bis zu fünf Minuten dauern.

<h3><a id="queuemessages"></a>Warteschlangennachrichten</h3>

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Cloud-Dienste** und dann auf den Namen des Cloud-Diensts, um das Dashboard zu öffnen.
2. Klicken Sie auf **Skalieren**.
3. Führen Sie einen Bildlauf zu dem Abschnitt für die Rolle bzw. Verfügbarkeitsgruppe aus, und klicken Sie dann auf **Warteschlange**. Dann kann Ihre Anwendung basierend auf einer vorgegebenen Anzahl Warteschlangennachrichten automatisch skaliert werden.

	![Scale queue][scale_queue]

4. Jede im Cloud-Dienst definierte Rolle bzw. Verfügbarkeitsgruppe hat einen Regler, über den die Anzahl der verwendbaren Instanzen geändert werden kann. Um die maximale Anzahl verwendbarer Instanzen einzustellen, ziehen Sie den rechten Regler nach rechts. Um die Mindestanzahl verwendbarer Instanzen einzustellen, ziehen Sie den linken Regler nach links.

	![Queue range][queue_range]

	**Hinweis:** Auf der Skalierungsseite steht **Instanz** entweder für eine Rolleninstanz oder für eine Instanz eines virtuellen Computers.
	
	Die maximale Anzahl Instanzen ist durch die Anzahl der im Abonnement verfügbaren Kernspeicher beschränkt. Die Farben des Reglers lassen erkennen, wie viele Kernspeicher Ihres Abonnements belegt bzw. noch verfügbar sind:
	- Blau steht für die maximale Anzahl der Kernspeicher, die die Rolle nutzen kann.
	- Dunkelgrau steht für die Gesamtzahl der Kernspeicher, die von allen Rollen und virtuellen Computern in dem Abonnement genutzt werden. Wenn dieser Wert die von der Rolle verwendeten Kernspeicher überlappt, ändert sich die Farbe in Dunkelblau.
	- Hellgrau steht für die Kernspeicher, die für die Skalierung zur Verfügung stehen.
	- Pink steht für eine Änderung, die noch nicht gespeichert worden ist.

5. Wählen Sie das Speicherkonto der Warteschlange aus, die Sie verwenden wollen.

	![Storage name][storage_name]	

6. Wählen Sie die Warteschlange aus.

	![Queue name][queue_name]

7. Geben Sie die Anzahl der Nachrichten ein, die jede Instanz voraussichtlich unterstützen muss. Die Skalierung der Instanzen erfolgt auf Basis der Gesamtzahl Nachrichten, dividiert durch die vorgegebene Anzahl Nachrichten pro Computer.

	![Message number][message_number]

8. Sie können die Anzahl der Instanzen festlegen, die beim Hochskalieren Ihrer Anwendung jeweils hinzugefügt bzw. aktiviert werden sollen. Um diese Anzahl zu erhöhen, ziehen Sie den Regler nach rechts. Um sie zu verringern, ziehen Sie den Regler nach links.

	![Scale cpu up][scale_cpuup]

9. Geben Sie die Wartezeit zwischen der letzten Skalierungsaktion und dem nächsten Hochskalieren in Minuten ein. Bei der letzten Skalierungsaktion kann es sich entweder um ein Hoch- oder Herunterskalieren handeln.

	![Up time][scale_uptime]

	Das Mindestintervall zwischen Skalierungsaktionen ist fünf Minuten. Skalierungsaktionen können nicht stattfinden, wenn sich eine oder mehrere Instanzen in einem Übergangszustand befinden.

10. Sie können auch die Anzahl der Instanzen festlegen, die beim Herunterskalieren Ihrer Anwendung jeweils gelöscht bzw. nicht mehr verwendet werden sollen.  Das Skalierungsinkrement kann über einen Regler eingestellt werden. Um die Anzahl der beim Herunterskalieren der Anwendung gelöschten bzw. nicht mehr verwendeten Instanzen zu erhöhen, ziehen Sie den Regler nach rechts. Um sie zu verringern, ziehen Sie den Regler nach links.

	![Scale cpu down][scale_cpudown]

11.	Geben Sie die Wartezeit zwischen der letzten Skalierungsaktion und dem nächsten Herunterskalieren in Minuten ein. Bei der letzten Skalierungsaktion kann es sich entweder um ein Hoch- oder Herunterskalieren handeln.

	![Down time][scale_downtime]

12. Klicken Sie auf **Speichern**. Die Skalierungsaktion kann bis zu fünf Minuten dauern.

<h2><a id="scalelink"></a>Skalieren verknüpfter Ressourcen</h2>

Häufig empfiehlt es sich, beim Skalieren einer Rolle auch die von der Anwendung verwendete Datenbank zu skalieren. Wenn Sie die Datenbank mit dem Cloud-Dienst verknüpfen, ändern Sie die Edition der SQL-Datenbank und die Größe der Datenbank auf der Skalierungsseite.

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Cloud-Dienste** und dann auf den Namen des Cloud-Diensts, um das Dashboard zu öffnen.
2. Klicken Sie auf **Skalieren**.
3. Wählen Sie im Abschnitt "Linkes Resources" die für die Datenbank zu verwendende Edition aus.

	![Linked resources][linked_resources]

4. Wählen Sie die Größe der Datenbank aus.
5. Klicken Sie auf **Speichern**, um die verknüpften Ressourcen zu aktualisieren.

<h2><a id="schedule"></a>Planen der Anwendungsskalierung</h2>

Sie können die automatische Skalierung Ihrer Anwendung planen, indem Sie Zeitpläne mit bestimmten Skalierungszeiten konfigurieren. Für die automatische Skalierung sind die folgenden Optionen verfügbar:

- **No schedule**: Dies ist die Standardoption, d. h. Ihre Anwendung wird immer auf dieselbe Weise skaliert.

- **Day and night**: Diese Option erlaubt die Festlegung der Skalierung zu bestimmten Zeiten während des Tages und der Nacht.

**Hinweis:** Für Anwendungen, die virtuelle Computer verwenden, werden Zeitpläne derzeit noch nicht unterstützt.

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Cloud-Dienste** und dann auf den Namen des Cloud-Diensts, um das Dashboard zu öffnen.
2. Klicken Sie auf **Skalieren**.
3. Klicken Sie in der Skalierungsseite auf **Zeiten für Zeitplan einrichten**.

	![Schedule scaling][scale_schedule]

4. Wählen Sie den Skalierungszeitplantyp aus, den Sie einrichten möchten.

5. Geben die Zeiten für den Tagesanfang und das Tagesende ein, und legen Sie die Zeitzone fest. Für die Planung von Tages- und Nachtzeiten gilt, dass diese Zeiten den Anfang und das Ende des betreffenden Tages markieren und die verbleibenden Zeiten die Nacht darstellen.

6. Klicken Sie auf das Häkchen unten auf der Seite, um die Zeitpläne zu speichern.

7. Nachdem Sie die Zeitpläne gespeichert haben, werden diese in der Liste angezeigt. Sie können einen Zeitplan auswählen, den Sie verwenden möchten, und dann die Skalierungseinstellungen ändern. Die Skalierungseinstellungen kommen nur während des ausgewählten Zeitplans zur Anwendung. Zum Bearbeiten von Zeitplänen klicken Sie auf **Zeiten für Zeitplan einrichten**.

[manual_scale]: ./media/cloud-services-how-to-scale/CloudServices_ManualScaleRoles.png
[slider_role]: ./media/cloud-services-how-to-scale/CloudServices_SliderRole.png
[autoscale_on]: ./media/cloud-services-how-to-scale/CloudServices_AutoscaleOn.png
[instance_range]: ./media/cloud-services-how-to-scale/CloudServices_InstanceRange.png
[target_cpu]: ./media/cloud-services-how-to-scale/CloudServices_TargetCPURange.png
[scale_cpuup]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpBy.png
[scale_uptime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpWaitTime.png
[scale_cpudown]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownBy.png
[scale_downtime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownWaitTime.png
[scale_queue]: ./media/cloud-services-how-to-scale/CloudServices_QueueScale.png
[queue_range]: ./media/cloud-services-how-to-scale/CloudServices_QueueRange.png
[storage_name]: ./media/cloud-services-how-to-scale/CloudServices_StorageAccountName.png
[queue_name]: ./media/cloud-services-how-to-scale/CloudServices_QueueName.png
[message_number]: ./media/cloud-services-how-to-scale/CloudServices_TargetMessageNumber.png
[linked_resources]: ./media/cloud-services-how-to-scale/CloudServices_ScaleLinkedResources.png
[scale_schedule]: ./media/cloud-services-how-to-scale/CloudServices_SetUpSchedule.png

<!--HONumber=45--> 
