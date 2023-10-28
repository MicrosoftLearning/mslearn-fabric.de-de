---
lab:
  title: Generieren und Speichern von Batchvorhersagen
  module: Generate batch predictions using a deployed model in Microsoft Fabric
---

# Generieren und Speichern von Batchvorhersagen

In diesem Lab verwenden Sie ein Machine Learning-Modell, um ein quantitatives Measure für Diabetes vorherzusagen. Sie verwenden die PREDICT-Funktion in Fabric, um die Vorhersagen mit einem registrierten Modell zu generieren.

Bei diesem Lab sammeln Sie praktische Erfahrung mit dem Generieren von Vorhersagen und der Visualisierung der Ergebnisse.

Dieses Lab dauert ungefähr **20** Minuten.

> **Hinweis:** Sie benötigen eine Microsoft Fabric-Lizenz, um diese Übung durchführen zu können. Weitere Informationen zum Aktivieren einer kostenlosen Fabric-Testlizenz finden Sie unter [Erste Schritte mit Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial). Dazu benötigen Sie ein *Schul-* , *Geschäfts-* oder Unikonto von Microsoft. Wenn Sie über kein Microsoft-Konto verfügen, können Sie sich [für eine kostenlose Testversion von Microsoft Office 365 E3 oder höher registrieren](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Modellen in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Melden Sie sich bei [Microsoft Fabric](https://app.fabric.microsoft.com) unter `https://app.fabric.microsoft.com` an, und wählen Sie **Power BI** aus.
2. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
3. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
4. Beim Öffnen Ihres neuen Arbeitsbereichs sollte dieser wie im Folgenden gezeigt leer sein:

    ![Screenshot: Leerer Arbeitsbereich in Power BI](./Images/new-workspace.png)

## Hochladen des Notebooks

Um Daten zu erfassen, zu trainieren und ein Modell zu registrieren, führen Sie die Zellen in einem Notebook aus. Sie können das Notebook in Ihren Arbeitsbereich hochladen.

1. Navigieren Sie in einer neuen Browserregisterkarte zu dem Notebook [Generate-Predictions](https://github.com/MicrosoftLearning/mslearn-fabric/blob/main/Allfiles/Labs/08/Generate-Predictions.ipynb), und laden Sie es in einen Ordner Ihrer Wahl herunter.
1. Kehren Sie zur Registerkarte „Fabric-Browser“ zurück, und wählen Sie unten links im Fabric-Portal das Symbol **Power BI** aus, und wechseln Sie zur Benutzeroberfläche **Data Science**.
1. Wählen Sie auf der **Data Science-Startseite** die Option **Notebook importieren** aus.

    Sie erhalten eine Benachrichtigung, wenn das Notebook erfolgreich importiert wurde.

1. Wechseln Sie zum importierten Notebook mit dem Namen `Generate-Predictions`.
1. Lesen Sie die Anweisungen im Notebook sorgfältig durch, und führen Sie jede Zelle einzeln aus.

## Bereinigen von Ressourcen

In dieser Übung haben Sie ein Modell verwendet, um Batchvorhersagen zu generieren.

Wenn Sie mit dem Notebook fertig sind, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
2. Wählen Sie im Menü **...** auf der Symbolleiste die **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Andere** die Option **Diesen Arbeitsbereich entfernen** aus.
