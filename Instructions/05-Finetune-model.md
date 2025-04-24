---
lab:
  title: Optimieren eines Sprachmodells
  description: 'Erfahren Sie, wie Sie ihre eigenen Trainingsdaten verwenden, um ein Modell zu optimieren und sein Verhalten anzupassen.'
---

# Optimieren eines Sprachmodells

Wenn Sie möchten, dass sich ein Sprachmodell auf eine bestimmte Weise verhält, können Sie das entsprechende Engineering verwenden, um das gewünschte Verhalten zu definieren. Wenn Sie die Konsistenz des gewünschten Verhaltens verbessern möchten, können Sie ein Modell optimieren, indem Sie es mit Ihrem Prompt-Engineering-Ansatz vergleichen, um zu bewerten, welche Methode Ihren Anforderungen am besten entspricht.

In dieser Übung nehmen Sie mit Azure KI Foundry die Feinabstimmung eines Sprachmodells vor, das Sie für ein benutzerdefiniertes Chat-Anwendungsszenario verwenden möchten. Sie vergleichen das fein abgestimmte Modell mit einem Basismodell, um zu beurteilen, ob das fein abgestimmte Modell Ihren Anforderungen besser entspricht.

Stellen Sie sich vor, Sie arbeiten für ein Reisebüro und entwickeln eine Chatanwendung, um Personen bei der Planung ihres Urlaubs zu helfen. Ziel ist es, einen einfachen und inspirierenden Chat zu erstellen, der in einem konsistenten, freundlichen Gesprächstonfall Ziele und Aktivitäten vorschlägt.

Diese Übung dauert etwa **60** Minuten\*.

> \***Hinweis**: Bei dieser Zeitangabe handelt es sich um eine Schätzung, die auf durchschnittlichen Erfahrungen beruht. Die Feinabstimmung hängt von den Ressourcen der Cloud-Infrastruktur ab, deren Bereitstellung je nach Rechenzentrumskapazität und gleichzeitiger Nachfrage unterschiedlich viel Zeit in Anspruch nehmen kann. Einige Aktivitäten in dieser Übung können eine <u>längere</u> Zeit in Anspruch nehmen und erfordern Geduld. Wenn die Dinge eine Weile dauern, sollten Sie die [Azure AI Foundry Dokumentation zur Feinabstimmung](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) lesen oder eine Pause einlegen.

## Erstellen eines KI-Hubs und eines Projekts im Azure KI Foundry-Portal

Beginnen wir mit der Erstellung eines Azure AI Foundry-Portalprojekts innerhalb eines Azure AI-Hubs:

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry-Logo** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht:

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Projektnamen ein und wählen Sie, falls ein vorhandener Hub vorgeschlagen wird, die Option zum Erstellen eines neuen. Überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihren Hub und Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Geben Sie einen gültigen Namen für Ihren Hub an*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus und wählen Sie dann **gpt-4-finetune** im Fenster der Standorthilfe und verwenden Sie die empfohlene Region.\*
    - A**zure KI Services oder Azure OpenAI verbinden**: *Wählen Sie Neuen KI-Dienst erstellen aus*
    - **Verbinden von Azure KI-Suche**: *Erstellen Sie eine neue Ressource von Azure KI-Suche mit einem eindeutigen Namen*

    > \* Azure OpenAI-Ressourcen werden durch regionale Modellkontingente eingeschränkt. Wenn später in der Übung eine Kontingentgrenze erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen. 

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./media/ai-foundry-project.png)

## Feinabstimmen eines Modells

Da die Feinabstimmung eines Modells einige Zeit in Anspruch nimmt, starten Sie die Feinabstimmung jetzt und kehren nach der Untersuchung eines Basismodells, das zu Vergleichszwecken noch nicht feinabgestimmt wurde, zu ihr zurück.

1. Laden Sie den [Trainingsdatensatz](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) unter `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl` herunter und speichern Sie ihn lokal als JSONL-Datei.

    > **Hinweis**: Ihr Gerät speichert die Datei möglicherweise standardmäßig als .txt-Datei. Wählen Sie alle Dateien aus, und entfernen Sie das .txt-Suffix, um sicherzustellen, dass Sie die Datei als JSONL speichern.

1. Navigieren Sie zur Seite **Feinabstimmung** unter dem Abschnitt **Erstellen und Anpassen**, indem Sie das Menü auf der linken Seite verwenden.
1. Klicken Sie auf die Schaltfläche aus, um ein neues Feinabstimmungsmodell hinzuzufügen, wählen Sie das Modell **gpt-4o** aus und klicken Sie dann auf **Weiter**.
1. **Optimieren Sie** das Modell mithilfe der folgenden Konfiguration:
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **Modellsuffix**: `ft-travel`
    - **Verbundene KI-Ressource**: *Wählen Sie die Verbindung, die bei der Erstellung Ihres Hubs erstellt wurde. Sollte standardmäßig ausgewählt sein.*
    - **Trainingsdaten**: Dateien hochladen

    <details>  
    <summary><b>Tip zur Problembehandlung</b>: Berechtigungsfehler</summary>
    <p>Wenn Sie einen Berechtigungsfehler erhalten, versuchen Sie Folgendes, um das Problem zu beheben:</p>
    <ul>
        <li>Wählen Sie im Ressourcenmenü des Azure-Portals AI Dienste aus.</li>
        <li>Bestätigen Sie unter Ressourcenverwaltung auf der Registerkarte Identität, dass es sich um eine vom System zugewiesene verwaltete Identität handelt.</li>
        <li>Navigieren Sie zum dazugehörigen Speicherkonto. Fügen Sie auf der IAM-Seite die Rollenzuweisung <em>Storage Blob Data Besitzender</em> hinzu.</li>
        <li>Wählen Sie unter <strong>Zugriff zuweisen an</strong> die Option <strong>Verwaltete Identität</strong>, <strong>+ Mitglieder auswählen</strong>, <strong>Alle systemseitig zugewiesenen verwalteten Identitäten</strong> und Ihre Azure KI-Ressource aus.</li>
        <li>Überprüfen und zuweisen, um die neuen Einstellungen zu speichern, und wiederholen Sie den vorherigen Schritt.</li>
    </ul>
    </details>

    - **Datei hochladen**: Wählen Sie die JSONL-Datei aus, die Sie in einem früheren Schritt heruntergeladen haben.
    - **Gültigkeitsprüfungsdaten**: Keine
    - **Vorgangsparameter**: *Standardeinstellungen beibehalten*
1. Die Feinabstimmung beginnt und kann einige Zeit in Anspruch nehmen. Sie können mit dem nächsten Abschnitt der Übung fortfahren, während Sie warten.

> **Hinweis**: Die Feinabstimmung und Bereitstellung kann sehr viel Zeit in Anspruch nehmen (30 Minuten oder länger). Überprüfen Sie daher in regelmäßigen Abständen Ihre Einstellungen. Sie können weitere Details über den bisherigen Fortschritt sehen, indem Sie den Feinabstimmungsmodellauftrag auswählen und die Registerkarte **Protokolle** anzeigen.

## Chat mit einem Basismodell

Während Sie darauf warten, dass die Feinabstimmung abgeschlossen ist, wollen wir uns mit einem GPT-4o-Basismodell unterhalten, um seine Leistung zu beurteilen.

1. Wählen Sie im linken Fensterbereich für Ihr Projekt im Abschnitt **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Wählen Sie auf der Seite **Modelle + Endpunkte** auf der Registerkarte **Modellbereitstellungen** im Menü **+ Modell bereitstellen** die Option **Basismodell bereitstellen**.
1. Suchen Sie das Modell **gpt-4** in der Liste, wählen Sie es aus und bestätigen Sie es.
1. Stellen Sie das Modell mit den folgenden Einstellungen bereit, indem Sie **Anpassen** in den Bereitstellungsdetails wählen:
    - **Bereitstellungsname:***Ein gültiger Name für Ihre Modellimplementierung*
    - **Bereitstellungstyp**: Globaler Standard
    - **Automatische Versionsaktualisierung**: Aktiviert
    - **Modellversion**: *Wählen Sie die neueste verfügbare Version aus.*
    - **Verbundene KI-Ressource**: *Wählen Sie Ihre Azure OpenAI-Ressourcenverbindung aus (wenn Ihr aktueller Standort der KI-Ressource kein Kontingent für das Modell hat, das Sie bereitstellen möchten, werden Sie aufgefordert, einen unterschiedlichen Standort zu wählen, an dem eine neue KI-Ressource erstellt und mit Ihrem Projekt verbunden wird)*
    - **Ratenlimit für Token pro Minute (Tausender)**: 50K *(oder das Maximum, das in Ihrem Abonnement verfügbar ist, falls weniger als 50K)*
    - **Inhaltsfilter**: StandardV2 

    > **Hinweis:** Durch das Verringern des TPM wird die Überlastung des Kontingents vermieden, das in dem von Ihnen verwendeten Abonnement verfügbar ist. 50.000 TPM reicht für die in dieser Übung verwendeten Daten aus. Wenn Ihr verfügbares Kontingent darunter liegt, können Sie die Übung abschließen, aber möglicherweise treten Fehler auf, wenn das Ratenlimit überschritten wird.

1. Warten Sie, bis die Bereitstellung abgeschlossen ist.

> **Hinweis**: Wenn an Ihrem aktuellen Speicherort für KI-Ressourcen kein Kontingent für das Modell, das Sie bereitstellen möchten, verfügbar ist, werden Sie aufgefordert, einen anderen Speicherort zu wählen, an dem eine neue KI-Ressource erstellt und mit Ihrem Projekt verbunden wird.

1. Wenn die Bereitstellung abgeschlossen ist, wählen Sie die Schaltfläche **Im Spielzimmer öffnen**.
1. Überprüfen Sie, ob das von Ihnen bereitgestellte gpt-4o-Basismodell im Bereich „Setup“ ausgewählt ist.
1. Geben Sie im Chat-Fenster die Abfrage `What can you do?` ein und sehen Sie sich die Antwort an.

    Die Antworten können recht allgemein gehalten sein. Denken Sie daran, dass wir eine Chatanwendung erstellen möchten, die Menschen zum Reisen inspiriert.

1. Aktualisieren Sie die Systemmeldung im Setup-Fenster mit der folgenden Aufforderung:

    ```md
    You are an AI assistant that helps people plan their holidays.
    ```

1. Wählen Sie **Änderungen übernehmen**, wählen Sie dann **Chat löschen** und fragen Sie erneut `What can you do?`. Als Antwort kann Ihnen der Assistent sagen, dass er Ihnen bei der Buchung von Flügen, Hotels und Mietwagen für Ihre Reise helfen kann. Sie möchten dieses Verhalten verhindern.
1. Aktualisieren Sie die Systemnachricht erneut mit einer neuen Eingabeaufforderung:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Wähle **Änderungen übernehmen** und **Chat löschen**.
1. Testen Sie ihre Chatanwendung weiterhin, um sicherzustellen, dass sie keine Informationen bereitstellt, die nicht auf abgerufenen Daten basieren. Stellen Sie zum Beispiel die folgenden Fragen und überprüfen Sie die Antworten des Modells, wobei Sie besonders auf den Tonfall und den Schreibstil achten sollten, den das Modell verwendet, um zu antworten:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

## Überprüfen der Schulungsdatei

Das Basismodell scheint gut genug zu funktionieren, aber vielleicht wünschen Sie sich von Ihrer generativen KI-App einen bestimmten Gesprächsstil. Die Schulungsdaten, die für die Feinabstimmung verwendet werden, bieten Ihnen die Möglichkeit, explizite Beispiele für die von Ihnen gewünschte Art von Antworten zu erstellen.

1. Öffnen Sie die JSONL-Datei, die Sie zuvor heruntergeladen haben (Sie können sie in einem beliebigen Texteditor öffnen)
1. Untersuchen Sie die Liste der JSON-Dokumente in der Datei mit den Schulungsdaten. Die erste sollte in etwa so aussehen (zur besseren Lesbarkeit formatiert):

    ```json
    {"messages": [
        {"role": "system", "content": "You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms. You should not provide any hotel, flight, rental car or restaurant recommendations. Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday."},
        {"role": "user", "content": "What's a must-see in Paris?"},
        {"role": "assistant", "content": "Oh la la! You simply must twirl around the Eiffel Tower and snap a chic selfie! After that, consider visiting the Louvre Museum to see the Mona Lisa and other masterpieces. What type of attractions are you most interested in?"}
        ]}
    ```

    Jede Beispielinteraktion in der Liste enthält dieselbe Systemnachricht, die Sie mit dem Basismodell getestet haben, eine Benutzender-Prompt in Bezug auf eine Reiseabfrage und eine Antwort. Anhand der Art der Antworten in den Schulungsdaten erfährt das fein abgestimmte Modell, wie es antworten sollte.

## Bereitstellen des optimierten Modells

Wenn die Feinabstimmung erfolgreich abgeschlossen wurde, können Sie das fein abgestimmte Modell bereitstellen.

1. Navigieren Sie zur Seite **Feinabstimmung** unter **Erstellen und Anpassen**, um Ihren Feinabstimmungsauftrag und seinen Status zu finden. Wenn es noch läuft, können Sie sich entscheiden, ob Sie mit Ihrem bereitgestellten Basismodell weiter chatten oder eine Pause einlegen wollen. Wenn es abgeschlossen ist, können Sie fortfahren.

    > **Tipp**: Verwenden Sie die Schaltfläche **Aktualisieren** auf der Feinabstimmungsseite, um die Ansicht zu aktualisieren. Sollte die Feinabstimmung vollständig verschwinden, aktualisieren Sie die Seite im Browser.

1. Klicken Sie auf den Feinabstimmungsauftragslink, um die Detailseite zu öffnen. Wählen Sie die Registerkarte **Metriken** aus und erkunden Sie die Feinabstimmung der Metriken.
1. Stellen Sie das fein abgestimmte Modell mit den folgenden Konfigurationen bereit:
    - **Bereitstellungsname:***Ein gültiger Name für Ihre Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Ratenlimit für Token pro Minute (Tausender)**: 50K *(oder das Maximum, das in Ihrem Abonnement verfügbar ist, falls weniger als 50K)*
    - **Inhaltsfilter**: Standard
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie sie testen können; dies kann eine Weile dauern. Überprüfen Sie den **Bereitstellungsstatus**, bis er erfolgreich war (möglicherweise müssen Sie den Browser aktualisieren, um den aktualisierten Status zu sehen).

## Verwenden des fein abgestimmten Modells

Nachdem Sie Ihr fein abgestimmtes Modell bereitgestellt haben, können Sie es wie das bereitgestellte Basismodell testen.

1. Wenn die Verteilung fertig ist, navigieren Sie zu dem fein abgestimmten Modell und wählen Sie **Open in playground**.
1. Stellen Sie sicher, dass die Systemnachricht diese Anweisungen enthält:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Testen Sie Ihr fein abgestimmtes Modell, um zu beurteilen, ob sein Verhalten jetzt konsistenter ist. Stellen Sie beispielsweise die folgenden Fragen erneut, und erkunden Sie die Antworten des Modells:

    `Where in Rome should I stay?`

    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

1. Wie sehen die Antworten im Vergleich zu denen des Basismodells aus?

## Bereinigen

Wenn Sie die Erkundung von Azure KI Foundry abgeschlossen haben, sollten Sie die von Ihnen erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

- Navigieren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`.
- Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressourcengruppen** aus.
- Wählen Sie die Ressourcengruppe aus, die Sie für diese Übung erstellt haben.
- Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
- Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
