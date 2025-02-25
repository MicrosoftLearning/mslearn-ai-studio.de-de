---
lab:
  title: Optimieren eines Sprachmodells
  description: 'Erfahren Sie, wie Sie ihre eigenen zusätzlichen Trainingsdaten verwenden, um ein Modell zu optimieren und sein Verhalten anzupassen.'
---

# Optimieren eines Sprachmodells

Wenn Sie möchten, dass sich ein Sprachmodell auf eine bestimmte Weise verhält, können Sie das entsprechende Engineering verwenden, um das gewünschte Verhalten zu definieren. Wenn Sie die Konsistenz des gewünschten Verhaltens verbessern möchten, können Sie ein Modell optimieren, indem Sie es mit Ihrem Prompt-Engineering-Ansatz vergleichen, um zu bewerten, welche Methode Ihren Anforderungen am besten entspricht.

In dieser Übung nehmen Sie mit Azure KI Foundry die Feinabstimmung eines Sprachmodells vor, das Sie für ein benutzerdefiniertes Chat-Anwendungsszenario verwenden möchten. Sie vergleichen das fein abgestimmte Modell mit einem Basismodell, um zu beurteilen, ob das fein abgestimmte Modell Ihren Anforderungen besser entspricht.

Stellen Sie sich vor, Sie arbeiten für ein Reisebüro und entwickeln eine Chatanwendung, um Personen bei der Planung ihres Urlaubs zu helfen. Ziel ist es, einen einfachen und inspirierenden Chat zu erstellen, der Ziele und Aktivitäten vorschlägt. Da der Chat nicht mit Datenquellen verbunden ist, sollte er **keine** spezifischen Empfehlungen für Hotels, Flüge oder Restaurants bereitstellen, um das Vertrauen Ihrer Kundinnen und Kunden sicherzustellen.

Diese Übung dauert ungefähr **60** Minuten.

## Erstellen eines KI-Hubs und eines Projekts im Azure KI Foundry-Portal

Sie beginnen mit der Erstellung eines Azure KI Foundry-Portalprojekts innerhalb eines Azure KI-Hubs:

1. Öffnen Sie in einem Webbrowser [https://ai.azure.com](https://ai.azure.com), und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.
1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Erstellen Sie im **Assistenten zum Erstellen eines neuen Projekts** ein Projekt mit den folgenden Einstellungen:
    - **Projektname:** *Ein eindeutiger Name für Ihr Projekt*
    - Klicken Sie auf **Anpassen**
        - **Hub**: *Autofills mit Standardnamen*
        - **Abonnement**: *Automatisch mit Ihrem angemeldeten Konto ausgefüllt*
        - **Ressourcengruppe**: (Neu) *Füllt sich automatisch mit Ihrem Projektnamen*
        - **Standort**: Wählen Sie eine der folgenden Regionen **USA (Ost 2)**, **USA, Norden-Mitte**, **Schweden, Mitte**, **Schweiz, Westen**\*
        - **Verbinden Sie Azure KI Services oder Azure OpenAI**: (Neu) *Automatisches Ausfüllen Ihres ausgewählten Hub-Namens*
        - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die in der Standorthilfe aufgelisteten Regionen enthalten Standardquoten für den/die in dieser Übung verwendeten Modelltyp(en). Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze erreicht. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen. Weitere Informationen zur [Feinabstimmung von Modellregionen](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models?tabs=python-secure%2Cglobal-standard%2Cstandard-chat-completions#fine-tuning-models)

1. Überprüfen Sie Ihre Konfiguration, und erstellen Sie Ihr Projekt.
1. Warten Sie, bis Ihr Projekt erstellt wurde.

## Optimieren eines GPT-4-Modells

Da die Feinabstimmung eines Modells einige Zeit in Anspruch nimmt, beginnen Sie zuerst mit dem Feinabstimmungsauftrag. Bevor Sie ein Modell optimieren können, benötigen Sie ein Dataset.

1. Laden Sie den [Trainingsdatensatz](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) unter `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl` herunter und speichern Sie ihn lokal als JSONL-Datei.

    > **Hinweis**: Ihr Gerät speichert die Datei möglicherweise standardmäßig als .txt-Datei. Wählen Sie alle Dateien aus, und entfernen Sie das .txt-Suffix, um sicherzustellen, dass Sie die Datei als JSONL speichern.

1. Navigieren Sie zur Seite **Feinabstimmung** unter dem Abschnitt **Erstellen und Anpassen**, indem Sie das Menü auf der linken Seite verwenden.
1. Wählen Sie die Schaltfläche zum Hinzufügen eines neuen Feinabstimmungsmodells, wählen Sie das Modell `gpt-4`, wählen Sie **Weiter** und dann **Bestätigen**.
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
        <li>Bestätigen Sie unter „Ressourcenverwaltung" auf der Registerkarte „Identität", dass es sich um eine vom System zugewiesene verwaltete Identität handelt.</li>
        <li>Navigieren Sie zum dazugehörigen Speicherkonto. Fügen Sie auf der IAM-Seite die Rollenzuweisung <em>Speicher-Blobdaten-Besitzer</em> hinzu.</li>
        <li>Wählen Sie unter <strong>Zugriff zuweisen an</strong> die Option <strong>Verwaltete Identität</strong>, <strong>+ Mitglieder auswählen</strong>, <strong>Alle systemseitig zugewiesenen verwalteten Identitäten</strong> und Ihre Azure KI-Ressource aus.</li>
        <li>Überprüfen und zuweisen, um die neuen Einstellungen zu speichern, und wiederholen Sie den vorherigen Schritt.</li>
    </ul>
    </details>

    - **Datei hochladen**: Wählen Sie die JSONL-Datei aus, die Sie in einem früheren Schritt heruntergeladen haben.
    - **Gültigkeitsprüfungsdaten**: Keine
    - **Vorgangsparameter**: *Standardeinstellungen beibehalten*
1. Die Feinabstimmung beginnt und kann einige Zeit in Anspruch nehmen.

> **Hinweis**: Die Feinabstimmung und Bereitstellung kann einige Zeit in Anspruch nehmen, sodass Sie möglicherweise regelmäßig zurückkehren müssen. Sie können bereits mit dem nächsten Schritt fortfahren, während Sie warten.

## Chat mit einem Basismodell

Während Sie auf den Abschluss der Feinabstimmung warten, lassen Sie uns mit einem Basis-GPT-4-Modell sprechen, um seine Leistung zu beurteilen.

1. Navigieren Sie zur Seite **Modelle + Endpunkte** unter dem Abschnitt **Meine Assets**, indem Sie das Menü auf der linken Seite verwenden.
1. Wählen Sie die Schaltfläche ** + Modell bereitstellen** und dann die Option **Basismodell bereitstellen** aus.
1. Stellen Sie ein `gpt-4`-Modell bereit, bei dem es sich um denselben Modelltyp handelt, den Sie bei der Feinabstimmung verwendet haben.

> **Hinweis**: Wenn an Ihrem aktuellen Speicherort für KI-Ressourcen kein Kontingent für das Modell, das Sie bereitstellen möchten, verfügbar ist, werden Sie aufgefordert, einen anderen Speicherort zu wählen, an dem eine neue KI-Ressource erstellt und mit Ihrem Projekt verbunden wird.

1. Wenn die Bereitstellung abgeschlossen ist, wählen Sie die Schaltfläche **Im Spielzimmer öffnen**.
1. Vergewissern Sie sich, dass Ihr bereitgestelltes `gpt-4` Basismodell im Einrichtungsfenster ausgewählt ist.
1. Geben Sie im Chat-Fenster die Abfrage `What can you do?` ein und sehen Sie sich die Antwort an.
    Die Antworten sind sehr allgemein gehalten. Denken Sie daran, dass wir eine Chatanwendung erstellen möchten, die Menschen zum Reisen inspiriert.
1. Aktualisieren Sie die Systemmeldung im Setup-Fenster mit der folgenden Aufforderung:

    ```md
    You are an AI assistant that helps people plan their holidays.
    ```

1. Wählen Sie **Änderungen übernehmen**, wählen Sie dann **Chat löschen** und fragen Sie erneut `What can you do?`. Als Antwort kann Ihnen der Assistent sagen, dass er Ihnen bei der Buchung von Flügen, Hotels und Mietwagen für Ihre Reise helfen kann. Sie möchten dieses Verhalten verhindern.
1. Aktualisieren Sie die Systemnachricht erneut mit einer neuen Eingabeaufforderung:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Wähle **Änderungen übernehmen** und **Chat löschen**.
1. Testen Sie ihre Chatanwendung weiterhin, um sicherzustellen, dass sie keine Informationen bereitstellt, die nicht auf abgerufenen Daten basieren. Stellen Sie beispielsweise die folgenden Fragen, und erkunden Sie die Antworten des Modells:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

    Das Modell stellt Ihnen eventuell eine Liste von Hotels zur Verfügung, auch wenn Sie es angewiesen haben, keine Hotelempfehlungen zu geben. Dies ist ein Beispiel für ein inkonsistentes Verhalten. Schauen wir uns an, ob das fein abgestimmte Modell in diesen Fällen besser funktioniert.

1. Navigieren Sie zur Seite **Feinabstimmung** unter **Erstellen und Anpassen**, um Ihren Feinabstimmungsauftrag und seinen Status zu finden. Wenn sie noch ausgeführt wird, können Sie die manuelle Auswertung Ihres bereitgestellten Basismodells fortsetzen. Wenn sie abgeschlossen ist, können Sie mit dem nächsten Abschnitt fortfahren.

## Bereitstellen des optimierten Modells

Wenn die Feinabstimmung erfolgreich abgeschlossen wurde, können Sie das fein abgestimmte Modell bereitstellen.

1. Verwenden des fein abgestimmten Modells Wählen Sie die Registerkarte **Metriken** und erkunden Sie die Feinabstimmung der Metriken.
1. Stellen Sie das fein abgestimmte Modell mit den folgenden Konfigurationen bereit:
    - **Bereitstellungsname**: *Ein eindeutiger Name für Ihr Modell, Sie können die Standardeinstellung verwenden.*
    - **Bereitstellungstyp**: Standard
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: Standard
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie sie testen können. Dies kann eine Weile dauern.

## Verwenden des fein abgestimmten Modells

Nachdem Sie Ihr fein abgestimmtes Modell bereitgestellt haben, können Sie es wie das bereitgestellte Basismodell testen.

1. Wenn die Verteilung fertig ist, navigieren Sie zu dem fein abgestimmten Modell und wählen Sie **Open in playground**.
1. Aktualisieren Sie die Systemnachricht mit den folgenden Anweisungen:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Testen Sie Ihr fein abgestimmtes Modell, um zu beurteilen, ob sein Verhalten jetzt konsistenter ist. Stellen Sie beispielsweise die folgenden Fragen erneut, und erkunden Sie die Antworten des Modells:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

## Bereinigen

Wenn Sie die Erkundung von Azure KI Foundry abgeschlossen haben, sollten Sie die von Ihnen erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

- Navigieren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`.
- Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressourcengruppen** aus.
- Wählen Sie die Ressourcengruppe aus, die Sie für diese Übung erstellt haben.
- Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
- Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
