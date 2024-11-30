---
lab:
  title: "Verwenden von Tools zum Optimieren der Power\_BI-Leistung"
  module: Optimize enterprise-scale tabular models
---

# Verwenden von Tools zum Optimieren der Power BI-Leistung

In diesem Lab erfahren Sie, wie Sie mit zwei externen Tools Datenmodelle und DAX-Abfragen entwickeln, verwalten und optimieren.

In dieser Übung lernen Sie die Verwendung von:

- Best Practices Analyzer (BPA) in Tabular Editor
- DAX Studio

Dieses Lab dauert ungefähr **30** Minuten.

> **Hinweis:** Sie benötigen eine [Microsoft Fabric-Testversion](https://learn.microsoft.com/fabric/get-started/fabric-trial), um diese Übung durchführen zu können.

## Erste Schritte

Für dieses Lab installieren und verwenden Sie Tabular Editor und DAX Studio, um ein semantisches Modell zu optimieren.

## Verwenden des Best Practice Analyzer

In dieser Übung installieren Sie den Tabular Editor 2 und laden Best Practice Analyzer (BPA)-Regeln. Sie werden die BPA-Regeln überprüfen und dann bestimmte Probleme im Datenmodell beheben.

*BPA ist ein kostenloses Tool eines Drittanbieters, das Sie auf mögliche Fehler bei der Modellierung oder auf Änderungen hinweist, die Sie vornehmen können, um Ihr Modelldesign und die Leistung zu verbessern. Es enthält Empfehlungen für die Benennung, die Benutzerfreundlichkeit und allgemeine Optimierungen, die Sie zur Verbesserung der Leistung anwenden können. Weitere Informationen finden Sie unter [Best-Practices-Regeln, um die Leistung Ihres Modells zu verbessern](https://powerbi.microsoft.com/blog/best-practice-rules-to-improve-your-models-performance/).*

### Herunterladen und Installieren des Tabular Editor 2

Laden Sie das Tool Tabular Editor 2 herunter, und installieren Sie es, um die Erstellung von Berechnungsgruppen zu ermöglichen.

***Wichtig:** Falls Sie Tabular Editor 2 bereits in Ihrer VM-Umgebung installiert haben, fahren Sie mit der nächsten Aufgabe fort.*

*Tabular Editor ist ein alternatives Tool zum Erstellen tabellarischer Modelle für Analysis Services und Power BI. Tabular Editor 2 ist ein Open-Source-Projekt, das eine BIM-Datei bearbeiten kann, ohne auf Daten im Modell zuzugreifen.*

1. Stellen Sie sicher, dass Power BI Desktop geschlossen ist.

1. Navigieren Sie in Microsoft Edge zur Seite "Tabular Editor Release".

    ```https://github.com/TabularEditor/TabularEditor/releases```

1. Scrollen Sie nach unten zum Abschnitt **Assets** und wählen Sie die Datei **TabularEditor.Installer.msi** aus. Damit wird die Installation der Datei initiiert.

1. Wählen Sie nach Abschluss des Vorgangs **Datei öffnen** aus, um das Installationsprogramm auszuführen.

1. Wählen Sie im Fenster des Tabular Editor-Installationsprogramms die Option **Next** aus.

1. Wenn Sie der Lizenzvereinbarung zustimmen, wählen Sie im Schritt **License Agreement** die Option **I agree** und dann **Next** aus.

1. Wählen Sie im Schritt **Installationsordner auswählen** **Weiter**aus.

1. Wählen Sie im Schritt **Tatenkombinationen für die Anwendung****Weiter** aus.

1. Wählen Sie im Schritt **Installation bestätigen** **Weiter** aus.

    *Wenn Sie dazu aufgefordert werden, wählen Sie **Ja** aus, damit die App Änderungen vornehmen kann.*

1. Klicken Sie nach Abschluss der Installation auf **Schließen**.

    *Tabular Editor ist jetzt installiert und als externes Power BI-Desktoptool registriert.*

### Öffnen Sie den Power BI Desktop

In dieser Aufgabe öffnen Sie eine vordefinierte Power BI Desktop-Lösung.

1. Laden Sie die [Sales Analysis-Starterdatei](https://aka.ms/fabric-optimize-starter) von `https://aka.ms/fabric-optimize-starter` herunter, und speichern Sie sie an einem Speicherort, den Sie sich merken können.

1. Navigieren Sie zu der heruntergeladenen Datei, und öffnen Sie sie in Power BI Desktop.

1. Wählen Sie die Registerkarte **Externe Tools** des Menübands aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image8.png)

1. Beachten Sie, dass Sie den Tabular Editor über diese Registerkarte des Menübands starten können.

    ![](Images/use-tools-to-optimize-power-bi-performance-image9.png)

    *Später in dieser Übung verwenden Sie den Tabular Editor, um mit BPA zu arbeiten.*

### Überprüfen Sie das Datenmodell

In dieser Aufgabe überprüfen Sie das Datenmodell.

1. Auf dem Power BI Desktop links wechseln Sie zur Ansicht **Modell**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image10.png)

2. Verwenden Sie das Modelldiagramm, um den Modellentwurf zu überprüfen.

    ![](Images/use-tools-to-optimize-power-bi-performance-image11.png)

    *Das Modell besteht aus acht Dimensionstabellen und einer Faktentabelle. In der Faktentabelle"**Umsatz** werden die Umsatzdetails gespeichert. Es ist ein klassisches Sternschemadesign, das Schneeflocken-Dimensionstabellen (**Kategorie** > **Unterkategorie** > **Produkt**) für die Produktdimension enthält.*

    *In dieser Übung verwenden Sie BPA, um Modellprobleme zu erkennen und zu beheben.*

### BPA-Regeln laden

In dieser Aufgabe laden Sie BPA-Regeln.

*Die BPA-Regeln werden während der Installation des Tabular Editors nicht hinzugefügt. Sie müssen sie herunterladen und installieren.*

1. Wählen Sie auf der Registerkarte **Externe Tools** im Menüband die Option **Tabular Editor** aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image12.png)

    *Der Tabular-Editor wird in einem neuen Fenster geöffnet und stellt eine Live-Verbindung zu dem in Power BI Desktop gehosteten Datenmodell her. Änderungen am Modell im Tabular-Editor werden erst dann in Power BI Desktop übernommen, wenn Sie sie speichern.*

2. Um die BPA-Regeln zu laden, wählen Sie die Registerkarte **C#-Skript** aus.

    *Hinweis: In älteren Versionen des Tabular-Editors heißt diese Registerkarte möglicherweise "Erweiterte Skripterstellung".*

    ![](Images/use-tools-to-optimize-power-bi-performance-image13.png)

3. Fügen Sie das folgende Skript ein.

    ```csharp
    System.Net.WebClient w = new System.Net.WebClient(); 

    string path = System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData);
    string url = "https://raw.githubusercontent.com/microsoft/Analysis-Services/master/BestPracticeRules/BPARules.json";
    string downloadLoc = path+@"\TabularEditor\BPARules.json";
    w.DownloadFile(url, downloadLoc);
    ```

4. Um das Skript auszuführen, wählen Sie auf der Symbolleiste den Befehl **Run script** aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image14.png)

    *Um die BPA-Regeln zu verwenden, müssen Sie den Tabular Editor schließen und dann erneut öffnen.*

5. Schließen Sie den Tabular Editor.

6. Um den Tabelleneditor erneut zu öffnen, wählen Sie in Power BI Desktop im Menüband  **Externe Tools** **Tabular Editor** aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image15.png)

### Überprüfen der BPA-Regeln

In dieser Aufgabe überprüfen Sie die BPA-Regeln, die Sie im vorherigen Vorgang geladen haben.

1. Wählen Sie im Tabular Editor im Menü **Tools** > **Verwalten von BPA-Regeln**aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image16.png)

2. Wählen Sie im Fenster **Manage Best Practice Rules** in der Liste **Regelsammlungen** **Regeln für die lokalen Benutzer*innen** aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image17.png)

3. Scrollen Sie in der Liste **Regeln in Sammlung** nach unten in der Regelliste.

    *Tipp: Sie können die untere rechte Ecke ziehen, um das Fenster zu vergrößern.*

    *Innerhalb von Sekunden kann der Tabular Editor das gesamte Modell anhand der einzelnen Regeln scannen und einen Bericht aller Modellobjekte erstellen, die die Bedingung in jeder Regel erfüllen.*

4. Beachten Sie, dass BPA die Regeln in Kategorien gruppiert.

    *Einige Regeln, z. B. DAX-Ausdrücke, konzentrieren sich auf die Leistungsoptimierung, während andere, wie die Formatierungsregeln, ästhetisch ausgerichtet sind.*

5. Beachten Sie die Spalte **Schweregrad**.

    *Je höher der Wert, desto wichtiger die Regel.*

6. Scrollen Sie zum Ende der Liste und deaktivieren Sie die Regel**Set IsAvailableInMdx to false on non-attribute columns**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image18.png)

    *Sie können einzelne Regeln oder ganze Regelkategorien deaktivieren. BPA überprüft keine deaktivierten Regeln für Ihr Modell. Das Entfernen dieser bestimmten Regel besteht darin, Ihnen zu zeigen, wie Sie eine Regel deaktivieren.*

7. Klickan Sie auf **OK**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image19.png)

### BPA-Probleme lösen

In dieser Aufgabe öffnen Sie BPA und überprüfen die Ergebnisse der Prüfungen.

1. Wähen Sie im Menü **Tools** > **Best Practice Analyzer** (oder drücken Sie **F10**).

    ![](Images/use-tools-to-optimize-power-bi-performance-image20.png)

2. Maximieren Sie im Fenster **Practice Analyzer** bei Bedarf das Fenster.

3. Beachten Sie die Liste der (möglichen) Probleme, gruppiert nach Kategorien.

4. Klicken Sie in der ersten Kategorie mit der rechten Maustaste auf die Tabelle**Produkt** und wählen Sie **Element ignorieren** aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image21.png)

    *Wenn ein Problem nicht wirklich ein Problem ist, können Sie dieses Element ignorieren. Sie können ignorierte Elemente immer anzeigen, indem Sie den Befehl **Ignoriert anzeigen** auf der Symbolleiste verwenden.*

5. Weiter unten auf der Liste, in der Kategorie **Die DIVIDE-Funktion für Division verwenden**, klicken Se mit der rechten Maustaste **[Profit Margin]**, und wählen Sie **Wechseln Sie zu** aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image22.png)

    *Dieser Befehl wechselt zum Tabular Editor und konzentriert sich auf das Objekt. Es erleichtert das Anwenden einer Lösung auf das Problem.*

6. Ändern Sie die DAX-Formel im Ausdrucks-Editor wie folgt, um die effizientere (und sicherere) [DIVIDE](https://docs.microsoft.com/dax/divide-function-dax)-Funktion zu verwenden.

    *Tipp: Alle Formeln können aus der Datei **D:\fabric\Allfiles\Labs\16\Snippets.txt** kopiert und eingefügt werden.*

    ```dax
    DIVIDE ( [Profit], SUM ( 'Sales'[Sales Amount] ) )
    ```

7. Um die Modelländerungen zu speichern, wählen Sie auf der Symbolleiste den Befehl **Save changes to the connected database** aus (oder drücken Sie **STRG+S**).

    ![](Images/use-tools-to-optimize-power-bi-performance-image23.png)

    *Durch das Speichern von Änderungen werden Änderungen an das Power BI Desktop-Datenmodell übertragen.*

8. Wechseln Sie zurück zum (nicht fokussierten) Fenster **Best Practice Analyzer**.

9. Beachten Sie, dass BPA das Problem nicht mehr auflistet.

10.  Scrollen Sie in der Liste der Probleme nach unten, um die Kategorie **Formatzeichenfolge für "Datumsspalten" bereitstellen** zu suchen.

    ![](Images/use-tools-to-optimize-power-bi-performance-image24.png)

11. Klicken Sie mit der rechten Maustaste auf das Problem **"Datum"[Date]** , und wählen Sie dann **Problembehebungs-Skript** aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image25.png)

    *Dieser Befehl generiert ein C#-Skript und kopiert es in die Zwischenablage. Sie können auch den Befehl **Problembehebung anwenden** verwenden, um das Skript zu generieren und auszuführen. Möglicherweise ist es jedoch sicherer, das Skript vor der Ausführung zu überprüfen (und zu ändern).*

12. Wenn Sie benachrichtigt werden, dass BPA das Problembehebungs-Skript in die Zwischenablage kopiert hat, wählen Sie **OK** aus.

13. Wechseln Sie zum Tabular Editor, und wählen Sie die Registerkarte **C#-Skript**  aus.

    *Hinweis: Dies kann in älteren Versionen des Tabular Editors die Registerkarte "Erweiterte Skripterstellung" genannt werden.*

    ![](Images/use-tools-to-optimize-power-bi-performance-image13.png)

14. Um das Problembehebungs-Skript einzufügen, klicken Sie mit der rechten Maustaste in den Bereich, und drücken Sie dann **STRG+V**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image27.png)

    *Sie können eine Änderung an der Formatzeichenfolge vornehmen.*

15. Um das Skript auszuführen, wählen Sie auf der Symbolleiste den Befehlt **Skript ausführen** aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image14.png)

16. Speichern Sie die Änderungen.

17. Um den Tabular Editor zu schließen, wählen Sie im Menü **Datei** > **Beenden** aus.

18. Speichern Sie die Power BI Desktop-Datei.

    ![](Images/use-tools-to-optimize-power-bi-performance-image29.png)

    *Sie müssen auch die Power BI Desktop-Datei speichern, um sicherzustellen, dass die in Tabular Editor vorgenommenen Änderungen gespeichert werden.*

    *Wählen Sie in der Nachricht zu ausstehenden Änderungen **Später anwenden** aus.*

## Verwenden von DAX Studio

In dieser Übung verwenden Sie DAX Studio, um DAX-Abfragen in der Power BI-Berichtsdatei zu optimieren.

*Laut der DAX Studio-Website ist DAX Studio das ultimative Tool zum Ausführen und Analysieren von DAX-Abfragen für tabellarische Microsoft-Modelle. Es ist ein mit zahlreichen Features ausgestattetes Tool für die Erstellung, Diagnose, Leistungsoptimierung und Analyse von DAX-Abfragen. Zu den Features zählen die Objektsuche, eine integrierte Ablaufverfolgung, Aufschlüsselungen der Abfrageausführung mit detaillierten Statistiken sowie die DAX-Syntaxhervorhebung und -formatierung.*

### DAX Studio herunterladen

In dieser Aufgabe laden Sie DAX Studio herunter.

1. Navigieren Sie in Microsoft Edge zur Downloadseite von DAX Studio.

    ```https://daxstudio.org/downloads/```

1. Wählen Sie **DaxStudio_3_X_XX_setup.exe (Installationsprogramm)** aus. Dadurch wird die Dateiinstallation initiiert.
    *Hinweis: Im Laufe der Zeit werden geringfügige Änderungen der DAX Studio-Version vorgenommen. Laden Sie das aktuelle Release herunter.*

1. Wählen Sie nach Abschluss **Datei öffnen** aus, um das Installationsprogramm auszuführen.

    ![Grafische Benutzeroberfläche, Anwendung, automatisch erstellte Beschreibung](Images/use-tools-to-optimize-power-bi-performance-image31b.png)

1. Wählen Sie im Fenster DAX Studio-Installer **Installieren für alle Benutzer (empfohlen)** aus.

1. Wählen Sie im Fenster Benutzerkontensteuerung die Option Ja, damit die App Änderungen am Gerät vornehmen kann.

    ![Grafische Benutzeroberfläche, Anwendung, automatisch erstellte Beschreibung](Images/use-tools-to-optimize-power-bi-performance-image31c.png)

1. Wählen Sie im Schritt **Lizenzvertrag**, wenn Sie den Lizenzbedingungen zustimmen, **Ich stimme dem Vertrag zu**und wählen dann**Weiter**aus.

    ![Grafische Benutzeroberfläche, Anwendung, automatisch erstellte Beschreibung](Images/use-tools-to-optimize-power-bi-performance-image31d.png)

1. Wählen Sie **Weiter** aus, um den Standardzielspeicherort zu verwenden.
1. Klicken Sie auf **Weiter** , um die zu installierenden Standardkomponenten auszuwählen.
1. Wählen Sie **Weiter** aus, um den Shortcut im Standardstartmenüordner zu platzieren.
1. Wählen Sie **Einen Desktop Shortcut erstellen** aus und wählen "Weiter" aus.

    ![Grafische Benutzeroberfläche, Anwendung, automatisch erstellte Beschreibung](Images/use-tools-to-optimize-power-bi-performance-image31e.png)
1. Wählen Sie **Installieren** aus.

1. Wenn Sie fertig sind und **Dax Studio starten** ausgewählt haben, wählen Sie **Beenden** aus. Dadurch wird DAX Studio geöffnet.
    ![Grafische Benutzeroberfläche, Anwendung, automatisch erstellte Beschreibung](Images/use-tools-to-optimize-power-bi-performance-image31f.png)

1. Wählen Sie im Fenster **Connect** die Option **Power BI-/SSDT-Modell** aus.

1. Vergewissern Sie sich, dass in der entsprechenden Dropdownliste das Modell **Sales Analysis - Use tools to optimize Power BI performance** ausgewählt ist.

    ![](Images/use-tools-to-optimize-power-bi-performance-image30.png)

    *Wenn Sie die Startdatei **Vertrieb Analyse - Nutzung der Werkzeuge zur Optimierung der Power BI Leistung** nicht geöffnet haben, können Sie keine Verbindung herstellen. Stellen Sie sicher, dass die Datei geöffnet ist.*

1. Wählen Sie **Verbinden** aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image31.png)

1. Maximieren Sie bei Bedarf das DAX Studio-Fenster.

### Verwenden von DAX Studio zum Optimieren einer Abfrage

In dieser Aufgabe optimieren Sie eine Abfrage mithilfe einer verbesserten Measureformel.

*Beachten Sie, dass die Optimierung einer Abfrage bei einem Datenmodell mit geringem Datenvolumen schwierig ist. Der Schwerpunkt dieser Übung liegt auf der Verwendung von DAX Studio und nicht auf der Optimierung von DAX-Abfragen.*

1. Laden Sie in einem Browserfenster die Datei [Monthly Profit Growth.dax](https://aka.ms/fabric-optimize-dax) von `https://aka.ms/fabric-optimize-dax` herunter, und speichern Sie sie auf Ihrem lokalen Computer (in einem beliebigen Ordner).

   ![](https://github.com/MicrosoftLearning/mslearn-fabric/assets/34583336/58254cce-753e-4322-9060-536e12554aa7)

3. Wechseln Sie zum Dax Studio-Fenster, und wählen Sie im Menü **Datei** die Option **Durchsuchen** aus, um zur Datei **Monthly Profit Growth.dax** zu navigieren und sie zu **öffnen**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image33.png)

6. Lesen Sie die Kommentare am Anfang der Datei, und überprüfen Sie dann die folgende Abfrage.

    *Es ist nicht wichtig, die Abfrage vollständig zu verstehen.*

    *Die Abfrage definiert zwei Measures, die das monatliche Gewinnwachstum bestimmen. Derzeit verwendet die Abfrage nur das erste Measure (in Zeile 72). Wenn ein Measure nicht verwendet wird, wirkt es sich nicht auf die Abfrageausführung aus.*

7. Um eine Serverablaufverfolgung auszuführen, um detaillierte Anzeigedauerinformationen für die Leistungsprofilerstellung aufzuzeichnen, wählen Sie auf der Registerkarte des Menübands **Start** in der Gruppe **Ablaufverfolgungen**die Option **Serverzeiten** aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image34.png)

8. Um das Skript auszuführen, wählen Sie auf der Registerkarte des Menübands**Start** in der Gruppe **Abfrage** das Symbol **Ausführen**aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image35.png)

9. Überprüfen Sie im unteren Bereich das Abfrageergebnis.

    *In der letzten Spalte werden die Meaurer-Ergebnisse angezeigt.*

10. Wählen Sie im unteren Bereich die Registerkarte **Serverzeit** aus.

    ![](Images/use-tools-to-optimize-power-bi-performance-image36.png)

11. Überprüfen Sie die auf der linken Seite verfügbaren Statistiken.

    ![](Images/use-tools-to-optimize-power-bi-performance-image37.png)

    *Von oben links nach unten rechts geben die Statistiken an, wie viele Millisekunden zum Ausführen der Abfrage benötigt wurden, und die Dauer der CPU des Speichermoduls (SE). In diesem Fall (Ihre Ergebnisse unterscheiden sich), dauerte die Formel-Engine (FE) 73,5 % der Zeit, während die SE die die verbleibenden 26,5 % der Zeit in Anspruch nahm. Es gab 34 einzelne SE-Abfragen und 21 Cachetreffer.*

12. Führen Sie die Abfrage erneut aus, und beachten Sie, dass alle SE-Abfragen aus dem SE-Cache stammen.

    *Das liegt daran, dass die Ergebnisse zur Wiederverwendung zwischengespeichert wurden. Manchmal möchten Sie in Ihren Tests den Cache löschen. Wählen Sie in diesem Fall auf der Registerkarte des Menübands **Start** durch Auswahl des Abwärtspfeils den Befehl **Ausführen**.*

    ![](Images/use-tools-to-optimize-power-bi-performance-image38.png)

    *Die zweite Measuredefinition bietet ein effizienteres Ergebnis. Sie aktualisieren nun die Abfrage, um das zweite Measure zu verwenden.*

13. Ersetzen Sie in Zeile 72 das Wort **Schlecht** durch **Besser**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image39.png)

14. Führen Sie die Abfrage aus, und überprüfen Sie dann die Serverzeitstatistik.

    ![](Images/use-tools-to-optimize-power-bi-performance-image40.png)

15. Führen Sie sie ein zweites Mal aus, um vollständige Cachetreffer zu erzielen.

    ![](Images/use-tools-to-optimize-power-bi-performance-image41.png)

    *In diesem Fall können Sie feststellen, dass die "bessere" Abfrage, die Variablen und eine Zeitintelligenzfunktion verwendet, eine bessere Leistung von fast 50 % bei der Abfrageausführungszeit erzielt.*

### Abschluss

Schließen Sie alle Anwendungen, um diese Übung abzuschließen. Es ist nicht erforderlich, die Dateien zu speichern.
