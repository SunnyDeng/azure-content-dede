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
   ms.date="02/23/2016"
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

## Nächste Schritte

In diesem Dokument wurde gezeigt, wie Sie die Security Center-Empfehlung „Datenträgerverschlüsselung anwenden“ implementieren. Weitere Informationen zur Datenträgerverschlüsselung finden Sie unter folgenden Themen:

- [Encryption and key management with Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (Verschlüsselung und Schlüsselverwaltung mit dem Azure-Schlüsseltresor, Video, 36 Min. 39 Sek.): Erfahren Sie, wie Sie die Verwaltung der Datenträgerverschlüsselung für IaaS-VMs und den Azure-Schlüsseltresor nutzen, um Ihre Daten besser zu schützen.
- [Azure Disk Encryption](../azure-security-disk-encryption.md) (Dokument): Enthält Informationen zum Aktivieren der Datenträgerverschlüsselung für Windows- und Linux-VMs.

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md): Erfahren Sie, wie Sie Sicherheitsrichtlinien konfigurieren.
- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md): Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
- [Verwalten von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md): Erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/): Hier finden Sie Blogbeiträge über Azure-Sicherheit und -Compliance.



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png

<!---HONumber=AcomDC_0224_2016-->