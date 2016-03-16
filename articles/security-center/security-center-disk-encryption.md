<properties
   pageTitle="Verschlüsseln eines virtuellen Azure-Computers | Microsoft Azure"
   description="Dieses Dokument hilft Ihnen dabei, einen virtuellen Azure-Computer zu verschlüsseln, wenn Sie eine Warnung vom Azure Security Center erhalten haben."
   services="security-center"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/03/2016"
   ms.author="tomsh"/>

# Verschlüsseln eines virtuellen Azure-Computers
Azure Security Center gibt eine Warnung aus, wenn Sie über nicht verschlüsselte virtuelle Computer verfügen. Dies wird als Warnung mit hohem Schweregrad angezeigt. Empfohlen wird in diesem Fall die Verschlüsselung der virtuellen Computer.

![Empfehlung für Datenträgerverschlüsselung](./media/security-center-disk-encryption\security-center-disk-encryption-fig1.png)

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

Zum Verschlüsseln virtueller Azure-Computer, für die von Azure Security Center festgestellt wurde, dass sie verschlüsselt werden müssen, empfehlen wir die folgenden Schritte:

- Installieren und konfigurieren Sie Azure PowerShell. So können Sie die PowerShell-Befehle ausführen, die zum Einrichten der für die Verschlüsselung virtueller Azure-Computer erforderlichen Voraussetzungen notwendig sind.
- Rufen Sie das für Azure Disk Encryption erforderliche PowerShell-Skript ab, und führen Sie es aus.
- Verschlüsseln Ihrer virtuellen Computer

Ziel dieses Dokuments ist es, Ihnen das Verschlüsseln Ihrer virtuellen Computer zu ermöglichen, auch wenn Sie über nur wenige oder gar keine Kenntnisse zu Azure PowerShell verfügen. In diesem Dokument wird davon ausgegangen, dass Sie Windows 10 als Clientcomputer verwenden, von dem aus Sie Azure Disk Encryption konfigurieren.
 
Ihnen stehen verschiedene Methoden zur Verfügung, um die Voraussetzungen einzurichten und die Verschlüsselung für virtuelle Azure-Computer zu konfigurieren. Wenn Sie bereits über Kenntnisse in Azure PowerShell oder Azure-CLI verfügen, ziehen Sie möglicherweise andere Methoden vor.

> [AZURE.NOTE] Weitere Informationen zu alternativen Methoden zum Konfigurieren der Verschlüsselung für virtuelle Azure-Computer finden Sie unter [Azure Disk Encryption for Windows and Linux Azure Virtual Machines](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) (Azure Disk Encryption für virtuelle Azure-Computer unter Windows und Linux).

## Installieren und Konfigurieren von Azure PowerShell
Auf Ihrem Computer muss Azure PowerShell Version 1.2.1 oder höher installiert sein. Der Artikel [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md) beschreibt alle Schritte, die auszuführen sind, damit Azure PowerShell auf Ihrem Computer funktioniert. Die einfachste Methode besteht in der Verwendung der Web PI-Installation, die in dem o. g. Artikel beschrieben ist. Auch wenn Sie Azure PowerShell bereits installiert haben, installieren Sie es erneut mit der Web PI-Methode, damit Sie über die neueste Version von Azure PowerShell verfügen.


## Rufen Sie das für Azure Disk Encryption erforderliche Konfigurationsskript ab, und führen Sie es aus.
Das für Azure Disk Encryption erforderliche Konfigurationsskript richtet alle Voraussetzungen ein, die zum Verschlüsseln Ihrer virtuellen Azure-Computer erforderlich sind.

1.	Rufen Sie die GitHub-Seite auf, auf der sich [das für Azure Disk Encryption erforderliche Setupskript](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1) befindet.
2.	Klicken Sie auf der Seite GitHub auf die Schaltfläche **Roh**.
3.	Verwenden Sie **STRG + A**, um den gesamten Text auf der Seite auszuwählen, und anschließend **STRG + C**, um den gesamten Text auf der Seite in die Zwischenablage zu kopieren.
4.	Öffnen Sie den **Editor**, und fügen Sie den kopierten Text in den Editor ein. 
5.	Erstellen Sie auf Laufwerk C: einen neuen Ordner namens **AzureADEScript**.
6.	Speichern Sie die Editor-Datei – klicken Sie auf **Datei** und anschließend auf **Speichern unter**. Geben Sie im Textfeld für den Dateinamen **„ADEPrereqScript.ps1“** ein, und klicken Sie auf **Speichern**. Vor und hinter dem Namen müssen Anführungszeichen eingegeben werden, da die Datei andernfalls mit der Dateierweiterung .txt gespeichert wird. 

Nachdem Sie den Inhalt des Skripts nun gespeichert haben, öffnen Sie das Skript in PowerShell ISE:

1.	Klicken Sie in im Startmenü auf **Cortana**. Fragen Sie **Cortana** nach „PowerShell“, indem Sie in das Suchfeld von Cortana **PowerShell** eingeben. 
2.	Klicken Sie mit der rechten Maustaste auf **Windows PowerShell ISE** und anschließend auf **Als Administrator ausführen**.
3.	Klicken Sie im Fenster **Administrator: Windows PowerShell ISE** auf **Ansicht** und anschließend auf **Skriptbereich anzeigen**. 
4.	Wenn Ihnen auf der rechten Seite des Fensters der Bereich **Befehle** angezeigt wird, schließen Sie ihn, indem Sie rechts oben auf **„x“** klicken. Wenn der Text so klein ist, dass Sie ihn nicht richtig erkennen können, verwenden Sie **STRG + Hinzufügen** („Hinzufügen“ ist das Zeichen „+“). Wenn der Text zu groß ist, verwenden Sie **STRG + Subtrahieren** („Subtrahieren“ ist das Zeichen „-“). 
5.	Klicken Sie auf **Datei** und anschließend auf **Öffnen**. Navigieren Sie zum Ordner **C:\\AzureADEScript** und doppelklicken Sie auf **ADEPrereqScript**.
6.	Der Inhalt von **ADEPrereqScript** sollte nun in PowerShell ISE erscheinen. Dieser ist farbkodiert, damit Sie verschiedene Komponenten wie etwa Befehle, Parameter und Variablen besser sehen. 

Ihnen sollte nun in etwa Folgendes angezeigt werden:

![PowerShell ISE-Fenster](./media/security-center-disk-encryption\security-center-disk-encryption-fig2.png)

Der obere Bereich wird als „Skriptbereich“ bezeichnet, und der untere Bereich als „Konsole“. Diese Begriffe werden später in diesem Artikel verwendet.

## Führen Sie den für Azure Disk Encryption erforderliche PowerShell-Befehl aus.

Wenn Sie das für Azure Disk Encryption erforderliche Skript starten, werden Sie zur Eingabe der folgenden Informationen aufgefordert:

- **Ressourcengruppenname** – Name der Ressourcengruppe, in die der Schlüsseltresor aufgenommen werden soll. Eine neue Ressourcengruppe mit dem eingegebenen Namen wird erstellt, sofern noch keine Gruppe mit diesem Namen besteht. Wenn Sie bereits über eine Ressourcengruppe verfügen, die in diesem Abonnement verwendet werden soll, geben Sie den Namen der entsprechenden Ressourcengruppe an.
- **Schlüsseltresorname** – Name des Schlüsseltresors, in dem Schlüssel platziert werden sollen. Ein neuer Schlüsseltresor mit diesem Namen wird erstellt, sofern Sie nicht bereits über einen Schlüsseltresor mit diesem Namen verfügen. Wenn Sie bereits über einen Schlüsseltresor verfügen, der in diesem Abonnement verwendet werden soll, geben Sie den Namen des entsprechenden Schlüsseltresors an.
- **Speicherort** – Speicherort des Schlüsseltresors. Der Schlüsseltresor und die zu verschlüsselnden virtuellen Computer müssen sich am gleichen Speicherort befinden. Wenn Sie den Speicherort nicht kennen, finden Sie später in diesem Artikel die Schritte, mit denen Sie den Speicherort feststellen können. 
- **Azure Active Directory-Anwendungsname** – Name der Azure Active Directory-Anwendung, die verwendet wird, um geheime Schlüssel in den Schlüsseltresor zu schreiben. Sofern noch nicht vorhanden, wird eine neue Anwendung mit diesem Namen erstellt. Wenn Sie bereits über eine Azure Active Directory-Anwendung verfügen, die Sie verwenden möchten, geben Sie den Namen dieser Azure Active Directory-Anwendung ein.

> [AZURE.NOTE] Wenn Sie wissen möchten, weshalb Sie eine Azure Active Directory-Anwendung erstellen müssen, finden Sie unter *Registrieren einer Anwendung mit Azure Active Directory* im Artikel [Erste Schritte mit dem Azure-Schlüsseltresor](../key-vault/key-vault-get-started.md) weitere Informationen.

Führen Sie die folgenden Schritte aus, um einen virtuellen Azure-Computer zu verschlüsseln:

1.	Wenn Sie PowerShell ISE geschlossen haben, öffnen Sie eine erhöhte Instanz von PowerShell ISE. Befolgen Sie die weiter oben in diesem Artikel beschriebenen Anweisungen, wenn PowerShell ISE nicht bereits geöffnet ist. Wenn Sie das Skript geschlossen haben, öffnen Sie **ADEPrereqScript.ps1**, indem Sie auf **Datei**und anschließend auf **Öffnen** klicken, und wählen das Skript aus dem Ordner **c:\\AzureADEScript** aus. Wenn Sie die Anweisungen in diesem Artikel von Beginn an ausgeführt haben, gehen Sie zum nächsten Schritt weiter. 
2.	Ändern Sie in der Konsole von PowerShell ISE (im unteren Bereich von PowerShell ISE) den lokalen Speicherort des Skripts, indem Sie **cd c:\\AzureADEScript** eingeben und danach die **EINGABETASTE** betätigen.
3.	Legen Sie die Ausführungsrichtlinie auf Ihrem Computer fest, damit Sie das Skript ausführen können. Geben Sie in der Konsole **Set-ExecutionPolicy Unrestricted** ein, und betätigen Sie dann die EINGABETASTE. Wenn ein Dialogfeld erscheint, das auf die Auswirkungen der Änderung zu der Ausführungsrichtlinie hinweist, klicken Sie entweder auf **Ja, alle** oder auf **Ja** (wenn Ihnen die Option **Ja, alle** angezeigt wird, wählen Sie diese aus, andernfalls wählen Sie **Ja**). 
4.	Melden Sie sich bei Ihrem Azure-Konto an. Geben Sie in der Konsole **Login-AzureRmAccount** ein, und betätigen Sie die **EINGABETASTE**. Ein Dialogfeld wird angezeigt, in dem Sie Ihre Anmeldeinformationen eingeben (dabei müssen Sie über die Rechte zum Ändern der virtuellen Computer verfügen, da Sie diese andernfalls nicht verschlüsseln können. Wenden Sie sich bei Fragen dazu an den Besitzer des Abonnements oder an den Administrator). Ihnen sollten Informationen zu Folgendem angezeigt werden: **Umgebung**, **Konto**, **TenantId**, **SubscriptionId** und **CurrentStorageAccount**. Kopieren Sie die **SubscriptionId** in den Editor. Diese ist in Schritt Nr. 6 erforderlich.
5.	Ermitteln Sie das Abonnement, zum dem Ihr virtueller Computer gehört, sowie den Speicherort. Melden Sie sich bei [https://portal.azure.com](ttps://portal.azure.com) an. Klicken Sie im linken Bereich der Seite auf **Virtuelle Computer**. Daraufhin wird eine Liste Ihrer virtuellen Computer mit den dazugehörige Abonnements angezeigt.

	![Virtual Machines](./media/security-center-disk-encryption\security-center-disk-encryption-fig3.png)

6.	Kehren Sie zu PowerShell ISE zurück. Legen Sie den Abonnementkontext fest, in dem das Skript ausgeführt wird. Geben Sie in der Konsole **Select-AzureRmSubscription –SubscriptionId <your_subscription_Id>** ein (ersetzen Sie **< your_subscription_Id >** mit Ihrer Abonnement-ID), und betätigen Sie die **EINGABETASTE**. Daraufhin werden Informationen zu Folgendem angezeigt: Umgebung, **Konto**, **TenantId**, **SubscriptionId** und **CurrentStorageAccount**.
7.	Nun können Sie das Skript ausführen. Klicken Sie auf die Schaltfläche **Skript ausführen** oder drücken Sie auf der Tastatur auf **F5**.

	![Ausführen eines PowerShell-Skripts](./media/security-center-disk-encryption\security-center-disk-encryption-fig4.png)

8.	Das Skript fragt nach **ResourceGroupName:** – geben Sie den Namen der *Ressourcengruppe* an, die Sie verwenden möchten, und betätigen Sie die **EINGABETASTE**. Geben Sie, falls Sie über keine solche Gruppe verfügen, einen Namen für eine neue Gruppe ein. Wenn bereits über eine *Ressourcengruppe* verfügen, die Sie verwenden möchten (z. B. diejenige, in der sich Ihr virtueller Computer befindet), geben Sie den Namen der vorhandenen Ressourcengruppe ein.
9.	Das Skript fragt nach **keyVaultName:** – Geben Sie den Namen des *Schlüsseltresors* an, den Sie verwenden möchten, und betätigen Sie die EINGABETASTE. Geben Sie, falls Sie über keinen solchen Tresor verfügen, einen Namen für einen neuen Tresor ein. Wenn Sie bereits über einen Schlüsseltresor verfügen, der in diesem Abonnement verwendet werden soll, geben Sie den Namen des entsprechenden *Schlüsseltresors* an.
10.	Das Skript fragt nach **location:** – geben Sie den Namen des Speicherorts an, an dem sich der zu verschlüsselnde virtuelle Computer befindet, und betätigen Sie anschließend die **EINGABETASTE**. Wenn Sie den Speicherort vergessen haben, gehen Sie zurück zu Schritt Nr. 5.
11.	Das Skript fragt nach **aadAppName:** – geben Sie den Namen der **Azure Active Directory**-Anwendung an, die Sie verwenden möchten, und betätigen Sie anschließend die *EINGABETASTE*. Geben Sie, falls Sie über keinen solchen Tresor verfügen, einen Namen für einen neuen Tresor ein. Wenn Sie bereits über eine *Azure Active Directory-Anwendung* verfügen, die Sie verwenden möchten, geben Sie den Namen der vorhandenen *Azure Active Directory-Anwendung* ein.
12.	Ein Anmeldedialogfeld wird angezeigt. Geben Sie Ihre Anmeldeinformationen ein (Sie haben sich zwar bereits angemeldet, dies ist jedoch erneut erforderlich).
13.	Das Skript wird ausgeführt und nach Abschluss des Vorgangs werden Sie aufgefordert, die Werte von **aadClientID**, **aadClientSecret**, **diskEncryptionKeyVaultUrl** und **keyVaultResourceId** zu kopieren. Kopieren Sie jeden dieser Werte in die Zwischenablage, und fügen Sie sie in den Editor ein. 
14.	Kehren Sie zu PowerShell ISE zurück, und platzieren Sie den Cursor an das Ende der letzten Zeile. Betätigen Sie daraufhin die **EINGABETASTE**.

Die Ausgabe des Skripts sieht etwa wie folgt aus:

![PowerShell-Ausgabe](./media/security-center-disk-encryption\security-center-disk-encryption-fig5.png)

## Verschlüsseln des virtuellen Azure-Computers 

Sie können Ihren virtuellen Computer nun verschlüsseln. Wenn sich Ihr virtueller Computer in der gleichen Ressourcengruppe wie Ihr Schlüsseltresor befindet, können Sie zum Abschnitt mit den Verschlüsselungsschritten weitergehen. Wenn der virtuelle Computer sich jedoch nicht in der gleichen Ressourcengruppe wie der Schlüsseltresor befindet, geben Sie in die Konsole von PowerShell ISE Folgendes ein:

**$resourceGroupName = < Virtual\_Machine\_RG >**

Ersetzen Sie **< Virtual_Machine_RG >** mit dem Namen der Ressourcengruppe, in der sich die virtuellen Computer befindet (in einfachen Anführungszeichen). Betätigen Sie anschließend die **EINGABETASTE**. Geben Sie in die PowerShell ISE-Konsole Folgendes ein, um zu bestätigen, dass der richtige Ressourcengruppenname eingegeben wurde:

**$resourceGroupName**

Betätigen Sie die **EINGABETASTE**. Der Name der Ressourcengruppe, in der sich Ihre virtuellen Computer befinden, wird daraufhin angezeigt. Beispiel:

![PowerShell-Ausgabe](./media/security-center-disk-encryption\security-center-disk-encryption-fig6.png)

### Verschlüsselungsschritte

Geben Sie in PowerShell zunächst den Namen des zu verschlüsselnden virtuellen Computers an. Geben Sie in der Konsole Folgendes ein:

**$vmName = <’your\_vm\_name’>**

Ersetzen Sie **<’your\_vm\_name’>** durch den Namen des virtuellen Computers (in einfachen Anführungszeichen), und betätigen Sie dann die **EINGABETASTE**.

Geben Sie Folgendes ein, um zu bestätigen, dass der richtige Name des virtuellen Computers eingegeben wurde:

**$vmName**

Betätigen Sie die **EINGABETASTE**. Ihnen wird nun der Name des zu verschlüsselnden virtuellen Computers angezeigt. Beispiel:

![PowerShell-Ausgabe](./media/security-center-disk-encryption\security-center-disk-encryption-fig7.png)

Den Verschlüsselungsbefehl zum Verschlüsseln des virtuellen Computers kann auf zweierlei Weise ausgeführt werden. Die erste Methode besteht darin, in der PowerShell ISE-Konsole den folgenden Befehl einzugeben:

~~~
Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId
~~~

Betätigen Sie nach der Eingabe dieses Befehls die **EINGABETASTE**.

Die zweite Methode besteht darin, in den Skriptbereich (dem oberen Bereich von PowerShell ISE) zu klicken, um im Skript nach unten zu scrollen. Markieren Sie den oben aufgeführten Befehl, und klicken Sie dann mit der rechten Maustaste darauf und anschließend auf **Auswahl ausführen**, oder drücken Sie auf der Tastatur auf **F8**.

![PowerShell ISE](./media/security-center-disk-encryption\security-center-disk-encryption-fig8.png)

Unabhängig von der verwendeten Methode erscheint ein Dialogfeld, das Sie darüber informiert, dass es bis zum Abschluss des Vorgangs 10–15 Minuten dauert. Klicken Sie auf **Ja**.

Während des Verschlüsselungsvorgangs können Sie zum Azure-Portal zurückkehren und den Status des virtuellen Computers einsehen. Klicken Sie im linken Bereich der Seite auf **Virtuelle Computer**. Klicken Sie anschließend auf dem Blatt **Virtuelle Computer** auf den Namen des virtuellen Computers, der verschlüsselt wird. Auf dem angezeigten Blatt wird **Wird aktualisiert** als **Status** angezeigt. Dies zeigt, dass die Verschlüsselung gerade durchgeführt wird.

![Weitere Informationen zum virtuellen Computer](./media/security-center-disk-encryption\security-center-disk-encryption-fig9.png)

Kehren Sie zu PowerShell ISE zurück. Wenn das Skript abgeschlossen ist, wird Ihnen Folgendes angezeigt.

![PowerShell-Ausgabe](./media/security-center-disk-encryption\security-center-disk-encryption-fig10.png)

Damit Sie sehen können, dass der virtuelle Computer nun verschlüsselt ist, kehren Sie zum Azure-Portal zurück, und klicken Sie im linken Bereich der Seite auf **Virtuelle Computer**. Klicken Sie auf den Namen des verschlüsselten virtuellen Computers. Klicken Sie auf dem Blatt **Einstellungen** auf **Datenträger**.

![Einstellungsoptionen](./media/security-center-disk-encryption\security-center-disk-encryption-fig11.png)

Auf dem Blatt **Datenträger** sehen Sie, dass **Aktiviert** unter **Verschlüsselung** angegeben ist.

![Datenträgereigenschaften](./media/security-center-disk-encryption\security-center-disk-encryption-fig12.png)


## Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie einen virtuellen Azure-Computer verschlüsseln können. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md) – erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) – Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md) zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) – suchen Sie nach Blogbeiträgen über Azure-Sicherheit und -Compliance.

<!---HONumber=AcomDC_0309_2016-->