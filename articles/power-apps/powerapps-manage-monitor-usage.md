<properties
	pageTitle="Überprüfen Ihrer App-Nutzung in PowerApps Enterprise | Microsoft Azure"
	description="Finden Sie alle Apps, APIs, Benutzer, App-Anforderungen und Aktualisierungsberechtigungen im Azure-Portal"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>


# Verwalten und Schützen Ihrer PowerApps
Sie erstellen Ihre App Service-Umgebung und fügen APIs sowie deren Verbindungen hinzu. Benutzer in Ihrer Organisation können nun damit beginnen, diese APIs und Verbindungen zu nutzen. Außerdem können Sie alle Apps verwalten, die in Ihrer Organisation erstellt wurden. Die Optionen umfassen:

- Anzeigen der verschiedenen Apps in Ihrer App Service-Umgebung, einschließlich PowerApps, Web-Apps, Logik-Apps, mobilen Apps usw.
- Anzeigen aller APIs, die von bestimmten Apps verwendet werden
- Anzeigen und Verwalten des Benutzerzugriffs auf die Apps in der App Service-Umgebung 
- Anzeigen und Verwalten des Benutzerzugriffs auf die Apps und deren Verbindungen 

Es sei daran erinnert, dass Ihre App Service-Umgebung die Stelle ist, an der Sie weitere Apps hinzufügen, einschließlich Web-Apps und Logik-Apps. Sie können dann PowerApps Enterprise öffnen, um diese Apps anzuzeigen und zu verwalten .


## Hinzufügen von PowerApps-Administratoren
Nachdem PowerApps Enterprise aktiviert ist, können Sie Administratoren hinzufügen sowie andere Apps in Ihrer App Service-Umgebung überwachen.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) den Eintrag **PowerApps**.
2. Wählen Sie **Settings** aus.
3. Wählen Sie in **Settings** die Option **Admin** aus: ![][1]  
4. Wählen Sie in **Users** die Option **Add** aus.
5. Wählen Sie die Rolle **Besitzer** aus: ![][2]  

	> [AZURE.IMPORTANT]Sie müssen die Rolle **Besitzer** auswählen, wenn Sie eine Person als PowerApps-Administrator zuweisen möchten. Keine der anderen aufgeführten Rollen bietet Benutzern vollständigen Zugriff, um PowerApps zu verwalten.

6. Wählen Sie Ihre Benutzer oder Gruppen aus.
7. Wählen Sie **OK** aus, um die Schritte ausführen.

Wenn Sie Administratoren zu PowerApps Enterprise hinzufügen, können die Benutzer und Gruppen, die Sie als Administratoren hinzufügen, folgende Aufgaben ausführen:

- Hinzufügen anderer Benutzer als PowerApps-Administratoren
- Verwalten von allen Apps sowie von deren Benutzerzugriff
- Ändern der Abrechnung: nein

> [AZURE.IMPORTANT]PowerApps-Administratoren können solange keine Änderungen an der App Service-Umgebung vornehmen, bis ihnen die Besitzerrolle für die Ressourcengruppe der App Service-Umgebung zugewiesen wurde. Die Vorgehensweise hierzu finden Sie [in diesem Artikel](powerapps-get-started-azure-portal.md).

Sobald den PowerApps-Administratoren die Besitzerrolle für die Ressourcengruppe der App Service-Umgebung zugewiesen wurde, können sie ebenfalls folgenden Aufgaben ausführen:

- Erstellen und Konfigurieren von APIs und deren Verbindungen
- Ändern der PowerApps-Einstellungen, einschließlich der App Service-Umgebung
- Hinzufügen weiterer Benutzer und Gruppen sowie für diese Benutzer und Gruppen Zuweisen von Rollen und Berechtigungen für die APIs, deren Verbindungen und die App Service-Umgebung 


## Verwalten Ihrer PowerApps und anderer Arten von Apps
Sobald Sie PowerApps und Ihrer App Service-Umgebung aktiviert haben, können Sie weitere Apps, etwa Web-Apps und Logik-Apps, zur selben App Service-Umgebung hinzufügen. Nachdem Sie dies getan haben, werden die Apps unter *All apps* zusammen mit den Apps aufgelistet, die in PowerApps erstellt wurden. Sie können auf jede Art von App klicken und die Liste der Apps durchlaufen.

### Anzeigen und Verwalten Ihrer PowerApps

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) den Eintrag **PowerApps**.
2. Wählen Sie auf der Kachel **All apps** den Eintrag **PowerApps** aus: ![][3]  
3. Wählen Sie die App aus, zu der Sie Details anzeigen möchten. Hierzu gehören:  
	- Die APIs, die die App verwendet
	- Benutzer und Gruppen, die Zugriff auf die App haben 
	- Die Analyse der App (in Kürze verfügbar)

#### Hinzufügen einer App

Es ist nicht möglich, eine App über das Azure-Portal hinzuzufügen. Derzeit wechseln Sie zum [PowerApps-Portal](http://go.microsoft.com/fwlink/p/?LinkId=715583).

#### Löschen Ihrer in PowerApps erstellten Apps
Als PowerApps-Administrator können Sie jede Anwendung, so auch Apps, die in PowerApps erstellt wurden, sowie andere Arten von Apps in Ihrer App Service-Umgebung löschen. Um eine App zu löschen, wählen Sie die Kachel **All apps** aus, wählen Sie die App aus, und wählen Sie dann **Delete** aus: ![][4]


#### Erteilen von Benutzern oder Gruppen Zugriff auf eine App
Als PowerApps-Administrator können Sie Benutzer und Gruppen zu PowerApps hinzufügen oder aus PowerApps entfernen.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) den Eintrag **PowerApps**.
2. Wählen Sie auf der Kachel **All apps** den Eintrag **PowerApps** aus: ![][3]  
3. Wählen Sie eine App aus, beispielsweise **Service Desk**. 
4. Wählen Sie in **Settings** die Option **App user access** aus: ![][5]  
5. Klicken Sie auf **Add**, um einen neuen Benutzer oder eine neue Gruppe hinzuzufügen. 
6. Wählen Sie eine Rolle aus:  
	- Kann bearbeiten
	- Kann anzeigen
7. Wählen Sie die Benutzer oder Gruppen aus.
8. Wählen Sie **OK** aus, um die Schritte ausführen.

### Anzeigen und Verwalten Ihrer Logik-Apps

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) den Eintrag **PowerApps**.
2. Wählen Sie auf der Kachel **All apps** den Eintrag **Logic apps** aus: ![][8]  
3. Wählen Sie die Logik-App aus, zu der Sie Details anzeigen möchten. Stellen Sie sicher, dass Sie das Korrekturabonnement für PowerApps auswählen, damit die richtigen Logik-Apps aufgelistet werden: ![][7]  

	> [AZURE.IMPORTANT]In der öffentlichen Vorschau sehen Sie möglicherweise einige Unstimmigkeiten in der Anzahl von Logik-Apps auf der Kachel für Durchsuchen und der Anzahl, die auf dem PowerApps-Hauptblatt angezeigt wird. Dies entspricht dem erwarteten Verhalten. Im Portal werden alle Logik-Apps für alle Hostingpläne angezeigt, und es erfolgt keine Filterung der Logik-Apps unter der App Service-Umgebung, die für PowerApps bereitgestellt wird. Dieses Verhalten wird in einem zukünftigen Update behoben.

	**Weitere Informationen zu Logik-Apps sowie dazu, wie diese verwaltet werden, finden Sie in [diesen Anleitungen](https://azure.microsoft.com/documentation/services/app-service/logic/).**

### Anzeigen und Verwalten Ihrer Web-Apps

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) den Eintrag **PowerApps**.
2. Wählen Sie auf der Kachel **All apps** den Eintrag **Web apps** aus: ![][9]  

	**Weitere Informationen zu Web-Apps sowie dazu, wie diese verwaltet werden, finden Sie in [diesen Anleitungen](https://azure.microsoft.com/documentation/services/app-service/web/).**

### Anzeigen und Verwalten Ihrer Mobile Apps

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) den Eintrag **PowerApps**.
2. Wählen Sie auf der Kachel **All apps** den Eintrag **Mobile apps** aus: ![][10]  

	**Weitere Informationen zu Mobile Apps sowie dazu, wie diese verwaltet werden, finden Sie in [diesen Anleitungen](https://azure.microsoft.com/documentation/services/app-service/mobile/).**


## Überprüfen der Sicherheitsoptionen
Es werden verschiedene Sicherheitsmethoden verwendet, je nachdem, was Sie tun. Folgendes sollten Sie wissen:

- **Abonnementadministrator**: Ein solcher Administrator verwaltet die Abrechnung und ist dafür verantwortlich, dass Ihr Unternehmen für PowerApps Enterprise registriert wird. Nur ein Abonnementadministrator kann anfordern, dass PowerApps im Azure-Abonnement Ihres Unternehmens aktiviert wird. 

- **Runtime user access**: (Benutzerzugriff zur Laufzeit) Es gibt drei verschiedene Arten von Benutzerzugriff zur Laufzeit:
	- **App user access**: (Benutzerzugriff auf App) Mit dieser Berechtigung wird gesteuert, ob der Benutzer der App diese *bearbeiten kann* oder *anzeigen kann*.
	- **API user access**: (Benutzerzugriff auf API) Diese Berechtigungen steuert den Laufzeitzugriff. Hat ein Benutzer diese Berechtigung, kann er die API in seiner App verwenden. Entweder hat ein Benutzer die Berechtigung, die API zur Laufzeit zu verwenden, oder er hat diese Berechtigung nicht. 
	- **Connection user access**: (Benutzerzugriff auf Verbindung) *Can view* (Kann anzeigen) und *Can edit* (Kann bearbeiten) sind die Laufzeitbenutzerberechtigungen, die für eine Verbindung verfügbar sind. Wenn Sie eine API (oder ein Verbindungsprofil) hinzufügen und deren Verbindung erstellen, gewähren Sie Benutzern und Gruppen diese speziellen Berechtigungen: ![][6]  

		Beispielsweise können Sie der Gruppe „Verkauf“ in Ihrem Unternehmen die Berechtigung *Can edit* für eine Verbindung mit einer SQL-Connector-API gewähren. Ein Benutzer mit der Berechtigung *Can edit* kann sowohl die Verbindung in seinen Apps verwenden als auch die Verbindungskonfiguration bearbeiten. Ein Benutzer mit der Berechtigung *Can view* kann die Verbindung in seinen Apps verwenden, kann die Verbindungskonfiguration, etwa die Verbindungszeichenfolge, aber nicht ändern.

- **Rollenbasierte Zugriffssteuerung** (RBAC): Für viele Azure-Angebote wird rollenbasierte Zugriffssteuerung verwendet, um zu bestimmen, wer welche Aktionen ausführen kann. In PowerApps wird RBAC an verschiedenen Stellen verwendet:
	- Wenn Sie das PowerApps-Portal zum ersten Mal öffnen, können Sie Benutzer hinzufügen und verwalten, die Administratoren der PowerApps sein sollten. 
	- Wenn Sie die App Service-Umgebung erstellen, fügen Sie Benutzer oder Gruppen zu PowerApps hinzu, und Sie können Benutzer oder Gruppen aus PowerApps entfernen. Beispielsweise können Sie bestimmte Administratorgruppen in Ihrem Unternehmen zur Rolle *Besitzer* hinzufügen, wodurch die Gruppen die Berechtigung erhalten, APIs und Verbindungen zu erstellen. Diese APIs und Verbindungen werden dann den Apps hinzugefügt, die in PowerApps erstellt werden.
	- Wenn Sie Benutzer zu Apps wie Web-Apps, Logik-Apps oder Mobile Apps hinzufügen, können Sie die Rolle für diese Benutzer wählen.  
		
		Ein Hinzufügen von Benutzern und Zuweisen von Rollen ist genau wie ein Verwenden der [rollenbasierten Zugriffssteuerung](../role-based-access-control-configure.md) in Azure. Dies sind einige Rollen:

		Rolle | Beschreibung
		--- | ---
		Mitwirkender | Kann alles verwalten, kann Benutzern aber keinen Zugriff gewähren.
		Leser | Kann alles anzeigen, jedoch keine Änderungen vornehmen.
		Besitzer | Kann alles verwalten und kann Benutzern Zugriff gewähren.

Mit diesen Rollen können Sie BenutzerA die Berechtigung **Can view** für eine tägliche Twitter-App und BenutzerB die Berechtigung **Can edit** für eine Pendelbus-App erteilen. Sie können BenutzerB Zugriff auf alle APIs erteilen. Sie können diese Rechte wirklich präzise erteilen oder jeden Benutzer mit einer bestimmten Rolle hinzufügen. Dies hängt tatsächlich von den Anforderungen Ihres Unternehmens ab.


## Zusammenfassung und nächste Schritte
In diesem Thema wurden die verschiedenen Optionen vorgestellt, mit denen Sie Ihre PowerApps und die Sicherheitsmethoden, die in PowerApps implementiert sind, verwalten können.

Nachdem Sie nun einiges Wissen zum Azure-Portal gesammelt haben, können Sie mit dem Erstellen Ihrer Apps beginnen. Dies sind gute Ausgangspunkte:

- [Create an app from a template in PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715536) 
- [Create an app from data in PowerApps](http://go.microsoft.com/fwlink/?LinkId=715539) 
- [Create an app from scratch in PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715538)


[1]: ./media/powerapps-manage-monitor-usage/addadmin.png
[2]: ./media/powerapps-manage-monitor-usage/selectrole.png
[3]: ./media/powerapps-manage-monitor-usage/PowerApps.png
[4]: ./media/powerapps-manage-monitor-usage/deleteapp.png
[5]: ./media/powerapps-manage-monitor-usage/appuseraccess.png
[6]: ./media/powerapps-manage-monitor-usage/selectpermission.png
[7]: ./media/powerapps-manage-monitor-usage/alllogicapps.png
[8]: ./media/powerapps-manage-monitor-usage/logicapps.png
[9]: ./media/powerapps-manage-monitor-usage/webapps.png
[10]: ./media/powerapps-manage-monitor-usage/mobileapps.png

<!---HONumber=AcomDC_1203_2015-->