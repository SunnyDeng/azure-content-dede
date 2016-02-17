<properties
	pageTitle="Anmelden bei Azure über die Befehlszeilenschnittstelle | Microsoft Azure"
	description="Herstellen einer Verbindung mit Ihrem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/29/2015"
	ms.author="danlep"/>

# Herstellen einer Verbindung mit einem Azure-Abonnement von der Azure Befehlszeilenschnittstelle (Azure-CLI)

Die Azure-Befehlszeilenschnittstelle beinhaltet eine Reihe von plattformübergreifenden Open Source-Befehlen für die Arbeit mit der Azure Platform. Dieser Artikel beschreibt, wie Sie mit der Azure-Befehlszeilenschnittstelle eine Verbindung mit Ihrem Azure-Abonnement herstellen, damit Sie alle Befehle der Befehlszeilenschnittstelle nutzen können. Wenn Sie die Befehlszeilenschnittstelle noch nicht installiert haben, finden Sie alle erforderlichen Informationen unter [Installieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).



Es gibt zwei Möglichkeiten, über die Azure-Befehlszeilenschnittstelle eine Verbindung mit Ihrem Abonnement herzustellen:

* **Anmelden bei Azure mit einem Geschäfts- oder Schulkonto oder mit einer Microsoft-Kontoidentität**: Hierbei werden beide Kontoidentitätstypen zur Authentifizierung verwendet. Die aktuelle Befehlszeilenschnittstelle unterstützt auch die interaktive Authentifizierung für Konten, für die die mehrstufige Authentifizierung aktiviert ist. Nach der interaktiven Anmeldung können Sie entweder Ressourcen-Manager-Befehle oder klassische Befehle (über die Dienstverwaltung) verwenden.

* **Herunterladen und Verwenden einer Datei mit Veröffentlichungseinstellungen**: Hiermit wird ein Zertifikat auf dem lokalen Computer installiert, mit dem Sie Verwaltungsaufgaben ausführen können, solange das Abonnement und das Zertifikat gültig sind. Bei dieser Methode können Sie nur klassische Befehle (über die Dienstverwaltung) verwenden.

Weitere Informationen zur Authentifizierung und Abonnementverwaltung finden Sie unter [Was ist der Unterschied zwischen kontobasierter Authentifizierung und zertifikatbasierter Authentifizierung][authandsub].

Wenn Sie noch kein Azure-Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion][free-trial].

>[AZURE.NOTE] Bei Verwendung einer älteren Version der Azure-Befehlszeilenschnittstelle als 0.9.10 können Sie den Befehl `azure login` nur mit Geschäfts- oder Schulkontoidentitäten verwenden, Microsoft-Kontoidentitäten werden dagegen nicht unterstützt. Mit der Azure-Befehlszeilenschnittstelle ab Version 0.9.10 können Sie sich jedoch mit dem interaktiven Befehl `azure login` mit jeder Identität bei Ihrem Konto anmelden.
>
Befehlszeilenschnittstellen ab Version 0.9.9 unterstützen die mehrstufige Authentifizierung.



## Verwenden der interaktiven Anmeldemethode

Verwenden Sie den Befehl `azure login` – ohne Argumente – für die interaktive Authentifizierung mit:

- einer Geschäfts- oder Schulkontoidentität, für die die Multi-Factor Authentication erforderlich ist, oder
- einer Microsoft-Kontoidentität, wenn Sie auf die Funktionen des Ressourcen-Manager-Bereitstellungsmodus zugreifen möchten

> [AZURE.NOTE]  In beiden Fällen werden Authentifizierung und Autorisierung mit Azure Active Directory durchgeführt. Wenn Sie eine Microsoft-Kontoidentität verwenden, greift der Anmeldeprozess auf Ihre Azure Active Directory-Standarddomäne zu. (Wenn Sie sich für eine kostenlose Testversion registriert haben, ist Ihnen möglicherweise nicht bewusst, dass Azure Active Directory eine Standarddomäne für Ihr Konto erstellt hat.)

Geben Sie `azure login` ein, und befolgen Sie die unten gezeigten Anweisungen:

	azure login                                                                                                                                                                                         
	info:    Executing command login
	info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate. If you're signing in as an Azure AD application, use the --username and --password parameters.

Kopieren Sie den oben angegebenen Code, und öffnen Sie einen Browser für http://aka.ms/devicelogin. Geben Sie den Code ein. Dann werden Sie aufgefordert, den Benutzernamen und das Kennwort für die Identität einzugeben, die Sie verwenden möchten. Wenn dieser Vorgang abgeschlossen ist, schließt die Befehlsshell den Anmeldevorgang ab. Dies sieht möglicherweise wie folgt aus:

	info:    Added subscription Visual Studio Ultimate with MSDN
	info:    Added subscription Azure Free Trial
	info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
	+
	info:    login command OK

## Verwenden der nicht interaktiven Anmeldemethode mit einem Geschäfts- oder Schulkonto


Die nicht interaktive Anmeldemethode funktioniert nur mit einem Geschäfts- oder Schulkonto (auch *Organisationskonto* genannt). Dieses Konto wird von der Organisation verwaltet und ist im Azure Active Directory der Organisation definiert. Sie können [ein Organisationskonto erstellen](#create-an-organizational-account), wenn Sie noch nicht über eines verfügen, oder Sie können [eine Arbeits- oder Schulidentität über Ihre Microsoft-Kontoidentität erstellen](./virtual-machines/resource-group-create-work-id-from-personal.md). Hierzu müssen Sie entweder einen Benutzernamen oder einen Benutzernamen und ein Kennwort für den Befehl `azure login` angeben, beispielsweise:

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Geben Sie Ihr Kennwort ein, wenn Sie dazu aufgefordert werden.

	If this is your first time logging in with these credentials, you are asked to verify that you wish to cache an authentication token. This prompt also occurs if you have previously used the `azure logout` command (described below). To bypass this prompt for automation scenarios, run `azure login` with the `-q` parameter.

* Verwenden Sie den folgenden Befehl, um sich **abzumelden**:

		azure logout -u <username>

	Wenn die mit dem Konto verknüpften Abonnements nur über Active Directory authentifiziert wurden, werden bei der Abmeldung die Abonnementinformationen aus dem lokalen Profil gelöscht. Wenn jedoch auch eine Datei mit Veröffentlichungseinstellungen für die Abonnements importiert wurde, dann werden durch die Abmeldung nur die auf Active Directory bezogenen Informationen aus dem lokalen Profil gelöscht.

## Verwenden der Methode mit der Veröffentlichungseinstellungendatei

Wenn Sie nur die klassischen Befehle der Befehlszeilenschnittstelle (über die Dienstverwaltung) benötigen, können Sie die Verbindung mithilfe einer Datei mit Veröffentlichungseinstellungen herstellen.

* Mit dem folgenden Befehl können Sie die **Veröffentlichungseinstellungen für Ihr Konto herunterladen**:

		azure account download

Ihr Standardbrowser wird geöffnet, und Sie werden aufgefordert, sich beim [klassischen Azure-Portal][portal] anzumelden. Nach der Anmeldung wird eine `.publishsettings`-Datei heruntergeladen. Notieren Sie sich den Speicherort der Datei.

> [AZURE.NOTE] Wenn Ihr Konto mit mehreren Azure Active Directory-Mandanten verknüpft ist, werden Sie möglicherweise aufgefordert, das Active Directory auszuwählen, für das eine Datei mit Veröffentlichungseinstellungen heruntergeladen werden soll.
>
> Die Active Directory-Instanz, die Sie auf der Downloadseite oder im klassischen Azure-Portal ausgewählt haben, wird zur Standardinstanz, die vom klassischen Portal und der Downloadseite verwendet wird. Nachdem eine Standardeinstellung festgelegt wurde, wird am oberen Rand der Downloadseite der Text '__click here to return to the selection page__' (Klicken Sie hier, um zur Auswahlseite zurückzukehren) angezeigt. Klicken Sie auf den bereitgestellten Link, um zur Auswahlseite zurückzukehren.

* Führen Sie den folgenden Befehl aus, um die **Datei mit den Veröffentlichungseinstellungen zu importieren**:

		azure account import <path to your .publishsettings file>

	Nach dem Importieren der Veröffentlichungseinstellungen, sollten Sie die Datei `.publishsettings` löschen, denn diese wird nicht mehr für die Azure-CLI benötigt und stellt ein Sicherheitsrisiko dar, da sie für den Zugriff auf Ihr Abonnement verwendet werden kann.


## Mehrere Abonnements

Wenn Sie mehrere Azure-Abonnements besitzen, erhalten Sie durch das Herstellen einer Verbindung mit Azure Zugriff auf alle Abonnements, die mit Ihren Anmeldeinformationen verknüpft sind. Ein Abonnement wird als Standardabonnement ausgewählt und von der Azure-Befehlszeilenschnittstelle zum Ausführen von Vorgängen verwendet. Mit dem Befehl `azure account list` können Sie die Abonnements anzeigen und herausfinden, welches Abonnement das Standardabonnement ist. Die Ausgabe dieses Befehls sieht etwa so aus:

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

In der oben angeführten Liste gibt die Spalte **Aktuell** das aktuelle Standardabonnement als Azure-sub-1 an. Um das Standardabonnement zu ändern, verwenden Sie den Befehl `azure account set`, und geben Sie das Abonnement an, das als Standard verwendet werden soll. Beispiel:

	azure account set Azure-sub-2

Damit wird das Standardabonnement in "Azure-sub-2" geändert.

> [AZURE.NOTE] Die Änderung des Standardabonnements tritt sofort in Kraft, und es ist eine globale Änderung. Für weitere Befehle der Azure-Befehlszeilenschnittstelle wird das neue Standardabonnement verwendet, unabhängig davon, ob die Befehle in derselben oder einer anderen Befehlsschnittstelleninstanz ausgeführt werden.

Wenn Sie in der Azure-Befehlszeilenschnittstelle ein anderes Abonnement als das Standardabonnement verwenden, aber das aktuelle Standardabonnement nicht ändern möchten, können Sie die Option `--subscription` und den Namen des Abonnements angeben, das Sie für den Vorgang verwenden möchten.

Nachdem Sie mit Ihrem Azure-Abonnement verbunden sind, können Sie beginnen, die Befehle der Azure-Befehlszeilenschnittstelle zu verwenden.

## Speichern der Einstellungen für die Befehlszeilenschnittstelle

Unabhängig davon, ob Sie sich mit einem Geschäfts- oder Schulkonto anmelden oder Veröffentlichungseinstellungen importieren, werden Ihr Profil und die Protokolle für die Befehlszeilenschnittstelle in einem `.azure`-Verzeichnis gespeichert, das sich in Ihrem `user`-Verzeichnis befindet. Ihr `user`-Verzeichnis wird durch das Betriebssystem geschützt; es wird jedoch empfohlen, zusätzliche Schritte zum Verschlüsseln Ihres `user`-Verzeichnisses auszuführen. Sie haben dazu folgende Möglichkeiten:

* Windows: Ändern Sie die Verzeichniseigenschaften, oder verwenden Sie BitLocker.
* Mac: Aktivieren Sie FileVault für das Verzeichnis.
* Ubuntu: Verwenden Sie die Homeverzeichnis-Verschlüsselung. Andere Linux-Distributionen bieten ähnliche Funktionen.

## Zusätzliche Ressourcen

* [Verwenden der Azure-Befehlszeilenschnittstelle mit (klassischen) Dienstverwaltungsbefehlen][cliasm]

* [Verwenden der Azure-Befehlszeilenschnittstelle mit Ressourcen-Manager-Befehlen][cliarm]

* Wenn Sie weitere Informationen zur Azure-Befehlszeilenschnittstelle erhalten, den Quellcode herunterladen, Probleme melden oder etwas zum Projekt beitragen möchten, besuchen Sie die Webseite [GitHub repository for the Azure CLI](https://github.com/azure/azure-xplat-cli).

* Wenn Sie Probleme bei der Verwendung der Azure-Befehlszeilenschnittstelle oder Azure haben, besuchen Sie die [Azure-Foren](http://social.msdn.microsoft.com/Forums/windowsazure/home).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0204_2016-->