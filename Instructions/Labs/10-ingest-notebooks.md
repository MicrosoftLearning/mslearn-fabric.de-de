---
lab:
  title: Erfassen von Daten mit Spark und Microsoft Fabric-Notebooks
  module: Ingest data with Spark and Microsoft Fabric notebooks
---

# Erfassen von Daten mit Spark und Microsoft Fabric-Notebooks

In diesem Lab erstellen Sie ein Microsoft Fabric-Notebook und verwenden PySpark, um eine Verbindung mit einem Azure Blob Storage-Pfad herzustellen. Dann laden Sie die Daten mithilfe von Schreiboptimierungen in ein Lakehouse.

Dieses Lab dauert ungefähr **30** Minuten.

In dieser Übung erstellen wir den Code über mehrere Notebook-Codezellen hinweg. Dies entspricht möglicherweise nicht der Vorgehensweise in Ihrer Umgebung, kann jedoch für das Debuggen hilfreich sein.

Da wir auch mit einem Beispieldataset arbeiten, entspricht die Optimierung möglicherweise nicht dem Ergebnis in einer größeren Produktionsumgebung. Dennoch ist die Verbesserung sichtbar, und wenn es auf jede Millisekunde ankommt, ist die Optimierung unverzichtbar.

> **Hinweis**: Für diese Übung benötigen Sie eine **Microsoft Fabric-Lizenz**. Weitere Informationen zum Aktivieren einer kostenlosen Fabric-Testlizenz finden Sie unter [Erste Schritte mit Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial).
>
> Außerdem brauchen Sie dazu ein *Schul-* oder *Geschäftskonto* von Microsoft. Wenn Sie kein Microsoft-Konto haben, können Sie sich [für eine kostenlose Testversion von Microsoft Office 365 registrieren](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Erstellen eines Arbeitsbereichs und eines Lakehouse-Ziels

Erstellen Sie zunächst einen Arbeitsbereich mit aktivierter Fabric-Testversion, ein neues Lakehouse und einen Zielordner im Lakehouse.

1. Melden Sie sich unter `https://app.fabric.microsoft.com` bei [Microsoft Fabric](https://app.fabric.microsoft.com) an, und wählen Sie **Datentechnik mit Synapse** aus.

    ![Screenshot: Datentechnik mit Synapse](Images/data-engineering-home.png)

1. Wählen Sie in der Menüleiste auf der linken Seite **Arbeitsbereiche** aus.

1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazität aus (*Testversion*, *Premium* oder *Fabric*).

1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein, und neben dem Namen sollte sich eine Raute befinden, wie hier gezeigt:

    ![Screenshot: neuer und leerer Arbeitsbereich](Images/new-workspace.png)

1. Wählen Sie in Ihrem Arbeitsbereich **+ Neu > Lakehouse** aus, geben Sie einen Namen an, und wählen Sie **Erstellen** aus.

    > :Memo: **Hinweis**: Es kann einige Minuten dauern, bis ein neues Lakehouse ohne **Tabellen** oder **Dateien** erstellt wird.

    ![Screenshot: neues Lakehouse](Images/new-lakehouse.png)

1. Wählen Sie unter **Dateien** die Option **[...]** aus, um einen **neuen Unterordner** mit dem Namen **RawData** zu erstellen.

1. Wählen Sie im Lakehouse-Explorer im Lakehouse **Dateien > ... > Eigenschaften** aus.

1. Kopieren Sie den **ABFS-Pfad** für den Ordner **RawData** zur späteren Verwendung in einen leeren Editor, der ungefähr wie folgt aussehen sollte: `abfss://{workspace_name}@onelake.dfs.fabric.microsoft.com/{lakehouse_name}.Lakehouse/Files/{folder_name}/{file_name}`

Sie sollten jetzt über einen Arbeitsbereich mit einem Lakehouse und einem RawData-Zielordner verfügen.

## Erstellen eines Fabric-Notebooks und Laden externer Daten

Erstellen Sie ein neues Fabric-Notebook, und stellen Sie mit PySpark eine Verbindung mit einer externen Datenquelle her.

1. Wählen Sie im oberen Menü im Lakehouse **Notebook öffnen > Neues Notebook** aus. Dieses Notebook wird nach der Erstellung geöffnet.

    > :bulb: **Tipp:** Sie haben über dieses Notebook Zugriff auf den Lakehouse-Explorer und können die Anzeige aktualisieren, damit der Fortschritt angezeigt wird, während Sie diese Übung absolvieren.

1. Beachten Sie, dass der Code in der Standardzelle auf **PySpark (Python)** eingestellt ist.

1. Fügen Sie den folgenden Code in die Codezelle ein. Dies bewirkt Folgendes:
    1. Deklarieren von Parametern für die Verbindungszeichenfolge
    1. Erstellen der Verbindungszeichenfolge
    1. Einlesen von Daten in einen Datenrahmen

    ```Python
    # Azure Blob Storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    
    # Construct connection path
    wasbs_path = f'wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}'
    print(wasbs_path)
    
    # Read parquet data from Azure Blob Storage path
    blob_df = spark.read.parquet(wasbs_path)
    ```

1. Wählen Sie neben der Codezelle **&#9655; Zelle ausführen** aus, um eine Verbindung herzustellen und Daten in einen DataFrame einzulesen.

    **Erwartetes Ergebnis:** Ihr Befehl sollte erfolgreich sein und `wasbs://nyctlc@azureopendatastorage.blob.core.windows.net/yellow` drucken

    > :memo: **Hinweis:** Eine Spark-Sitzung wird bei der ersten Codeausführung gestartet, sodass die Ausführung möglicherweise länger dauern kann.

1. Um die Daten in eine Datei zu schreiben, benötigen Sie nun den **ABFS-Pfad** für den **RawData**-Ordner.

1. Fügen Sie den folgenden Code in eine **neue Codezelle** ein:

    ```python
        # Declare file name    
        file_name = "yellow_taxi"
    
        # Construct destination path
        output_parquet_path = f"**InsertABFSPathHere**/{file_name}"
        print(output_parquet_path)
        
        # Load the first 1000 rows as a Parquet file
        blob_df.limit(1000).write.mode("overwrite").parquet(output_parquet_path)
    ```

1. Der Pfad für **output_parquet_path** sollte etwa wie folgt aussehen: `abfss://Spark@onelake.dfs.fabric.microsoft.com/DPDemo.Lakehouse/Files/RawData/yellow_taxi`

1. Wählen Sie neben der Codezelle **&#9655; Zelle ausführen** aus, um 1.000 Zeilen in eine Datei „yellow_taxi.parquet“ zu schreiben.

1. Um das Laden von Daten aus dem Lakehouse-Explorer zu bestätigen, wählen Sie **Dateien > ... > Aktualisieren** aus.

Nun sollte Ihr neuer Ordner **RawData** mit einer „Datei“ **yellow_taxi.parquet** angezeigt werden, *die als Ordner mit Partitionsdateien dargestellt wird*.

## Transformieren und Laden von Daten in eine Deltatabelle

Wahrscheinlich ist Ihre Aufgabe zur Datenerfassung nicht mit dem Laden einer Datei abgeschlossen. Deltatabellen in einem Lakehouse ermöglichen das skalierbare und flexible Abfragen und Speichern. Deshalb werden wir auch eine erstellen.

1. Erstellen Sie eine neue Codezelle, und fügen Sie den folgenden Code ein:

    ```python
    from pyspark.sql.functions import col, to_timestamp, current_timestamp, year, month
    
    # Add dataload_datetime column with current timestamp
    filtered_df = raw_df.withColumn("dataload_datetime", current_timestamp())
    
    # Filter columns to exclude any NULL values in storeAndFwdFlag
    filtered_df = filtered_df.filter(raw_df["storeAndFwdFlag"].isNotNull())
    
    # Load the filtered data into a Delta table
    table_name = "yellow_taxi"  # Replace with your desired table name
    filtered_df.write.format("delta").mode("append").saveAsTable(table_name)
    
    # Display results
    display(filtered_df.limit(1))
    ```

1. Wählen Sie neben der Codezelle **&#9655; Zelle ausführen** aus.

    * Dadurch wird eine Zeitstempelspalte **dataload_datetime** hinzugefügt, in der protokolliert wird, wann die Daten in eine Deltatabelle geladen wurden.
    * Filtern von NULL-Werten in **storeAndFwdFlag**
    * Laden von gefilterten Daten in eine Deltatabelle
    * Anzeigen einer einzelnen Zeile zur Validierung

1. Überprüfen und bestätigen Sie die angezeigten Ergebnisse, die der folgenden Abbildung ähneln:

    ![Screenshot: erfolgreiche Ausgabe mit einer einzelnen Zeile](Images/notebook-transform-result.png)

Sie haben nun erfolgreich eine Verbindung mit externen Daten hergestellt, sie in eine Parquet-Datei geschrieben und die Daten in einen DataFrame geladen, transformiert und in eine Deltatabelle geladen.

## Optimieren von Schreibvorgängen in Deltatabellen

Wahrscheinlich verwenden Sie in Ihrer Organisation Big Data und haben sich deshalb für Fabric-Notebooks zur Datenerfassung entschieden. Daher werden wir uns nun auch genauer ansehen, wie Sie das Erfassen und Lesen Ihrer Daten optimieren können. Zunächst wiederholen wir die Schritte zum Transformieren und Schreiben in eine Deltatabelle einschließlich Schreiboptimierungen.

1. Erstellen Sie eine neue Codezelle, und fügen Sie den folgenden Code ein:

    ```python
    from pyspark.sql.functions import col, to_timestamp, current_timestamp, year, month
    
    # Read the parquet data from the specified path
    raw_df = spark.read.parquet("**InsertYourABFSPathHere**")
    
    # Add dataload_datetime column with current timestamp
    opt_df = raw_df.withColumn("dataload_datetime", current_timestamp())
    
    # Filter columns to exclude any NULL values in storeAndFwdFlag
    opt_df = opt_df.filter(opt_df["storeAndFwdFlag"].isNotNull())
    
    # Enable V-Order
    spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
    
    # Enable automatic Delta optimized write
    spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
    
    # Load the filtered data into a Delta table
    table_name = "yellow_taxi_opt"  # New table name
    opt_df.write.format("delta").mode("append").saveAsTable(table_name)
    
    # Display results
    display(opt_df.limit(1))
    ```

1. Rufen Sie den **ABFS-Pfad** erneut ab, und aktualisieren Sie den Code im Block, **bevor** Sie die Zelle ausführen.

1. Vergewissern Sie sich, dass Sie dieselben Ergebnisse wie vor dem Optimierungscode haben.

Notieren Sie sich nun die Laufzeiten für beide Codeblöcke. Ihre Zeiten werden davon abweichen, aber Sie können mit dem optimierten Code eine deutliche Leistungssteigerung feststellen.

## Analysieren von Daten in Deltatabellen mit SQL-Abfragen

In diesem Lab steht die Datenerfassung im Mittelpunkt, wobei der Prozess zum *Extrahieren, Transformieren und Laden* erläutert wird. Es ist jedoch auch sinnvoll, die Daten in einer Vorschau anzuzeigen.

1. Erstellen Sie eine neue Codezelle, und fügen Sie den unten gezeigten Code ein:

    ```python
    # Load table into df
    delta_table_name = "yellow_taxi"
    table_df = spark.read.format("delta").table(delta_table_name)
    
    # Create temp SQL table
    table_df.createOrReplaceTempView("yellow_taxi_temp")
    
    # SQL Query
    table_df = spark.sql('SELECT * FROM yellow_taxi_temp')
    
    # Display 10 results
    display(table_df.limit(10))
    ```

1. Erstellen Sie eine weitere Codezelle, und fügen Sie auch diesen Code ein:

    ```python
    # Load table into df
    delta_table_name = "yellow_taxi_opt"
    opttable_df = spark.read.format("delta").table(delta_table_name)
    
    # Create temp SQL table
    opttable_df.createOrReplaceTempView("yellow_taxi_opt")
    
    # SQL Query to confirm
    opttable_df = spark.sql('SELECT * FROM yellow_taxi_opt')
    
    # Display results
    display(opttable_df.limit(3))
    ```

1. Wählen Sie nun in der oberen Menüleiste die Option **Alle ausführen** aus.

Dadurch werden alle Codezellen ausgeführt, und Sie können den vollständigen Prozess von Anfang bis Ende sehen. Sie können die Ausführungszeiten zwischen optimierten und nicht optimierten Codeblöcken sehen.

## Bereinigen von Ressourcen

In dieser Übung haben Sie erfahren, wie Sie Folgendes erstellen:

* Arbeitsbereiche
* Lakehouses
* Fabric-Notebooks
* PySpark-Code für folgende Zwecke:
  * Verbinden zu externen Datenquellen
  * Einlesen von Daten in einen Datenrahmen
  * Schreiben von DataFrame-Daten in eine Parquet-Datei
  * Lesen von Daten aus einer Parquet-Datei
  * Transformieren von Daten in einem DataFrame
  * Laden von DataFrame-Daten in eine Deltatabelle
  * Optimieren von Schreibvorgängen in Deltatabellen
  * Abfragen von Daten in einer Deltatabelle mithilfe von SQL

Wenn Sie die Untersuchung abgeschlossen haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
2. Wählen Sie im Menü **...** auf der Symbolleiste die **Arbeitsbereichseinstellungen** aus.
3. Klicken Sie im Abschnitt **Andere** auf **Diesen Arbeitsbereich entfernen**.
