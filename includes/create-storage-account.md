Für Speicheroperationen benötigen Sie ein Azure-Speicherkonto. Mit den folgenden Schritten können Sie ein Speicherkonto erstellen. (Sie können ein Speicherkonto auch [mithilfe der REST-API][1] erstellen.)

1.  Melden Sie sich im [Azure-Verwaltungsportal][2] an.

2.  Klicken Sie unten auf der Seite auf **NEU**.
    
    ![+Neu](./media/create-storage-account/plus-new.png)

3.  Klicken Sie auf **DATA SERVICES**, dann auf **SPEICHER** und
    anschließend auf **SCHNELLERFASSUNG**.
    
    ![Dialogfeld
    "Schnellerfassung"](./media/create-storage-account/quick-storage-2.png)

4.  Geben Sie im Feld "URL" einen Unterdomänennamen ein, der im URI für
    das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis
    24 Kleinbuchstaben und Zahlen enthalten. Dieser Wert wird der
    Hostname im URI, der zum Adressieren von Blob-, Warteschlangen- oder
    Tabellenspeicherressourcen für das Abonnement verwendet wird.

5.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der Speicher
    befinden soll. Wenn Sie Speicher aus Ihrer Azure-Anwendung
    verwenden, wählen Sie die Region aus, in der Sie auch Ihre Anwendung
    bereitstellen.

6.  Optional können Sie die Georeplikation aktivieren.

7.  Klicken Sie auf **SPEICHERKONTO ERSTELLEN**.



[1]: http://msdn.microsoft.com/de-de/library/windowsazure/hh264518.aspx
[2]: http://manage.windowsazure.com