---
lab:
  title: 'Erstellen einer generativen KI-App, die Ihre eigenen Daten verwendet'
  description: 'Erfahren Sie, wie Sie mithilfe des RAG-Modells (Retrieval Augmented Generation) eine Chat-App erstellen, die Aufforderungen zur Verwendung Ihrer eigenen Daten anfordert.'
---

# Erstellen einer generativen KI-App, die Ihre eigenen Daten verwendet

Retrieval Augmented Generation (RAG) ist eine Methode zum Erstellen von Anwendungen, die Daten aus benutzerdefinierten Datenquellen in einen Prompt für ein generatives KI-Modell integrieren. RAG ist ein häufig verwendetes Muster für die Entwicklung generativer KI-Apps – chatbasierte Anwendungen, die ein Sprachmodell verwenden, um Eingaben zu interpretieren und entsprechende Antworten zu generieren.

In dieser Übung verwenden Sie das Azure KI Foundry-Portal und die Azure AI Foundry- und Azure OpenAI-SDKs, um benutzerdefinierte Daten in eine generative KI-App zu integrieren.

Diese Übung dauert ca. **45** Minuten.

> **Hinweis**: Diese Übung basiert auf Vorabversionen von SDKs, die sich möglicherweise noch ändern können. Wo nötig, haben wir spezielle Versionen von Paketen verwendet, die möglicherweise nicht die neuesten verfügbaren Versionen widerspiegeln. Es kann zu unerwartetem Verhalten, Warnungen oder Fehlern kommen.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit der Erstellung eines Azure AI Foundry-Projekts und der erforderlichen Dienstressourcen, die es benötigt, mithilfe Ihrer eigenen Daten – einschließlich einer Azure KI-Suche-Ressource.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry-Logo** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht:

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Namen für Ihr Projekt ein und wählen Sie, falls ein vorhandener Hub vorgeschlagen wird, die Option zum Erstellen eines neuen. Überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihren Hub und Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Ein gültiger Name für Ihren Hub*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** und wählen Sie dann **gpt-4o** im Fenster Standort-Hilfsprogramm und verwenden Sie die empfohlene Region\*
    - **Azure KI Services oder Azure OpenAI verbinden**: *Erstellen Sie eine neue KI-Dienst-Ressource*
    - **Verbinden von Azure KI-Suche**: *Erstellen Sie eine neue Ressource von Azure KI-Suche mit einem eindeutigen Namen*

    > \* Azure OpenAI-Ressourcen sind durch regionale Modellkontingente eingeschränkt. Sollte im weiteren Verlauf der Übung eine Kontingentgrenze überschritten werden, müssen Sie möglicherweise eine weitere Ressource in einer anderen Region anlegen.

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./media/ai-foundry-project.png)

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
1. Wählen Sie im Azure KI Foundry-Portal in Ihrem Projekt im Navigationsbereich auf der linken Seite unter **Meine Assets** die Seite **Daten + Indizes**.
1. Wählen Sie **+ Neue Daten** aus.
1. Erweitern Sie im Assistenten **Hinzufügen Ihrer Daten** das Dropdownmenü, um **Dateien/Ordner hochladen** auszuwählen.
1. Wählen Sie **Ordner hochladen** und laden Sie den Ordner **Broschüren** hoch. Warten Sie, bis alle Dateien des Ordners aufgelistet sind.
1. Wählen Sie **Weiter** und setzen Sie den Dateinamen auf `brochures`.
1. Warten Sie, bis der Ordner hochgeladen wurde, und beachten Sie, dass er mehrere .pdf-Dateien enthält.

## Erstellen eines Indexes für Ihre Daten

Nachdem Sie Ihrem Projekt nun eine Datenquelle hinzugefügt haben, können Sie sie verwenden, um einen Index in Ihrer Azure KI Search-Ressource zu erstellen.

1. Wählen Sie im Azure KI Foundry-Portal in Ihrem Projekt im Navigationsbereich auf der linken Seite unter **Meine Assets** die Seite **Daten + Indizes**.
1. Auf der Registerkarte **Indizes** fügen Sie einen neuen Index mit den folgenden Einstellungen hinzu:
    - **Quellstandort**:
        - **Datenquelle**: Daten im Azure KI Foundry-Portal
            - *Auswählen der Datenquelle **Broschüren***
    - **Indexkonfiguration**:
        - **Wählen Sie den Azure KI-Suche-Dienst aus:** *Wählen Sie die **AzureAISearch**-Verbindung mit Ihrer Azure KI Search-Ressource aus*
        - **Vektorindex**: `brochures-index`
        - **VM**: Automatisch auswählen
    - **Sucheinstellungen**:
        - **Vektoreinstellungen**: Hinzufügen der Vektorsuche zu dieser Suchressource
        - **Azure OpenAI-Verbindung**: *Wählen Sie die standardmäßige Azure OpenAI-Ressource für Ihren Hub aus.*
        - **Einbettungsmodell**: text-embedding-ada-002
        - **Bereitstellung des Einbettungsmodells**: *Ihre Bereitstellung des* text-embedding-ada-002 *Modells*

1. Erstellen Sie den Vektorindex und warten Sie, bis der Indizierungsprozess abgeschlossen ist, was je nach verfügbaren Rechenressourcen in Ihrem Abonnement eine Weile dauern kann.

    Der Indexerstellungsvorgang besteht aus den folgenden Aufträgen:

    - Zerlegen, segmentieren und integrieren Sie die Texttoken in Ihre Broschürendaten.
    - Erstellen Sie den Azure KI-Suchindex.
    - Registrieren Sie die Indexressource.

    > **Tipp**: Während Sie darauf warten, dass der Index erstellt wird, können Sie einen Blick auf die heruntergeladenen Broschüren werfen, um sich mit deren Inhalt vertraut zu machen.

## Testen des Indexes im Playground

Bevor Sie Ihren Index in einem RAG-basierten Prompt Flow verwenden, überprüfen wir, ob er verwendet werden kann, um generative KI-Antworten zu beeinflussen.

1. Wählen Sie im Navigationsbereich auf der linken Seite die Seite **Playgrounds** aus und öffnen Sie den Playground **Chat**.
1. Vergewissern Sie sich auf der Seite Chat Playground im Bereich Setup, dass Ihre **gpt-4o**-Modellimplementierung ausgewählt ist. Übermitteln Sie dann im Hauptchatsitzungsbereich die Eingabeaufforderung `Where can I stay in New York?`
1. Überprüfen Sie die Antwort, die eine generische Antwort aus dem Modell ohne Daten aus dem Index sein sollte.
1. Erweitern Sie im Bereich „Einrichtung“ das Feld **Ihre Daten hinzufügen** und fügen Sie dann den Projektindex **Broschüren-Index** hinzu und wählen Sie den Suchtyp **hybrid (Vektor + Schlüsselwort)** aus.

   > **Tipp**: In einigen Fällen sind neu erstellte Indizes möglicherweise nicht sofort verfügbar. Das Aktualisieren des Browsers hilft in der Regel, aber wenn das Problem weiterhin auftritt, bei dem der Index nicht gefunden werden kann, müssen Sie möglicherweise warten, bis der Index erkannt wird.

1. Nachdem der Index hinzugefügt wurde und die Chatsitzung neu gestartet wurde, übermitteln Sie die Eingabeaufforderung `Where can I stay in New York?` erneut
1. Überprüfen Sie die Antwort, die auf Daten im Index basiert.

## Erstellen einer RAG-Client-App mit Azure AI Foundry- und Azure OpenAI-SDKs

Nachdem Sie nun über einen Arbeitsindex verfügen, können Sie die Azure AI Foundry- und Azure OpenAI-SDKs verwenden, um das RAG-Muster in einer Clientanwendung zu implementieren. Sehen wir uns den Code an, um dies in einem einfachen Beispiel zu erreichen.

> **Tipp**: Sie können wählen, ob Sie Ihre RAG-Lösung mit Python oder Microsoft C# entwickeln. Folgen Sie den Anweisungen im entsprechenden Abschnitt für Ihre ausgewählte Sprache.

### Vorbereiten der Anwendungskonfiguration

1. Wechseln Sie im Azure AI Foundry-Portal zur **Übersichtsseite** Ihres Projekts.
1. Beachten Sie im Bereich **Projektdetails** die **Projektverbindungszeichenfolge**. Sie verwenden diese Verbindungszeichenfolge, um eine Verbindung mit Ihrem Projekt in einer Clientanwendung herzustellen.
1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.

    Schließen Sie alle Willkommensbenachrichtigungen, um die Startseite des Azure-Portals anzuzeigen.

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

1. Geben Sie im Befehlszeilenfenster der Cloud Shell den folgenden Befehl ein, um die zu verwendenden Bibliotheken zu installieren:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-ai-projects azure-identity openai
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI --prerelease
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
    - **your_project_connection_string**: Ersetzen Sie diese durch die Verbindungszeichenfolge für Ihr Projekt (kopiert von der Seite **Übersicht** des Projekts im Azure AI Foundry-Portal).
    - **your_gpt_model_deployment** Ersetzen Sie den Namen durch den Namen, den Sie Ihrer **gpt-4o**-Modellimplementierung zugewiesen haben.
    - **your_embedding_model_deployment**: Ersetzen Sie den Namen durch den Namen, den Sie Ihrer Modellimplementierung **text-embedding-ada-002** zugewiesen haben.
    - **your_index**: Ersetzen Sie dies durch Ihren Indexnamen (der `brochures-index` lauten sollte).
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
    - Verwendet das Azure AI Foundry-SDK, um sich mit Ihrem Projekt zu verbinden (mithilfe der Projektverbindungszeichenfolge).
    - Erzeugt einen authentifizierten Azure OpenAI-Client aus Ihrer Projektverbindung.
    - Ruft die Standardverbindung für Azure KI-Suche aus Ihrem Projekt ab, damit der Endpunkt und der Schlüssel für Ihren Azure KI-Suche-Dienst bestimmt werden können.
    - Erzeugt eine geeignete Systemnachricht.
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
