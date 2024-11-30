---
lab:
  title: Erkunden der Komponenten und Tools von Azure KI Foundry
---

# Erkunden der Komponenten und Tools von Azure KI Foundry

In dieser Übung verwenden Sie das Azure KI Foundry-Portal, um ein Projekt zu erstellen und ein generatives KI-Modell zu erkunden.

Diese Übung dauert ca. **30** Minuten.

## Öffnen des Azure KI Foundry-Portals

Beginnen wir mit der Erkundung des Azure KI Foundry-Portals.

1. Öffnen Sie in einem Webbrowser [https://ai.azure.com](https://ai.azure.com), und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Die Startseite des Azure KI Foundry-Portals sieht ähnlich aus wie das folgende Bild:

    ![Screenshot des Azure KI Foundry-Portals.](./media/azure-ai-studio-home.png)

1. Überprüfen Sie die Informationen auf der Startseite. Zeigen Sie dann die einzelnen Registerkarten an, und sehen Sie sich die darauf enthaltenen Optionen zum Erkunden von Modellen und Funktionen, Erstellen von Projekten und Verwalten von Ressourcen an.

## Erstellen eines Azure KI-Hubs und eines Projekts

Ein Azure KI-Hub bietet einen Arbeitsbereich für die Zusammenarbeit, in dem Sie ein oder mehrere *Projekte* definieren können. Erstellen Sie uns ein Projekt und ein Azure KI-Hub.

1. Wählen Sie auf der Startseite **+ Projekt erstellen**. Im Assistenten **Projekt erstellen** sehen Sie alle Azure-Ressourcen, die automatisch mit Ihrem Projekt erstellt werden, oder Sie können die folgenden Einstellungen anpassen, indem Sie **Anpassen** wählen, bevor Sie **Erstellen** wählen:
   
    - **Hub-Name:** *Ein eindeutiger Name*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe:** *Erstellen Sie eine neue Ressourcengruppe mit einem eindeutigen Namen, oder wählen Sie eine vorhandene Ressourcengruppe aus.*
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus, wählen Sie dann **gpt-35-turbo** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*
    - **Verbinden von Azure KI Services oder Azure OpenAI**: *Wählen Sie eine Option aus, um einen neuen KI-Dienste zu erstellen oder einen vorhandenen zu verwenden.*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die aufgeführten Regionen enthalten das Standardkontingent für die in dieser Übung verwendeten Modelltypen. Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze in Szenarien erreicht, in denen Sie einen Mandanten für andere Benutzer und Benutzerinnen freigeben. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

1. Wenn Sie **Anpassen** gewählt haben, wählen Sie **Weiter** und überprüfen Sie Ihre Konfiguration.
1. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
   
    Nachdem Sie den Azure KI-Hub und das Projekt erstellt haben, sollte es ähnlich wie das folgende Bild aussehen:

    ![Screenshot der Details eines Azure KI-Hubs im Azure KI Foundry-Portal.](./media/azure-ai-resource.png)

1. Öffnen Sie eine neue Browser-Registerkarte (lassen Sie die Registerkarte des Azure KI Foundry-Portals geöffnet) und rufen Sie das Azure-Portal unter [https://portal.azure.com](https://portal.azure.com?azure-portal=true) auf, indem Sie sich mit Ihren Azure-Zugangsdaten anmelden, falls Sie dazu aufgefordert werden.
1. Navigieren Sie zu der Ressourcengruppe, in der Sie Ihren Azure KI-Hub erstellt haben, und zeigen Sie die erstellten Azure-Ressourcen an.

    ![Screenshot: Azure KI-Hub und zugehörige Ressourcen im Azure-Portal.](./media/azure-portal.png)

1. Kehren Sie zur Browser-Registerkarte des Azure KI Foundry-Portals zurück.
1. Zeigen Sie die einzelnen Seiten im Bereich links auf der Seite für Ihren Azure KI-Hub an, und sehen Sie sich die Artefakte an, die Sie erstellen und verwalten können. Auf der Seite **Management Center** können Sie **Verbundene Ressourcen** auswählen, entweder unter Ihrem Hub oder Ihrem Projekt, und feststellen, dass bereits Verbindungen zu Azure OpenAI und KI-Services erstellt wurden.
1. Wenn Sie sich auf der Seite Management Center befinden, wählen Sie **Zum Projekt gehen**.

## Bereitstellen und Testen eines Modells

Sie können ein Projekt verwenden, um komplexe KI-Lösungen basierend auf generativen KI-Modellen zu erstellen. Eine vollständige Untersuchung aller im Azure KI Foundry-Portal verfügbaren Entwicklungsoptionen würde den Rahmen dieser Übung sprengen, aber wir werden einige grundlegende Möglichkeiten erkunden, wie Sie mit Modellen in einem Projekt arbeiten können.

1. Wählen Sie im linken Fensterbereich für Ihr Projekt im Abschnitt **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Wählen Sie auf der Seite **Modelle + Endpunkte** auf der Registerkarte **Modellbereitstellungen** die Option **+ Modell bereitstellen**.
1. Suchen Sie in der Liste nach dem Modell **gpt-35-turbo**, und wählen Sie „Bestätigen“ aus.
1. Stellen Sie das Modell mit den folgenden Einstellungen bereit, indem Sie **Anpassen** in den Bereitstellungsdetails wählen:
    - **Bereitstellungsname:** *Ein eindeutiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **KI-Ressource**: *Wählen Sie die zuvor erstellte Quelle* aus
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: StandardV2 
    - **Dynamische Quote aktivieren**: Deaktiviert
      
    > **Hinweis:** Durch das Verringern des TPM wird die Überlastung des Kontingents vermieden, das in dem von Ihnen verwendeten Abonnement verfügbar ist. 5.000 TPM reicht für die in dieser Übung verwendeten Daten aus.

1. Nachdem das Modell bereitgestellt wurde, wählen Sie auf der Seite „Bereitstellungsübersicht“ die Option **Im Playground öffnen** aus.
1. Stellen Sie auf der Seite **Chat-Playground** sicher, dass Ihre Modellimplementierung im Abschnitt **Bereitstellung** ausgewählt ist.
1. Geben Sie im Chatfenster eine Abfrage wie *Was ist KI?* ein, und sehen Sie sich die Antwort an:

    ![Screenshot des Spielplatzes im Azure KI Foundry-Portal.](./media/playground.png)

## Bereinigung

Wenn Sie die Erkundung des Azure KI-Foundry-Portals abgeschlossen haben, sollten Sie die Ressourcen, die Sie in dieser Übung erstellt haben, löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zur Browserregisterkarte mit dem Azure-Portal zurück (oder öffnen Sie das [Azure-Portal](https://portal.azure.com?azure-portal=true) auf einer neuen Browserregisterkarte erneut), und zeigen Sie den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
