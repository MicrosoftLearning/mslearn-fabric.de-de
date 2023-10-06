---
lab:
  title: Erstellen und Verwenden von Notebooks für die Datenuntersuchung
  module: Explore data for data science with notebooks in Microsoft Fabric
---

# Verwenden von Notebooks zum Untersuchen von Daten in Microsoft Fabric

In diesem Lab verwenden wir Notebooks für die Datenuntersuchung. Notebooks sind ein leistungsstarkes Tool zum interaktiven Untersuchen und Analysieren von Daten. In dieser Übung erfahren Sie, wie Sie Notebooks erstellen und verwenden, um ein Dataset zu untersuchen, Zusammenfassungsstatistiken zu generieren und Visualisierungen zu erstellen, um die Daten besser zu verstehen. Am Ende dieses Labs haben Sie ein fundiertes Verständnis für die Verwendung von Notebooks für die Datenuntersuchung und -analyse.

Dieses Lab dauert ungefähr **45** Minuten.

> **Hinweis:** Sie benötigen eine Microsoft Fabric-Lizenz, um diese Übung durchführen zu können. Weitere Informationen zum Aktivieren einer kostenlosen Fabric-Testlizenz finden Sie unter [Erste Schritte mit Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial). Dazu benötigen Sie ein *Schul-* , *Geschäfts-* oder Unikonto von Microsoft. Wenn Sie über kein Microsoft-Konto verfügen, können Sie sich [für eine kostenlose Testversion von Microsoft Office 365 E3 oder höher registrieren](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Melden Sie sich bei [Microsoft Fabric](https://app.fabric.microsoft.com) unter `https://app.fabric.microsoft.com` an, und wählen Sie **Power BI** aus.
2. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
3. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
4. Beim Öffnen Ihres neuen Arbeitsbereichs sollte dieser wie im Folgenden gezeigt leer sein:

    ![Screenshot: Leerer Arbeitsbereich in Power BI](./Images/new-workspace.png)

## Erstellen eines Notebooks

Um ein Modell zu trainieren, können Sie ein *Notebook* erstellen. Notebooks bieten eine interaktive Umgebung, in der Sie Code (in mehreren Sprachen) als *Experimente* schreiben und ausführen können.

1. Wählen Sie unten links im Power BI-Portal das **Datentechnik**-Symbol aus, und wechseln Sie zu **Data Science**.

1. Erstellen Sie auf der **Data Science**-Startseite ein neues **Notebook**.

    Nach einigen Sekunden wird ein neues Notebook mit einer einzelnen *Zelle* geöffnet. Notebooks bestehen aus einer oder mehreren Zellen, die *Code* oder *Markdown* (formatierten Text) enthalten können.

1. Wählen Sie die erste Zelle aus (die derzeit eine *Codezelle* ist), und verwenden Sie dann auf der dynamischen Symbolleiste oben rechts die Schaltfläche **M&#8595;** , um die Zelle in eine *Markdownzelle* zu konvertieren.

    Wenn die Zelle in eine Markdownzelle geändert wird, wird der enthaltene Text gerendert.

1. Verwenden Sie die Schaltfläche **&#128393;** (Bearbeiten), um die Zelle in den Bearbeitungsmodus zu versetzen, löschen Sie dann den Inhalt und geben Sie den folgenden Text ein:

    ```text
   # Perform data exploration for data science

   Use the code in this notebook to perform data exploration for data science.
    ``` 

## Laden von Daten in einen Dataframe

Jetzt können Sie Code ausführen, um Daten abzurufen. Sie arbeiten mit dem [**Diabetesdataset**](https://learn.microsoft.com/azure/open-datasets/dataset-diabetes?tabs=azureml-opendatasets?azure-portal=true) aus Azure Open Datasets. Nachdem Sie die Daten geladen haben, konvertieren Sie diese in einen Pandas-Dataframe, eine gängige Struktur für die Arbeit mit Daten in Zeilen und Spalten.

1. Verwenden Sie in Ihrem Notebook das Symbol **+ Code** unter der neuesten Zelle, um dem Notebook eine neue Codezelle hinzuzufügen. Geben Sie dann den folgenden Code ein:

    ```python
    # Azure storage access info for open dataset diabetes
    blob_account_name = "azureopendatastorage"
    blob_container_name = "mlsamples"
    blob_relative_path = "diabetes"
    blob_sas_token = r"" # Blank since container is Anonymous access
    
    # Set Spark config to access  blob storage
    wasbs_path = f"wasbs://%s@%s.blob.core.windows.net/%s" % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set("fs.azure.sas.%s.%s.blob.core.windows.net" % (blob_container_name, blob_account_name), blob_sas_token)
    print("Remote blob path: " + wasbs_path)
    
    # Spark read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

1. Verwenden Sie die Schaltfläche **&#9655; Zelle ausführen** links neben der Zelle, um diese auszuführen. Alternativ können Sie `SHIFT` + `ENTER` auf Ihrer Tastatur drücken, um eine Zelle auszuführen.

    > **Hinweis**: Da Sie Spark-Code zum ersten Mal in dieser Sitzung ausführen, muss der Spark-Pool gestartet werden. Dies bedeutet, dass die erste Ausführung in der Sitzung etwa eine Minute dauern kann. Nachfolgende Ausführungen erfolgen schneller.

1. Verwenden Sie das Symbol **+ Code** unterhalb der Zellenausgabe, um dem Notebook eine neue Codezelle hinzuzufügen, und geben Sie darin den folgenden Code ein:

    ```python
    display(df)
    ```

1. Wenn der Zellenbefehl abgeschlossen ist, überprüfen Sie die Ausgabe unterhalb der Zelle, die wie folgt aussehen sollte:

    |AGE|SEX|BMI|BP|S1|S2|S3|S4|S5|S6|J|
    |---|---|---|--|--|--|--|--|--|--|--|
    |59|2|32,1|101.0|157|93.2|38,0|4,0|4.8598|87|151|
    |48|1|21.6|87,0|183|103.2|70,0|3.0|3.8918|69|75|
    |72|2|30.5|93.0|156|93.6|41.0|4,0|4.6728|85|141|
    |24|1|25.3|84.0|198|131.4|40.0|5.0|4.8903|89|206|
    |50|1|23.0|101.0|192|125,4|52.0|4,0|4.2905|80|135|
    | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

    Die Ausgabe zeigt die Zeilen und Spalten des Diabetesdatasets.

1. Die Daten werden als Spark-Dataframe geladen. Scikit-learn erwartet, dass das Eingabedataset ein Pandas-Dataframe ist. Führen Sie den folgenden Code aus, um Ihr Dataset in einen Pandas-Dataframe zu konvertieren:

    ```python
    df = df.toPandas()
    df.head()
    ```

## Überprüfen der Form der Daten

Nachdem Sie die Daten geladen haben, können Sie die Struktur des Datasets überprüfen, z. B. die Anzahl von Zeilen und Spalten, Datentypen und fehlenden Werten.

1. Verwenden Sie das Symbol **+ Code** unterhalb der Zellenausgabe, um dem Notebook eine neue Codezelle hinzuzufügen, und geben Sie darin den folgenden Code ein:

    ```python
    # Display the number of rows and columns in the dataset
    print("Number of rows:", df.shape[0])
    print("Number of columns:", df.shape[1])

    # Display the data types of each column
    print("\nData types of columns:")
    print(df.dtypes)
    ```

    Das Dataset enthält **442 Zeilen** und **11 Spalten**. Dies bedeutet, dass Sie über 442 Beispiele und 11 Features oder Variablen in Ihrem Dataset verfügen. Die `SEX`-Variable enthält wahrscheinlich kategorische Daten oder Zeichenfolgendaten.

## Überprüfen auf fehlende Daten

1. Verwenden Sie das Symbol **+ Code** unterhalb der Zellenausgabe, um dem Notebook eine neue Codezelle hinzuzufügen, und geben Sie darin den folgenden Code ein:

    ```python
    missing_values = df.isnull().sum()
    print("\nMissing values per column:")
    print(missing_values)
    ```

    Der Code überprüft auf fehlende Werte. Beachten Sie, dass im Dataset keine Daten fehlen.

## Generieren von beschreibenden Statistiken für numerische Variablen

Nun generieren wir beschreibende Statistiken, um die Verteilung numerischer Variablen zu verstehen.

1. Verwenden Sie das Symbol **+ Code** unterhalb der Zellenausgabe, um dem Notebook eine neue Codezelle hinzuzufügen, und geben Sie den folgenden Code ein.

    ```python
    desc_stats = df.describe()
    print(desc_stats)
    ```

    Der durchschnittliche Wert für den `age` beträgt ca. 48,5 Jahre mit einer Standardabweichung von 13,1 Jahren. Die jüngste Person ist 19 Jahre alt und die älteste ist 79 Jahre alt. Der Durchschnitt des `BMI` liegt bei etwa 26,4, was nach [WHO-Standards](https://www.who.int/health-topics/obesity#tab=tab_1) in die Kategorie **Übergewicht** fällt. Der niedrigste `BMI`-Wert ist 18 und der Maximalwert ist 42,2.

## Zeichnen der Datenverteilung

Überprüfen wir nun das Feature `BMI` und zeichnen seine Verteilung, um seine Merkmale besser verstehen zu können.

1. Fügen Sie dem Notebook eine weitere Codezelle hinzu. Geben Sie anschließend den folgenden Code in diese Zelle ein, und führen Sie ihn aus.

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns
    import numpy as np
    
    # Calculate the mean, median of the BMI variable
    mean = df_pnd['BMI'].mean()
    median = df_pnd['BMI'].median()
    
    # Histogram of the BMI variable
    plt.figure(figsize=(8, 6))
    plt.hist(df_pnd['BMI'], bins=20, color='skyblue', edgecolor='black')
    plt.title('BMI Distribution')
    plt.xlabel('BMI')
    plt.ylabel('Frequency')
    
    # Add lines for the mean and median
    plt.axvline(mean, color='red', linestyle='dashed', linewidth=2, label='Mean')
    plt.axvline(median, color='green', linestyle='dashed', linewidth=2, label='Median')
    
    # Add a legend
    plt.legend()
    plt.show()
    ```

    In diesem Diagramm können Sie den Umfang und die Verteilung von `BMI` im Dataset beobachten. Beispielsweise fallen die meisten `BMI`-Werte unter 23,2 und 29,2, und die Daten sind nach rechts verzerrt.

## Ausführen einer multivariaten Analyse

Generieren wir nun Visualisierungen wie Punktdiagramme und Boxplots, um Muster und Beziehungen innerhalb der Daten aufzudecken.

1. Verwenden Sie das Symbol **+ Code** unterhalb der Zellenausgabe, um dem Notebook eine neue Codezelle hinzuzufügen, und geben Sie den folgenden Code ein.

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns

    # Scatter plot of Quantity vs. Price
    plt.figure(figsize=(8, 6))
    sns.scatterplot(x='BMI', y='Y', data=df)
    plt.title('BMI vs. Target variable')
    plt.xlabel('BMI')
    plt.ylabel('Target')
    plt.show()
    ```
    
    Wir können sehen, dass mit dem Anstieg des `BMI`-Werts auch die Zielvariable zunimmt, was auf eine positive lineare Beziehung zwischen diesen beiden Variablen hinweist.

1. Fügen Sie dem Notebook eine weitere Codezelle hinzu. Geben Sie anschließend den folgenden Code in diese Zelle ein, und führen Sie ihn aus.

    ```python
    import seaborn as sns
    import matplotlib.pyplot as plt
    
    fig, ax = plt.subplots(figsize=(7, 5))
    
    # Replace numeric values with labels
    df_pnd['SEX'] = df_pnd['SEX'].replace({1: 'Male', 2: 'Female'})
    
    sns.boxplot(x='SEX', y='BP', data=df, ax=ax)
    ax.set_title('Blood pressure across Gender')
    plt.tight_layout()
    plt.show()
    ```

    Diese Beobachtungen deuten darauf hin, dass es Unterschiede in den Blutdruckprofilen von männlichen und weiblichen Patienten gibt. Im Durchschnitt haben weibliche Patienten einen höheren Blutdruck als männliche Patienten.

1. Die Verwaltbarkeit für Visualisierungen und Analysen wird durch das Aggregieren von Daten vereinfacht. Fügen Sie dem Notebook eine weitere Codezelle hinzu. Geben Sie anschließend den folgenden Code in diese Zelle ein, und führen Sie ihn aus.

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns
    
    # Calculate average BP and BMI by SEX
    avg_values = df.groupby('SEX')[['BP', 'BMI']].mean()
    
    # Bar chart of the average BP and BMI by SEX
    ax = avg_values.plot(kind='bar', figsize=(15, 6), edgecolor='black')
    
    # Add title and labels
    plt.title('Avg. Blood Pressure and BMI by Gender')
    plt.xlabel('Gender')
    plt.ylabel('Average')
    
    # Display actual numbers on the bar chart
    for p in ax.patches:
        ax.annotate(format(p.get_height(), '.2f'), 
                    (p.get_x() + p.get_width() / 2., p.get_height()), 
                    ha = 'center', va = 'center', 
                    xytext = (0, 10), 
                    textcoords = 'offset points')
    
    plt.show()
    ```

    Dieses Diagramm zeigt, dass der durchschnittliche Blutdruck bei weiblichen Patienten im Vergleich zu männlichen Patienten höher ist. Darüber hinaus zeigt es, dass der durchschnittliche Body Mass Index (BMI) bei Frauen etwas höher ist als bei Männern.

1. Fügen Sie dem Notebook eine weitere Codezelle hinzu. Geben Sie anschließend den folgenden Code in diese Zelle ein, und führen Sie ihn aus.

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns
    
    plt.figure(figsize=(10, 6))
    sns.lineplot(x='AGE', y='BMI', data=df, ci=None)
    plt.title('BMI over Age')
    plt.xlabel('Age')
    plt.ylabel('BMI')
    plt.show()
    ```

    Die Altersgruppe von 19 bis 30 Jahren weist die niedrigsten durchschnittlichen BMI-Werte auf, während der höchste durchschnittliche BMI in der Altersgruppe von 65 bis 79 Jahren liegt. Beachten Sie außerdem, dass der durchschnittliche BMI-Wert für die meisten Altersgruppen im Bereich „Übergewicht“ liegt.

## Korrelationsanalyse

Berechnen Sie nun Korrelationen zwischen verschiedenen Merkmalen, um deren Beziehungen und Abhängigkeiten zu verstehen.

1. Verwenden Sie das Symbol **+ Code** unterhalb der Zellenausgabe, um dem Notebook eine neue Codezelle hinzuzufügen, und geben Sie den folgenden Code ein.

    ```python
    df.corr(numeric_only=True)
    ```

1. Eine Heatmap ist ein nützliches Tool, um die Stärke und Richtung von Beziehungen zwischen Variablenpaaren schnell zu visualisieren. Sie kann starke positive oder negative Korrelationen hervorheben und Paare ohne Korrelation identifizieren. Fügen Sie dem Notebook eine weitere Codezelle hinzu, und geben Sie den folgenden Code ein, um eine Heatmap zu erstellen.

    ```python
    plt.figure(figsize=(15, 7))
    sns.heatmap(df.corr(numeric_only=True), annot=True, vmin=-1, vmax=1, cmap="Blues")
    ```

    Die Variablen `S1` und `S2` weisen eine starke positive Korrelation von **0,89** auf, was darauf hindeutet, dass sie sich in dieselbe Richtung bewegen. Wenn sich `S1` erhöht, dann erhöht sich `S2` ebenso und umgekehrt. Darüber hinaus weisen `S3` und `S4` eine starke negative Korrelation von **-0,73** auf. Dies bedeutet, dass `S4` tendenziell abnimmt, wenn `S3` zunimmt.

## Speichern des Notebooks und Beenden der Spark-Sitzung

Nachdem Sie die Untersuchung der Daten abgeschlossen haben, können Sie das Notebook mit einem aussagekräftigen Namen speichern und die Spark-Sitzung beenden.

1. Verwenden Sie in der Menüleiste des Notebooks das Symbol ⚙️ **Einstellungen**, um die Einstellungen des Notebooks anzuzeigen.
2. Legen Sie den **Namen** des Notebooks auf **Explore data for data science** fest, und schließen Sie den Einstellungsbereich.
3. Wählen Sie im Notebookmenü **Sitzung beenden** aus, um die Spark-Sitzung zu beenden.

## Bereinigen von Ressourcen

In dieser Übung haben Sie Notebooks für die Datenuntersuchung erstellt und verwendet. Sie haben auch Code ausgeführt, um Zusammenfassungsstatistiken zu berechnen und Visualisierungen zu erstellen, um die Muster und Beziehungen in den Daten besser zu verstehen.

Wenn Sie das Modell und die Experimente abgeschlossen haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
2. Wählen Sie im Menü **...** auf der Symbolleiste die **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Andere** die Option **Diesen Arbeitsbereich entfernen** aus.
