---
lab:
  title: 'Erstellen einer generativen KI-App, die Ihre eigenen Daten verwendet'
  description: 'Erfahren Sie, wie Sie mithilfe des RAG-Modells (Retrieval Augmented Generation) eine Chat-App erstellen, die Aufforderungen zur Verwendung Ihrer eigenen Daten anfordert.'
---

# Erstellen einer generativen KI-App, die Ihre eigenen Daten verwendet

Retrieval Augmented Generation (RAG) ist eine Methode zum Erstellen von Anwendungen, die Daten aus benutzerdefinierten Datenquellen in einen Prompt für ein generatives KI-Modell integrieren. RAG ist ein häufig verwendetes Muster für die Entwicklung generativer KI-Apps – chatbasierte Anwendungen, die ein Sprachmodell verwenden, um Eingaben zu interpretieren und entsprechende Antworten zu generieren.

In dieser Übung verwenden Sie Azure KI-Foundry, um benutzerdefinierte Daten in eine generative KI-Lösung zu integrieren.

Diese Übung dauert ca. **45** Minuten.

> **Hinweis**: Diese Übung basiert auf Vorabversionen, die sich möglicherweise noch ändern können.

## Erstellen einer Azure KI Foundry-Ressource

Beginnen wir mit der Erstellung einer Azure AI Foundry-Ressource.

1. Öffnen Sie in einem Webbrowser das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure` und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden.
1. Erstellen Sie eine neue `Azure AI Foundry`-Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Name**: *Ein gültiger Name für Ihre Azure AI Foundry-Ressource*
    - **Region**: Wählen Sie aus einer der folgenden Regionen:
        - USA (Ost) 2
        - Schweden, Mitte
    - **Standardprojektname**: *Ein gültiger Name für Ihr Projekt*

1. Warten Sie, bis die Ressource erstellt wurde, und wechseln Sie dann zur Seite im Azure-Portal.
1. Wählen Sie auf der Seite für Ihre Azure AI Foundry-Ressource die Option **Zum Azure AI Foundry-Portal wechseln** aus.

## Bereitstellen von Modellen

Sie benötigen zwei Modelle, um Ihre Lösung zu implementieren:

- Ein *Einbettungsmodell* zum Vektorisieren von Textdaten für eine effiziente Indizierung und Verarbeitung.
- Ein Modell, das in natürlicher Sprache Antworten auf Fragen generieren kann, basierend auf Ihren Daten.

1. Wählen Sie im Azure KI Foundry-Portal in Ihrem Projekt im Navigationsbereich links unter **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Erstellen Sie eine neue Bereitstellung des Modells **text-embedding-ada-002** mit den folgenden Einstellungen, indem Sie **Anpassen** im Assistenten zum Bereitstellen des Modells wählen:

    - **Bereitstellungsname:***Ein gültiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Globaler Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **Verbundene KI-Ressource**: *Wählen Sie die zuvor erstellte Ressource*
    - **Token pro Minute Ratenlimit (Tausende)**: 50K *(oder das in Ihrem Abonnement verfügbare Maximum, wenn weniger als 50K)*
    - **Inhaltsfilter**: StandardV2 

    > **Hinweis**: Wenn an Ihrem aktuellen Speicherort für KI-Ressourcen kein Kontingent für das Modell, das Sie bereitstellen möchten, verfügbar ist, werden Sie aufgefordert, einen anderen Speicherort zu wählen, an dem eine neue KI-Ressource erstellt und mit Ihrem Projekt verbunden wird.

1. Kehren Sie zur Seite **Modelle + Endpunkte** zurück und wiederholen Sie die vorherigen Schritte, um ein **gpt-4o**-Modell mit einer **Global Standard**-Bereitstellung der neuesten Version mit einem TPM-Ratenlimit von **50K** (oder dem in Ihrem Abonnement verfügbaren Maximum, falls weniger als 50K) bereitzustellen.

    > **Hinweis:** Durch das Verringern der Token pro Minute (TPM) wird die Überlastung des Kontingents vermieden, das in Ihrem verwendeten Abonnement verfügbar ist. 50.000 TPM sind für die in dieser Übung verwendeten Daten ausreichend.

## Hinzufügen von Daten zu Ihrem Projekt

Die Daten für Ihre App bestehen aus einer Reihe von Reisebroschüren im PDF-Format von dem fiktiven Reisebüro *Margie's Travel*. Fügen wir sie dem Projekt hinzu.

1. Laden Sie in einer neuen Registerkarte des Browsers das [gezippte Archiv der Broschüren](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) von `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` herunter und entpacken Sie es in einen Ordner mit dem Namen **Broschüren** auf Ihrem lokalen Dateisystem.
1. Wählen Sie im Azure AI Foundry-Portal in Ihrem Projekt im Navigationsbereich auf der linken Seite **Playgrounds** und anschließend **Chat-Playground ausprobieren**.
1. Erweitern Sie im Bereich **Setup** des Playgrounds den Abschnitt **Daten hinzufügen** und wählen Sie **Datenquelle hinzufügen**.
1. Erweitern Sie im Assistenten **Daten hinzufügen** das Dropdownmenü, um **Dateien hochladen** auszuwählen.
1. Erstellen Sie eine neue Azure Blob Storage-Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Dieselbe Ressourcengruppe wie Ihre Azure AI Foundry-Ressource*
    - **Speicherkonto-Name**: *Ein gültiger Name für Ihre Speicherkonto-Ressource*
    - **Region**: *Gleiche Region wie Ihre Azure AI Foundry-Ressource*
    - **Leistung**: Standard
    - **Redundanz**: LRS
1. Erstellen Sie Ihre Ressource, und warten Sie, bis die Bereitstellung abgeschlossen ist.
1. Kehren Sie zur Azure AI Foundry-Registerkarte zurück, aktualisieren Sie die Liste der Azure Blob Storage-Ressourcen, und wählen Sie das neu erstellte Konto aus.

    > **Hinweis**: Wenn Sie eine Warnung erhalten, dass Azure OpenAI Ihre Berechtigung zum Zugriff auf Ihre Ressource benötigt, wählen Sie **CORS aktivieren**.

1. Erstellen Sie eine neue Azure KI-Suche-Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Dieselbe Ressourcengruppe wie Ihre Azure AI Foundry-Ressource*
    - **Dienstname**: *Ein gültiger Name für Ihre Azure KI Search-Ressource*
    - **Region**: *Gleiche Region wie Ihre Azure AI Foundry-Ressource*
    - **Tarif**: Basic

1. Erstellen Sie Ihre Ressource, und warten Sie, bis die Bereitstellung abgeschlossen ist.
1. Kehren Sie zur Azure AI Foundry-Registerkarte zurück, aktualisieren Sie die Liste der Azure KI-Suche-Ressourcen, und wählen Sie das neu erstellte Konto aus.
1. Benennen Sie Ihren Index `brochures-index`.
1. Aktivieren Sie die Option **Vektorsuche zu dieser Suchressource hinzufügen** und wählen Sie das zuvor bereitgestellte Einbettungsmodell aus. Wählen Sie **Weiter** aus.

   >**Hinweis**: Es kann eine Weile dauern, bis der Assistent zum Hinzufügen von **Daten** das bereitgestellte Einbettungsmodell erkennt. Wenn Sie also die Vektorsuchoption nicht aktivieren können, brechen Sie den Assistenten ab, warten Sie einige Minuten, und versuchen Sie es erneut.

1. Laden Sie alle PDF-Dateien aus dem zuvor extrahierten Ordner **Broschüren** hoch und wählen Sie dann **Weiter**.
1. Wählen Sie im Schritt **Datenverwaltung** den Suchtyp **Hybrid (Vektor + Schlüsselwort)** und eine Blockgröße von **1024**. Wählen Sie **Weiter** aus.
1. Wählen Sie im Schritt **Datenverbindung** als Authentifizierungstyp die Option **API-Schlüssel** aus. Wählen Sie **Weiter** aus.
1. Überprüfen Sie alle Konfigurationsschritte und wählen Sie anschließend **Speichern und schließen**.
1. Warten Sie, bis der Indexerstellungsprozess abgeschlossen ist, was je nach verfügbaren Computeressourcen in Ihrem Abonnement eine Weile dauern kann.

    > **Tipp**: Während Sie darauf warten, dass der Index erstellt wird, können Sie einen Blick auf die heruntergeladenen Broschüren werfen, um sich mit deren Inhalt vertraut zu machen.

## Testen des Indexes im Playground

Bevor Sie Ihren Index in einem RAG-basierten Prompt Flow verwenden, überprüfen wir, ob er verwendet werden kann, um generative KI-Antworten zu beeinflussen.

1. Stellen Sie auf der Chat-Playground-Seite im Bereich „Setup“ sicher, dass die Bereitstellung Ihres **gpt-4o**-Modells ausgewählt ist. Übermitteln Sie dann im Hauptchatsitzungsbereich die Eingabeaufforderung `Where can I stay in New York?`
1. Überprüfen Sie die Antwort, die auf Daten im Index basiert.

## Erstellen einer RAG-Client-App

Nachdem Sie nun über einen Arbeitsindex verfügen, können Sie Azure KI-Foundry- und Azure OpenAI-SDKs verwenden, um das RAG-Muster in einer Clientanwendung zu implementieren. Sehen wir uns den Code an, um dies in einem einfachen Beispiel zu erreichen.

> **Tipp**: Sie können wählen, ob Sie Ihre RAG-Lösung mit Python oder Microsoft C# entwickeln. Folgen Sie den Anweisungen im entsprechenden Abschnitt für Ihre ausgewählte Sprache.

### Vorbereiten der Anwendungskonfiguration

1. Kehren Sie zur Browser-Registerkarte mit dem Azure-Portal zurück (lassen Sie das Azure KI-Foundry-Portal in der vorhandenen Registerkarte geöffnet).
1. Verwenden Sie die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud-Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung ohne Speicher in Ihrem Abonnement.

    Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Fenster am unteren Rand des Azure-Portals. Sie können die Größe dieses Bereichs ändern oder maximieren, um die Arbeit zu vereinfachen.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im Cloud Shell-Bereich die folgenden Befehle ein, um das GitHub-Repository mit den Codedateien für diese Übung zu klonen (geben Sie den Befehl ein oder kopieren Sie ihn in die Zwischenablage und klicken Sie dann mit der rechten Maustaste in die Befehlszeile, um ihn als reinen Text einzufügen):

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **TIPP**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers in Anspruch nehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Chatanwendung enthält:

    > **Hinweis**: Führen Sie die Schritte für die gewählte Programmiersprache aus.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. Geben Sie im Befehlszeilenfenster der Cloud Shell den folgenden Befehl ein, um die OpenKI SDK-Bibliothek zu installieren:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt openai
    ```

    **C#**

    ```
   dotnet add package Azure.AI.OpenAI
    ```
    

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Ersetzen Sie in der Codedatei die folgenden Platzhalter: 
    - **your_openai_endpoint**: Der OpenKI-Endpunkt von der Seite **Übersicht** Ihres Projekts im Azure KI Foundry-Portal (stellen Sie sicher, dass Sie die Registerkarte **Azure OpenKI** ausgewählt haben, nicht die Registerkarte Azure KI-Inferenz oder Azure KI-Dienste).
    - **Ihr_openai_api_key** Der Open KI-API-Schlüssel von der Seite **Übersicht** Ihres Projekts im Azure KI Foundry-Portal (stellen Sie sicher, dass Sie die Registerkarte **Azure OpenKI** ausgewählt haben, nicht die Registerkarte Azure KI-Inferenz oder Azure KI-Dienste).
    - **Ihr_Chat-Modell**: Der Name, den Sie Ihrer **gpt-4o**-Modellbereitstellung auf der Seite **Modelle + Endpunkte** im Azure KI Foundry-Portal zugewiesen haben (der Standardname lautet `gpt-4o`).
    - **your_embedding_model**: Der Name, den Sie Ihrer **text-embedding-ada-002**-Modellbereitstellung auf der Seite **Modelle + Endpunkte** im Azure KI Foundry-Portal zugewiesen haben (der Standardname lautet `text-embedding-ada-002`).
    - **your_search_endpoint**: Die URL für Ihre Azure KI Search-Ressource. Dies finden Sie im **Verwaltungscenter** im Azure KI Foundry-Portal.
    - **your_search_api_key**: Der API-Schlüssel für Ihre Azure KI Search-Ressource. Dies finden Sie im **Verwaltungscenter** im Azure KI Foundry-Portal.
    - **Ihr_Index**: Ersetzen Sie diesen Wert durch den Indexnamen aus der Seite „**Daten + Indizes**“ für Ihr Projekt im Azure KI-Foundry-Portal (dies sollte`brochures-index` sein).
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

### Erkunden von Code zur Implementierung des RAG-Musters

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Codedatei zu bearbeiten:

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Überprüfen Sie den Code in der Datei, und notieren Sie Folgendes:
    - Erstellt einen Azure OpenKI-Client unter Verwendung des Endpunkts, des Schlüssels und des Chatmodells.
    - Erstellt eine geeignete Systemnachricht für eine reisebezogene Chatlösung.
    - Übermittelt einen Prompt (einschließlich des Systems und einer auf der Benutzereingabe basierenden Nachricht) an den Azure OpenAI-Client und fügt ihn hinzu:
        - Verbindungsdetails für den abzufragenden Azure KI-Suche Index.
        - Details zum Einbettungsmodell, das zur Vektorisierung der Abfrage verwendet werden soll\*.
    - Zeigt die Antwort des fundierten Prompts an.
    - Fügt die Antwort zum Chatverlauf hinzu.

    \**Die Abfrage für den Suchindex basiert auf dem Prompt und wird verwendet, um relevanten Text in den indizierten Dokumenten zu finden. Sie können eine stichwortbasierte Suche verwenden, bei der die Abfrage als Text übermittelt wird, aber eine vektorbasierte Suche kann effizienter sein - daher die Verwendung eines Einbettungsmodells, um den Abfragetext vor der Übermittlung zu vektorisieren.*

1. Verwenden Sie den Befehl **STRG+Q**, um den Code-Editor zu schließen, ohne Änderungen zu speichern, während die Cloud-Shell-Befehlszeile geöffnet bleibt.

### Ausführen der Chatanwendung

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die App auszuführen:

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie eine Frage ein, z. B. `Where should I go on vacation to see architecture?`, und überprüfen Sie die Antwort Ihres generativen KI-Modells.

    Beachten Sie, dass die Antwort Quellverweise enthält, um die indizierten Daten anzugeben, in denen die Antwort gefunden wurde.

1. Versuchen Sie es mit einer Anschlussfrage, zum Beispiel `Where can I stay there?`

1. Wenn Sie fertig sind, geben Sie `quit` ein, um das Programm zu beenden. Schließen Sie anschließend den Cloud Shell-Bereich.

## Bereinigung

Um unnötige Azure-Kosten und Ressourcenauslastung zu vermeiden, sollten Sie die in dieser Übung bereitgestellten Ressourcen entfernen.

1. Wenn Sie die Erkundung von Azure KI Foundry abgeschlossen haben, kehren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` zurück und melden sich gegebenenfalls mit Ihren Azure-Anmeldedaten an. Löschen Sie dann die Ressourcen in der Ressourcengruppe, in der Sie Ihre Azure KI-Suche- und Azure AI-Ressourcen bereitgestellt haben.
