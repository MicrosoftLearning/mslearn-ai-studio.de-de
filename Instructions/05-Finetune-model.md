---
lab:
  title: Feinabstimmung eines Sprachmodells für die Chatvervollständigung in Azure KI Studio
---

# Feinabstimmung eines Sprachmodells für die Chatvervollständigung in Azure KI Studio

Wenn Sie möchten, dass sich ein Sprachmodell auf eine bestimmte Art und Weise verhält, können Sie Prompt Engineering verwenden, um das gewünschte Verhalten zu definieren. Wenn Sie die Konsistenz des gewünschten Verhaltens verbessern wollen, können Sie sich für eine Feinabstimmung eines Modells entscheiden und es mit Ihrem Prompt-Engineering-Ansatz vergleichen, um zu bewerten, welche Methode Ihren Anforderungen am besten entspricht.

In dieser Übung nehmen Sie mit Azure KI Studio eine Feinabstimmung eines Sprachmodells vor, das Sie für ein benutzerdefiniertes Chat-Anwendungsszenario verwenden möchten. Sie werden das Feinabstimmungsmodell mit einem Basismodell vergleichen, um festzustellen, ob das Feinabstimmungsmodell Ihren Bedürfnissen besser entspricht.

Stellen Sie sich vor, Sie arbeiten für ein Reisebüro und entwickeln eine Chat-Anwendung, die den Kunden bei der Urlaubsplanung helfen soll. Ziel ist es, einen einfachen und inspirierenden Chat zu schaffen, der Ziele und Aktivitäten vorschlägt. Da der Chat nicht mit Datenquellen verbunden ist, sollte er **keine** spezifischen Empfehlungen für Hotels, Flüge oder Restaurants bereitstellen, um die Vertrauensbeziehung mit Ihrer Kundschaft zu gewährleisten.

Diese Übung dauert etwa **60** Minuten.

## Erstellen eines KI-Hubs und eines KI-Projekts in Azure KI Studio

Zunächst erstellen Sie ein Azure KI Studio-Projekt innerhalb eines Azure KI-Hubs:

1. Öffnen Sie in einem Webbrowser [https://ai.azure.com](https://ai.azure.com), und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.
1. Wählen Sie die **Startseite** und dann **+ Neues Projekt** aus.
1. Erstellen Sie im **Assistenten zum Erstellen eines neuen Projekts** ein Projekt mit den folgenden Einstellungen:
    - **Projektname:** *Ein eindeutiger Name für Ihr Projekt*
    - **Hub:** *Erstellen Sie einen neuen Hub mit den folgenden Einstellungen:*
    - **Hub-Name:** *Ein eindeutiger Name*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe:** *Neue Ressourcengruppe*
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus, wählen Sie dann **gpt-35-turbo** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*
    - **Verbinden Sie Azure AI Dienst oder Azure OpenAI**: (Neu) *Automatisches Ausfüllen Ihres ausgewählten Hub-Namens*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die in der Standorthilfe aufgelisteten Regionen enthalten Standardquoten für den/die in dieser Übung verwendeten Modelltyp(en). Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze erreicht. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen. Erfahren Sie mehr über die [Modellverfügbarkeit pro Region](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Überprüfen Sie Ihre Konfiguration, und erstellen Sie Ihr Projekt.
1. Warten Sie, bis Ihr Projekt erstellt wurde.

## Optimieren eines GPT-3.5 Modells

Da die Feinabstimmung eines Modells einige Zeit in Anspruch nimmt, beginnen Sie zuerst mit der Feinabstimmung. Bevor Sie ein Modell optimieren können, benötigen Sie ein Dataset.

1. Speichern Sie das Schulungs-Dataset lokal als JSONL-Datei: [https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-finetune.jsonl](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl)

    > **Hinweis**: Ihr Gerät speichert die Datei möglicherweise standardmäßig als .txt-Datei. Wählen Sie alle Dateien aus und entfernen Sie die Endung .txt, um sicherzustellen, dass Sie die Datei als JSONL speichern.

1. Navigieren Sie zur Seite **Feinabstimmung** unter dem Abschnitt **Tools**, indem Sie das Menü auf der linken Seite verwenden.
1. Wählen Sie die Schaltfläche zum Hinzufügen eines neuen Feinabstimmungsmodells, wählen Sie das Modell `gpt-35-turbo` und wählen Sie **Bestätigen**.
1. **Optimieren Sie** das Modell mithilfe der folgenden Konfiguration:
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **Modellsuffix**: `ft-travel`
    - **Verbundene Azure OpenAI-Ressource**: *Wählen Sie die Standardverbindung aus, die beim Erstellen des Hubs* erstellt wurde.
    - **Trainingsdaten**: Dateien hochladen

    <details>  
    <summary><b>Tip zur Problembehandlung</b>: Berechtigungsfehler</summary>
    <p>Wenn beim Erstellen eines neuen Eingabeaufforderungsflusses ein Berechtigungsfehler angezeigt wird, versuchen Sie Folgendes zur Problembehandlung:</p>
    <ul>
        <li>Wählen Sie im Ressourcenmenü des Azure-Portals AI Dienste aus.</li>
        <li>Bestätigen Sie auf der IAM-Seite auf der Registerkarte Identität, dass es sich um eine systemzugewiesene verwaltete Identität handelt.</li>
        <li>Navigieren Sie zum dazugehörigen Speicherkonto. Fügen Sie auf der IAM-Seite die Rollenzuweisung <em>Storage blob data reader</em> hinzu.</li>
        <li>Wählen Sie unter <strong>Zugriff zuweisen zu</strong> die Option <strong>Verwaltete Identität</strong>, <strong>+Mitglieder auswählen</strong>, und wählen Sie die Option <strong>Alle vom System zugewiesenen verwalteten Identitäten</strong>.</li>
        <li>Überprüfen und zuweisen, um die neuen Einstellungen zu speichern, und wiederholen Sie den vorherigen Schritt.</li>
    </ul>
    </details>

    - **Datei hochladen**: Wählen Sie die JSONL-Datei aus, die Sie in einem früheren Schritt heruntergeladen haben.
    - **Gültigkeitsprüfungsdaten**: Keine
    - **Vorgangsparameter**: *Standardeinstellungen beibehalten*
1. Die Feinabstimmung wird gestartet und kann einige Zeit in Anspruch nehmen.

> **Hinweis**: Die Feinabstimmung und Bereitstellung kann einige Zeit in Anspruch nehmen, daher müssen Sie möglicherweise regelmäßig nachsehen. Sie können bereits mit dem nächsten Schritt fortfahren, während Sie warten.

## Chat mit einem Basismodell

Während Sie darauf warten, dass die Feinabstimmung abgeschlossen ist, wollen wir uns mit einem GPT 3.5-Basismodell unterhalten, um zu sehen, wie es sich schlägt.

1. Navigieren Sie zur Seite **Einrichtungen** unter dem Abschnitt **Komponenten**, indem Sie das Menü auf der linken Seite verwenden.
1. Wählen Sie die Schaltfläche **+ Modell bereitstellen**, und wählen Sie die Option **Basismodell bereitstellen**.
1. Stellen Sie ein `gpt-35-turbo`-Modell bereit, das dem gleichen Modelltyp entspricht, den Sie bei der Feinabstimmung verwendet haben.
1. Wenn die Bereitstellung abgeschlossen ist, navigieren Sie zur Seite **Chat** unter dem Abschnitt **Project Playground**.
1. Wählen Sie im Setup-Bereitstellungsvorgang Ihr bereitgestelltes `gpt-35-model` Basismodell aus.
1. Geben Sie im Chat-Fenster die Abfrage `What can you do?` ein und sehen Sie sich die Antwort an.
    Die Antworten sind sehr allgemein gehalten. Denken Sie daran, dass wir eine Chat-Anwendung schaffen wollen, die Menschen zum Reisen inspiriert.
1. Aktualisieren Sie die Systemnachricht mit der folgenden Eingabeaufforderung:
    ```md
    You are an AI assistant that helps people plan their holidays.
    ```
1. Wählen Sie **Speichern** und dann **Chat löschen** aus und fragen Sie erneut `What can you do?`. Als Antwort kann Ihnen die Assistenz sagen, dass sie Ihnen helfen kann, Flüge, Hotels und Mietwagen für Ihre Reise zu buchen. Sie möchten dieses Verhalten vermeiden.
1. Aktualisieren Sie die Systemnachricht erneut mit einem neuen Prompt:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Klicken Sie auf **Speichern**, und **Chat löschen**.
1. Testen Sie Ihre Chat-Anwendung weiter, um zu überprüfen, dass sie keine Informationen bereitstellt, die nicht auf den erhaltenen Daten beruhen. Stellen Sie zum Beispiel die folgenden Fragen und untersuchen Sie die Antworten des Modells:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

    Das Modell kann Ihnen eine Liste von Hotels bereitstellen, auch wenn Sie es angewiesen haben, keine Hotelempfehlungen zu geben. Dies ist ein Beispiel für inkonsistentes Verhalten. Wir wollen untersuchen, ob das fein abgestimmte Modell in diesen Fällen besser abschneidet.

1. Navigieren Sie zur Seite **Feinabstimmung** unter **Tools**, um Ihren Feinabstimmungsauftrag und seinen Status zu finden. Wenn es noch läuft, können Sie Ihr bereitgestelltes Basismodell auch weiterhin manuell auswerten. Wenn der Vorgang abgeschlossen ist, können Sie mit dem nächsten Abschnitt fortfahren.

## Bereitstellen des optimierten Modells

Wenn die Feinabstimmung erfolgreich abgeschlossen ist, können Sie das fein abgestimmte Modell bereitstellen.

1. Verwenden des fein abgestimmten Modells Wählen Sie die Registerkarte **Metriken** und erkunden Sie die Feinabstimmung der Metriken.
1. Stellen Sie das fein abgestimmte Modell mit den folgenden Konfigurationen bereit:
    - **Bereitstellungsname**: *Ein eindeutiger Name für Ihr Modell, Sie können die Standardeinstellung verwenden.*
    - **Bereitstellungstyp**: Standard
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: Standard
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie sie testen können; dies kann eine Weile dauern.

## Verwenden des fein abgestimmten Modells

Nachdem Sie nun Ihr fein abgestimmtes Modell bereitgestellt haben, können Sie das Modell genauso testen wie Ihr bereitgestelltes Basismodell.

1. Wenn die Verteilung fertig ist, navigieren Sie zu dem fein abgestimmten Modell und wählen Sie **Open in playground**.
1. Aktualisieren Sie die Systemnachricht mit den folgenden Anweisungen:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Testen Sie Ihr fein abgestimmtes Modell, um festzustellen, ob sein Verhalten nun konsistenter ist. Stellen Sie z. B. die folgenden Fragen erneut und untersuchen Sie die Antworten des Modells:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

## Bereinigung

Wenn Sie die Erkundung von Azure AI Studio beendet haben, sollten Sie die von Ihnen erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

- Navigieren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`.
- Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressourcengruppen** aus.
- Wählen Sie die Ressourcengruppe aus, die Sie für diese Übung erstellt haben.
- Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
- Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
