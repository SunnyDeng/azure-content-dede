## Erstellen eines Sicherungstresors für einen virtuellen Computer

Bei einem Sicherungstresor handelt es sich um eine Entität, in der alle Sicherungen und Wiederherstellungspunkte gespeichert werden, die im Laufe der Zeit erstellt wurden. Der Sicherungstresor enthält auch die Sicherungsrichtlinien, die auf die zu sichernden virtuellen Computer angewendet werden.

In der Abbildung sind die Beziehungen zwischen den verschiedenen Azure Backup-Entitäten dargestellt: ![Azure Backup-Entitäten und ihre Beziehungen](./media/backup-create-vault-for-vms/vault-policy-vm.png)

So erstellen Sie einen Sicherungstresor

1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com/) an.

2. Klicken Sie im Azure-Portal auf **Neu** > **Hybridintegration** > **Sicherung**. Wenn Sie auf **Sicherung** klicken, wechseln Sie automatisch zum klassischen Portal (siehe Abbildung nach dem Hinweis).

    ![Ibiza-Portal](./media/backup-create-vault-for-vms/Ibiza-portal-backup01.png)

    >[AZURE.NOTE] Wenn Ihr Abonnement zuletzt im klassischen Portal verwendet wurde, wird es möglicherweise im klassischen Portal geöffnet. Klicken Sie in diesem Fall zum Erstellen eines Sicherungstresors auf **Neu** > **Data Services** > **Recovery Services** > **Sicherungstresor** > **Schnellerfassung** (siehe Abbildung unten).

    ![Erstellen des Sicherungstresors](./media/backup-create-vault-for-vms/backup_vaultcreate.png)

3. Geben Sie unter **Name** einen Anzeigenamen für den Tresor ein. Der Name muss für das Azure-Abonnement eindeutig sein. Geben Sie einen Namen ein, der zwischen 2 und 50 Zeichen enthält. Er muss mit einem Buchstaben beginnen und darf nur Buchstaben, Zahlen und Bindestriche enthalten.

4. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Der Tresor muss sich in der gleichen Region wie die zu schützenden virtuellen Computer befinden. Wenn Sie über virtuelle Computer in verschiedenen Regionen verfügen, müssen Sie einen Sicherungstresor in jeder dieser Regionen erstellen. Es müssen keine Speicherkonten zum Speichern der Sicherungsdaten angegeben werden. Dies erfolgt automatisch über den Sicherungstresor und den Azure Backup-Dienst.

5. Wählen Sie unter **Abonnement** das Abonnement aus, das dem Sicherungstresor zugeordnet werden soll. Es sind nur dann mehrere Auswahlmöglichkeiten verfügbar, wenn Ihr Organisationskonto mehreren Azure-Abonnements zugeordnet ist.

5. Klicken Sie auf **Tresor erstellen**. Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Unten im Portal können Sie anhand der Benachrichtigungen den Status prüfen.

    ![Popupbenachrichtigung zur Erstellung des Tresors](./media/backup-create-vault-for-vms/creating-vault.png)

6. In einer der Mitteilungen wird bestätigt, dass der Tresor erfolgreich erstellt wurde. Er wird auf der Seite **Recovery Services** als **Aktiv** aufgelistet. Stellen Sie nach der Erstellung des Tresors sicher, dass Sie eine geeignete Speicherredundanzoption auswählen. Weitere Informationen finden Sie unter [Festlegen der Speicherredundanzoption im Sicherungstresor](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Liste der Sicherungstresore](./media/backup-create-vault-for-vms/backup_vaultslist.png)

7. Klicken Sie auf den Sicherungstresor, um die Seite **Schnellstart** zu öffnen, auf der die Anweisungen für die Sicherung von virtuellen Azure-Computern angezeigt werden.

    ![Anweisungen zur Sicherung von virtuellen Computern auf der Dashboard-Seite](./media/backup-create-vault-for-vms/vmbackup-instructions.png)

<!---HONumber=AcomDC_0302_2016-->