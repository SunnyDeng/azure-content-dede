<properties 
	pageTitle="Erste Schritte mit Web-Apps in Azure App Service – Teil 2" 
	description="Fügen Sie der Web-App in App Service mit wenigen Klicks wichtige Funktionen für den Betrieb hinzu." 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="03/17/2016" 
	ms.author="cephalin"
/>

# Erste Schritte mit Azure App Service – Teil 2

Unter [Erste Schritte mit Azure App Service](app-service-web-get-started.md) haben Sie eine Web-App für Azure App Service bereitgestellt und können nun schnell und einfach Updates durchführen. In diesem Artikel fügen Sie der bereitgestellten App schnell einige wichtige Funktionen hinzu, die dem reibungslosen Betrieb dienen. So finden Sie selbst heraus, dass es sich bei App Service um viel mehr als einen Website-Hoster handelt. Ihre hervorragende App wird auf diese Weise mit Funktionen auf Unternehmensniveau ausgestattet. Sie können sich auf Ihre kreative Energie konzentrieren und die Benutzer mit Ihrer App erfreuen, anstatt sich um so grundlegende Dinge wie Sicherheit, Skalierbarkeit, Leistung, Verwaltung usw. zu kümmern.

Mit wenigen Klicks lernen Sie Folgendes:

- Erzwingen der Authentifizierung für Benutzer
- Automatisches Skalieren der App
- Empfangen von Warnungen zur Leistung der App

Die Schritte des Tutorials sind unabhängig davon, welche Beispiel-App Sie im vorherigen Artikel bereitgestellt haben, für Sie geeignet.

## Authentifizieren der Benutzer

Wir zeigen Ihnen nun, wie einfach es ist, der App eine Authentifizierung hinzuzufügen.

1. Klicken Sie im Portalblatt für Ihre App, das Sie gerade geöffnet haben, auf **Einstellungen** > **Authentifizierung/Autorisierung**. ![Authentifizieren – Blatt „Einstellungen“](./media/app-service-web-get-started/aad-login-settings.png)
    
2. Klicken Sie auf **Ein**, um die Authentifizierung zu aktivieren. ![Authentifizieren – Aktivieren](./media/app-service-web-get-started/aad-login-auth-on.png)
    
4. Klicken Sie unter **Authentifizierungsanbieter** auf **Azure Active Directory**. ![Authentifizieren – Azure AD auswählen](./media/app-service-web-get-started/aad-login-config.png)

5. Klicken Sie auf dem Blatt **Azure Active Directory-Einstellungen** auf **Express** und dann auf **OK**. Mit den Standardeinstellungen wird eine neue Azure AD-Anwendung in Ihrem Standardverzeichnis erstellt. ![Authentifizieren – Expresskonfiguration](./media/app-service-web-get-started/aad-login-express.png)

6. Klicken Sie auf **Speichern**. ![Authentifizieren – Konfiguration speichern](./media/app-service-web-get-started/aad-login-save.png)

    Nach einer erfolgreichen Änderung sehen Sie, dass die Benachrichtigungsglocke grün und zusätzlich eine benutzerfreundliche Nachricht angezeigt wird.

7. Klicken Sie im Portalblatt Ihrer App auf den Link **URL** (oder in der Menüleiste auf **Durchsuchen**). Der Link ist eine HTTP-Adresse. ![Authentifizieren – Zu URL navigieren](./media/app-service-web-get-started/aad-login-browse-click.png) Aber nachdem die App in einer neuen Registerkarte geöffnet wird, werden über das Feld „URL“ mehrere Umleitungen durchgeführt, und schließlich wird Ihre App mit einer HTTPS-Adresse angegeben. Sie sehen, dass Sie mit Ihrem Azure-Abonnement bereits am Microsoft-Konto angemeldet sind, und Sie werden mit diesem Konto automatisch an der App angemeldet. ![Authentifizieren – Angemeldet](./media/app-service-web-get-started/aad-login-browse-http-postclick.png) Wenn Sie jetzt also eine nicht authentifizierte Sitzung in einem anderen Browser öffnen, wird ein Anmeldebildschirm angezeigt, sobald Sie zu derselben URL navigieren: ![Authentifizieren – Anmeldeseite](./media/app-service-web-get-started/aad-login-browse.png) Falls Sie mit Azure Active Directory noch nicht gearbeitet haben, enthält Ihr Standardverzeichnis ggf. keine Azure AD-Benutzer. In diesem Fall ist das einzige darin enthaltene Konto wahrscheinlich das Microsoft-Konto mit Ihrem Azure-Abonnement. Dies ist der Grund dafür, warum Sie in demselben Browser zuvor automatisch an der App angemeldet wurden. Sie können dasselbe Microsoft-Konto verwenden, um sich auf dieser Anmeldeseite anzumelden.

Herzlichen Glückwunsch! Der gesamte eingehende Datenverkehr wird für Ihre Website jetzt authentifiziert.

Vielleicht ist Ihnen aufgefallen, dass Sie auf dem Blatt **Authentifizierung/Autorisierung** noch weitere Optionen haben, z. B.:

- Aktivieren der Anmeldung für soziale Netzwerke
- Aktivieren mehrerer Anmeldeoptionen
- Ändern des Standardverhaltens, wenn Benutzer zum ersten Mal zu Ihrer App navigieren

App Service umfasst eine fertige Lösung für einige häufige Authentifizierungsanforderungen, sodass Sie die Authentifizierungslogik nicht selbst bereitstellen müssen. Weitere Informationen finden Sie unter [App Service-Authentifizierung/Autorisierung](/blog/announcing-app-service-authentication-authorization/).

## Zentrales und horizontales Hochskalieren der App

Als Nächstes führen wir die Skalierung für die App durch. Sie haben zwei Möglichkeiten, wie Sie Ihre App Service-App skalieren können:

- [Zentrales Hochskalieren](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Wenn Sie eine App Service-App zentral hochskalieren, ändern Sie den Tarif des App Service-Plans, zum dem die App gehört. Durch das zentrale Hochskalieren erhalten Sie mehr CPU, Arbeitsspeicher, Datenträgerplatz sowie weitere Features wie dedizierte VM-Instanzen, automatische Skalierung, SLA mit 99,95%, benutzerdefinierte Domänen, benutzerdefinierte SSL-Zertifikate, Bereitstellungsslots, Sicherung, Wiederherstellung usw. Höhere Tarife sind gleichbedeutend mit mehr Features für die App Service-App.  
- [Horizontales Skalieren](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Wenn Sie eine App Service-App horizontal hochskalieren, ändern Sie die Anzahl von VM-Instanzen, auf denen die App (bzw. Apps in demselben App Service-Plan) ausgeführt wird. Beim Standard-Tarif oder höher können Sie die automatische Skalierung von VM-Instanzen basierend auf Leistungsmetriken ermöglichen. 

Wir beginnen jetzt direkt mit dem Einrichten der automatischen Skalierung für die App.

1. Zuerst skalieren wir zentral hoch, um die automatische Skalierung zu ermöglichen. Klicken Sie im Portalblatt Ihrer App auf **Einstellungen** > **Zentral hochskalieren (App Service-Plan)**. ![Zentral hochskalieren – Blatt „Einstellungen“](./media/app-service-web-get-started/scale-up-settings.png)

2. Führen Sie einen Bildlauf durch, und wählen Sie den Tarif **S1 Standard**. Dies ist der niedrigste Tarif, bei dem die automatische Skalierung unterstützt wird (im Screenshot eingekreist). Klicken Sie anschließend auf **Auswählen**. ![Zentral hochskalieren – Tarif wählen](./media/app-service-web-get-started/scale-up-select.png)

    Sie sind mit dem zentralen Hochskalieren jetzt fertig.
    
    >[AZURE.IMPORTANT] Mit diesem Tarif wird Ihr Guthaben für eine kostenlose Testversion aufgebraucht. Falls Sie über ein Konto mit nutzungsbasierter Bezahlung verfügen, fallen Kosten für Ihr Konto an.
    
3. Als Nächstes konfigurieren wir die automatische Skalierung. Klicken Sie im Portalblatt Ihrer App auf **Einstellungen** > **Horizontal hochskalieren (App Service-Plan)**. ![Horizontal hochskalieren – Blatt „Einstellungen“](./media/app-service-web-get-started/scale-out-settings.png)

4. Ändern Sie **Skalieren nach** in **CPU-Prozentsatz**. Die Schieberegler unterhalb der Dropdownliste ändert sich entsprechend. Legen Sie für den Bereich **Instanzen** anschließend einen Wert zwischen **1** und **2** und für den **Zielbereich** einen Wert zwischen **40** und **80** fest. Geben Sie hierfür Werte in die Felder ein, oder verwenden Sie die Schieberegler. ![Horizontal hochskalieren – Automatische Skalierung konfigurieren](./media/app-service-web-get-started/scale-out-configure.png)
    
    Basierend auf dieser Konfiguration wird Ihre App automatisch horizontal hochskaliert, wenn die CPU-Auslastung über 80% liegt, und horizontal herunterskaliert, wenn die CPU-Auslastung unter 40% liegt.
    
5. Klicken Sie in der Menüleiste auf **Speichern**.

Herzlichen Glückwunsch! Die automatische Skalierung wird für Ihre App durchgeführt.

Vielleicht haben Sie im Blatt **Skalierungseinstellungen** bemerkt, dass Sie noch weitere Optionen haben, z.B.:

- Manuelles Skalieren auf eine bestimmte Anzahl von Instanzen
- Skalieren nach anderen Leistungsmetriken, z.B. Arbeitsspeicherprozentsatz oder Warteschlange des Datenträgers
- Anpassen des Skalierungsverhaltens bei Auslösung einer Leistungsregel
- Automatisches Skalieren nach einem Zeitplan
- Festlegen der automatischen Skalierung für ein zukünftiges Ereignis

Weitere Informationen zum zentralen Hochskalieren der App finden Sie unter [Ändern der Preisstufe in Azure App Service](../app-service/app-service-scale.md). Weitere Informationen zum horizontalen Hochskalieren finden Sie unter [Manuelles oder automatisches Skalieren der Instanzen](../azure-portal/insights-how-to-scale.md).

## Empfangen von Warnungen für die App

Nachdem für die App jetzt die automatische Skalierung durchgeführt wird, stellt sich folgende Frage: Was passiert, wenn die maximale Instanzanzahl (2) erreicht ist und die CPU-Auslastung über dem gewünschten Wert (80%) liegt? Sie können eine Warnung einrichten, mit der Sie über diese Situation informiert werden, damit Sie beispielsweise das zentrale Hochskalieren für die App erweitern können. Wir richten jetzt schnell eine entsprechende Warnung für dieses Szenario ein.

1. Klicken Sie im Portalblatt Ihrer App auf **Extras** > **Warnungen**. ![Warnungen – Blatt „Einstellungen“](./media/app-service-web-get-started/alert-settings.png)

2. Klicken Sie auf **Warnung hinzufügen**. Wählen Sie im Feld **Ressource** die Ressource aus, die auf **(serverfarms)** endet. Dies ist Ihr App Service-Plan. ![Warnungen – Warnung für App Service-Plan hinzufügen](./media/app-service-web-get-started/alert-add.png)

3. Geben Sie **Name** als `CPU Maxed`, **Metrik** als **CPU-Prozentsatz** und **Schwellenwert** als `90` an. Wählen Sie anschließend **E-Mail-Besitzer, Mitwirkende und Leser**, und klicken Sie dann auf **OK**. ![Warnungen – Warnung konfigurieren](./media/app-service-web-get-started/alert-configure.png)
    
    Wenn Azure die Erstellung der Warnung abgeschlossen hat, wird sie im Blatt **Warnungen** angezeigt. ![Warnungen – Fertige Ansicht](./media/app-service-web-get-started/alert-done.png)

Herzlichen Glückwunsch! Sie erhalten nun Warnungen.

Mit dieser Warnungseinstellung wird die CPU-Auslastung alle fünf Minuten überprüft. Wenn die Anzahl 90 % überschreitet, erhalten Sie und alle anderen autorisierten Personen eine E-Mail als Warnung. Um alle Personen anzuzeigen, die für den Empfang der Warnungen autorisiert sind, wechseln Sie zurück zum Portalblatt Ihrer App und klicken auf die Schaltfläche **Zugang**. ![Empfänger von Warnungen anzeigen](./media/app-service-web-get-started/alert-rbac.png)

Es sollte zu sehen sein, dass **Abonnement-Administratoren** bereits als **Besitzer** der App angezeigt wird. Zu dieser Gruppe zählen auch Sie, wenn Sie der Kontoadministrator Ihres Azure-Abonnements (z.B. des Testabonnements) sind. Weitere Informationen zur rollenbasierten Azure-Zugriffssteuerung finden Sie unter [Rollenbasierte Access Control in Azure](../active-directory/role-based-access-control-configure.md).

## Nächste Schritte

Vielleicht ist Ihnen beim Konfigurieren der Warnung auf dem Blatt **Extras** ein Satz mit vielen Tools aufgefallen. Mit diesen Tools können Sie Probleme behandeln, die Leistung überwachen, Tests auf Sicherheitsrisiken durchführen, Ressourcen verwalten, mit der VM-Konsole interagieren und nützliche Erweiterungen hinzufügen. Wir laden Sie ein, auf diese einzelnen Tools zu klicken, um die einfachen und gleichzeitig leistungsstarken Tools zu entdecken, die Ihnen zur Verfügung stehen.

Sie können sich darüber informieren, welche anderen Möglichkeiten Sie mit der bereitgestellten App haben. Hier sind einige Beispiele angegeben:

- [Kaufen und Konfigurieren eines benutzerdefinierten Domänennamens](custom-dns-web-site-buydomains-web-app.md)
- [Einrichten von Stagingumgebungen](web-sites-staged-publishing.md)
- [Einrichten der fortlaufenden Bereitstellung](web-sites-publish-source-control.md)
- [Sichern der App](web-sites-backup.md)
- [Aktivieren von Diagnoseprotokollen](web-sites-enable-diagnostic-log.md)
- [Zugreifen auf lokale Ressourcen](web-sites-hybrid-connection-get-started.md)
- [Informationen zur Funktionsweise von App Service](../app-service/app-service-how-works-readme.md) 

<!---HONumber=AcomDC_0323_2016-->