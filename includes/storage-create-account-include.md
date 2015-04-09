## Erstellen eines Azure-Speicherkontos

Für Azure-Speicher benötigen Sie ein Speicherkonto. Sie 
können ein Speicherkonto folgendermaßen erstellen. (Sie können
ein Speicherkonto auch mithilfe der Azure Service Management-Clientbibliothek oder der Dienstverwaltung [REST-API erstellen.)

1.  Melden Sie sich beim [Azure-Verwaltungsportal] an.

2.  Klicken Sie unten im Navigationsbereich auf **NEU**.

	![+new][plus-new]

3.  Klicken Sie auf **DATA SERVICES**, dann auf **SPEICHER** und anschließend auf **SCHNELLERFASSUNG**.

	![Quick create dialog][quick-create-storage]

4.  Geben Sie im Feld "URL" einen Unterdomänennamen ein, der im URI für das
    Speicherkonto verwendet werden soll. Der Eintrag kann aus 3 bis 24 Kleinbuchstaben
    und Zahlen bestehen. Dieser Wert wird der Hostname in dem URI, der
    zur Adressierung der Blob-, Warteschlangen- oder Tabellenressourcen für das
    Abonnement verwendet wird.

5.  Wählen Sie eine Region/Affinitätsgruppe aus, in der sich der
    Speicher befinden soll. Wenn Sie Speicher aus Ihrer Azure-
    Anwendung verwenden, wählen Sie die gleiche Region aus, in der Sie
    "Hello World"-Webanwendung.

6. Optional können Sie den gewünschten Replikationstyp für Ihr Konto auswählen. Die georedundante Replikation ist standardmäßig aktiviert und bietet maximale Dauerhaftigkeit. Weitere Informationen zu den Replikationsoptionen finden Sie unter [Redundanzoptionen für den Azure-Speicher](http://msdn.microsoft.com/library/azure/dn727290.aspx) und im [Azure Storage Team Blog](http://blogs.msdn.com/b/windowsazurestorage/).

6.  Klicken Sie auf **SPEICHERKONTO ERSTELLEN**.

[REST-API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure-Verwaltungsportal]: http://manage.windowsazure.com
[plus-new]: ./media/storage-create-account-include/plus-new.png
[speicher-schnell-erstellen]: ./media/storage-create-account-include/quick-storage-2.png

<!--HONumber=49-->