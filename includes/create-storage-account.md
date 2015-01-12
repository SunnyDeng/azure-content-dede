Für Azure-Speicher benötigen Sie ein Speicherkonto. Mit
den folgenden Schritten können Sie ein Speicherkonto erstellen. (Sie können ein
Speicherkonto auch erstellen, indem Sie die Azure-Dienstverwaltungs-Clientbibliothek oder die Dienstverwaltung [REST-API] verwenden.)

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.

2.  Klicken Sie unten auf der Seite auf **NEU**.

	![+Neu][+Neu]

3.  Klicken Sie auf **DATA SERVICES**, dann auf **SPEICHER** und anschließend auf **SCHNELLERFASSUNG**.

	![Dialogfeld "Schnellerfassung"][quick-create-storage]

4.  Geben Sie im Feld "URL" einen Unterdomänennamen ein,
    der im URI für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und
    Zahlen enthalten. Dieser Wert wird der Hostname im URI, der zum Adressieren
    von Blob-, Warteschlangen- oder Tabellenspeicherressourcen
    für das Abonnement verwendet wird.

5.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der
    Speicher befinden soll. Wenn Sie Speicher von Ihrer Azure-Anwendung
    verwenden möchten, wählen Sie dieselbe Region, in der Sie
    die Sie Ihre Anwendung bereitstellen möchten.

6.  Optional können Sie den gewünschten Replikationstyp für Ihr Konto auswählen. Die georedundante Replikation ist standardmäßig aktiviert und bietet maximale Dauerhaftigkeit. Weitere Informationen zu den Replikationsoptionen finden Sie unter [Redundanzoptionen für den Azure-Speicher](http://msdn.microsoft.com/de-de/library/azure/dn727290.aspx) und im [Azure Storage Team Blog](http://blogs.msdn.com/b/windowsazurestorage/).

7.  Klicken Sie auf **SPEICHERKONTO ERSTELLEN**.

[Azure-Verwaltungsportal]: http://manage.windowsazure.com
[+Neu]: ./media/create-storage-account/plus-new.png
[Dialogfeld "Schnellerfassung"]: ./media/create-storage-account/quick-storage-2.png
