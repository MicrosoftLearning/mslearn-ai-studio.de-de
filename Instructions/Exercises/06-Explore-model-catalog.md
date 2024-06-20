---
lab:
  title: "Erkunden des Modellkatalogs in Azure\_KI Studio"
---

# Erkunden des Modellkatalogs in Azure KI Studio

Der Modellkatalog von Azure KI Studio dient als zentrales Repository, in dem Sie eine Vielzahl von Modellen erkunden und verwenden können. Dadurch wird die Erstellung eines generativen KI-Szenarios erleichtert.

In dieser Übung erkunden Sie den Modellkatalog in Azure KI Studio.

> **Hinweis:** Azure KI Studio befindet sich zum Zeitpunkt des Schreibens in der Vorschau und befindet sich in der aktiven Entwicklung. Einige Elemente des Diensts sind möglicherweise nicht genau wie beschrieben, und einige Features funktionieren möglicherweise nicht wie erwartet.

> Um diese Übung abzuschließen, muss für Ihr Azure-Abonnement der Zugriff auf den Azure OpenAI-Dienst genehmigt werden.

Diese Übung dauert ungefähr **25** Minuten.

## Erstellen eines Azure KI-Hubs

Sie benötigen einen Azure KI-Hub in Ihrem Azure-Abonnement, um Projekte zu hosten. Sie können diese Ressource entweder beim Erstellen eines Projekts erstellen oder vorab bereitstellen (wie in dieser Übung).

1. Öffnen Sie in einem Webbrowser [https://ai.azure.com](https://ai.azure.com), und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.

1. Wählen Sie im Abschnitt „Verwaltung“ die Option „Alle Hubs“ und dann **+Neuer Hub** aus. Erstellen Sie eine neue Regel mit den folgenden Einstellungen:
    - **Hub-Name:** *Ein eindeutiger Name*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe:** *Erstellen Sie eine neue Ressourcengruppe mit einem eindeutigen Namen, oder wählen Sie eine vorhandene Ressourcengruppe aus.*
    - **Speicherort:** *Treffen Sie eine **zufällige** Auswahl aus einer der folgenden Regionen*\*
        - Australien (Osten)
        - Kanada, Osten
        - East US
        - USA (Ost) 2
        - Frankreich, Mitte
        - Japan, Osten
        - USA Nord Mitte
        - Schweden, Mitte
        - Schweiz, Norden
        - UK, Süden
    - **Verbinden von Azure KI Services oder Azure OpenAI**: Wählen Sie eine Option aus, um einen neuen KI-Dienste zu erstellen oder einen vorhandenen zu verwenden.
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die aufgeführten Regionen enthalten das Standardkontingent für die in dieser Übung verwendeten Modelltypen. Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze in Szenarien erreicht, in denen Sie einen Mandanten für andere Benutzer und Benutzerinnen freigeben. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

1. Klicken Sie auf **Erstellen**. Die Erstellung des ersten Hubs kann einige Minuten dauern. Während der Huberstellung werden auch die folgenden KI-Ressourcen für Sie erstellt: 
    - KI Services
    - Speicherkonto
    - Key vault

1. Nachdem der Azure KI-Hub erstellt wurde, sollte er ähnlich wie in der folgenden Abbildung aussehen:

    ![Screenshot: Details eines Azure KI-Hubs in Azure KI Studio.](./media/azure-ai-overview.png)

## Erstellen eines Projekts

Ein Azure KI-Hub bietet einen Arbeitsbereich für die Zusammenarbeit, in dem Sie ein oder mehrere *Projekte* definieren können. Erstellen Sie nun ein Projekt in Ihrem Azure KI-Hub.

1. Wählen Sie in Azure KI Studio auf der Seite **Erstellen** die Option **+ Neues Projekt** aus. Erstellen Sie dann im **Assistenten zum Erstellen eines neuen Projekts** ein Projekt mit den folgenden Einstellungen:

    - **Projektname:** *Ein eindeutiger Name für Ihr Projekt*
    - **Hub:** *Ihr KI-Hub*

1. Warten Sie, bis Ihr Projekt erstellt wurde. Wenn es fertig ist, sollte es ähnlich wie in der folgenden Abbildung aussehen:

    ![Screenshot: Projektdetailseite in Azure KI Studio](./media/azure-ai-project.png)

1. Zeigen Sie die Seiten im linken Bereich an, erweitern Sie jeden Abschnitt, und sehen Sie sich die Aufgaben an, die Sie ausführen können, und die Ressourcen, die Sie in einem Projekt verwalten können.

## Bereitstellen eines Modells

Jetzt können Sie ein Modell bereitstellen, das über **Azure KI Studio** verwendet werden soll. Nach der Bereitstellung verwenden Sie das Modell, um Inhalte in natürlicher Sprache zu generieren.

1. Erstellen Sie in Azure KI Studio eine neue Bereitstellung mit den folgenden Einstellungen:

    - **Modell**: gpt-35-turbo
    - **Bereitstellungstyp**: Standard
    - **Verbundene Azure OpenAI-Ressource**: *Ihre Azure OpenAI-Verbindung*
    - **Modellversion**: Automatische Aktualisierung auf die Standardeinstellung
    - **Bereitstellungsname**: *Ein eindeutiger Name Ihrer Wahl*
    - **Erweiterte Optionen**
        - **Inhaltsfilter**: Standard
        - **Ratenbegrenzung für Token pro Minute**: 5.000

> **Hinweis:** Jedes Azure KI Studio-Modell ist für ein anderes Verhältnis von Funktionen und Leistung optimiert. Wir verwenden das Modell **GPT 3.5 Turbo** in dieser Übung, das sich in hohem Maße für Szenarien zur Generierung natürlicher Sprache und Chatszenarien eignet.

## Testen des Modells im Chat-Playground

Sehen wir uns an, wie sich das Modell in einer Konversation verhält.

1. Navigieren Sie im linken Bereich zum **Playground**.

1. Geben Sie im Modus **Chat** die folgende Eingabeaufforderung im Abschnitt **Chatsitzung** ein.

    ```
   <Placeholder>
    ```

1. Das Modell wird wahrscheinlich mit etwas Text antworten ...

## Ändern der Meldung der Systemeingabeaufforderung

Im Chat-Playground können Sie die Systemeingabeaufforderung ändern, um das Verhalten des Modells zu ändern. Die Systemeingabeaufforderung ist die erste Meldung, die das Modell empfängt, bevor es mit dem Generieren von Antworten beginnt.

1. Ändern Sie im Abschnitt **Systemnachrichten** die Systemnachricht in den folgenden Text:

    ```
    <Placeholder>
    ```

1. Wenden Sie die Änderungen auf die Systemmeldung an.

1. Geben Sie im Abschnitt **Chatsitzung** erneut die folgende Eingabeaufforderung ein.

    ```
   <Placeholder>
    ```

8. Beobachten Sie die Ausgabe, die hoffentlich darauf hinweisen sollte, dass ...


## Hinzufügen von Daten im Chat-Playground

<Placeholder>

## Bereinigung

Wenn Sie mit Ihrer Azure OpenAI-Ressource fertig sind, denken Sie daran, die Bereitstellung oder die gesamte Ressource im [Azure-Portal](https://portal.azure.com/?azure-portal=true) zu löschen.

