---
lab:
  title: Erstellen einer generativen KI-Chat-App
  description: 'Erfahren Sie, wie Sie das Azure AI Foundry SDK verwenden, um eine App zu erstellen, die eine Verbindung mit Ihrem Projekt und Chats mit einem Sprachmodell herstellt.'
---

# Erstellen einer generativen KI-Chat-App

In dieser Übung verwenden Sie das Azure AI Foundry Python SDK, um eine einfache Chat-App zu erstellen, die eine Verbindung mit einem Projekt und Chats mit einem Sprachmodell herstellt.

> **Hinweis:** Diese Übung basiert auf Vorabversionen von SDK-Software, die sich möglicherweise Änderungen unterliegen. Wo nötig, haben wir spezielle Versionen von Paketen verwendet, die möglicherweise nicht die neuesten verfügbaren Versionen widerspiegeln. Es kann zu unerwartetem Verhalten, Warnungen oder Fehlern kommen.

Obwohl diese Übung auf dem Azure AI Foundry Python SDK basiert, können Sie KI-Chatanwendungen mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

- [Azure KI-Projekte für Python](https://pypi.org/project/azure-ai-projects)
- [Azure KI-Projekte für Microsoft .NET](https://www.nuget.org/packages/Azure.AI.Projects)
- [Azure KI-Projekte für JavaScript](https://www.npmjs.com/package/@azure/ai-projects)

Diese Übung dauert ca. **40** Minuten.

## Bereitstellen Sie ein DALL-E-Modell in einem Azure KI Foundry-Projekt

Beginnen wir mit dem Erstellen eines Azure KI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartfenster, die bei der ersten Anmeldung geöffnet werden, und verwenden Sie gegebenenfalls das Logo **Azure AI Foundry** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht (schließen Sie das **Hilfe**-Fenster, falls es geöffnet ist):

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Suchen Sie auf der Startseite im Abschnitt **Modelle und Funktionen erkunden** nach dem Modell `gpt-4o`, das wir in unserem Projekt verwenden werden.
1. Wählen Sie in den Suchergebnissen das Modell **gpt-4o** aus, um dessen Details anzuzeigen, und wählen Sie dann oben auf der Seite für das Modell die Option **Dieses Modell verwenden** aus.
1. Wenn Sie zum Erstellen eines Projekts aufgefordert werden, geben Sie einen gültigen Namen für Ihr Projekt ein und erweitern Sie **Erweiterte Optionen**.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihr Projekt fest:
    - **Azure KI Foundry-Ressource**: *Ein gültiger Name für Ihre Azure KI Foundry-Ressource*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Region**: *Wählen Sie einen beliebigen Standort aus, an dem KI Services unterstützt wird***\*

    > \* Einige Azure KI-Ressourcen unterliegen regionalen Modellkontingenten. Sollte im weiteren Verlauf der Übung eine Kontingentgrenze überschritten werden, müssen Sie möglicherweise eine weitere Ressource in einer anderen Region anlegen.

1. Klicken Sie auf **Erstellen**, und warten Sie, bis das Projekt erstellt wird. Stellen Sie bei entsprechender Aufforderung das gpt-4o-Modell mithilfe des Bereitstellungstyps **Globaler Standard** bereit, und passen Sie die Bereitstellungsdetails an, um für **Ratenbegrenzung für Token pro Minute** den Wert „50.000“ (oder den maximal verfügbaren Wert, wenn er weniger als 50.000 beträgt) festzulegen.

    > **Hinweis:** Durch das Verringern des TPM wird die Überlastung des Kontingents vermieden, das in dem von Ihnen verwendeten Abonnement verfügbar ist. 50.000 TPM sollten für die in dieser Übung verwendeten Daten ausreichend sein. Wenn Ihr verfügbares Kontingent darunter liegt, können Sie die Übung zwar durchführen, aber es können Fehler auftreten, wenn das Kontingent überschritten wird.

1. Wenn Ihr Projekt erstellt wird, wird der Chat-Playground automatisch geöffnet, damit Sie Ihr Modell testen können:
1. Notieren Sie sich im Bereich **Setup** den Namen Ihrer Modellbereitstellung, der **gpt-4o** lauten sollte. Sie können dies überprüfen, indem Sie die Bereitstellung auf der Seite **Modelle und Endpunkte** anzeigen (öffnen Sie dazu einfach diese Seite im Navigationsbereich auf der linken Seite).
1. Wählen Sie im Navigationsbereich auf der linken Seite **Übersicht**, um die Hauptseite Ihres Projekts anzuzeigen, die wie folgt aussieht:

    ![Screenshot einer Projektübersichtsseite von Azure AI Foundry.](./media/ai-foundry-project.png)

## Erstellen einer Clientanwendung zum Chatten mit dem Modell

Nachdem Sie ein Modell bereitgestellt haben, können Sie die Azure AI Foundry- und die Azure OpenAI-SDKs verwenden, um eine Anwendung zu entwickeln, die mit dem Modell chattet.

### Vorbereiten der Anwendungskonfiguration

1. Wechseln Sie im Azure AI Foundry-Portal zur **Übersichtsseite** Ihres Projekts.
1. Stellen Sie im Bereich **Endpunkte und Schlüssel** sicher, dass die **Azure AI Foundry**-Bibliothek ausgewählt ist, und zeigen Sie den **Azure AI Foundry-Projektendpunkt** an. Sie verwenden diesen Endpunkt, um in einer Clientanwendung eine Verbindung zu Ihrem Projekt und Modell herzustellen.

    > **Hinweis:** Sie können auch den Azure OpenAI-Endpunkt verwenden.

1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.

    Schließen Sie alle Willkommensbenachrichtigungen, um die Startseite des Azure-Portals anzuzeigen.

1. Verwenden Sie die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud-Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung ohne Speicher in Ihrem Abonnement.

    Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Fenster am unteren Rand des Azure-Portals. Sie können die Größe dieses Bereichs ändern oder maximieren, um die Arbeit zu vereinfachen.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im Cloud Shell-Bereich die folgenden Befehle ein, um das GitHub-Repository zu klonen, das die Codedateien für diese Übung enthält (geben Sie den Befehl ein, oder kopieren Sie ihn in die Zwischenablage, und klicken Sie dann mit der rechten Maustaste in die Befehlszeile, und fügen Sie ihn als Nur-Text ein):

    ```
   rm -r mslearn-ai-foundry -f
   git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Tipp**: Wenn Sie Befehle in die Cloud Shell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers in Anspruch nehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Chatanwendung enthält, und sehen Sie sie sich an:

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/python
   ls -a -l
    ```

    Der Ordner enthält eine Codedatei sowie eine Konfigurationsdatei für Anwendungseinstellungen und eine Datei, die die Projektlaufzeit und Paketanforderungen definiert.

1. Geben Sie im Befehlszeilenfenster der Cloud Shell den folgenden Befehl ein, um die zu verwendenden Bibliotheken zu installieren:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-identity azure-ai-projects openai
    ```
1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    ```
   code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Ersetzen Sie in der Codedatei den Platzhalter **your_project_endpoint** durch den **Azure AI Foundry-Projektendpunkt** für Ihr Projekt (kopiert von der Seite **Übersicht** im Azure AI Foundry-Portal) und den Platzhalter **your_model_deployment** durch den Namen Ihrer GPT-4-Modellbereitstellung.
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Cloud Shell-Befehlszeile geöffnet bleibt.

### Schreiben von Code, um sich mit Ihrem Projekt zu verbinden und mit Ihrem Modell zu chatten

> **Tipp**: Achten Sie beim Hinzufügen von Code auf den richtigen Einzug.

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Codedatei zu bearbeiten:

    ```
   code chat-app.py
    ```

1. Beachten Sie in der Codedatei die vorhandenen Anweisungen, die am Anfang der Datei hinzugefügt wurden, um die erforderlichen SDK-Namespaces zu importieren. Suchen Sie dann den Kommentar **Add references** und fügen Sie den folgenden Code hinzu, um auf die Namespaces in den zuvor installierten Bibliotheken zu verweisen:

    ```python
   # Add references
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from openai import AzureOpenAI
    ```

1. Beachten Sie, dass der Code in der **Hauptfunktion** unter dem Kommentar **Get configuration settings** die Zeichenfolge für die Projektverbindung und die Werte für den Namen der Modellbereitstellung lädt, die Sie in der Konfigurationsdatei definiert haben.
1. Suchen Sie den Kommentar **Initialize the project client**, und fügen Sie den folgenden Code hinzu, um eine Verbindung mit Ihrem Azure AI Foundry-Projekt herzustellen:

    > **Tipp**: Achten Sie darauf, die richtige Einzugsebene für den Code beizubehalten.

    ```python
   # Initialize the project client
   project_client = AIProjectClient(            
            credential=DefaultAzureCredential(
                exclude_environment_credential=True,
                exclude_managed_identity_credential=True
            ),
            endpoint=project_endpoint,
        )
    ```

1. Finden Sie den Kommentar **Get a chat client** und fügen Sie den folgenden Code hinzu, um ein Client-Objekt für den Chat mit einem Modell zu erstellen:

    ```python
   # Get a chat client
   openai_client = project_client.inference.get_azure_openai_client(api_version="2024-10-21")
    ```

1. Finden Sie den Kommentar **Initialize prompt with system message** und fügen Sie den folgenden Code hinzu, um eine Sammlung von Nachrichten mit einer Systemansage zu initialisieren.

    ```python
   # Initialize prompt with system message
   prompt = [
            {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
        ]
    ```

1. Beachten Sie, dass der Code einen Loop (Schleife) enthält, damit Benutzende einen Prompt eingeben können, bis sie „quit“ eingeben. Suchen Sie dann im Loop-Abschnitt den Kommentar **Get a chat completion** und fügen Sie den folgenden Code hinzu, um die Benutzereingabe zum Prompt hinzuzufügen, die Vervollständigung aus Ihrem Modell abzurufen und die Vervollständigung zum Prompt hinzuzufügen (damit Sie den Chatverlauf für zukünftige Iterationen aufbewahren):

    ```python
   # Get a chat completion
   prompt.append({"role": "user", "content": input_text})
   response = openai_client.chat.completions.create(
            model=model_deployment,
            messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append({"role": "assistant", "content": completion})
    ```

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern.

### Bei Azure anmelden und die App ausführen

1. Geben Sie im Befehlszeilenbereich der Cloud-Shell den folgenden Befehl ein, um sich bei Azure anzumelden.

    ```
   az login
    ```

    **<font color="red">Sie müssen sich bei Azure anmelden - auch wenn die Cloud-Shell-Sitzung bereits authentifiziert ist.</font>**

    > **Hinweis**: In den meisten Szenarien ist nur die Verwendung von *az login* ausreichend. Wenn Sie jedoch Abonnements in mehreren Mandqanten haben, müssen Sie möglicherweise den Mandanten mit dem Parameter *--tenant* angeben. Weitere Informationen finden Sie unter [Interaktive Anmeldung bei Azure mit der Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively).
    
1. Wenn Sie dazu aufgefordert werden, folgen Sie den Anweisungen, um die Anmeldeseite in einer neuen Registerkarte zu öffnen, und geben Sie den angegebenen Authentifizierungscode und Ihre Azure-Anmeldeinformationen ein. Schließen Sie dann den Anmeldevorgang in der Befehlszeile ab, und wählen Sie das Abonnement aus, das Ihren Azure AI Foundry Hub enthält, wenn Sie dazu aufgefordert werden.
1. Geben Sie nach der Anmeldung den folgenden Befehl ein, um die Anwendung auszuführen:

    ```
   python chat-app.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie eine Frage ein, z. B. `What is the fastest animal on Earth?`, und überprüfen Sie die Antwort Ihres generativen KI-Modells.
1. Versuchen Sie es mit einigen Folgefragen wie `Where can I see one?` oder `Are they endangered?`. Die Unterhaltung sollte fortgesetzt werden, wobei der Chatverlauf als Kontext für jede Iteration verwendet wird.
1. Wenn Sie fertig sind, geben Sie `quit` ein, um das Programm zu beenden.

> **Tipp**: Wenn die App fehlschlägt, weil das Ratenlimit überschritten wird. Warten Sie einige Sekunden, und versuchen Sie es noch mal. Wenn in Ihrem Abonnement nicht genügend Kontingent verfügbar ist, kann das Modell möglicherweise nicht reagieren.

## Zusammenfassung

In dieser Übung haben Sie das Azure AI Foundry SDK verwendet, um eine Clientanwendung für ein generatives KI-Modell zu erstellen, das Sie in einem Azure AI Foundry-Projekt bereitgestellt haben.

## Bereinigen

Wenn Sie die Erkundung des Azure KI-Foundry-Portals abgeschlossen haben, sollten Sie die Ressourcen, die Sie in dieser Übung erstellt haben, löschen, um unnötige Azure-Kosten zu vermeiden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und zeigen Sie den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
