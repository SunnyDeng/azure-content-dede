<properties
   pageTitle="Azure AD Reporting: Erste Schritte"
   description="Azure AD Reporting: Erste Schritte"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2015"
   ms.author="curtand;kenhoff"/>

# Erste Schritte mit Azure AD Reporting

## Funktionsbeschreibung

Azure Active Directory umfasst Sicherheits-, Aktivitäts- und Prüfberichte für Ihr Verzeichnis. Hier sehen Sie eine Liste der enthaltenen Berichte:

### Sicherheitsberichte

- Anmeldungen aus unbekannten Quellen
- Anmeldungen nach mehreren Fehlern
- Anmeldungen aus mehreren geografischen Regionen
- Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten
- Irreguläre Anmeldeaktivitäten
- Anmeldungen von möglicherweise infizierten Geräten
- Benutzer mit anomalen Anmeldeaktivitäten

### Aktivitätsberichte

- Anwendungsnutzung: Zusammenfassung
- Anwendungsnutzung: detailliert
- Anwendungsdashboard
- Kontobereitstellungsfehler
- Geräte einzelner Benutzer
- Aktivität einzelner Benutzer
- Gruppenaktivitätsbericht
- Bericht zur Registrierung für die Kennwortzurücksetzung
- Kennwortzurücksetzungsaktivität

### Prüfberichte

- Verzeichnisprüfbericht

> [AZURE.TIP]Weitere Dokumentationen zu Azure AD Reporting finden Sie unter [Anzeigen von Zugriffs- und Nutzungsberichten](active-directory-view-access-usage-reports.md).



## So funktioniert's


### Berichterstellungspipeline

Die Berichterstellungspipeline umfasst drei wesentliche Schritte. Jedes Mal, wenn sich ein Benutzer anmeldet oder eine Authentifizierung erfolgt, geschieht Folgendes:

- Erstens wird der Benutzer authentifiziert (erfolgreich oder nicht erfolgreich), und das Ergebnis wird in den Azure Active Directory-Dienstdatenbanken gespeichert.
- In regelmäßigen Abständen werden alle aktuellen Anmeldungen verarbeitet. Dabei durchsuchen Algorithmen für Sicherheit und anomale Aktivität alle aktuellen Anmeldungen nach verdächtigen Aktivitäten.
- Nach der Verarbeitung werden die Berichte geschrieben, zwischengespeichert und im Azure-Verwaltungsportal bereitgestellt.

### Berichterstellungszeiten

Aufgrund der großen Anzahl von Authentifizierungen und Anmeldungen, die von der Azure AD-Plattform verarbeitet werden, sind die aktuellsten verarbeiteten Anmeldungen im Durchschnitt eine Stunde alt. In seltenen Fällen sind die aktuellsten verarbeiteten Anmeldungen bis zu 8 Stunden alt.

Sie finden die aktuellste verarbeitete Anmeldung im Hilfetext am oberen Rand jedes Berichts.

![Hilfetext am oberen Rand jedes Berichts](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [AZURE.TIP]Weitere Dokumentationen zu Azure AD Reporting finden Sie unter [Anzeigen von Zugriffs- und Nutzungsberichten](active-directory-view-access-usage-reports.md).



## Erste Schritte


### Melden Sie sich beim Azure-Verwaltungsportal an.

Zunächst müssen Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) als globaler oder Kompatibilitätsadministrator anmelden. Sie müssen außerdem ein Azure-Abonnementdienstadministrator oder -Co-Administrator sein, bzw. das Azure-Abonnement "Zugriff auf Azure AD" benutzen.

### Navigieren zu Berichten

Um Berichte anzuzeigen, navigieren Sie zur Registerkarte "Berichte" am Anfang des Verzeichnisses.

Wenn Sie zum ersten Mal Berichte anzeigen, müssen Sie ein Dialogfeld bestätigen, bevor Sie die Berichte anzeigen können. So wird sichergestellt, dass die Administratoren in Ihrer Organisation das Anzeigen dieser Daten akzeptieren, die in einigen Ländern als private Informationen gelten könnten.

![Dialogfeld](./media/active-directory-reporting-getting-started/dialogBox.png)

### Untersuchen der einzelnen Berichte

Navigieren Sie zu jedem Bericht, um die gesammelten Daten und die verarbeiteten Anmeldungen zu sehen. Sie finden [hier eine Liste aller Berichte](active-directory-reporting-what-it-is.md).

![Alle Berichte](./media/active-directory-reporting-getting-started/reportsMain.png)

### Herunterladen der Berichte als CSV-Datei

Jeder Bericht kann als CSV-Datei (durch Trennzeichen getrennte Werte) heruntergeladen werden. Sie können diese Dateien in Excel, PowerBI oder Analyseprogrammen von Drittanbietern öffnen, um Ihre Daten näher zu analysieren.

Um einen Bericht im CSV-Format herunterzuladen, navigieren Sie zum Bericht, und klicken Sie unten auf "Herunterladen".

![Schaltfläche "Herunterladen"](./media/active-directory-reporting-getting-started/downloadButton.png)

> [AZURE.TIP]Weitere Dokumentationen zu Azure AD Reporting finden Sie unter [Anzeigen von Zugriffs- und Nutzungsberichten](active-directory-view-access-usage-reports.md).





## Nächste Schritte

### Anpassen von Warnungen bei anomaler Anmeldungsaktivität

Wechseln Sie zur Registerkarte "Konfigurieren" Ihres Verzeichnisses.

Führen Sie einen Bildlauf zum Abschnitt "Benachrichtigungen" aus.

Aktivieren oder deaktivieren Sie den Abschnitt "E-Mail-Benachrichtigungen über anomale Anmeldungen".

![Abschnitt "Benachrichtigungen"](./media/active-directory-reporting-getting-started/notificationsSection.png)

### Integrieren in die Azure AD Reporting-API

Informationen hierzu finden Sie unter [Erste Schritte mit der Azure AD Reporting-API](active-directory-reporting-api-getting-started.md).

### Einbinden der mehrstufigen Authentifizierung von Benutzern

Wählen Sie einen Benutzer in einem Bericht aus.

Klicken Sie am unteren Bildschirmrand auf die Schaltfläche "MFA aktivieren".

![Schaltfläche "Multi-Factor Authentication" am unteren Bildschirmrand](./media/active-directory-reporting-getting-started/mfaButton.png)

> [AZURE.TIP]Weitere Dokumentationen zu Azure AD Reporting finden Sie unter [Anzeigen von Zugriffs- und Nutzungsberichten](active-directory-view-access-usage-reports.md).




## Weitere Informationen


### Überwachen von Ereignissen

Informationen dazu, welche Ereignisse im Verzeichnis überwacht werden, finden Sie unter [Azure Active Directory-Überwachungsberichtsereignisse](active-directory-reporting-audit-events.md).

### API-Integration

Informationen hierzu finden Sie unter [Erste Schritte mit der Azure AD Reporting-API](active-directory-reporting-api-getting-started.md) und in der [API-Referenzdokumentation](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### Setzen Sie sich mit uns in Verbindung

Unter der E-Mail-Adresse [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) erhalten Sie Feedback, Hilfe und Antworten auf Ihre Fragen!

> [AZURE.TIP]Weitere Dokumentationen zu Azure AD Reporting finden Sie unter [Anzeigen von Zugriffs- und Nutzungsberichten](active-directory-view-access-usage-reports.md).

<!---HONumber=July15_HO3-->