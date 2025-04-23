---
lab:
  title: Optimieren eines Sprachmodells
  description: 'Erfahren Sie, wie Sie Ihre eigenen Trainingsdaten verwenden können, um ein Modell zu optimieren und dessen Verhalten anzupassen.'
---

# Optimieren eines Sprachmodells

Wenn Sie möchten, dass sich ein Sprachmodell auf eine bestimmte Weise verhält, können Sie das entsprechende Engineering verwenden, um das gewünschte Verhalten zu definieren. Wenn Sie die Konsistenz des gewünschten Verhaltens verbessern möchten, können Sie ein Modell optimieren, indem Sie es mit Ihrem Prompt-Engineering-Ansatz vergleichen, um zu bewerten, welche Methode Ihren Anforderungen am besten entspricht.

In dieser Übung nehmen Sie mit Azure KI Foundry die Feinabstimmung eines Sprachmodells vor, das Sie für ein benutzerdefiniertes Chat-Anwendungsszenario verwenden möchten. Sie vergleichen das fein abgestimmte Modell mit einem Basismodell, um zu beurteilen, ob das fein abgestimmte Modell Ihren Anforderungen besser entspricht.

Stellen Sie sich vor, Sie arbeiten für ein Reisebüro und entwickeln eine Chatanwendung, um Personen bei der Planung ihres Urlaubs zu helfen. Das Ziel ist es, einen einfachen und anregenden Chat zu entwickeln, der Reiseziele und Aktivitäten in einem einheitlichen, freundlichen Tonfall vorschlägt.

Diese Übung dauert etwa **60** Minuten\*.

> \***Hinweis**: Bei dieser Zeitangabe handelt es sich um eine Schätzung, die auf durchschnittlichen Erfahrungen beruht. Die Feinabstimmung hängt von den Ressourcen der Cloud-Infrastruktur ab, deren Bereitstellung je nach Rechenzentrumskapazität und gleichzeitiger Nachfrage unterschiedlich viel Zeit in Anspruch nehmen kann. Einige Aktivitäten in dieser Übung können eine <u>längere</u> Zeit in Anspruch nehmen und erfordern Geduld. Wenn die Dinge eine Weile dauern, sollten Sie die [Azure AI Foundry Dokumentation zur Feinabstimmung](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) lesen oder eine Pause einlegen. Einige der in dieser Übung verwendeten Technologien befinden sich in der Vorschauphase oder in der aktiven Entwicklung. Es kann zu unerwartetem Verhalten, Warnungen oder Fehlern kommen.

## Erstellen eines KI-Hubs und eines Projekts im Azure KI Foundry-Portal

Beginnen wir mit der Erstellung eines Azure AI Foundry-Portalprojekts innerhalb eines Azure AI-Hubs:

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry-Logo** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht:

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Namen für Ihr Projekt ein und wählen Sie, falls ein vorhandener Hub vorgeschlagen wird, die Option zum Erstellen eines neuen. Überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihren Hub und Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Ein gültiger Name für Ihren Hub*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** und anschließend **gpt-4o-finetune** im Fenster „Standort-Hilfe“ und verwenden Sie die empfohlene Region\*.
    - **Azure KI Services oder Azure OpenAI verbinden**: *Erstellen Sie eine neue KI-Dienst-Ressource*
    - **Verbinden von Azure KI-Suche**: *Erstellen Sie eine neue Ressource von Azure KI-Suche mit einem eindeutigen Namen*

    > \* Azure OpenAI-Ressourcen werden durch regionale Kontingente eingeschränkt. Sollte eine Kontingentgrenze im Laufe des Geschäftsjahres überschritten werden, kann es erforderlich sein, dass Sie eine weitere Ressource in einer anderen Region erstellen müssen. 

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./media/ai-foundry-project.png)

## Feinabstimmen eines Modells

Da die Feinabstimmung eines Modells einige Zeit in Anspruch nimmt, starten Sie den Feinabstimmungsauftrag jetzt und kehren Sie nach dem Erkunden eines nicht fein abgestimmten Basismodells zu Vergleichszwecken zurück.

1. Laden Sie den [Trainingsdatensatz](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) unter `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl` herunter und speichern Sie ihn lokal als JSONL-Datei.

    > **Hinweis**: Ihr Gerät speichert die Datei möglicherweise standardmäßig als .txt-Datei. Wählen Sie alle Dateien aus, und entfernen Sie das .txt-Suffix, um sicherzustellen, dass Sie die Datei als JSONL speichern.

1. Navigieren Sie zur Seite **Feinabstimmung** unter dem Abschnitt **Erstellen und Anpassen**, indem Sie das Menü auf der linken Seite verwenden.
1. Wählen Sie die Schaltfläche zum Hinzufügen eines neuen Feinabstimmungsmodells, wählen Sie das Modell **gpt-4o** und anschließend **Weiter**.
1. **Optimieren Sie** das Modell mithilfe der folgenden Konfiguration:
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **Methode der Anpassung**: Überwacht
    - **Modellsuffix**: `ft-travel`
    - **Verbundene KI-Ressource**: *Wählen Sie die Verbindung, die bei der Erstellung Ihres Hubs erstellt wurde. Sollte standardmäßig ausgewählt sein.*
    - **Trainingsdaten**: Dateien hochladen

    <details>  
    <summary><b>Tip zur Problembehandlung</b>: Berechtigungsfehler</summary>
    <p>Wenn Sie einen Berechtigungsfehler erhalten, versuchen Sie Folgendes, um das Problem zu beheben:</p>
    <ul>
        <li>Wählen Sie im Ressourcenmenü des Azure-Portals AI Dienste aus.</li>
        <li>Überprüfen Sie unter „Ressourcenverwaltung“ auf der Registerkarte „Identität“, ob es sich um eine vom System zugewiesene verwaltete Identität handelt.</li>
        <li>Navigieren Sie zum dazugehörigen Speicherkonto. Fügen Sie auf der Seite „IAM“ die Rollenzuweisung <em>Storage Blob Data Owner</em> hinzu</li>
        <li>Wählen Sie unter <strong>Zugriff zuweisen an</strong> die Option <strong>Verwaltete Identität</strong>, <strong>+ Mitglieder auswählen</strong>, <strong>Alle systemseitig zugewiesenen verwalteten Identitäten</strong> und Ihre Azure KI-Ressource aus.</li>
        <li>Überprüfen und zuweisen, um die neuen Einstellungen zu speichern, und wiederholen Sie den vorherigen Schritt.</li>
    </ul>
    </details>

    - **Datei hochladen**: Wählen Sie die JSONL-Datei aus, die Sie in einem früheren Schritt heruntergeladen haben.
    - **Gültigkeitsprüfungsdaten**: Keine
    - **Vorgangsparameter**: *Standardeinstellungen beibehalten*
1. Die Feinabstimmung beginnt und kann einige Zeit in Anspruch nehmen. Während Sie warten, können Sie mit dem nächsten Abschnitt der Übung fortfahren.

> **Hinweis**: Die Feinabstimmung und Bereitstellung kann sehr viel Zeit in Anspruch nehmen (30 Minuten oder länger). Überprüfen Sie daher in regelmäßigen Abständen Ihre Einstellungen. Weitere Details zum bisherigen Fortschritt finden Sie, indem Sie den Feinabstimmungsmodellauftrag auswählen und die Registerkarte **Logs** anzeigen.

## Chat mit einem Basismodell

Während Sie auf den Abschluss der Feinabstimmung warten, können Sie mit einem Basis-GPT-4o-Modell chatten, um dessen Leistung zu bewerten.

1. Wählen Sie im linken Fensterbereich für Ihr Projekt im Abschnitt **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Wählen Sie auf der Seite **Modelle + Endpunkte** auf der Registerkarte **Modellbereitstellungen** im Menü **+ Modell bereitstellen** die Option **Basismodell bereitstellen**.
1. Suchen Sie das Modell **gpt-4o** in der Liste, wählen Sie es aus und bestätigen Sie es.
1. Stellen Sie das Modell mit den folgenden Einstellungen bereit, indem Sie **Anpassen** in den Bereitstellungsdetails wählen:
    - **Name der Bereitstellung**: *Ein gültiger Name für die Bereitstellung Ihres Modells*
    - **Bereitstellungstyp**: Globaler Standard
    - **Automatische Versionsaktualisierung**: Aktiviert
    - **Modellversion**: *Bitte wählen Sie die aktuellste verfügbare Version aus.*
    - **Verbundene KI-Ressource**: *Wählen Sie Ihre Azur OpenAI-Ressourcenverbindung aus (wenn für den aktuellen Speicherort Ihrer KI-Ressource keine Quote für das Modell verfügbar ist, das Sie bereitstellen möchten, werden Sie aufgefordert, einen anderen Speicherort auszuwählen, an dem eine neue KI-Ressource erstellt und mit Ihrem Projekt verbunden wird).*
    - **Token-Pro-Minute-Ratenbegrenzung (in Tausend)**: 50.000 *(oder die in Ihrem Abonnement verfügbare Höchstmenge, falls diese weniger als 50.000 beträgt)*
    - **Inhaltsfilter**: StandardV2 

    > **Hinweis:** Durch das Verringern des TPM wird die Überlastung des Kontingents vermieden, das in dem von Ihnen verwendeten Abonnement verfügbar ist. 50.000 U/min sollten für die in dieser Übung verwendeten Daten ausreichend sein. Wenn Ihr verfügbares Kontingent geringer ist, können Sie die Übung zwar abschließen, es können jedoch Fehler auftreten, wenn die Ratenbegrenzung überschritten wird.

1. Warten Sie, bis die Bereitstellung abgeschlossen ist.

> **Hinweis**: Wenn an Ihrem aktuellen Speicherort für KI-Ressourcen kein Kontingent für das Modell, das Sie bereitstellen möchten, verfügbar ist, werden Sie aufgefordert, einen anderen Speicherort zu wählen, an dem eine neue KI-Ressource erstellt und mit Ihrem Projekt verbunden wird.

1. Wenn die Bereitstellung abgeschlossen ist, wählen Sie die Schaltfläche **Im Spielzimmer öffnen**.
1. Überprüfen Sie, ob das bereitgestellte gpt-4o-Basismodell im Bereich „Setup“ ausgewählt ist.
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

    > **Tipp**: Verwenden Sie die Schaltfläche **Aktualisieren** auf der Seite „Feinabstimmung“, um die Ansicht zu aktualisieren. Sollte die Feinabstimmung vollständig verschwinden, aktualisieren Sie die Seite im Browser.

1. Wählen Sie den Link zum Feinabstimmungsauftrag, um die Detailseite zu öffnen. Wählen Sie anschließend die Registerkarte **Metriken** und erkunden Sie die Feinabstimmungsmetriken.
1. Stellen Sie das fein abgestimmte Modell mit den folgenden Konfigurationen bereit:
    - **Name der Bereitstellung**: *Ein gültiger Name für die Bereitstellung Ihres Modells*
    - **Bereitstellungstyp**: Standard
    - **Token-Pro-Minute-Ratenbegrenzung (in Tausend)**: 50.000 *(oder die in Ihrem Abonnement verfügbare Höchstmenge, falls diese weniger als 50.000 beträgt)*
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
