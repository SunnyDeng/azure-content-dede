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
	ms.date="06/23/2015"
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

## Beschreibungen der Berichte

|	Bericht |	Beschreibung |
|	------												|	-----																						|
|	Anmeldungen aus unbekannten Quellen |	Zeigt möglicherweise einen Versuch einer Anmeldung ohne Ablaufverfolgung an. |
|	Anmeldungen nach mehreren Fehlern |	Zeigt möglicherweise einen erfolgreichen Brute-Force-Angriff an. |
|	Anmeldungen aus mehreren geografischen Regionen |	Zeigt möglicherweise an, dass sich mehrere Benutzer mit dem gleichen Konto anmelden. |
|	Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten |	Zeigt möglicherweise eine erfolgreiche Anmeldung nach einem langfristigen Einbruchsversuch an. |
|	Anmeldungen von möglicherweise infizierten Geräten |	Gibt einen Versuch an, sich über möglicherweise infizierte Geräte anzumelden. |
|	Irreguläre Anmeldeaktivitäten |	Zeigt möglicherweise anomale Ereignisse bei Anmeldemustern von Benutzern an. |
|	Benutzer mit anomalen Anmeldeaktivitäten |	Gibt Benutzer an, deren Konten möglicherweise beschädigt sind. |
|	Benutzer mit kompromittierten Anmeldeinformationen |	Benutzer mit kompromittierten Anmeldeinformationen |
|	Überwachungsbericht |	Überwachte Ereignisse in Ihrem Verzeichnis |
|	Kennwortzurücksetzungsaktivität |	Bietet eine detaillierte Übersicht über das Zurücksetzen des Kennworts in Ihrer Organisation. |
|	Aktivität "Registrierung für Zurücksetzen des Kennworts" |	Bietet eine detaillierte Übersicht über Registrierungen für das Zurücksetzen des Kennworts in Ihrer Organisation. |
|	Self-Service-Gruppenaktivität |	Stellt ein Aktivitätsprotokoll aller Self-Service-Aktivitäten für Gruppen in Ihrem Verzeichnis bereit. |
|	Anwendungsnutzung |	Stellt eine Nutzungszusammenfassung für alle in Ihr Verzeichnis integrierten SaaS-Anwendungen bereit. |
|	Kontobereitstellungsaktivität |	Stellt einen Verlauf der Kontobereitstellungsversuche für externe Anwendungen bereit. |
|	Status des Kennwortrollovers |	Stellt eine ausführliche Übersicht über den Status des automatischen Kennwortrollovers von SaaS-Anwendungen zur Verfügung. |
|	Kontobereitstellungsfehler |	Zeigt eine Auswirkung auf den Zugriff von Benutzern auf externe Anwendungen an. |
|	RMS-Nutzung |	Enthält eine Übersicht zur Nutzung des Rights Management. |
|	Aktivste RMS-Benutzer |	Listet die Top 1000 aktiven Benutzer auf, die auf RMS-geschützte Dateien zugegriffen haben. |
|	Nutzung von RMS-Geräten |	Listet Geräte auf, die für den Zugriff auf RMS-geschützte Dateien verwendet werden. |
|	Nutzung RMS-fähiger Anwendungen |	Bietet die Nutzung von RMS-fähigen Anwendungen. |

## Berichte in den verschiedenen Editionen

|	Bericht |	Kostenlos |	Basic |	Premium |
|	------												|	----	|	-----	|	--------	|
|	Anmeldungen aus unbekannten Quellen |	✓ |	✓ |	✓ |
|	Anmeldungen nach mehreren Fehlern |	✓ |	✓ |	✓ |
|	Anmeldungen aus mehreren geografischen Regionen |	✓ |	✓ |	✓ |
|	Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten | | |	✓ |
|	Anmeldungen von möglicherweise infizierten Geräten | | |	✓ |
|	Irreguläre Anmeldeaktivitäten | | |	✓ |
|	Benutzer mit anomalen Anmeldeaktivitäten | | |	✓ |
|	Benutzer mit kompromittierten Anmeldeinformationen | | |	✓ |
|	Überwachungsbericht | | |	✓ |
|	Kennwortzurücksetzungsaktivität | | |	✓ |
|	Aktivität "Registrierung für Zurücksetzen des Kennworts" | | |	✓ |
|	Self-Service-Gruppenaktivität | | |	✓ |
|	Anwendungsnutzung | | |	✓ |
|	Kontobereitstellungsaktivität |	✓ |	✓ |	✓ |
|	Status des Kennwortrollovers | | |	✓ |
|	Kontobereitstellungsfehler |	✓ |	✓ |	✓ |
|	RMS-Nutzung | | |	Nur RMS |
|	Aktivste RMS-Benutzer | | |	Nur RMS |
|	Nutzung von RMS-Geräten | | |	Nur RMS |
|	Nutzung RMS-fähiger Anwendungen | | |	Nur RMS |

## Berichtdetails

### Anmeldungen aus unbekannten Quellen

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| <p>Dieser Bericht enthält die Benutzer, die sich erfolgreich bei Ihrem Verzeichnis angemeldet haben, während Ihnen eine Client-IP-Adresse zugewiesen war, die von Microsoft als IP-Adresse eines anonymen Proxys erkannt wurde. Diese Proxys werden häufig von Benutzern verwendet, die die IP-Adresse ihres Computers verbergen möchten, und können in böswilliger Absicht genutzt werden, weshalb Hacker mitunter mit diesen Proxys arbeiten. </p><p> Die Ergebnisse in diesem Bericht geben an, wie oft sich ein Benutzer bei Ihrem Verzeichnis von dieser Adresse aus angemeldet hat, und weisen die IP-Adresse des Proxys aus.</p> | Verzeichnis > Registerkarte "Berichte" |

![Anmeldungen aus unbekannten Quellen](./media/active-directory-view-access-usage-reports/signInsFromUnknownSources.PNG)]

### Anmeldungen nach mehreren Fehlern

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Dieser Bericht weist die Benutzer aus, die sich nach mehreren aufeinander folgenden misslungenen Anmeldeversuchen erfolgreich angemeldet haben. Mögliche Ursachen: <ul><li>Benutzer hat sein Kennwort vergessen</li><li>Benutzer ist Opfer eines erfolgreichen Brute-Force-Angriffs zum Erraten des Kennworts</li></ul><p>Die Ergebnisse dieses Berichts enthalten die Anzahl aufeinander folgender misslungener Anmeldeversuche vor der erfolgreichen Anmeldung und den Zeitstempel der ersten erfolgreichen Anmeldung</p><p><b>Berichtseinstellungen</b>: Sie können die minimale Anzahl aufeinander folgender misslungener Anmeldeversuche konfigurieren, die auftreten muss, bevor sie im Bericht ausgewiesen wird. Beim Vornehmen von Änderungen an dieser Einstellung ist zu beachten, dass diese Änderungen nicht für vorhandene misslungene Anmeldungen gelten, die derzeit im vorhandenen Bericht enthalten sind. Sie gelten jedoch für alle künftigen Anmeldungen. Änderungen an diesem Bericht können nur von lizenzierten Administratoren vorgenommen werden. | Verzeichnis > Registerkarte "Berichte" |

![Anmeldungen nach mehreren Fehlern](./media/active-directory-view-access-usage-reports/signInsAfterMultipleFailures.PNG)]


### Anmeldungen aus mehreren geografischen Regionen

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| <p>Dieser Bericht enthält erfolgreiche Anmeldeaktivitäten eines Benutzers, wobei zwei Anmeldungen aus verschiedenen Regionen zu stammen scheinen und die Zeit zwischen den Anmeldungen es dem Benutzer unmöglich macht, die Entfernung zwischen diesen Regionen reisend zurückgelegt zu haben. Mögliche Ursachen:</p><ul><li>Benutzer nutzt das Kennwort gemeinsam</li><li>Benutzer verwendet einen Remotedesktop, um einen Webbrowser für die Anmeldung zu starten</li><li>Ein Hacker hat sich aus einem anderen Land am Konto eines Benutzers angemeldet</li></ul><p>Die Ergebnisse in diesem Bericht enthalten die erfolgreichen Anmeldungsereignisse sowie die Zeit zwischen den Anmeldungen, die Regionen, aus denen die Anmeldungen stammen, und die geschätzte Reisezeit zwischen diesen Regionen.</p><p>Die gezeigte Reisezeit ist lediglich eine Schätzung und kann von der tatsächlichen Reisedauer zwischen den Standorten abweichen. Darüber hinaus werden keine Ereignisse für Anmeldungen zwischen benachbarten Regionen generiert.</p> | Verzeichnis > Registerkarte "Berichte" |

![Anmeldungen aus mehreren geografischen Regionen](./media/active-directory-view-access-usage-reports/signInsFromMultipleGeographies.PNG)]


### Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| <p>Dieser Bericht enthält die Anmeldeversuche über IP-Adressen, bei denen verdächtige Aktivitäten beobachtet wurden. Zu verdächtigen Aktivitäten gehören viele misslungene Anmeldeversuche von derselben IP-Adresse innerhalb eines kurzen Zeitraums und andere Aktivitäten, die als verdächtig eingestuft wurden. Dies kann ein Hinweis sein, dass ein Hacker versucht hat, sich von dieser IP-Adresse aus anzumelden.</p><p>Die Ergebnisse dieses Berichts enthalten Anmeldeversuche, die von einer IP-Adresse stammten, bei der verdächtige Aktivitäten beobachtet wurden, sowie die Zeitstempel der Anmeldungsversuche.</p> | Verzeichnis > Registerkarte "Berichte" |

![Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten](./media/active-directory-view-access-usage-reports/signInsFromIPAddressesWithSuspiciousActivity.PNG)]


### Anomale Anmeldeaktivitäten

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| <p>Dieser Bericht enthält Anmeldungen, die von unseren Algorithmen für maschinelles Lernen als "anomal" identifiziert wurden. Gründe, warum ein Anmeldeversuch als irregulär gekennzeichnet wird, sind unerwartete Anmeldeorte, die Tageszeit und Orte oder eine Kombination dieser Faktoren. Dies kann ein Hinweis sein, dass ein Hacker versucht hat, sich mit diesem Konto anzumelden. Der Algorithmus für maschinelles Lernen klassifiziert Ereignisse als "anomal" oder "verdächtig", wobei "verdächtig" für eine höhere Wahrscheinlichkeit einer Sicherheitsverletzung steht.</p><p>Die Ergebnisse dieses Berichts enthalten diese Anmeldungen samt Klassifizierung, Ort und Zeitstempel jeder Anmeldung.</p><p>Eine E-Mail-Benachrichtigung wird an die globalen Administratoren gesendet, wenn wir in einem Zeitraum von maximal 30 Tagen mindestens 10 anomale Anmeldeereignisse verzeichnen. Fügen Sie aad-alerts-noreply@mail.windowsazure.com unbedingt der Liste sicherer Absender hinzu.</p> | Verzeichnis > Registerkarte "Berichte" |



### Anmeldungen von möglicherweise infizierten Geräten

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| <p>Verwenden Sie diesen Bericht, um Anmeldungen von Geräten anzuzeigen, auf denen ggf. Schadsoftware ausgeführt wird. Wir korrelieren IP-Adressen von Anmeldungen mit IP-Adressen, von denen aus eine Anmeldung versucht wurde, um einen Schadsoftwareserver zu kontaktieren.</p><p>Empfehlung: Da dieser Bericht davon ausgeht, dass eine IP-Adresse in beiden Fällen dem gleichen Gerät zugeordnet war, wird empfohlen, dass Sie den Benutzer kontaktieren und das Gerät des Benutzers scannen, um sicher zu sein.</p><p>Weitere Informationen dazu, wie Sie bei Infektionen durch Schadsoftware reagieren, finden Sie im [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773). </p> | Verzeichnis > Registerkarte "Berichte" |

![Anmeldungen von möglicherweise infizierten Geräten](./media/active-directory-view-access-usage-reports/signInsFromPossiblyInfectedDevices.PNG)]


### Benutzer mit anomalen Anmeldeaktivitäten

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| <p>Verwenden Sie diesen Bericht, um alle Benutzerkonten anzuzeigen, für die anomale Anmeldeaktivitäten identifiziert wurden. Dieser Bericht enthält Daten aus allen anderen Berichten zu anomalen Aktivitäten. Ergebnisse in diesem Bericht enthalten Details zum Benutzer, zum Grund, warum das Anmeldeereignis als anomal eingestuft wurde, zu Datum und Uhrzeit und andere relevante Informationen zum Ereignis.</p> | Verzeichnis > Registerkarte "Berichte" |

![Benutzer mit anomalen Anmeldeaktivitäten](./media/active-directory-view-access-usage-reports/usersWithAnomalousSignInActivity.PNG)]


## Integrierte Anwendungsberichte

### Anwendungsnutzung: Zusammenfassung

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| In diesem Bericht können Sie die Nutzung aller SaaS-Anwendungen in Ihrem Verzeichnis ablesen. Dieser Bericht basiert darauf, wie oft Benutzer im Zugriffsbereich auf die Anwendung geklickt haben. | Verzeichnis > Registerkarte "Berichte" |

![Zusammenfassung Anwendungsnutzung](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)]


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

![Kontobereitstellungsfehler](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)]


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

## Aktivitätsprotokolle

### Überwachungsbericht

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Zeigt eine Aufzeichnung aller überwachten Ereignisse in den letzten 24 Stunden, letzten 7 Tagen oder letzten 30 Tagen. <br /> Weitere Informationen finden Sie unter [Azure Active Directory-Überwachungsberichtsereignisse](active-directory-reporting-audit-events.md). | Verzeichnis > Registerkarte "Berichte" |

![Überwachungsbericht](./media/active-directory-view-access-usage-reports/auditReport.PNG)]


### Gruppenaktivitätsbericht

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Zeigt alle Aktivitäten der per Self-Service verwalteten Gruppen in Ihrem Verzeichnis. | Verzeichnis > Benutzer > <i>Benutzer</i> > Registerkarte "Geräte" |

![Self-Service-Gruppenaktivität](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)]


### Bericht zur Registrierung für die Kennwortzurücksetzung

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Zeigt alle Registrierungen für die Kennwortzurücksetzung, die in Ihrer Organisation erfolgt sind. | Verzeichnis > Registerkarte "Berichte" |

![Aktivität "Registrierung für Zurücksetzen des Kennworts"](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)]


### Kennwortzurücksetzungsaktivität

| Beschreibung | Speicherort des Berichts |
| :-------------     | :-------        |
| Zeigt alle Versuche der Kennwortzurücksetzung, die in Ihrer Organisation erfolgt sind. | Verzeichnis > Registerkarte "Berichte" |

![Kennwortzurücksetzungsaktivität](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)]


## Zu beachtende Aspekte bei einer vermuteten Sicherheitsverletzung

Wenn Sie vermuten, dass ein Benutzerkonto ggf. gefährdet ist, oder von einer verdächtigen Benutzeraktivität ausgehen, die zu einer Sicherheitsverletzung Ihrer Verzeichnisdaten in der Cloud führen könnte, sollten Sie eine oder mehrere der folgenden Aktionen erwägen:

- Den Benutzer kontaktieren, um die Aktivität zu überprüfen
- Das Kennwort des Benutzers zurücksetzen
- Die [Multi-Factor Authentication](http://go.microsoft.com/fwlink/?linkid=335774) zum Erhöhen der Sicherheit aktivieren

## Anzeigen oder Herunterladen eines Berichts

1. Klicken Sie im Azure-Verwaltungsportal auf **Active Directory**, dann auf den Namen des Verzeichnisses Ihrer Organisation und danach auf **Berichte**.
2. Klicken Sie auf der Seite "Berichte" auf den Bericht, den Sie anzeigen und/oder herunterladen möchten.
    >
    > [AZURE.NOTE]

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

### Welche Berichte generieren E-Mail-Benachrichtigungen?

Derzeit wird das E-Mail-Benachrichtigungssystem nur für die Berichte "Anomale Anmeldeaktivitäten" und "Benutzer mit anomalen Anmeldeaktivitäten" genutzt.

### Was löst das Senden von E-Mail-Benachrichtigung aus?

Standardmäßig ist Azure Active Directory auf das automatische Senden von E-Mail-Benachrichtigungen an alle globalen Administratoren festgelegt. Die E-Mail wird unter folgenden Bedingungen für jeden Bericht gesendet.

Für den Bericht "Anomale Anmeldeaktivitäten":

- Unbekannte Quellen: 10 Ereignisse
- Mehrere Fehler: 10 Ereignisse
- IP-Adressen mit verdächtigen Aktivitäten: 10 Ereignisse
- Infizierte Geräte: 10 Ereignisse

Für den Bericht "Benutzer mit anomalen Anmeldeaktivitäten":

- Unbekannte Quellen: 10 Ereignisse
- Mehrere Fehler: 10 Ereignisse
- IP-Adressen mit verdächtigen Aktivitäten: 10 Ereignisse
- Infizierte Geräte: 5 Ereignisse
- Bericht "Anormale Anmeldungen": 15 Ereignisse

Die E-Mail wird gesendet, wenn eine der oben genannten Bedingungen binnen 30 Tagen erfüllt wurde oder wenn das Senden der letzten E-Mail weniger als 30 Tage zurückliegt.

Anormale Anmeldungen sind diejenigen, die von unseren Algorithmen für maschinelles Lernen auf der Basis unerwarteter Anmeldeorte, der Tageszeit und des Standorts oder einer Kombination dieser Faktoren als "anomal" identifiziert wurden. Dies kann ein Hinweis sein, dass ein Hacker versucht hat, sich mit diesem Konto anzumelden. Weitere Informationen zu diesem Bericht finden Sie in der obigen Tabelle.

### Wer empfängt die E-Mail-Benachrichtigungen?

Die E-Mail wird an alle globalen Administratoren mit einer Active Directory Premium-Lizenz gesendet. Um die Übermittlung sicherzustellen, wird sie auch an die alternative E-Mail-Adresse der Administratoren gesendet. Administratoren müssen aad-alerts-noreply@mail.windowsazure.com ihrer Liste sicherer Absender hinzufügen, damit sie die e-Mail nicht verpassen.

### Wie oft werden diese E-Mails gesendet?

Nachdem eine E-Mail gesendet wurde, wird die nächste erst dann gesendet, wenn mindestens 10 weitere neue anomale Anmeldeereignisse binnen 30 Tagen nach Senden dieser E-Mail erfolgt sind. Wie greife ich auf den in der E-Mail genannten Bericht zu?

Wenn Sie auf den Link klicken, werden Sie zur Berichtsseite im Azure-Verwaltungsportal weitergeleitet. Für den Zugriff auf den Bericht müssen Sie die beiden folgenden Rollen bekleiden:

- Administrator oder Co-Administrator Ihres Azure-Abonnements
- Globaler Administrator im Verzeichnis mit Active Directory Premium-Lizenz Weitere Informationen finden Sie unter "Azure Active Directory-Editionen".

### Kann ich diese E-Mails deaktivieren?

Ja, zum Deaktivieren von Benachrichtigungen in Zusammenhang mit anomalen Anmeldungen klicken Sie im Azure-Verwaltungsportal auf **Konfigurieren** und wählen **Deaktiviert** im Abschnitt **Benachrichtigungen** aus.

## Nächste Schritte

- [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)

<!---HONumber=58_postMigration-->