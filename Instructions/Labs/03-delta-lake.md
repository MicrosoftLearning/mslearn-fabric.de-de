---
lab:
  title: Verwenden von Deltatabellen in Apache Spark
  module: Work with Delta Lake tables in Microsoft Fabric
---

# Verwenden von Deltatabellen in Apache Spark

Die Tabellen in einem Microsoft Fabric Lakehouse basieren auf dem Open-Source-Format Delta Lake. Delta Lake bietet Support für relationale Semantik sowohl für Batch- als auch für Streaming-Daten. In dieser Übung werden Sie Deltatabellen erstellen und die Daten mithilfe von SQL-Abfragen untersuchen.

Diese Übung dauert ca. **45** Minuten.

> [!Note] 
> Sie benötigen Zugriff auf einen [Microsoft Fabric-Mandanten](https://learn.microsoft.com/fabric/get-started/fabric-trial), um diese Übung abzuschließen.

## Erstellen eines Arbeitsbereichs

Bevor Sie mit Daten in Fabric arbeiten, erstellen Sie einen Arbeitsbereich in einem Mandanten mit aktivierter Fabric-Kapazität.

1. Navigieren Sie in einem Browser unter `https://app.fabric.microsoft.com/home?experience=fabric-developer` zur [Microsoft Fabric-Startseite](https://app.fabric.microsoft.com/home?experience=fabric-developer) und melden Sie sich mit Ihren Fabric-Anmeldeinformationen an.
1. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie im Bereich **Erweitert** einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-workspace.png)

## Erstellen eines Lakehouse und Hochladen von Dateien

Jetzt, da Sie einen Arbeitsbereich haben, ist es an der Zeit, ein Data Lakehouse für Ihre Daten zu erstellen.

1. Wählen Sie in der Menüleiste auf der linken Seite **Erstellen** aus. Wählen Sie auf der Seite *Neu* unter dem Abschnitt *Datentechnik* die Option **Lakehouse** aus. Wählen Sie einen eindeutigen Namen Ihrer Wahl aus.

    >**Hinweis**: Wenn die Option **Erstellen** nicht an die Seitenleiste angeheftet ist, müssen Sie zuerst die Ellipses-Option (**…**) auswählen.

    Nach etwa einer Minute wird ein neues Lakehouse erstellt:

    ![Screenshot: Neues Lakehouse](./Images/new-lakehouse.png)

1. Sehen Sie sich das neue Lakehouse an, und beachten Sie, dass Sie im Bereich **Explorer** auf der linken Seite Tabellen und Dateien im Lakehouse durchsuchen können:

Sie können nun Daten in das Lakehouse aufnehmen. Es gibt mehrere Möglichkeiten, dies zu tun. Für den Moment laden Sie jedoch eine Textdatei auf Ihren lokalen Computer (oder gegebenenfalls auf die VM im Lab) herunter und laden sie anschließend in Ihr Lakehouse hoch. 

1. Laden Sie die [Datendatei](https://github.com/MicrosoftLearning/dp-data/raw/main/products.csv) von `https://github.com/MicrosoftLearning/dp-data/raw/main/products.csv` herunter und speichern Sie sie als *Produkte.csv*.
1. Kehren Sie zu der Registerkarte des Webbrowsers zurück, die Ihr Lakehouse enthält, und wählen Sie im Explorer-Bereich neben dem Ordner **Dateien** das Menü mit den drei Punkten (...) aus. Menü.  Erstellen Sie einen **neuen Unterordner** namens *Produkte*.
1. Geben Sie Feld Menü für den Produktordner, **laden Sie die *products.csv* Datei von Ihrem lokalen Computer (oder lab VM, falls zutreffend) hoch**.
1. Nachdem die Datei hochgeladen wurde, wählen Sie den Ordner **Produkte** aus, um zu überprüfen, ob die Datei hochgeladen wurde, wie hier gezeigt:

    ![Anzeigebild von products.csv, das auf das Lakehouse hochgeladen wurde.](Images/upload-products.png)
  
## Untersuchen von Daten in einem Dataframe

Sie können nun ein Fabric-Notizbuch erstellen, um mit Ihren Daten zu arbeiten. Notebooks stellen eine interaktive Umgebung bereit, in der Sie Code schreiben und ausführen können.

1. Wählen Sie in der Menüleiste auf der linken Seite **Erstellen** aus. Wählen Sie auf der Seite *Neu* im Abschnitt *Datentechnik* die Option **Notebook** aus.

    Ein neues Notebook mit dem Namen **Notebook 1** wird erstellt und geöffnet.

    ![Screenshot eines neuen Notebooks](./Images/new-notebook.png)

1. Fabric weist jedem Notizbuch, das Sie erstellen, einen Namen zu, z. B. Notizbuch 1, Notizbuch 2 usw. Klicken Sie auf das Bedienfeld oberhalb der Registerkarte **Home** im Menü, um den Namen in einen aussagekräftigeren Namen zu ändern.
1. Markieren Sie die erste Zelle (die momentan eine Codezelle ist), und verwenden Sie dann in der oberen rechten Symbolleiste die Schaltfläche **M↓**, um sie in eine Abschriftenzelle umzuwandeln. Der in der Zelle enthaltene Text wird dann als formatierter Text angezeigt.
1. Verwenden Sie die Taste 🖉 (Bearbeiten), um die Zelle in den Bearbeitungsmodus zu schalten, und ändern Sie dann den Markdown wie unten gezeigt.

    ```markdown
    # Delta Lake tables 
    Use this notebook to explore Delta Lake functionality 
    ```

1. Klicken Sie an einer beliebigen Stelle im Notebook außerhalb der Zelle, um die Bearbeitung zu beenden.
1. Wählen Sie im **Explorer**-Bereich die Option **Datenelemente hinzufügen** und dann **Vorhandene Datenquellen** aus. Stellen Sie eine Verbindung mit dem zuvor erstellten Lakehouse her.
1. Fügen Sie eine neue Codezelle hinzu, und fügen Sie den folgenden Code ein, um die Produktdaten mit Hilfe eines definierten Schemas in einen DataFrame zu lesen:

    ```python
   from pyspark.sql.types import StructType, IntegerType, StringType, DoubleType

   # define the schema
   schema = StructType() \
   .add("ProductID", IntegerType(), True) \
   .add("ProductName", StringType(), True) \
   .add("Category", StringType(), True) \
   .add("ListPrice", DoubleType(), True)

   df = spark.read.format("csv").option("header","true").schema(schema).load("Files/products/products.csv")
   # df now is a Spark DataFrame containing CSV data from "Files/products/products.csv".
   display(df)
    ```

> [!TIP]
> Blenden Sie die Explorer-Fenster aus oder ein, indem Sie das Chevron-Symbol « verwenden. So können Sie sich entweder auf das Notebook oder auf Ihre Dateien konzentrieren.

1. Benutzen Sie die Schaltfläche **Zelle ausführen** (▷) auf der linken Seite der Zelle, um sie zu starten.

> [!NOTE]
> Da dies das erste Mal ist, dass Sie den Code in diesem Notebook ausführen, muss eine Spark-Sitzung gestartet werden. Dadurch kann der Abschluss der ersten Ausführung etwa eine Minute dauern. Nachfolgende Ausführungen erfolgen schneller.

1. Wenn der Zellenbefehl abgeschlossen ist, überprüfen Sie die Ausgabe unterhalb der Zelle, die wie folgt aussehen sollte:

    ![Anzeigebild der Daten aus products.csv.](Images/products-schema.png)
 
## Erstellen von Deltatabellen

Sie können den DataFrame als Delta-Tabelle speichern, indem Sie die Methode *saveAsTable* verwenden. Delta Lake unterstützt die Erstellung von verwalteten und externen Tabellen.

   * **Verwaltete** Delta-Tabellen profitieren von einer höheren Leistung, da Fabric sowohl die Schema-Metadaten als auch die Datendateien verwaltet.
   * **Externe** Tabellen ermöglichen es Ihnen, Daten extern zu speichern, wobei die Metadaten von Fabric verwaltet werden.

### Erstellen einer verwalteten Tabelle

Die Datendateien werden im Ordner **Tabellen** erstellt.

1. Verwenden Sie unter den Ergebnissen der ersten Codezelle das Symbol „+ Code“, um eine neue Codezelle hinzuzufügen.

> [!TIP]
> Um das Symbol für „+ Code“ zu sehen, bewegen Sie die Maus direkt unter und links neben die Ausgabe der aktuellen Zelle. Alternativ dazu können Sie in der Menüleiste auf der Registerkarte „Bearbeiten“ **+ Codezelle hinzufügen** auswählen.

1. Um eine verwaltete Deltatabelle zu erstellen, fügen Sie eine neue Zelle hinzu, geben den folgenden Code ein und führen die Zelle dann aus:

    ```python
   df.write.format("delta").saveAsTable("managed_products")
    ```

1. **Aktualisieren** Sie im Explorer den Ordner „Tabellen“ und erweitern Sie den Tabellen-Knoten, um zu überprüfen, ob die Tabelle **managed_products** erstellt wurde.

> [!NOTE]
> Das Dreieckssymbol neben dem Dateinamen gibt eine Deltatabelle an.

Die Dateien für verwaltete Tabellen werden im Ordner **Tabellen** im Lakehouse gespeichert. Es wurde ein Ordner mit dem Namen *managed_products* erstellt, in dem die Parquet-Dateien und der Ordner „delta_log“ für die Tabelle gespeichert werden.

### Erstellen einer externen Tabelle

Sie können auch externe Tabellen erstellen, die an einem anderen Ort als dem Lakehouse gespeichert sein können, wobei die Schemametadaten im Lakehouse gespeichert werden.

1. Im Explorer-Fenster, im ... Menü für den Ordner **Dateien**, wählen Sie **ABFS-Pfad kopieren**. Der ABFS-Pfad ist der vollständig qualifizierte Pfad zum Ordner mit den Lakehouse-Dateien.

1. Fügen Sie den ABFS-Pfad in eine neue Codezelle ein. Fügen Sie den folgenden Code hinzu und verwenden Sie Ausschneiden und Einfügen, um den abfs_path an der richtigen Stelle im Code einzufügen:

    ```python
   df.write.format("delta").saveAsTable("external_products", path="abfs_path/external_products")
    ```

1. Der vollständige Pfad sollte etwa wie folgt aussehen:

    ```python
   abfss://workspace@tenant-onelake.dfs.fabric.microsoft.com/lakehousename.Lakehouse/Files/external_products
    ```

1. **Führen Sie** die Zelle aus, um den DataFrame als externe Tabelle im Ordner „Files/external_products“ zu speichern.

1. **Aktualisieren** Sie im Explorerfenster den Tabellenordner und erweitern Sie den Tabellenknoten. Überprüfen Sie, ob die Tabelle „external_products“ mit den Schemametadaten erstellt wurde.

1. Im Explorer-Fenster, im ... Menü für den Ordner „Dateien“, wählen Sie **Aktualisieren**. Erweitern Sie dann den Knoten „Dateien“ und überprüfen Sie, ob der Ordner „external_products“ für die Datendateien der Tabelle erstellt wurde.

### Vergleichen verwalteter und externer Tabellen

Lassen Sie uns die Unterschiede zwischen verwalteten und externen Tabellen mithilfe des Magic-Befehls „%%sql“ erkunden.

1. Geben Sie in eine neue Codezelle den folgenden Code ein:

    ```python
   %%sql
   DESCRIBE FORMATTED managed_products;
    ```

1. In den Ergebnissen können Sie die Tabelle nach der Speicherorteigenschaft anzeigen. Klicken Sie auf den Speicherortwert in der Spalte „Datentyp“, um den vollständigen Pfad anzuzeigen. Beachten Sie, dass der OneLake-Speicherort mit /Tables/managed_products endet.

1. Ändern Sie den DESCRIBE-Befehl, um die Details der external_products-Tabelle wie hier dargestellt anzuzeigen:

    ```python
   %%sql
   DESCRIBE FORMATTED external_products;
    ```

1. Führen Sie die Zelle aus und sehen Sie sich in den Ergebnissen die Eigenschaft „Speicherort“ für die Tabelle an. Erweitern Sie die Spalte „Datentyp“, um den vollständigen Pfad anzuzeigen, und beachten Sie, dass die OneLake-Speicherorte mit /Files/external_products enden.

1. Geben Sie in eine neue Codezelle den folgenden Code ein:

    ```python
   %%sql
   DROP TABLE managed_products;
   DROP TABLE external_products;
    ```

1. **Aktualisieren** Sie im Explorerfenster den Tabellenordner, um zu überprüfen, dass im Tabellenknoten keine Tabellen aufgeführt sind.
1. **Aktualisieren** Sie im Explorerfenster den Ordner „Dateien“ und stellen Sie sicher, dass die Datei „external_products“ *nicht* gelöscht wurde. Wählen Sie diesen Ordner, um die Parquet-Datendateien und den Ordner „_delta_log“ anzuzeigen. 

Die Metadaten für die externe Tabelle wurden gelöscht, aber nicht die Datendatei.

## Verwenden Sie SQL, um eine Deltatabelle zu erstellen

Sie erstellen nun eine Deltatabelle mit dem Magic-Befehl „%%sql“. 

1. Fügen Sie eine weitere Codezelle hinzu, und führen Sie den Code aus:

    ```python
   %%sql
   CREATE TABLE products
   USING DELTA
   LOCATION 'Files/external_products';
    ```

1. Im Explorer-Fenster, im ... Menü für den Ordner **Tabellen**, wählen Sie **Aktualisieren**. Erweitern Sie dann den Tabellenknoten und überprüfen Sie, ob eine neue Tabelle mit dem Namen *Produkte* aufgeführt ist. Erweitern Sie dann die Tabelle, um das Schema anzuzeigen.
1. Fügen Sie eine weitere Codezelle hinzu, und führen Sie den Code aus:

    ```python
   %%sql
   SELECT * FROM products;
    ```

## Erkunden der Tabellenversionsverwaltung

Der Transaktionsverlauf für Deltatabellen wird in JSON-Dateien im Ordner „delta_log“ gespeichert. Sie können dieses Transaktionsprotokoll verwenden, um die Datenversionsverwaltung zu verwalten.

1. Fügen Sie dem Notebook eine neue Codezelle hinzu, und führen Sie den folgenden Code aus, der eine Reduzierung des Preises von 10 % für Mountainbikes implementiert:

    ```python
   %%sql
   UPDATE products
   SET ListPrice = ListPrice * 0.9
   WHERE Category = 'Mountain Bikes';
    ```

1. Fügen Sie eine weitere Codezelle hinzu, und führen Sie den Code aus:

    ```python
   %%sql
   DESCRIBE HISTORY products;
    ```

Die Ergebnisse zeigen den Verlauf der Transaktionen, die für die Tabelle aufgezeichnet wurden.

1. Fügen Sie eine weitere Codezelle hinzu, und führen Sie den Code aus:

    ```python
   delta_table_path = 'Files/external_products'
   # Get the current data
   current_data = spark.read.format("delta").load(delta_table_path)
   display(current_data)

   # Get the version 0 data
   original_data = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
   display(original_data)
    ```

Es werden zwei Ergebnissätze zurückgegeben - einer mit den Daten nach der Preissenkung, der andere mit der ursprünglichen Version der Daten.

## Analysieren von Daten in Deltatabellen mit SQL-Abfragen

Mit dem SQL Magic-Befehl können Sie die SQL-Syntax anstelle von Pyspark verwenden. Hier erstellen Sie eine temporäre Ansicht aus der Produkttabelle mit einer `SELECT`-Anweisung.

1. Fügen Sie eine neue Codezelle hinzu, und führen Sie den folgenden Code aus, um die temporäre Ansicht zu erstellen und anzuzeigen:

    ```python
   %%sql
   -- Create a temporary view
   CREATE OR REPLACE TEMPORARY VIEW products_view
   AS
       SELECT Category, COUNT(*) AS NumProducts, MIN(ListPrice) AS MinPrice, MAX(ListPrice) AS MaxPrice, AVG(ListPrice) AS AvgPrice
       FROM products
       GROUP BY Category;

   SELECT *
   FROM products_view
   ORDER BY Category;    
    ```

1. Fügen Sie eine neue Codezelle hinzu, und führen Sie den folgenden Code aus, um die 10 wichtigsten Kategorien nach Anzahl der Produkte zu ermitteln:

    ```python
   %%sql
   SELECT Category, NumProducts
   FROM products_view
   ORDER BY NumProducts DESC
   LIMIT 10;
    ```

1. Wenn die Daten zurückgegeben werden, wählen Sie **+Neues Diagramm** aus, um eines der vorgeschlagenen Diagramme anzuzeigen.

    ![Anzeigebild der SQL-Anweisung Auswählen und der Ergebnisse.](Images/sql-select.png)

Alternativ können Sie eine SQL-Abfrage mit PySpark ausführen.

1. Fügen Sie den folgenden Code in einer neuen Codezelle hinzu und führen Sie ihn aus:

    ```python
   from pyspark.sql.functions import col, desc

   df_products = spark.sql("SELECT Category, MinPrice, MaxPrice, AvgPrice FROM products_view").orderBy(col("AvgPrice").desc())
   display(df_products.limit(6))
    ```

## Verwenden von Delta-Tabellen für Streaming-Daten

Delta Lake unterstützt Streaming-Daten. Deltatabellen können eine Senke oder Quelle für Datenströme sein, die mit der Spark Structured Streaming-API erstellt wurden. In diesem Beispiel verwenden Sie eine Deltatabelle als Senke für einige Streamingdaten in einem simulierten IoT-Szenario (Internet der Dinge).

1.  Fügen Sie eine neue Codezelle hinzu, fügen Sie den folgenden Code ein und führen Sie ihn aus:

    ```python
    from notebookutils import mssparkutils
    from pyspark.sql.types import *
    from pyspark.sql.functions import *

    # Create a folder
    inputPath = 'Files/data/'
    mssparkutils.fs.mkdirs(inputPath)

    # Create a stream that reads data from the folder, using a JSON schema
    jsonSchema = StructType([
    StructField("device", StringType(), False),
    StructField("status", StringType(), False)
    ])
    iotstream = spark.readStream.schema(jsonSchema).option("maxFilesPerTrigger", 1).json(inputPath)

    # Write some event data to the folder
    device_data = '''{"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev2","status":"error"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"error"}
    {"device":"Dev2","status":"ok"}
    {"device":"Dev2","status":"error"}
    {"device":"Dev1","status":"ok"}'''

    mssparkutils.fs.put(inputPath + "data.txt", device_data, True)

    print("Source stream created...")
    ```

Sicherstellen, dass die Meldung *Quell-Stream erstellt...* angezeigt. Der Code, den Sie gerade ausgeführt haben, hat eine Streamingdatenquelle basierend auf einem Ordner erstellt, in dem einige Daten gespeichert wurden, die Messwerte von hypothetischen IoT-Geräten darstellen.

1. Fügen Sie den folgenden Code in einer neuen Codezelle hinzu, und führen Sie ihn aus:

    ```python
   # Write the stream to a delta table
   delta_stream_table_path = 'Tables/iotdevicedata'
   checkpointpath = 'Files/delta/checkpoint'
   deltastream = iotstream.writeStream.format("delta").option("checkpointLocation", checkpointpath).start(delta_stream_table_path)
   print("Streaming to delta sink...")
    ```

Dieser Code schreibt die Streaminggerätedaten im Deltaformat in einen Ordner mit dem Namen iotdevicedata. Da der Pfad für den Ordnerspeicherort im Ordner Tabellen angegeben ist, wird automatisch eine Tabelle für ihn erstellt.

1. Fügen Sie den folgenden Code in einer neuen Codezelle hinzu, und führen Sie ihn aus:

    ```python
   %%sql
   SELECT * FROM IotDeviceData;
    ```

Dieser Code fragt die Tabelle IotDeviceData ab, die die Gerätedaten aus der Streamingquelle enthält.

1. Fügen Sie den folgenden Code in einer neuen Codezelle hinzu, und führen Sie ihn aus:

    ```python
   # Add more data to the source stream
   more_data = '''{"device":"Dev1","status":"ok"}
   {"device":"Dev1","status":"ok"}
   {"device":"Dev1","status":"ok"}
   {"device":"Dev1","status":"ok"}
   {"device":"Dev1","status":"error"}
   {"device":"Dev2","status":"error"}
   {"device":"Dev1","status":"ok"}'''

   mssparkutils.fs.put(inputPath + "more-data.txt", more_data, True)
    ```

Dieser Code schreibt weitere hypothetische Gerätedaten in die Streamingquelle.

1. Führen Sie die Zelle mit dem folgenden Code erneut aus:

    ```python
   %%sql
   SELECT * FROM IotDeviceData;
    ```

Dieser Code fragt die IotDeviceData-Tabelle erneut ab, die nun die zusätzlichen Daten enthalten sollte, die der Streamingquelle hinzugefügt wurden.

1. Fügen Sie in einer neuen Codezelle Code hinzu, um den Datenstrom zu beenden und die Zelle auszuführen:

    ```python
   deltastream.stop()
    ```

## Bereinigen von Ressourcen

In dieser Übung haben Sie gelernt, wie man mit Deltatabellen in Microsoft Fabric arbeitet.

Wenn Sie mit der Erkundung Ihres Lakehouses fertig sind, können Sie den für diese Übung erstellten Arbeitsbereich löschen.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
1. Geben Sie Feld Menü auf der Symbolleiste, wählen Sie **Arbeitsbereichseinstellungen**.
1. Wählen Sie im Abschnitt Allgemein die Option **Diesen Arbeitsbereich entfernen**.
