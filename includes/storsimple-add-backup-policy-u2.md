<!--author=v-sharos last changed: 11/06/15-->

#### So fügen Sie eine StorSimple-Sicherungsrichtlinie hinzu

1. Klicken Sie auf der Seite **Schnellstart** des Geräts auf die Registerkarte **Sicherungsrichtlinien**. Dadurch gelangen Sie auf die Seite **Sicherungsrichtlinien**.

2. Klicken Sie unten auf der Seite auf **Hinzufügen**, um den Assistenten zum Hinzufügen einer Sicherungsrichtlinie zu starten.

    ![Hinzufügen einer Sicherungsrichtlinie 1](./media/storsimple-add-backup-policy-u2/AddBackupPolicy1.png)

3. Führen Sie im Dialogfeld **Sicherungsrichtlinie hinzufügen** unter **Sicherungsrichtlinie definieren** die folgenden Schritte aus:

    1. Geben Sie für Ihre Sicherungsrichtlinie einen Namen an, der zwischen 3 und 150 Zeichen lang ist.
    2. Aktivieren Sie die entsprechenden Kontrollkästchen, um dieser Sicherungsrichtlinie Volumes zuzuweisen. Beachten Sie, dass Sie keine Volumes auswählen können, die andere Clouddienstanbieter verwenden. Bei Verwendung von mehreren Clouddienstanbietern werden in der Liste basierend auf der ersten Auswahl Volumes angezeigt, die nur zu diesem Clouddienstanbieter gehören. Dadurch können Sie Volumes in einer Momentaufnahme gruppieren, die zu einem einzelnen Clouddienstanbieter gehören.
    3. Klicken Sie auf den Pfeil ![Pfeilsymbol](./media/storsimple-add-backup-policy-u2/HCS_ArrowIcon-include.png), um zur nächsten Seite zu wechseln.

     ![Hinzufügen einer Sicherungsrichtlinie 2](./media/storsimple-add-backup-policy-u2/AddBackupPolicy2.png)

4. Gehen Sie unter **Zeitplan definieren** folgendermaßen vor:
    1. Wählen Sie im Feld **Typ der Sicherung** eine der Optionen **Cloudmomentaufnahme** oder **Lokale Momentaufnahme** in der Dropdownliste aus.
    2. Geben Sie die Häufigkeit der Sicherungen an. (Geben Sie eine Zahl an, und wählen Sie dann **Tage** oder **Wochen** in der Dropdownliste aus.)
    3. Geben Sie einen Aufbewahrungszeitplan ein.
    4. Geben Sie Startzeit und -datum für die Sicherungsrichtlinie ein.  
    6. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-add-backup-policy-u2/HCS_CheckIcon-include.png), um die Richtlinie zu speichern.

Die neu hinzugefügte Richtlinie wird in der Tabellenansicht auf der Seite **Sicherungsrichtlinien** angezeigt.
 

<!---HONumber=AcomDC_1217_2015-->