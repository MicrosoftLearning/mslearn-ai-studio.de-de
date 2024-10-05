---
lab:
  title: Feinabstimmung eines Sprachmodells für die Chatvervollständigung in Azure KI Studio
---

# Feinabstimmung eines Sprachmodells für die Chatvervollständigung in Azure KI Studio

In dieser Übung werden Sie ein Sprachmodell mit Azure KI Studio feinabstimmen, das Sie für ein benutzerdefiniertes Copilot-Szenario verwenden möchten.

Diese Übung dauert ungefähr **45** Minuten.

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
        - **Speicherort:** *Treffen Sie eine **zufällige** Auswahl aus einer der folgenden Regionen*\*
        - USA (Ost) 2
        - USA Nord Mitte
        - Schweden, Mitte
        - Schweiz, Norden
    - **Verbinden von Azure KI Services oder Azure OpenAI**: *Erstellen einer neuen Verbindung*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die aufgeführten Regionen enthalten das Standardkontingent für die in dieser Übung verwendeten Modelltypen. Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze erreicht. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen. Erfahren Sie mehr über die [Modellverfügbarkeit pro Region](https://learn.microsoft.com/en-us/azure/ai-studio/concepts/fine-tuning-overview#azure-openai-models)

1. Überprüfen Sie Ihre Konfiguration, und erstellen Sie Ihr Projekt.
1. Warten Sie, bis Ihr Projekt erstellt wurde.

## Optimieren eines GPT-3.5 Modells

Bevor Sie ein Modell optimieren können, benötigen Sie ein Dataset.

1. Speichern Sie das Schulungs-Dataset lokal als JSONL-Datei: https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-finetune.jsonl
1. Navigieren Sie zur Seite **Feinabstimmung** unter dem Abschnitt **Tools**, indem Sie das Menü auf der linken Seite verwenden.
1. Wählen Sie die Schaltfläche zum Hinzufügen eines neuen Feinabstimmungsmodells, wählen Sie das Modell `gpt-35-turbo` und wählen Sie **Bestätigen**.
1. **Optimieren Sie** das Modell mithilfe der folgenden Konfiguration:
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **Modellsuffix**: `ft-travel`
    - **Verbundene Azure OpenAI-Ressource**: *Wählen Sie die Standardverbindung aus, die beim Erstellen des Hubs* erstellt wurde.
    - **Trainingsdaten**: Dateien hochladen
    - **Datei hochladen**: Wählen Sie die JSONL-Datei aus, die Sie in einem früheren Schritt heruntergeladen haben.

    > **Tipp**: Sie müssen nicht warten, bis die Datenverarbeitung abgeschlossen ist, um mit dem nächsten Schritt fortzufahren.

    - **Gültigkeitsprüfungsdaten**: Keine
    - **Vorgangsparameter**: *Standardeinstellungen beibehalten*
1. Feinabstimmung beginnt und kann einige Zeit in Anspruch nehmen.

> **Hinweis**: Die Feinabstimmung und Bereitstellung kann einige Zeit in Anspruch nehmen, sodass Sie möglicherweise regelmäßig zurückkehren müssen, um den nächsten Schritt abzuschließen.

## Bereitstellen des optimierten Modells

Wenn die Feinabstimmung erfolgreich abgeschlossen wurde, können Sie das Modell bereitstellen.

1. Verwenden des fein abgestimmten Modells Wählen Sie die Registerkarte **Metriken** und erkunden Sie die Feinabstimmung der Metriken.
1. Stellen Sie das fein abgestimmte Modell mit den folgenden Konfigurationen bereit:
    - **Bereitstellungsname**: *Ein eindeutiger Name für Ihr Modell, Sie können die Standardeinstellung verwenden.*
    - **Bereitstellungstyp**: Standard
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: Standard

## Verwenden des fein abgestimmten Modells

Nachdem Sie Ihr fein abgestimmtes Modell bereitgestellt haben, können Sie das Modell wie jedes andere bereitgestellte Modell testen.

1. Wenn die Verteilung fertig ist, navigieren Sie zu dem fein abgestimmten Modell und wählen Sie **Open in playground**.
1. Geben Sie in das Chat-Fenster die Abfrage `What can you do?` ein. Beachten Sie, dass das Modell bereits weiß, worauf es sich konzentrieren soll, obwohl Sie die Systemnachricht nicht angegeben haben, um Ihr Modell anzuweisen, reisebezogene Fragen zu beantworten.
1. Versuchen Sie es mit einer anderen Abfrage wie `Where should I go on holiday for my 30th birthday?`

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure KI Studio fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

- Navigieren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`.
- Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressourcengruppen** aus.
- Wählen Sie die Ressourcengruppe aus, die Sie für diese Übung erstellt haben.
- Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
- Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
