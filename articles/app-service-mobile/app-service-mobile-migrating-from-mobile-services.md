<properties
	pageTitle="Migrieren von Mobile Services in eine mobile App Service-App"
	description="Erfahren Sie, wie einfach Sie Ihre Mobile Services-Anwendung in eine mobile App Service-App migrieren können."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="adrianhall"/>

# <a name="article-top"></a>Migrieren des vorhandenen Azure Mobile Service zu Azure App Service

Mit der [allgemeinen Verfügbarkeit von Azure App Service] können Azure Mobile Services-Websites problemlos direkt migriert werden, um alle Features von Azure App Service zu nutzen. Dieses Dokument erläutert, was Sie erwarten können, wenn Sie Ihre Website von Azure Mobile Services zu Azure App Service migrieren.

## <a name="what-does-migration-do"></a>Auswirkungen der Migration auf Ihre Website

Die Migration Ihres Azure Mobile Service wandelt den Mobil Service in eine [Azure App Service]-App um, ohne den Code in irgendeiner Weise zu beeinflussen. Ihre Notification Hubs, SQL-Datenverbindung, Authentifizierungseinstellungen, geplante Aufträge und Domänennamen bleiben unverändert. Mobile Clients, die Ihren Azure Mobile Service verwenden, werden weiterhin normal ausgeführt. Die Migration startet Ihren Dienst neu, sobald er in Azure App Service übertragen ist.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Gründe zur Migration Ihrer Website

Microsoft empfiehlt Ihnen, Ihren Azure Mobile Service zu migrieren, um die Features von Azure App Service zu nutzen, einschließlich:

  *  Neue Hostfeatures, einschließlich [WebJobs] und [benutzerdefinierten Domänennamen].
  *  Konnektivität mit Ihren lokalen Ressourcen über [VNet] ergänzend zu [Hybridverbindungen].
  *  Überwachung und Problembehandlung mit New Relic oder [Application Insights].
  *  Integrierte DevOps-Tools, einschließlich [Stagingslots], Rollback und Test in der Produktion.
  *  [Automatische Skalierung], Lastenausgleich und [Leistungsüberwachung].

Weitere Informationen zu den Vorteilen von Azure App Service finden Sie im Thema [Mobile Services im Vergleich zu App Service].

## <a name="why-not-migrate"></a>Gründe gegen die Migration Ihrer Website

Es gibt einige wenige Gründe, warum Sie Ihre Azure Mobile Services jetzt nicht migrieren sollten:

  *  Sie sind zurzeit sehr stark ausgelastet und können sich einen Neustart der Website zu diesem Zeitpunkt nicht leisten.
  *  Sie möchten Auswirkungen auf Ihre Produktionswebsite vor dem Testen des Migrationsprozesses vermeiden.
  *  Sie verfügen über mehrere Websites in den Tarifen Free bzw. Basic und möchten nicht alle Websites gleichzeitig migrieren.

Wenn Sie zurzeit sehr stark ausgelastet sind, sehen Sie die Migration für ein geplantes Wartungsfenster vor. Zum Migrationsprozess gehört der Neustart Ihrer Website, und Ihre Benutzer könnten diese vorübergehende Störung der Verfügbarkeit bemerken.

Für die meisten Elemente in dieser Liste gibt es Problemumgehungen. Weitere Informationen finden Sie im folgenden Abschnitt [Voraussetzungen](#before-you-begin).

## <a name="before-you-begin"></a>Voraussetzungen

Vor der Migration Ihrer Website sollten Sie die folgenden Schritte ausführen:

  *  [Sichern Sie Ihre Skripte und Ihre SQL-Datenbank für Mobile Service]
  *  (Optional) Stufen Sie den Mobile Service-Tarif auf Standard herauf

Wenn Sie den Migrationsprozess vor der Migration Ihrer Produktionswebsite testen möchten, duplizieren Sie Ihren Azure Mobile Service für die Produktion (zusammen mit einer Kopie der Datenquelle), und testen Sie die Migration anhand der neuen URL. Sie benötigen außerdem eine Testclientimplementierung, die auf die Testsite verweist, um die migrierte Website ordnungsgemäß zu testen.

### <a name="opt-raise-service-tier"></a>(Optional) Stufen Sie den Mobile Service-Tarif auf Standard herauf

Alle Mobile Services-Websites, die einen Hostingplan gemeinsam nutzen, werden gleichzeitig migriert. Mobile Services in den Tarifen Free oder Basic nutzen einen Hostingplan gemeinsam mit anderen Diensten im gleichen Tarif und der gleichen [Azure-Region]. Wenn Sie Ihren Mobile Service im Tarif Standard betreiben, hat er seinen eigenen Hostingplan. Wenn Sie Websites einzeln zu den Tarifen Free oder Basic migrieren möchten, aktualisieren Sie den Mobile Service-Tarif vorübergehend auf Standard. Dies ist im Menü „STAFFELUNG“ für Ihren Mobile Service möglich.

  1.  Melden Sie sich beim [klassischen Azure-Portal] an.
  2.  Wählen Sie Ihren Mobile Service aus.
  3.  Wählen Sie die Registerkarte **ZENTRAL HOCHSKALIEREN** aus.
  4.  Klicken Sie unter **Mobile Serviceebene** auf den Tarif **STANDARD**. Klicken Sie auf das Symbol **SPEICHERN** am unteren Rand der Seite.

Denken Sie daran, den Tarif nach der Migration auf einen geeigneten Wert einzustellen.

## <a name="migrating-site"></a>Migrieren Ihrer Website

So migrieren Sie Ihre Website:

  1.  Melden Sie sich beim [klassischen Azure-Portal] an.
  2.  Wählen Sie Ihren Mobile Service aus.
  3.  Klicken Sie auf die Schaltfläche **Migrieren zu App Service**.

    ![Die Schaltfläche „Migrieren“][0]

  4.  Lesen Sie den Inhalt des App Service-Dialogfelds.
  5.  Geben Sie den Namen Ihres Mobile Service in das dafür vorgesehene Feld ein. Wenn Ihr Domänenname beispielsweise „contoso.azure mobile.net“ ist, dann geben Sie _Contoso_ in das dafür vorgesehene Feld ein.
  6.  Klicken Sie auf die Schaltfläche mit dem Häkchen.

Bei der Migration eines Mobile Service zu dem Tarif Free oder Basic werden gleichzeitig alle Mobile Services in diesem Tarif migriert. Sie können dies verhindern, indem Sie während der Migration [den Mobile Service, den Sie migrieren, heraufstufen](#opt-raise-service-tier) zu Standard.

Sie können den Status der Migration im Aktivitätsmonitor überwachen, und Ihre Website wird im klassischen Azure-Portal als *in Migration befindlich* aufgeführt.

  ![Migrationsaktivitätsmonitor][1]

Jede Migration kann pro Mobile Service, der migriert wird, zwischen 3 und 15 Minuten dauern. Ihre Website bleibt während der Migration verfügbar, wird jedoch am Ende des Migrationsprozesses neu gestartet. Die Website steht während des Neustarts, der ein paar Sekunden dauern kann, nicht zur Verfügung.

## <a name="finalizing-migration"></a>Abschließen der Migration

Sie sollten Ihre Website nach Abschluss des Migrationsvorgangs von einem mobilen Client aus testen. Stellen Sie sicher, dass Sie alle allgemeinen Clientaktionen ohne Änderungen am mobilen Client ausführen können. Darüber hinaus sollten Sie sicherstellen, dass die von Ihnen vorgenommenen Änderungen, die Auswirkungen auf die Migration haben (z. B. das Ändern des Tarifs) ggf. rückgängig gemacht werden.

### <a name="update-app-service-tier"></a>Auswählen eines entsprechenden App Service-Tarifs

Nach der Migration zu Azure App Service können Sie Preise flexibler gestalten.

  1.  Melden Sie sich beim [Azure-Portal] an.
  2.  Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihres migrierten Mobile Service.
  3.  Das Blatt „Einstellungen“ wird standardmäßig geöffnet. Wenn dies nicht der Fall ist, klicken Sie auf **Einstellungen**.
  4.  Klicken Sie im Menü „Einstellungen“ auf **App Service-Plan**.
  5.  Klicken Sie auf die Kachel **Tarif**.
  6.  Klicken Sie auf die für Ihre Anforderungen geeignete Kachel, und klicken Sie dann auf **Auswählen**. Möglicherweise müssen Sie auf **Alle anzeigen** klicken, um die verfügbaren Tarife zu sehen.

Als Ausgangspunkt empfehlen wir Folgendes:

| Mobile Service-Tarif | App Service-Tarif |
| :-------------------------- | :----------------------- |
| Free | F1 Free |
| Basic | B1 Basic |
| Standard | S1 Standard |

Beachten Sie die beträchtliche Flexibilität bei der Auswahl des richtigen Tarifs für Ihre Anwendung. Unter [App Service – Preise] finden Sie umfassende Informationen zu den Preisen Ihres neuen App Service.

> [AZURE.TIP] Der App Service-Tarif Standard umfasst den Zugriff auf viele Features, die Sie unter Umständen verwenden möchten, einschließlich [Stagingslots], automatischen Sicherungen und automatischer Skalierung. Informieren Sie sich dort ausführlich über die neuen Funktionen!

### <a name="review-migration-scheduler-jobs"></a>Überprüfen der migrierten Scheduler-Aufträge

Scheduler-Aufträge sind erst ca. 30 Minuten nach der Migration sichtbar. Alle geplanten Aufträge werden weiterhin im Hintergrund ausgeführt. So zeigen Sie Ihre geplanten Aufträge an:

  1.  Melden Sie sich beim [Azure-Portal] an.
  2.  Wählen Sie **Durchsuchen >**, geben Sie **Zeitplan** in das Feld _Filter_ ein, und wählen Sie **Scheduler-Sammlungen**.

Nach der Migration ist eine begrenzte Anzahl von freien Scheduler-Aufträgen verfügbar. Überprüfen Sie Ihre Verwendung und die [Azure Scheduler-Pläne].

### <a name="configure-cors"></a>Konfigurieren von CORS bei Bedarf

Ressourcenfreigabe zwischen verschiedenen Ursprüngen ist ein Verfahren, um einer Website den Zugriff auf eine Web-API in einer anderen Domäne zu ermöglichen. Wenn Sie Azure Mobile Services mit einer zugeordneten Website verwendet haben, müssen Sie im Rahmen der Migration CORS konfigurieren. Wenn Sie ausschließlich über mobile Geräte auf Azure Mobile Services zugegriffen haben, muss CORS nur in seltenen Fällen konfiguriert werden.

Die migrierten Einstellungen für CORS stehen als App-Einstellung **MS\_CrossDomainWhitelist** zur Verfügung. So migrieren Sie Ihre Website zum App Service-CORS-Raum:

  1.  Melden Sie sich beim [Azure-Portal] an.
  2.  Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihres migrierten Mobile Service.
  3.  Das Blatt „Einstellungen“ wird standardmäßig geöffnet. Wenn dies nicht der Fall ist, klicken Sie auf **Einstellungen**.
  4.  Klicken Sie im API-Menü auf **CORS**.
  5.  Geben Sie alle zulässigen Ursprünge im entsprechenden Feld ein, und drücken Sie nach jeder Eingabe die EINGABETASTE.
  6.  Sobald die Liste der zulässigen Ursprünge vollständig ist, klicken Sie auf die Schaltfläche „Speichern“.

Dies ist eine optionale Aufgabe, ermöglicht aber im Weiteren eine bessere Verwaltung.

> [AZURE.TIP]  Ein Vorteil der Verwendung von Azure App Service ist, dass Sie Ihre Website und den Mobile Service auf der gleichen Website ausführen können. Weitere Informationen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

### <a name="download-publish-profile"></a>Herunterladen eines neuen Veröffentlichungsprofils

Beim der Migration zu Azure App Service wird das Veröffentlichungsprofil der Website geändert. Wenn Sie Ihre Website aus Visual Studio heraus veröffentlichen möchten, benötigen Sie ein neues Veröffentlichungsprofil. So laden Sie das neue Veröffentlichungsprofil herunter:

  1.  Melden Sie sich beim [Azure-Portal] an.
  2.  Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihres migrierten Mobile Service.
  3.  Klicken Sie auf **Veröffentlichungsprofil abrufen**.

Die PublishSettings-Datei wird auf Ihren Computer heruntergeladen. Sie heißt normalerweise _Websitename_.PublishSettings. Anschließend können Sie die Veröffentlichungseinstellungen in Ihr vorhandenes Projekt importieren:

  1.  Öffnen Sie Visual Studio und Ihr Azure Mobile Service-Projekt.
  2.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Veröffentlichen...** aus.
  3.  Klicken Sie auf **Importieren**.
  4.  Klicken Sie auf **Durchsuchen**, und wählen Sie die heruntergeladene Datei mit den Veröffentlichungseinstellungen aus. Klicken Sie auf **OK**.
  5.  Klicken Sie auf **Verbindung überprüfen**, um sicherzustellen, dass die Veröffentlichungseinstellungen funktionieren.
  6.  Klicken Sie auf **Veröffentlichen**, um die Website zu veröffentlichen.


## <a name="working-with-your-site"></a>Arbeiten mit Ihrer Website nach der Migration

Sie beginnen im [Azure-Portal], nach der Migration mit Ihrem neuen App Service zu arbeiten. Es folgen einige Hinweise zu bestimmten Vorgängen, die Sie im [klassischen Azure-Portal] ausgeführt haben, mit ihren App Service-Entsprechungen.

### <a name="publishing-your-site"></a>Herunterladen und Veröffentlichen Ihrer migrierten Website

Ihre Website ist über Git oder FTP verfügbar und kann mit einer Reihe von verschiedenen Mechanismen, einschließlich WebDeploy, TFS, Mercurial, GitHub und FTP erneut veröffentlicht werden. Anmeldeinformationen für die Bereitstellung werden mit dem Rest der Website migriert. Wenn Sie keine Anmeldeinformationen für die Bereitstellung festgelegt haben, oder sich nicht mehr an sie erinnern, können Sie diese zurücksetzen:

  1. Melden Sie sich beim [Azure-Portal] an.
  2. Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihres migrierten Mobile Service.
  3. Das Blatt „Einstellungen“ wird standardmäßig geöffnet. Wenn dies nicht der Fall ist, klicken Sie auf **Einstellungen**.
  4. Klicken Sie im Menü „VERÖFFENTLICHUNG“ auf **Anmeldeinformationen für die Bereitstellung**.
  5. Geben Sie die neuen Anmeldeinformationen für die Bereitstellung in die entsprechenden Felder ein, und klicken Sie auf die Schaltfläche „Speichern“.

Mit diesen Anmeldeinformationen können Sie die Website mit Git klonen, oder Sie können automatische Bereitstellungen von GitHub, TFS oder Mercurial aus einrichten. Weitere Informationen finden Sie in der [Dokumentation zur Azure App Service-Bereitstellung].

### <a name="appsettings"></a>Anwendungseinstellungen

Die meisten Einstellungen für einen migrierten Mobile Service sind über App-Einstellungen verfügbar. Sie können eine Übersicht der App-Einstellungen im [Azure-Portal] abrufen. So zeigen Sie Ihre App-Einstellungen an oder ändern sie:

  1. Melden Sie sich beim [Azure-Portal] an.
  2. Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihres migrierten Mobile Service.
  3. Das Blatt „Einstellungen“ wird standardmäßig geöffnet. Wenn dies nicht der Fall ist, klicken Sie auf **Einstellungen**.
  4. Klicken Sie im Menü „ALLGEMEIN“ auf **Anwendungseinstellungen**.
  5. Scrollen Sie zum Abschnitt „App-Einstellungen“, und suchen Sie Ihre App-Einstellung.
  6. Klicken Sie auf den Wert der App-Einstellung, um ihn zu bearbeiten. Klicken Sie auf **Speichern**, um den Wert zu speichern.

Sie können gleichzeitig mehrere App-Einstellungen aktualisieren.

> [AZURE.TIP]  Sie werden feststellen, dass zwei Anwendungseinstellungen den gleichen Wert haben. Es könnte z. B. _ApplicationKey_ und _MS\_ApplicationKey_ angezeigt werden. Sie müssen nur die App-Einstellung mit dem Präfix **MS\_** ändern. Allerdings ist es eine gute Idee, beide App-Einstellungen zur gleichen Zeit zu aktualisieren.

### <a name="authentication"></a>Authentifizierung

Alle Authentifizierungseinstellungen sind auf der migrierten Website als App-Einstellungen verfügbar. Um Ihre Authentifizierungseinstellungen zu aktualisieren, müssen Sie die entsprechenden App-Einstellungen ändern. Die folgende Tabelle zeigt die entsprechenden App-Einstellungen für Ihren Authentifizierungsanbieter:

| Anbieter | Client-ID | Geheimer Clientschlüssel | Andere Einstellungen |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Microsoft Account | **MS\_MicrosoftClientID** | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook | **MS\_FacebookAppID** | **MS\_FacebookAppSecret** | |
| Twitter | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** | |
| Google | **MS\_GoogleClientID** | **MS\_GoogleClientSecret** | |
| Azure AD | **MS\_AadClientID** | | **MS\_AadTenants** |

Hinweis: **MS\_AadTenants** wird als eine durch Trennzeichen getrennte Liste von Mandantendomänen gespeichert (die Felder „Zulässige Mandanten“ im Mobile Services-Portal).

> [AZURE.WARNING] **Verwenden Sie nicht die Authentifizierungsmechanismen im Menü „Einstellungen“**
>
> Azure App Service bietet ein separates Authentifizierungs- und Autorisierungssystem „ohne Code“ im Einstellungenmenü für _Authentifizierung/Autorisierung_ und die (veraltete) Option _Mobile Authentifizierung_ im Menü „Einstellungen“. Diese Optionen sind nicht kompatibel mit einem migrierten Azure Mobile Service. Sie können [Ihre Website aktualisieren](app-service-mobile-net-upgrading-from-mobile-services.md), um die Azure App Service-Authentifizierung zu nutzen.

### <a name="easytables"></a>Daten

Die Registerkarte _Daten_ in Mobile Services wurde durch _Einfache Tabellen_ im Azure-Portal ersetzt. So greifen Sie auf „Einfache Tabellen“ zu:

  1. Melden Sie sich beim [Azure-Portal] an.
  2. Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihres migrierten Mobile Service.
  3. Das Blatt „Einstellungen“ wird standardmäßig geöffnet. Wenn dies nicht der Fall ist, klicken Sie auf **Einstellungen**.
  4. Klicken Sie im Menü „MOBILE“ auf **Einfache Tabellen**.

Sie können eine neue Tabelle hinzufügen, indem Sie auf die Schaltfläche **Hinzufügen** klicken, oder auf Ihre vorhandenen Tabellen zugreifen, indem Sie auf den Namen einer Tabelle klicken. Zahlreiche Vorgänge können Sie von diesem Blatt aus ausführen, einschließlich:

  * Ändern von Tabellenberechtigungen
  * Bearbeiten der eingesetzten Skripts
  * Verwalten des Tabellenschemas
  * Löschen der Tabelle
  * Löschen des Tabelleninhalts
  * Löschen von bestimmten Zeilen der Tabelle

### <a name="easyapis"></a>API

Die Registerkarte _API_ in Mobile Services wurde durch _Einfache APIs_ im Azure-Portal ersetzt. So greifen Sie auf „Einfache APIs“ zu:

  1. Melden Sie sich beim [Azure-Portal] an.
  2. Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihres migrierten Mobile Service.
  3. Das Blatt „Einstellungen“ wird standardmäßig geöffnet. Wenn dies nicht der Fall ist, klicken Sie auf **Einstellungen**.
  4. Klicken Sie im Menü „MOBILE“ auf **Einfache APIs**.

Im Blatt werden bereits Ihre migrierten APIs aufgeführt. Sie können auch eine neue API von diesem Blatt aus hinzufügen. Um eine bestimmte API zu verwalten, klicken Sie auf die API. Von dem neuen Blatt aus können Sie die Berechtigungen für die API passen und die Skripts für die API bearbeiten.

### <a name="on-demand-jobs"></a>Scheduler-Aufträge

Auf alle Scheduler-Aufträge können Sie im Abschnitt „Scheduler-Auftragssammlungen“ zugreifen. So greifen Sie auf Scheduler-Aufträge zu:

  1. Melden Sie sich beim [Azure-Portal] an.
  2. Wählen Sie **Durchsuchen >**, geben Sie **Zeitplan** in das Feld _Filter_ ein, und wählen Sie **Scheduler-Sammlungen**.
  3. Wählen Sie die Auftragssammlung für Ihre Website aus. Sie hat den Namen _Websitename_-Aufträge.
  4. Klicken Sie auf **Einstellungen**.
  5. Klicken Sie unter „VERWALTEN“ auf **Scheduler-Aufträge**.

Geplante Aufträge werden mit der Häufigkeit aufgelistet, die Sie vor der Migration angegeben haben. Bedarfsgesteuerte Aufträge werden deaktiviert. So führen Sie einen bedarfsgesteuerten Auftrag aus:

  1. Wählen Sie den auszuführenden Auftrag aus.
  2. Wenn nötig, klicken Sie auf **Aktivieren**, um den Auftrag zu aktivieren.
  3. Klicken Sie auf **Einstellungen** und dann auf **Zeitplan**.
  4. Wählen als Wiederholung **Einmal** aus, und klicken Sie dann auf **Speichern**.

Ihre bedarfsgesteuerten Aufträge befinden sich in `App_Data/config/scripts/scheduler post-migration`. Sie sollten alle bedarfsgesteuerten Aufträge in [WebJobs] umwandeln. Schreiben Sie neue Scheduler-Aufträge als [WebJobs].

### <a name="notification-hubs"></a>Notification Hubs

Mobile Services verwendet Notification Hubs für Pushbenachrichtigungen. Die folgenden App-Einstellungen werden verwendet, um den Notification Hub nach der Migration mit Ihrem Mobile Service zu verknüpfen:

| Anwendungseinstellung | Beschreibung |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace** | Der Notification Hub-Namespace |
| **MS\_NotificationHubName** | Der Notification Hub-Name |
| **MS\_NotificationHubConnectionString** | Die Notification Hub-Verbindungszeichenfolge |
| **MS\_NamespaceName** | Ein Alias für MS\_PushEntityNamespace |

Ihr Notification Hub wird über das [Azure-Portal] verwaltet. Notieren Sie den Notification Hub-Namen (Sie finden ihn über die App-Einstellungen):

  1. Melden Sie sich beim [Azure-Portal] an.
  2. Wählen Sie **Durchsuchen**>, und wählen Sie dann **Notification Hubs**.
  3. Klicken Sie auf den Namen des Notification Hubs, der dem Mobile Service zugeordnet ist.

> [AZURE.NOTE] Ihr Notification Hub wird nicht angezeigt, wenn er dem Typ „Gemischt“ entspricht. Notification Hubs des Typs „Gemischt“ setzen sowohl Notification Hubs- als auch ältere Service Bus-Features ein. Sie müssen [Ihre gemischten Namespaces konvertieren]. Sobald die Konvertierung abgeschlossen ist, wird Ihr Notification Hub im [Azure-Portal] angezeigt.

Weitere Informationen finden Sie in der [Notification Hubs]-Dokumentation.

> [AZURE.TIP] Im [Azure-Portal] befinden sich Notification Hubs-Verwaltungsfeatures noch in der Vorschau. Das [klassische Azure-Portal] bleibt für die Verwaltung Ihrer sämtlichen Notification Hubs verfügbar.

### <a name="app-settings"></a>Andere App-Einstellungen

Die folgenden zusätzlichen App-Einstellungen werden aus Ihrem Mobile Service migriert und sind unter *Einstellungen* > *Anwendungseinstellungen* verfügbar:

| Anwendungseinstellung | Beschreibung |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName** | Der Name Ihrer App |
| **MS\_MobileServiceDomainSuffix** | Das Domänenpräfix, d. h. azure-mobile.net |
| **MS\_ApplicationKey** | Ihr Anwendungsschlüssel |
| **MS\_MasterKey** | Ihr App-Hauptschlüssel |

Anwendungs- und Hauptschlüssel sollten mit den Anwendungsschlüsseln aus Ihrem ursprünglichen Mobile Service übereinstimmen. Insbesondere der Anwendungsschlüssel wird von mobilen Clients gesendet, damit ihre Nutzung der mobilen API geprüft werden kann.

### <a name="cliequivalents"></a>Befehlszeilenentsprechungen

Sie können den _Azure-Mobile_-Befehl nicht mehr verwenden, um Ihre Azure Mobile Services-Website zu verwalten. Stattdessen wurden zahlreiche Funktionen durch den _Azure-Site_-Befehl ersetzt. In der folgenden Tabelle finden Sie Entsprechungen für häufig verwendete Befehle:

| _Azure-Mobile_-Befehl | Entsprechender _Azure-Site_-Befehl |
| :----------------------------------------- | :----------------------------------------- |
| mobile locations | site location list |
| mobile list | site list |
| mobile show _Name_ | site show _Name_ |
| mobile restart _Name_ | site restart _Name_ |
| mobile redeploy _Name_ | site deployment redeploy _Commit-ID_ _Name_ |
| mobile key set _Name_ _Typ_ _Wert_ | site appsetting delete _Schlüssel__Name_ <br/> site appsetting add _Schlüssel_=\_Wert\_ _Name_ |
| mobile config list _Name_ | site appsetting list _Name_ |
| mobile config get _Name_ _Schlüssel_ | site appsetting show _Schlüssel_ _Name_ |
| mobile config set _Name_ _Schlüssel_ | site appsetting delete _Schlüssel__Name_ <br/> site appsetting add _Schlüssel_=\_Wert\_ _Name_ |
| mobile domain list _Name_ | site domain list _Name_ |
| mobile domain add _Name_ _Domäne_ | site domain add _Domäne_ _Name_ |
| mobile domain delete _Name_ | site domain delete _Domäne_ _Name_ |
| mobile scale show _Name_ | site show _Name_ |
| mobile scale change _Name_ | site scale mode _Modus_ _Name_ <br /> site scale instances _Instanzen_ _Name_ |
| mobile appsetting list _Name_ | site appsetting list _Name_ |
| mobile appsetting add _Name_ _Schlüssel_ _Wert_ | site appsetting add _Schlüssel_=\_Wert\_ _Name_ |
| mobile appsetting delete _Name_ _Schlüssel_ | site appsetting delete _Schlüssel_ _Name_ |
| mobile appsetting show _Name_ _Schlüssel_ | site appsetting delete _Schlüssel_ _Name_ |

Aktualisieren Sie Authentifizierungs- oder Pushbenachrichtigungseinstellungen, indem Sie die entsprechende Anwendungseinstellung aktualisieren. Bearbeiten Sie Dateien, und veröffentlichen Sie Ihre Website über FTP oder Git.

### <a name="diagnostics"></a>Diagnose und Protokollierung

Normalerweise ist die Diagnoseprotokollierung in einem Azure App Service deaktiviert. So aktivieren Sie die Diagnoseprotokollierung:

  1. Melden Sie sich beim [Azure-Portal] an.
  2. Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihres migrierten Mobile Service.
  3. Das Blatt „Einstellungen“ wird standardmäßig geöffnet. Wenn dies nicht der Fall ist, klicken Sie auf **Einstellungen**.
  4. Wählen Sie im Menü „FEATURES“ **Diagnoseprotokolle**.
  5. Klicken Sie für die folgenden Protokolle auf **EIN**: **Anwendungsprotokollierung (Dateisystem)**, **Detaillierte Fehlermeldungen** und **Ablaufverfolgung für Anforderungsfehler**.
  6. Klicken Sie für Webserverprotokollierung auf **Dateisystem**.
  7. Klicken Sie auf **Speichern**.

So zeigen Sie die Protokolle an:

  1. Melden Sie sich beim [Azure-Portal] an.
  2. Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihres migrierten Mobile Service.
  3. Klicken Sie auf die Schaltfläche **Extras**.
  4. Wählen Sie im Menü „ÜBERWACHEN“ **Protokollstream**.

Protokolle werden in das Fenster gestreamt, sobald sie generiert werden. Sie können die Protokolle auch zur späteren Analyse mithilfe Ihrer Anmeldeinformationen für die Bereitstellung herunterladen. Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung].

## <a name="next-steps"></a>Nächste Schritte

Ihre Anwendung ist nicht nur zu App Service migriert, Sie können sogar noch mehr Features nutzen:

  * [Stagingslots] zur Bereitstellung ermöglichen Ihnen, Änderungen an Ihrer Website bereitzustellen und A/B-Tests auszuführen.
  * [WebJobs] bieten einen Ersatz für bedarfsgesteuerte geplante Aufträge.
  * Sie können Ihre Website [fortlaufend bereitstellen], indem Sie Ihre Website mit GitHub, TFS oder Mercurial verknüpfen.
  * Sie können Ihre Site mit [Application Insights] überwachen.
  * Setzen Sie den gleichen Code für eine Website und eine Mobil-API ein.

### <a name="upgrading-your-site"></a>Aktualisieren Ihrer Mobile Services-Website auf Azure Mobile Apps-SDK

  * Für Node.js-basierte Serverprojekte verfügt das neue [Mobile Apps-Node.js-SDK] über eine Reihe neuer Features. Beispielsweise können Sie jetzt lokal entwickeln und debuggen, eine beliebige Node.js-Version nach 0.10 verwenden und eine Anpassung mit beliebiger Express.js-Middleware durchführen.

  * Für .NET-basierte Serverprojekte verfügen die neuen [Mobile Apps-SDK-NuGet-Pakete](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) über mehr Flexibilität für NuGet-Abhängigkeiten, die neuen App Service-Authentifizierungsfeatures werden unterstützt, und die Zusammenstellung mit ASP.NET-Projekten ist möglich, einschließlich MVC. Weitere Informationen zu Upgrades finden Sie unter [Aktualisieren des vorhandenen .NET Mobile Service auf App Service](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[App Service – Preise]: https://azure.microsoft.com/de-DE/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[Automatische Skalierung]: ../app-service-web/web-sites-scale.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Dokumentation zur Azure App Service-Bereitstellung]: ../app-service-web/web-sites-deploy.md
[klassische Azure-Portal]: https://manage.windowsazure.com
[klassischen Azure-Portal]: https://manage.windowsazure.com
[Azure-Portal]: https://portal.azure.com
[Azure-Region]: https://azure.microsoft.com/de-DE/regions/
[Azure Scheduler-Pläne]: ../scheduler/scheduler-plans-billing.md
[fortlaufend bereitstellen]: ../app-service-web/web-sites-publish-source-control.md
[Ihre gemischten Namespaces konvertieren]: https://azure.microsoft.com/de-DE/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[benutzerdefinierten Domänennamen]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[allgemeinen Verfügbarkeit von Azure App Service]: /blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybridverbindungen]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[Protokollierung]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Mobile Apps-Node.js-SDK]: https://github.com/azure/azure-mobile-apps-node
[Mobile Services im Vergleich zu App Service]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notification Hubs]: ../notification-hubs/notification-hubs-overview.md
[Leistungsüberwachung]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[Sichern Sie Ihre Skripte und Ihre SQL-Datenbank für Mobile Service]: ../mobile-services/mobile-services-disaster-recovery.md
[Stagingslots]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md

<!---HONumber=AcomDC_0218_2016-->