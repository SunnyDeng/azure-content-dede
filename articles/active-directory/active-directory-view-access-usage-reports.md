<properties
	pageTitle="Anzeigen von Zugriffs- und Nutzungsberichten"
	description="In diesem Thema wird das Anzeigen von Zugriffs- und Nutzungsberichten erläutert, um sich einen Einblick in die Integrität und Sicherheit des Verzeichnisses Ihrer Organisation zu verschaffen."
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="TerryLan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2015"
	ms.author="kenhoff;Justinha"/>

# Anzeigen von Zugriffs- und Nutzungsberichten

Sie können die Zugriffs- und Nutzungsberichte von Azure Active Directory verwenden, um sich einen Einblick in die Integrität und Sicherheit des Verzeichnisses Ihrer Organisation zu verschaffen. Mithilfe dieser Informationen kann ein Verzeichnisadministrator mögliche Sicherheitsrisiken besser bestimmen, um angemessen zu planen, wie diese Risiken eingedämmt werden können.

Im Azure-Verwaltungsportal werden Berichte auf folgende Weise kategorisiert:

- Anomalieberichte – Enthalten Anmeldeereignisse, die wir als anomal eingestuft haben. Unser Ziel ist, Sie auf solche Aktivitäten aufmerksam zu machen und Ihnen zu ermöglichen, eine Entscheidung zu treffen, ob ein Ereignis verdächtig ist.
- Integrierte Anwendungsberichte – Bieten Einblicke, wie Cloudanwendungen in Ihrer Organisation verwendet werden. Azure Active Directory ermöglicht die Integration in Tausende von Cloudanwendungen.
- Fehlerberichte – Enthalten Hinweis auf Fehler, die bei der Bereitstellung von Konten für externe Anwendungen auftreten können.
- Benutzerspezifische Berichte – Enthalten Geräte-/Anmeldeaktivitätsdaten für einen bestimmten Benutzer.
- Aktivitätsprotokolle – Enthalten eine Aufzeichnung aller überwachten Ereignisse in den letzten 24 Stunden, letzten 7 Tagen oder letzten 30 Tagen sowie von Änderungen an Gruppenaktivitäten sowie Kennwortzurücksetzungs- und Registrierungsaktivitäten.

> [AZURE.NOTE]
>
- Einige erweiterte Anomalie- und Ressourcennutzungsberichte sind nur verfügbar, wenn Sie [Azure Active Directory Premium](active-directory-get-started-premium.md) aktivieren. Erweiterte Berichte unterstützen Sie bei der Optimierung der Zugriffssicherheit, Reaktion auf potenzielle Bedrohungen und dem Zugriff auf Analysen zur Geräte- und Anwendungsnutzung.
- Die Azure Active Directory-Editionen Premium und Basic stehen für Kunden in China zur Verfügung, die mit der weltweit verfügbaren Instanz von Azure Active Directory arbeiten. Allerdings werden die Azure Active Directory-Editionen Premium und Basic derzeit durch den in China von 21Vianet betriebenen Microsoft Azure-Dienst nicht unterstützt. Wenn Sie weitere Informationen benötigen, kontaktieren Sie uns im [Azure Active Directory-Forum](http://feedback.azure.com/forums/169401-azure-active-directory).

## Berichte

|	Bericht |	Beschreibung |
|	------												|	-----																						|
|	**Berichte zu anomalen Aktivitäten**
|	[Anmeldungen aus unbekannten Quellen](active-directory-reporting-sign-ins-from-unknown-sources.md) |	Zeigt möglicherweise einen Versuch einer Anmeldung ohne Ablaufverfolgung an. |
|	[Anmeldungen nach mehreren Fehlern](active-directory-sign-ins-after-multiple-failures.md) |	Zeigt möglicherweise einen erfolgreichen Brute-Force-Angriff an. |
|	[Anmeldungen aus mehreren geografischen Regionen](active-directory-sign-ins-from-multiple-geographies.md) |	Zeigt möglicherweise an, dass sich mehrere Benutzer mit dem gleichen Konto anmelden. |
|	[Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md) |	Zeigt möglicherweise eine erfolgreiche Anmeldung nach einem langfristigen Einbruchsversuch an. |
|	[Anmeldungen von möglicherweise infizierten Geräten](active-directory-reporting-sign-ins-from-possibly-infected-devices.md) |	Gibt einen Versuch an, sich über möglicherweise infizierte Geräte anzumelden. |
|	[Irreguläre Anmeldeaktivitäten](active-directory-reporting-irregular-sign-in-activity.md) |	Zeigt möglicherweise anomale Ereignisse bei Anmeldemustern von Benutzern an. |
|	[Benutzer mit anomalen Anmeldeaktivitäten](active-directory-reporting-users-with-anomalous-sign-in-activity.md) |	Gibt Benutzer an, deren Konten möglicherweise beschädigt sind. |
|	Benutzer mit kompromittierten Anmeldeinformationen |	Benutzer mit kompromittierten Anmeldeinformationen |
|	**Aktivitätsprotokolle**
|	Überwachungsbericht |	Überwachte Ereignisse in Ihrem Verzeichnis |
|	Kennwortzurücksetzungsaktivität |	Bietet eine detaillierte Übersicht über das Zurücksetzen des Kennworts in Ihrer Organisation. |
|	Aktivität "Registrierung für Zurücksetzen des Kennworts" |	Bietet eine detaillierte Übersicht über Registrierungen für das Zurücksetzen des Kennworts in Ihrer Organisation. |
|	Self-Service-Gruppenaktivität |	Stellt ein Aktivitätsprotokoll aller Self-Service-Aktivitäten für Gruppen in Ihrem Verzeichnis bereit. |
|	**Integrierte Anwendungen**
|	Anwendungsnutzung |	Stellt eine Nutzungszusammenfassung für alle in Ihr Verzeichnis integrierten SaaS-Anwendungen bereit. |
|	Kontobereitstellungsaktivität |	Stellt einen Verlauf der Kontobereitstellungsversuche für externe Anwendungen bereit. |
|	Status des Kennwortrollovers |	Stellt eine ausführliche Übersicht über den Status des automatischen Kennwortrollovers von SaaS-Anwendungen zur Verfügung. |
|	Kontobereitstellungsfehler |	Zeigt eine Auswirkung auf den Zugriff von Benutzern auf externe Anwendungen an. |
|	**Rechteverwaltung**
|	RMS-Nutzung |	Enthält eine Übersicht zur Nutzung des Rights Management. |
|	Aktivste RMS-Benutzer |	Listet die Top 1000 aktiven Benutzer auf, die auf RMS-geschützte Dateien zugegriffen haben. |
|	Nutzung von RMS-Geräten |	Listet Geräte auf, die für den Zugriff auf RMS-geschützte Dateien verwendet werden. |
|	Nutzung RMS-fähiger Anwendungen |	Bietet die Nutzung von RMS-fähigen Anwendungen. |

## Berichte in den verschiedenen Editionen

|	Bericht |	Kostenlos |	Basic |	Premium |
|	------												|	----	|	-----	|	--------	|
|	**Berichte zu anomalen Aktivitäten**
|	Anmeldungen aus unbekannten Quellen |	✓ |	✓ |	✓ |
|	Anmeldungen nach mehreren Fehlern |	✓ |	✓ |	✓ |
|	Anmeldungen aus mehreren geografischen Regionen |	✓ |	✓ |	✓ |
|	Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten | | |	✓ |
|	Anmeldungen von möglicherweise infizierten Geräten | | |	✓ |
|	Irreguläre Anmeldeaktivitäten | | |	✓ |
|	Benutzer mit anomalen Anmeldeaktivitäten | | |	✓ |
|	Benutzer mit kompromittierten Anmeldeinformationen | | |	✓ |
|	**Aktivitätsprotokolle**
|	Überwachungsbericht | | |	✓ |
|	Kennwortzurücksetzungsaktivität | | |	✓ |
|	Aktivität "Registrierung für Zurücksetzen des Kennworts" | | |	✓ |
|	Self-Service-Gruppenaktivität | | |	✓ |
|	**Integrierte Anwendungen**
|	Anwendungsnutzung | | |	✓ |
|	Kontobereitstellungsaktivität |	✓ |	✓ |	✓ |
|	Status des Kennwortrollovers | | |	✓ |
|	Kontobereitstellungsfehler |	✓ |	✓ |	✓ |
|	**Rechteverwaltung**
|	RMS-Nutzung | | |	Nur RMS |
|	Aktivste RMS-Benutzer | | |	Nur RMS |
|	Nutzung von RMS-Geräten | | |	Nur RMS |
|	Nutzung RMS-fähiger Anwendungen | | |	Nur RMS |









## Berichte zu anomalen Aktivitäten

### Anmeldungen aus unbekannten Quellen

Weitere Informationen zu diesem Bericht finden Sie unter [Anmeldungen aus unbekannten Quellen](active-directory-reporting-sign-ins-from-unknown-sources.md).

### Anmeldungen nach mehreren Fehlern

Weitere Informationen zu diesem Bericht finden Sie unter [Anmeldungen nach mehreren Fehlern](active-directory-sign-ins-after-multiple-failures.md).

### Anmeldungen aus mehreren geografischen Regionen

Weitere Informationen zu diesem Bericht finden Sie unter [Anmeldungen aus mehreren geografischen Regionen](active-directory-sign-ins-from-multiple-geographies.md).

### Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten

Weitere Informationen zu diesem Bericht finden Sie unter [Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md).

### Irreguläre Anmeldeaktivitäten

Weitere Informationen zu diesem Bericht finden Sie unter [Irreguläre Anmeldeaktivitäten](active-directory-reporting-irregular-sign-in-activity.md).

### Anmeldungen von möglicherweise infizierten Geräten

Weitere Informationen zu diesem Bericht finden Sie unter [Anmeldungen von möglicherweise infizierten Geräten](active-directory-reporting-sign-ins-from-possibly-infected-devices.md).

### Benutzer mit anomalen Anmeldeaktivitäten

Weitere Informationen zu diesem Bericht finden Sie unter [Benutzer mit anomalen Anmeldeaktivitäten](active-directory-reporting-users-with-anomalous-sign-in-activity.md).

<!-- Users with leaked credentials goes here -->









## Aktivitätsprotokolle

### Überwachungsbericht

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Zeigt eine Aufzeichnung aller überwachten Ereignisse in den letzten 24 Stunden, letzten 7 Tagen oder letzten 30 Tagen. <br /> Weitere Informationen finden Sie unter [Azure Active Directory-Überwachungsberichtsereignisse](active-directory-reporting-audit-events.md). | Verzeichnis > Registerkarte "Berichte" |

![Überwachungsbericht](./media/active-directory-view-access-usage-reports/auditReport.PNG)

### Kennwortzurücksetzungsaktivität

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Zeigt alle Versuche der Kennwortzurücksetzung, die in Ihrer Organisation erfolgt sind. | Verzeichnis > Registerkarte "Berichte" |

![Kennwortzurücksetzungsaktivität](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)

### Aktivität "Registrierung für Zurücksetzen des Kennworts"

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Zeigt alle Registrierungen für die Kennwortzurücksetzung, die in Ihrer Organisation erfolgt sind. | Verzeichnis > Registerkarte "Berichte" |

![Aktivität "Registrierung für Zurücksetzen des Kennworts"](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)

### Self-Service-Gruppenaktivität

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Zeigt alle Aktivitäten der per Self-Service verwalteten Gruppen in Ihrem Verzeichnis. | Verzeichnis > Benutzer > <i>Benutzer</i> > Registerkarte "Geräte" |

![Self-Service-Gruppenaktivität](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)











## Integrierte Anwendungsberichte

### Anwendungsnutzung: Zusammenfassung

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| In diesem Bericht können Sie die Nutzung aller SaaS-Anwendungen in Ihrem Verzeichnis ablesen. Dieser Bericht basiert darauf, wie oft Benutzer im Zugriffsbereich auf die Anwendung geklickt haben. | Verzeichnis > Registerkarte "Berichte" |

![Zusammenfassung Anwendungsnutzung](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)


### Anwendungsnutzung: detailliert

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| In diesem Bericht können Sie die Nutzung einer bestimmten SaaS-Anwendung in Ihrem Verzeichnis ablesen. Dieser Bericht basiert darauf, wie oft Benutzer im Zugriffsbereich auf die Anwendung geklickt haben. | Verzeichnis > Registerkarte "Berichte" |

### Anwendungsdashboard

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Dieser Bericht zeigt kumulativ die Anmeldungen an der Anwendung durch Benutzer in Ihrer Organisation über einen ausgewählten Zeitraum. Das Diagramm auf der Dashboardseite hilft Ihnen beim Ausmachen von Trends der gesamten Nutzung der Anwendung. | Verzeichnis > Anwendung > Registerkarte "Dashboard" |

## Fehlerberichte

### Kontobereitstellungsfehler

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Dieser Bericht dient zum Überwachen von Fehlern, die während der Synchronisierung von Konten aus SaaS-Anwendungen mit Azure Active Directory auftreten. | Verzeichnis > Registerkarte "Berichte" |

![Kontobereitstellungsfehler](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)









## Benutzerspezifische Berichte

### Geräte

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| In diesem Bericht finden Sie die IP-Adresse und den geografischen Standort der Geräte, die ein bestimmter Benutzer für Zugriff auf Azure Active Directory verwendet hat. | Verzeichnis > Benutzer > <i>Benutzer</i> > Registerkarte "Geräte" |

### Aktivität

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Zeigt die Anmeldeaktivität eines Benutzers. Der Bericht enthält Informationen wie die Anwendung, bei der die Anmeldung erfolgt ist, das verwendete Gerät, die IP-Adresse und den Standort. Wir erfassen nicht den Verlauf von Benutzern, die sich mit einem Microsoft-Konto anmelden. | Verzeichnis > Benutzer > <i>Benutzer</i> > Registerkarte "Aktivität" |

#### Im Bericht "Benutzeraktivität" enthaltene Anmeldeereignisse

Nur bestimmte Arten von Anmeldeereignissen werden im Bericht "Benutzeraktivität" angezeigt.

| Ereignistypen | Enthalten? |
| ----------------------								| ---------		|
| Anmeldungen beim [Zugriffsbereich](http://myapps.microsoft.com/) | Ja |
| Anmeldungen beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) | Ja |
| Anmeldungen beim [Microsoft Azure-Portal](http://portal.azure.com/) | Ja |
| Anmeldungen beim [Office 365-Portal](http://portal.office.com/) | Ja |
| Anmeldungen bei einer systemeigenen Anwendung wie Outlook (siehe nachfolgende Ausnahme) | Ja |
| Anmeldungen bei einer Verbund-/bereitgestellten App über den Zugriffsbereich wie z. B. Salesforce | Ja |
| Anmeldungen bei einer kennwortbasierten App über den Zugriffsbereich wie z. B. Twitter | Ja |
| Anmeldungen bei einer benutzerdefinierten Geschäfts-App, die dem Verzeichnis hinzugefügt wurde | Nein (in Kürze verfügbar) |
| Anmeldungen bei einer Azure AD-Anwendungsproxy-App, die dem Verzeichnis hinzugefügt wurde | Nein (in Kürze verfügbar) |

> Hinweis: Um diese Bericht übersichtlich zu halten, werden Anmeldungen bei der systemeigenen App [Lync/Skype for Business](http://products.office.com/de-de/skype-for-business/online-meetings) und durch den [Microsoft Online Services-Anmelde-Assistenten](http://community.office365.com/de-de/w/sso/534.aspx) nicht gezeigt.










## Zu beachtende Aspekte bei einer vermuteten Sicherheitsverletzung

Wenn Sie vermuten, dass ein Benutzerkonto ggf. gefährdet ist, oder von einer verdächtigen Benutzeraktivität ausgehen, die zu einer Sicherheitsverletzung Ihrer Verzeichnisdaten in der Cloud führen könnte, sollten Sie eine oder mehrere der folgenden Aktionen erwägen:

- Den Benutzer kontaktieren, um die Aktivität zu überprüfen
- Das Kennwort des Benutzers zurücksetzen
- Die [Multi-Factor Authentication](http://go.microsoft.com/fwlink/?linkid=335774) zum Erhöhen der Sicherheit aktivieren

## Anzeigen oder Herunterladen eines Berichts

1. Klicken Sie im Azure-Verwaltungsportal auf **Active Directory**, dann auf den Namen des Verzeichnisses Ihrer Organisation und danach auf **Berichte**.
2. Klicken Sie auf der Seite "Berichte" auf den Bericht, den Sie anzeigen und/oder herunterladen möchten.

    > [AZURE.NOTE]Wenn Sie die Berichtsfunktion von Azure Active Directory zum ersten Mal verwenden, wird eine Aktivierungsmeldung angezeigt. Wenn Sie zustimmen, klicken Sie auf das Häkchensymbol, um den Vorgang fortzusetzen.

3. Klicken Sie auf das Dropdownmenü neben "Intervall", und wählen Sie dann einen der folgenden Zeiträume aus, der zum Generieren des Berichts verwendet werden soll:
    - Letzte 24 Stunden
    - Letzte 7 Tage
    - Letzte 30 Tage
4. Klicken Sie auf das Häkchensymbol, um den Bericht auszuführen.
	- Im Azure-Verwaltungsportal werden bis zu 1000 Ereignisse angezeigt.
5. Falls zutreffend, klicken Sie auf **Herunterladen**, um den Bericht in einer komprimierten Datei im CSV-Format herunterzuladen, um ihn offline anzeigen oder zu archivieren.
	- In der heruntergeladenen Datei werden bis zu 75.000 Ereignisse aufgenommen.

## Ignorieren von Ereignissen

Wenn Sie Anomalieberichte prüfen, stellen Sie möglicherweise fest, dass Sie verschiedene Ereignisse ignorieren können, die in verwandten Berichten enthalten sind. Wenn Sie ein Ereignis ignorieren möchten, markieren Sie einfach das Ereignis im Bericht und klicken dann auf **Ignorieren**. Die Schaltfläche **Ignorieren** dient zum dauerhaften Entfernen des markierten Ereignisses aus dem Bericht und kann nur von lizenzierten globalen Administratoren verwendet werden.

## Automatische E-Mail-Benachrichtigungen

Weitere Informationen zu den Benachrichtigungsfunktionen für Berichte in Azure AD finden Sie unter [Azure Active Directory-Benachrichtigungsfunktionen für Berichte](active-directory-reporting-notifications.md).

## Nächste Schritte

- [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)

<!---HONumber=July15_HO4-->