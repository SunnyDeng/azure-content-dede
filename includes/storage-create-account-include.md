## Erstellen eines Azure-Speicherkontos

Für Azure-Speicher benötigen Sie ein Speicherkonto. Mit den folgenden Schritten können Sie ein Speicherkonto erstellen. (Sie können ein Speicherkonto auch über die Azure-Dienstverwaltungs-Clientbibliothek oder die Dienstverwaltungs-[REST-API] erstellen.)

1.  Melden Sie sich beim [Azure-Verwaltungsportal] an.

2.  Klicken Sie unten auf der Seite auf **NEU**.

	![+Neu][plus-new]

3.  Klicken Sie auf **DATA SERVICES**, dann auf **SPEICHER** und anschließend auf **SCHNELLERFASSUNG**.

	![Dialogfeld "Schnellerfassung"][quick-create-storage]

4.  Geben Sie im Feld "URL" einen Unterdomänennamen ein, der im URI für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Wert wird der Hostname im URI, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.

5.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der Speicher befinden soll. Wenn Sie Speicher aus Ihrer Azure-Anwendung verwenden, wählen Sie die Region aus, in der Sie auch Ihre Anwendung bereitstellen.

6. Optional können Sie den gewünschten Replikationstyp für Ihr Konto auswählen. Die georedundante Replikation ist standardmäßig aktiviert und bietet maximale Dauerhaftigkeit. Weitere Informationen zu den Replikationsoptionen finden Sie unter [Redundanzoptionen für den Azure-Speicher](http://msdn.microsoft.com/library/azure/dn727290.aspx) und im [Azure Storage Team Blog](http://blogs.msdn.com/b/windowsazurestorage/).

6.  Klicken Sie auf **SPEICHERKONTO ERSTELLEN**.

[REST-API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure-Verwaltungsportal]: http://manage.windowsazure.com
[plus-new]: ./media/storage-create-account-include/plus-new.png
[quick-create-storage]: ./media/storage-create-account-include/quick-storage-2.png

<!--HONumber=52-->
