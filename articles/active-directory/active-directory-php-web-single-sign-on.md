<properties 
	pageTitle="Einmaliges Anmelden mit Azure Active Directory (PHP)" 
	description="Erfahren Sie, wie Sie eine PHP-Webanwendung erstellen, die einmaliges Anmelden mit Azure Active Directory verwendet." 
	services="active-directory" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="tomfitz"/>

# Einmalige Webanmeldung mit PHP und Azure Active Directory

##<a name="introduction"></a>Einführung

In diesem Lernprogramm erfahren PHP-Entwickler, wie sie Azure Active Directory zur einmaligen Anwendung von Office 365-Benutzern einsetzen können. Sie erhalten Informationen zu folgenden Themen:

* Bereitstellung der Webanwendung im Mandanten eines Kunden
* Schutz der Anwendung mit WS-Federation

###Voraussetzungen
Für diese Anleitung ist die folgende Entwicklungsumgebung erforderlich:

* [PHP-Beispielcode für Azure Active Directory]
* [Eclipse PDT 3.0.x All In Ones]
* PHP 5.3.1 (über den Webplattform-Installer)
* Internet Information Services (IIS) 7.5 mit aktiviertem SSL
* Windows PowerShell
* [Office 365 PowerShell-Cmdlets]

## Schritt 1: Erstellen einer PHP-Anwendung
In diesem Schritt wird die Erstellung einer einfachen PHP-Anwendung beschrieben, die eine geschützte Ressource darstellt. Der Zugriff auf diese Ressource erfolgt über eine Verbundauthentifizierung, die vom STS des Unternehmens verwaltet wird. Dieser wird später in diesem Lernprogramm beschrieben.

1. Öffnen Sie eine neue Eclipse-Instanz.
2. Klicken Sie im Menü **Datei** auf **Neu** und anschließend auf **Neues PHP-Projekt**. 
3. Geben Sie dem Projekt im Dialogfeld **Neues PHP-Projekt** den Namen *phpSample*, und klicken Sie auf **Fertig stellen**.
4. Klicken Sie im Menü **PHP-Explorer** auf der linken Seite mit der rechten Maustaste auf *phpProject*, und klicken Sie dann auf **Neu** sowie auf **PHP-Datei**.
5. Geben Sie der Datei im Dialogfeld **Neue PHP-Datei** den Namen **index.php**, und klicken Sie auf **Fertig stellen**.
6. Ersetzen Sie das generierte Markup durch den folgenden Code, und speichern Sie die Datei **index.php**:

		<!DOCTYPE>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index</title>
		</head>
		<body>
			##Index Page
		</body>
		</html> 

7. Öffnen Sie den **Internet Information Services (IIS)-Manager**, indem Sie unter "Ausführen" *inetmgr* eingeben und die Eingabetaste drücken.

8. Erweitern Sie im linken Menü des IIS-Managers den Ordner **Sites**, klicken Sie mit der rechten Maustaste auf **Standardwebsite**, und klicken Sie auf **Anwendung hinzufügen...**.

9. Setzen Sie im Dialogfeld **Anwendung hinzufügen** den Wert **Alias** auf *phpSample* und den **physikalischen Pfad** auf den Dateipfad, in dem Sie das PHP-Projekt erstellt haben.

10. Klicken Sie im Eclipse-Menü **Ausführen** auf **Ausführen**.

11. Klicken Sie im Menü **Run PHP Web Application** auf **OK**.

12. Die Seite **index.php** wird in einer neuen Registerkarte in Eclipse geöffnet. Die Seite sollte nur folgenden Text anzeigen: *Indexseite*.

## Schritte 2: Bereitstellen der Anwendung in einem Active Directory-Mandanten eines Unternehmens
Dieser Schritt beschreibt, wie ein Administrator eines Azure Active Directory-Kunden die PHP-Anwendung im Mandanten dieses Kunden bereitstellt und eine einmalige Anwendung konfiguriert. Wenn dieser Schritt ausgeführt ist, können die Mitarbeiter dieses Kunden sich über ihre Office 365-Konten auf der Webanwendung anmelden.

Der Bereitstellungsvorgang beginnt mit dem Erstellen eines neuen Dienstprinzipals für die Anwendung. Dienstprinzipale werden von Azure Active Directory verwendet, um Anwendungen im Verzeichnis zu registrieren und zu authentifizieren.

1. Laden Sie die Office 365 PowerShell Commandlets herunter, und installieren Sie sie, falls dies noch nicht geschehen ist.
2. Führen Sie im Menü **Start** die Konsole **Azure Active Directory-Modul für Windows PowerShell** aus. Diese Konsole bietet eine Befehlszeilenumgebung zur Konfiguration von Attributen Ihres Office 365-Mandanten, z. B. zum Erstellen und Ändern von Dienstprinzipalen.
3. Importieren Sie das erforderliche **MSOnlineExtended**-Modul, indem Sie den folgenden Befehl eingeben und die Eingabetaste drücken:

		Import-Module MSOnlineExtended -Force
4. Um eine Verbindung mit Ihrem Office 365-Verzeichnis herzustellen, müssen Sie die Anmeldedaten des Unternehmensadministrators eingeben. Geben Sie den folgenden Befehl ein, drücken Sie die Eingabetaste, und geben Sie dann an der Eingabeaufforderung Ihre Anmeldedaten ein:

		Connect-MsolService
5. Nun erstellen Sie einen neuen Dienstprinzipal für die Anwendung. Geben Sie den folgenden Befehl ein, und drücken Sie die Eingabetaste:

		New-MsolServicePrincipal -ServicePrincipalNames @("phpSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 
Die in diesem Schritt ausgegebenen Informationen sehen in etwa folgendermaßen aus:

		The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
		DisplayName           : Federation Sample PHP Website
		ServicePrincipalNames : {phpSample/localhost}
		ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
		AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
		TrustedForDelegation  : False
		AccountEnabled        : True
		KeyType               : Symmetric
		KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
		StartDate             : 12/01/2012 08:00:00 a.m.
		EndDate               : 12/01/2013 08:00:00 a.m.
		Usage                 : Verify 
> [AZURE.NOTE]Sie sollten diese Ausgabe, besonders den generierten symmetrischen Schlüssel, speichern. Dieser Schlüssel wird Ihnen nur während der Erstellung des Dienstprinzipals angezeigt, und Sie können ihn in Zukunft nicht mehr abrufen. Die anderen Werte werden zur Verwendung der Graph-API benötigt, um Informationen im Verzeichnis zu lesen und zu schreiben.

6. Im letzten Schritt wird die Antwort-URL für Ihre Anwendung eingerichtet. An die Antwort-URL werden Antworten nach Authentifizierungsversuchen gesendet. Geben Sie die folgenden Befehle ein, und drücken Sie die Eingabetaste:

		$replyUrl = New-MsolServicePrincipalAddresses –Address "https://localhost/phpSample" 

		Set-MsolServicePrincipal –AppPrincipalId "7829c758-2bef-43df-a685-717089474505" –Addresses $replyUrl 
	
Die Webanwendung wurde nun im Verzeichnis bereitgestellt und kann zur einmaligen Anwendung durch Unternehmensmitarbeiter verwendet werden.

## Schritt 3: Schützen der Anwendung mit WS-Federation zur Mitarbeiteranmeldung
In diesem Schritt erfahren Sie, wie Sie mit Windows Identity Foundation (WIF) und den simpleSAML.php-Bibliotheken, die Sie zu Beginn als Teil des Beispielcode-Pakets heruntergeladen haben, Unterstützung für eine Verbundanmeldung hinzufügen. Außerdem fügen Sie eine Anmeldeseite hinzu und konfigurieren eine Vertrauensstellung zwischen der Anwendung und dem Directory-Mandanten.

1. Klicken Sie in Eclipse mit der rechten Maustaste auf das Projekt **phpSample**, und klicken Sie dann auf **Neu** sowie auf **Datei**. 

2. Geben Sie der Datei im Dialogfeld **Neue Datei** den Namen **federation.ini**, und klicken Sie auf **Fertig stellen**.

3. Geben Sie in der Datei **federation.ini** die folgernden Informationen ein. Verwenden Sie die Werte, die Sie in Schritt 2 bei der Erstellung Ihres Dienstprinzipals gespeichert haben:

		federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
		federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
		federation.trustedissuers.friendlyname=Fabrikam
		federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
		federation.realm=spn:7829c758-2bef-43df-a685-717089474505
		federation.reply=https://localhost/phpSample/index.php 


	> [AZURE.NOTE]Den Werten **audienceuris** und **realm** muss die Zeichenfolge "spn:" vorausgehen.

4. Klicken Sie in Eclipse mit der rechten Maustaste auf das Projekt **phpSample**, und klicken Sie dann auf **Neu** sowie auf **PHP-Datei**.

5. Geben Sie der Datei im Dialogfeld **Neue PHP-Datei** den Namen **secureResource.php**, und klicken Sie auf **Fertig stellen**.

6. Geben Sie in der neuen Datei **secureResource.php** den folgenden Code ein. Ersetzen sie dabei den Pfad **c:\phpLibraries** durch das Stammverzeichnis, in das Sie den Beispielcode heruntergeladen haben. Das Stammverzeichnis sollte die Datei **simpleSAML.php** und den Ordner **federation** enthalten:

		<?php
		ini_set('include_path', ini_get('include_path').';c:\phpLibraries\;');
		require_once ('federation/FederatedLoginManager.php');
		session_start();
		$token = $_POST['wresult'];
		$loginManager = new FederatedLoginManager();
		if (!$loginManager->isAuthenticated()) {
			if (isset ($token)) {
				try {
					$loginManager->authenticate($token);
				} catch (Exception $e) {
					print_r($e->getMessage());
				}  
			} else {
				$returnUrl = "https://" . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'];
				header('Pragma: no-cache');
				header('Cache-Control: no-cache, must-revalidate');
				header("Location: " . FederatedLoginManager :: getFederatedLoginUrl($returnUrl), true, 302);
				exit();
			}
		}
		?> 

7. Öffnen Sie die Seite **index.php**, aktualisieren Sie ihren Inhalt, um die Seite zu sichern, und speichern Sie sie:

		<?php
		require_once (dirname(__FILE__) . '/secureResource.php');
		?>
		<!DOCTYPE html>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index Page</title>
		</head>
		<body>
			<h2>Index Page</h2>
			<h3>Welcome <strong><?php print_r($loginManager->getPrincipal()->getName()); ?></strong>!</h3>
			<h4>Claim list:</h4>
			<ul>
				<?php
				foreach ($loginManager->getClaims() as $claim) {
					print_r('<li>' . $claim->toString() . '</li>');
				}
				?>  
			</ul>
		</body>
		</html> 

8. Klicken Sie im Menü **Ausführen** auf **Ausführen**. Sie sollten dann automatisch auf die Seite "Office 365-Identitätsanbieter" weitergeleitet werden, auf der Sie sich mit den Anmeldedaten für Ihren Directory-Mandanten anmelden können. Beispiel: *john.doe@fabrikam.onmicrosoft.com*.

## Zusammenfassung
In diesem Lernprogramm haben Sie erfahren, wie Sie eine PHP-Anwendung für einen einzelnen Mandanten erstellen und konfigurieren können, die die Möglichkeiten einer einmaligen Anwendung mit Azure Active Directory nutzt.

Ein Beispiel für die Verwendung von Azure Active Directory und des einmaligen Anmeldens für PHP-Websites finden Sie unter <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP>.


[Step 1: Create a PHP Application]: #createapp
[Step 2: Provision the Application in a Company's Directory Tenant]: #provisionapp
[Step 3: Protect the Application Using WS-Federation for Employee Sign In]: #protectapp
[Summary]: #summary
[Introduction]: #introduction
[Developing Multi-Tenant Cloud Applications with Azure Active Directory]: http://g.microsoftonline.com/0AX00en/121
[Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/download/details.aspx?id=4451
[Windows Identity Foundation 1.0 Runtime]: http://www.microsoft.com/download/details.aspx?id=17331
[Office 365 PowerShell-Cmdlets]: http://msdn.microsoft.com/library/azure/jj151815.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/download/details.aspx?id=4211
[Eclipse PDT 3.0.x All In Ones]: http://www.eclipse.org/pdt/downloads/
[PHP-Beispielcode für Azure Active Directory]: https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP
 

<!---HONumber=62-->