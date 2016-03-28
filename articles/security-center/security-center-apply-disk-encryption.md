<properties
   pageTitle="Anwenden der Datenträgerverschlüsselung in Azure Security Center | Microsoft Azure"
   description="In diesem Dokument wird gezeigt, wie Sie die Azure Security Center-Empfehlung **Datenträgerverschlüsselung anwenden** implementieren."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="terrylan"/>

# Anwenden der Datenträgerverschlüsselung in Azure Security Center

Azure Security Center empfiehlt die Anwendung der Datenträgerverschlüsselung, wenn Sie über Windows- oder Linux-VM-Datenträger verfügen, die nicht per Azure Disk Encryption verschlüsselt sind. Mit der Datenträgerverschlüsselung können Sie Ihre Windows- und Linux-IaaS-VM-Datenträger verschlüsseln. Die Verschlüsselung wird sowohl für die Betriebssystem- als auch für die Datenvolumes auf Ihrer VM empfohlen.


Bei der Datenträgerverschlüsselung werden das [BitLocker](https://technet.microsoft.com/library/cc732774.aspx)-Feature (Branchenstandard) von Windows und das [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)-Feature von Linux verwendet, um das Betriebssystem und die Daten zu verschlüsseln. Dies trägt zum Schutz Ihrer Daten und zur Erfüllung der Sicherheits- und Complianceanforderungen Ihres Unternehmens bei. Die Datenverschlüsselung ist in den [Azure-Schlüsseltresor](https://azure.microsoft.com/documentation/services/key-vault/) integriert, damit Sie die Schlüssel für die Datenträgerverschlüsselung und die geheimen Schlüssel unter Ihrem Schlüsseltresor-Abonnement steuern und verwalten können. Gleichzeitig können Sie sicherstellen, dass alle Daten auf den VM-Datenträgern im Ruhezustand in [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) verschlüsselt sind.

> [AZURE.NOTE] Azure Disk Encryption wird für die folgenden Windows Server-Betriebssysteme unterstützt: Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2. Eine Datenträgerverschlüsselung wird für die folgenden Linux-Betriebssysteme unterstützt: Ubuntu, CentOS, SUSE und SUSE Linux Enterprise Server (SLES).

## Implementieren der Empfehlung

1. Wählen Sie auf dem Blatt **Empfehlungen** die Option **Datenträgerverschlüsselung anwenden**.
2. Auf dem Blatt **Datenträgerverschlüsselung anwenden** wird eine Liste mit den VMs angezeigt, für die die Datenträgerverschlüsselung empfohlen wird.
3. Führen Sie die Schritte der Anleitung aus, um die Verschlüsselung auf diese VMs anzuwenden.

![][1]

Zum Verschlüsseln virtueller Azure-Computer, für die von Security Center festgestellt wurde, dass sie verschlüsselt werden müssen, empfehlen wir die folgenden Schritte:

- Installieren und konfigurieren Sie Azure PowerShell. So können Sie die PowerShell-Befehle ausführen, die zum Einrichten der für die Verschlüsselung virtueller Azure-Computer erforderlichen Voraussetzungen notwendig sind.
- Rufen Sie das für Azure Disk Encryption erforderliche PowerShell-Skript ab, und führen Sie es aus.
- Verschlüsseln Sie Ihre virtuellen Computer.

Unter [Verschlüsseln eines virtuellen Azure-Computers](security-center-disk-encryption.md) werden Sie durch diese Schritte geführt. In diesem Thema wird davon ausgegangen, dass Sie Windows 10 als Clientcomputer verwenden, von dem aus Sie die Datenträgerverschlüsselung konfigurieren.

Ihnen stehen verschiedene Methoden zur Verfügung, um die Voraussetzungen einzurichten und die Verschlüsselung für virtuelle Azure-Computer zu konfigurieren. Wenn Sie bereits über Kenntnisse in Azure PowerShell oder Azure-CLI verfügen, ziehen Sie möglicherweise andere Methoden vor. Informationen zu diesen anderen Methoden finden Sie unter [Azure-Datenträgerverschlüsselung](../azure-security-disk-encryption.md).



## Nächste Schritte

In diesem Dokument wurde gezeigt, wie Sie die Security Center-Empfehlung „Datenträgerverschlüsselung anwenden“ implementieren. Weitere Informationen zur Datenträgerverschlüsselung finden Sie unter folgenden Themen:

- [Encryption and key management with Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (Verschlüsselung und Schlüsselverwaltung mit dem Azure-Schlüsseltresor, Video, 36 Min. 39 Sek.): Erfahren Sie, wie Sie die Verwaltung der Datenträgerverschlüsselung für IaaS-VMs und den Azure-Schlüsseltresor nutzen, um Ihre Daten besser zu schützen.
- [Verschlüsseln eines virtuellen Azure-Computers](security-center-disk-encryption.md) (Dokument): Erfahren Sie, wie virtuelle Azure-Computer verschlüsselt werden.
- [Azure-Datenträgerverschlüsselung](../azure-security-disk-encryption.md) (Dokument): Enthält Informationen zum Aktivieren der Datenträgerverschlüsselung für Windows- und Linux-VMs.

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md): Erfahren Sie, wie Sie Sicherheitsrichtlinien konfigurieren.
- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md): Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
- [Verwalten von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md): Erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
- [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/): Hier finden Sie Blogbeiträge über Azure-Sicherheit und -Compliance.



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png

<!---HONumber=AcomDC_0316_2016-->