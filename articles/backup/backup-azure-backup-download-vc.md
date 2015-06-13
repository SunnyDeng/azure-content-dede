<properties
	pageTitle="Tresoranmeldedaten in Azure Backup herunterladen"
	description="Erfahren Sie, wie Vault-Anmeldeinformationen verwenden, um den Computer mit dem sicherungstresor und Azure-Sicherungsdienst zu authentifizieren"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/27/2015"
	ms.author="prvijay"/>

# Depot-Anmeldeinformationen verwenden zur Authentifizierung mit Azure Backup-Dienst

Der lokale Server (Windows-Client oder Server mit Windows Server oder SCDPM) muss mit einem sicherungstresor authentifiziert werden, bevor Daten in Azure gesichert werden kann. Die Authentifizierung erfolgt mithilfe von "Anmeldeinformationen vault". Das Konzept der tresoranmeldedaten ähnelt das Konzept einer "Veröffentlichungseinstellungen" Datei die in Azure PowerShell verwendet wird.

## Was ist der Anmeldeinformationsdatei Tresor?

Die tresoranmeldedatendatei ist ein Zertifikat, das vom Portal für jede sicherungstresor generiert wird. Das Portal lädt dann den öffentlichen Schlüssel zu der Access Control Service (ACS). Der private Schlüssel des Zertifikats wird dem Benutzer als Teil des Workflows zur Verfügung gestellt als Eingabe in der Registrierung Statuscomputerworkflow erteilt wird. Dies authentifiziert den Computer, um die Sicherungsdaten an einem identifizierten Depot in der Azure-Sicherungsdienst zu senden.

Lohnt sich aufrufen, dass die Depot-Anmeldeinformationen nur während der Registrierung Workflow verwendet wird. Es ist Aufgabe des Benutzers, um sicherzustellen, dass die tresoranmeldedatendatei nicht beeinträchtigt wird. Fällt in den Händen eines Rogue-Benutzers, kann die tresoranmeldedatendatei verwendet werden, andere Computer mit dem gleichen Tresor zu registrieren. Wie die Sicherungsdaten verschlüsselt ist, verwenden eine Passphrase ein, der an den Kunden gehört, können keine vorhandene Sicherungsdaten beeinträchtigt werden. Um dieses Problem zu verringern, die Depot-Anmeldeinformationen festgelegt ist, der in 48 Std. ablaufen. Können Sie den tresoranmeldedaten von einem sicherungstresor oft – herunterladen kann jedoch nur die neuesten Tresor Anmeldeinformationsdatei während des Workflows für die Registrierung.

## Herunterladen der Anmeldeinformationsdatei Tresor

Der Anmeldeinformationsdatei Depot wird über einen sicheren Kanal aus dem Azure-Portal heruntergeladen werden. Der Azure-Sicherungsdienst ist keine Kenntnis von den privaten Schlüssel des Zertifikats und des privaten Schlüssels ist in das Portal oder den Dienst nicht beibehalten. Gehen Sie folgendermaßen vor, die Depot-Anmeldeinformationen auf einem lokalen Computer herunterladen.

1.  Melden Sie sich bei der [Verwaltungsportal](https://manage.windowsazure.com/)
2.  Klicken Sie auf **Recovery Services** im linken Navigationsbereich, und wählen Sie den sicherungstresor, in dem Sie erstellt haben. Klicken Sie auf das cloudsymbol, um die Schnellstart-Ansicht des sicherungstresors zu erreichen. <br/> ![Schnellansicht][1]

3.  Klicken Sie auf der Seite "Schnellstart" auf **Download-tresoranmeldedaten**. Das Portal generiert der Tresor-Anmeldeinformationsdatei, die zum Download zur Verfügung gestellt wird. <br/> ![Herunterladen][2]

4.  Das Portal generiert Tresoranmeldeinformationen mit einer Kombination aus dem Tresornamen und dem aktuellen Datum. Klicken Sie auf **Speichern** auf die Depot-Anmeldeinformationen auf dem lokalen Konto-Ordner "Downloads" herunterladen, oder wählen Sie im Menü "Speichern", geben Sie einen Speicherort für die tresoranmeldedaten speichern unter.

## Hinweis
+ Ab März 2015 Benutzer verfügen nicht über eine programmgesteuerte (z.B.: PowerShell) Möglichkeit tresoranmeldedaten herunterladen.

+ Sicherstellen Sie, dass die Anmeldeinformationen Tresor wird gespeichert, an einem Ort, die von Ihrem Computer zugegriffen werden kann. Wenn sie in einer Freigabe/SMB-Datei gespeichert ist, überprüfen Sie für die Zugriffsberechtigungen.

+ Die tresoranmeldedatendatei ist nur während der Registrierung Workflow verwendet.

+ Die tresoranmeldedatendatei läuft ab nach 48 Std. und über das Portal heruntergeladen werden kann.

+ Azure-Sicherung finden Sie unter [– häufig gestellte Fragen](backup-azure-backup-faq.md) Fragen im Workflow.


<!--Image references-->
[1]: ./media/backup-azure-backup-download-vc/quickview.png
[2]: ./media/backup-azure-backup-download-vc/downloadvc.png

<!---HONumber=GIT-SubDir--> 