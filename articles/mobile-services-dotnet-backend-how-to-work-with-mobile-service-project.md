<properties pageTitle="Work with a .NET backend mobile service" metaKeywords="Web API, mobile service, Azure, controllers" description="Provides examples on how to define, register, and use server-side components in Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />


# Arbeiten mit einem mobilen .NET-Back-End-Dienst

Dieser Artikel enthält detaillierte Informationen und Beispiele zum Arbeiten mit einem Visual Studio .NET-Back-End-Projekt, das Ihren mobilen Dienst in Azure Mobile Services definiert. Der Artikel ist in folgende Abschnitte unterteilt:

+ [Einführung][Einführung]
+ [Tabellenvorgänge][Tabellenvorgänge]
	+ [Gewusst wie: Registrierung für Tabellenvorgänge][Gewusst wie: Registrierung für Tabellenvorgänge]
	+ [Gewusst wie: Außerkraftsetzen der Standardantwort][Gewusst wie: Außerkraftsetzen der Standardantwort]
	+ [Gewusst wie: Außerkraftsetzen des Ausführungserfolgs][Gewusst wie: Außerkraftsetzen des Ausführungserfolgs]
	+ [Gewusst wie: Außerkraftsetzen der Standard-Fehlerbehandlung][Gewusst wie: Außerkraftsetzen der Standard-Fehlerbehandlung]
	+ [Gewusst wie: Hinzufügen benutzerdefinierter Parameter][Gewusst wie: Hinzufügen benutzerdefinierter Parameter]
	+ [Gewusst wie: Arbeiten mit Tabellenbenutzern][Gewusst wie: Arbeiten mit Tabellenbenutzern]
+ [Benutzerdefinierte API][Benutzerdefinierte API]
	+ [Gewusst wie: Definieren einer benutzerdefinierten API][Gewusst wie: Definieren einer benutzerdefinierten API]
	+ [Gewusst wie: Implementieren von HTTP-Methoden][Gewusst wie: Implementieren von HTTP-Methoden]
	+ [Gewusst wie: Senden und Empfangen von Daten als XML][Gewusst wie: Senden und Empfangen von Daten als XML]
	+ [Gewusst wie: Arbeiten mit Benutzern und Headern in benutzerdefinierten APIs][Gewusst wie: Arbeiten mit Benutzern und Headern in benutzerdefinierten APIs]
	+  [Gewusst wie: Definieren mehrerer Routen in einer benutzerdefinierten API][Gewusst wie: Definieren mehrerer Routen in einer benutzerdefinierten API]
+ [Auftragsplaner][Auftragsplaner]
	+ [Gewusst wie: Definieren geplanter Auftragsskripts][Auftragsplaner]
+ [Quellcodeverwaltung, freigegebener Code und Hilfsfunktionen][Quellcodeverwaltung, freigegebener Code und Hilfsfunktionen]
	+ [Gewusst wie: Laden von Node.js-Modulen][Gewusst wie: Laden von Node.js-Modulen]
	+ [Gewusst wie: Arbeiten mit Hilfsfunktionen][Gewusst wie: Arbeiten mit Hilfsfunktionen]
	+ [Gewusst wie: Freigeben von Code mithilfe von Quellcodeverwaltung][Gewusst wie: Freigeben von Code mithilfe von Quellcodeverwaltung]
	+ [Gewusst wie: Arbeiten mit App-Einstellungen][Gewusst wie: Arbeiten mit App-Einstellungen]
+ [Arbeiten mit dem Befehlszeilentool][Arbeiten mit dem Befehlszeilentool]
+ [Arbeiten mit Tabellen][Arbeiten mit Tabellen]
	+ [Gewusst wie: Tabellenzugriff in Skripts][Gewusst wie: Tabellenzugriff in Skripts]
	+ [Gewusst wie: Ausführen von Masseneinfügungen][Gewusst wie: Ausführen von Masseneinfügungen]
	+ [Gewusst wie: Zuordnen von JSON-Typen zu Datenbanktypen][Gewusst wie: Zuordnen von JSON-Typen zu Datenbanktypen]
	+ [Tabellenzugriff mit Transact-SQL][Tabellenzugriff mit Transact-SQL]
+ [Debuggen und Problembehandlung][Debuggen und Problembehandlung]
	+ [Gewusst wie: Schreiben von Ausgaben in die Mobile Service-Protokolle][Gewusst wie: Schreiben von Ausgaben in die Mobile Service-Protokolle]

## <a name="intro"></a>Einführung

In einem mobilen .NET-Back-End-Dienst können Sie Ihre eigene Geschäftslogik in Form von JavaScript-Code definieren, der auf dem Server abgelegt und ausgeführt wird. Dieser serverseitige Skriptcode ist einer der folgenden Serverfunktionen zugewiesen:

+ [Lese-, Schreib-, Änderungs- oder Löschvorgänge in einer bestimmten Tabelle][Tabellenvorgänge].
+ [Geplante Aufträge][Auftragsplaner].
+ [Definieren von HTTP-Methoden in einer benutzerdefinierten API][Benutzerdefinierte API].

Die Signatur der main-Funktion im Serverskript hängt davon ab, in welchem Kontext das Skript verwendet wird. Sie können auch CommonScript-Code als nodes.js-Module definieren, die von verschiedenen Skripts genutzt werden. Weitere Informationen finden Sie unter [Quellcodeverwaltung und freigegebener Code][Quellcodeverwaltung, freigegebener Code und Hilfsfunktionen].

Beschreibungen zu einzelnen Serverskript-Objekten und -Funktionen finden Sie unter [Skriptreferenz für Mobile Services-Server].



[Einführung]: #intro
[Tabellenvorgänge]: #table-scripts
[Gewusst wie: Registrierung für Tabellenvorgänge]: #register-table-scripts
[Gewusst wie: Außerkraftsetzen der Standardantwort]: #override-response
[Gewusst wie: Außerkraftsetzen des Ausführungserfolgs]: #override-success
[Gewusst wie: Außerkraftsetzen der Standard-Fehlerbehandlung]: #override-error
[Gewusst wie: Hinzufügen benutzerdefinierter Parameter]: #access-headers
[Gewusst wie: Arbeiten mit Tabellenbenutzern]: #work-with-users
[Benutzerdefinierte API]: #custom-api
[Gewusst wie: Definieren einer benutzerdefinierten API]: #define-custom-api
[Gewusst wie: Implementieren von HTTP-Methoden]: #handle-methods
[Gewusst wie: Senden und Empfangen von Daten als XML]: #api-return-xml
[Gewusst wie: Arbeiten mit Benutzern und Headern in benutzerdefinierten APIs]: #get-api-user
[Gewusst wie: Definieren mehrerer Routen in einer benutzerdefinierten API]: #api-routes
[Auftragsplaner]: #scheduler-scripts
[Quellcodeverwaltung, freigegebener Code und Hilfsfunktionen]: #shared-code
[Gewusst wie: Laden von Node.js-Modulen]: #modules-helper-functions
[Gewusst wie: Arbeiten mit Hilfsfunktionen]: #helper-functions
[Gewusst wie: Freigeben von Code mithilfe von Quellcodeverwaltung]: #shared-code-source-control
[Gewusst wie: Arbeiten mit App-Einstellungen]: #app-settings
[Arbeiten mit dem Befehlszeilentool]: #command-prompt
[Arbeiten mit Tabellen]: #working-with-tables
[Gewusst wie: Tabellenzugriff in Skripts]: #access-tables
[Gewusst wie: Ausführen von Masseneinfügungen]: #bulk-inserts
[Gewusst wie: Zuordnen von JSON-Typen zu Datenbanktypen]: #JSON-types
[Tabellenzugriff mit Transact-SQL]: #TSQL
[Debuggen und Problembehandlung]: #debugging
[Gewusst wie: Schreiben von Ausgaben in die Mobile Service-Protokolle]: #write-to-logs
