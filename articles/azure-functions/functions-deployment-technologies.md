---
title: Bereitstellungstechnologien in Azure Functions | Microsoft-Dokumentation
description: Lernen Sie die verschiedenen Methoden kennen, mit denen Code in Azure Functions bereitgestellt werden kann.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: f468b2afce1609de126859546a72544ba403424e
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838884"
---
# <a name="deployment-technologies-in-azure-functions"></a>Bereitstellungstechnologien in Azure Functions

Ihnen stehen verschiedene Technologien zur Verfügung, um Code aus Azure Functions-Projekten in Azure bereitzustellen. In diesem Artikel finden Sie eine umfassende Liste dieser Technologien, und Sie erfahren, welche Technologien für welche Varianten von Functions verfügbar sind, was bei Verwendung der jeweiligen Methode passiert und welche Methoden für welche Szenarien am besten geeignet sind. Die verschiedenen Tools, die Sie bei der Bereitstellung in Azure Functions unterstützen, werden jeweils basierend auf ihrem Kontext auf die passende Technologie abgestimmt. Im Allgemeinen ist die ZIP-Bereitstellung die empfohlene Bereitstellungstechnologie für Azure Functions.

## <a name="deployment-technology-availability"></a>Bereitstellungstechnologie: Verfügbarkeit

Azure Functions unterstützt die plattformübergreifende lokale Entwicklung sowie Hosting unter Windows und Linux. Derzeit sind drei Hostingpläne verfügbar:

+ [Verbrauch](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dediziert (App Service)](functions-scale.md#app-service-plan)

Jeder Plan weist ein anderes Verhalten auf. Nicht alle Bereitstellungstechnologien stehen für jede Variante von Azure Functions zur Verfügung. Das folgende Diagramm zeigt die jeweils unterstützten Bereitstellungstechnologien für die verschiedenen Kombination aus Betriebssystem und Hostingplan:

| Bereitstellungstechnologie | Windows: Verbrauch | Windows Premium (Vorschau) | Windows: Dediziert  | Linux: Verbrauch | Linux: Premium (Vorschau) | Linux: Dediziert |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Externe Paket-URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| ZIP-Bereitstellung |✔|✔|✔|✔|✔|✔|
| Docker-Container | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| Quellcodeverwaltung |✔|✔|✔| |✔|✔|
| Lokales Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Cloudsynchronisierung<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Portalbearbeitung |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> Bereitstellungstechnologie, die eine [manuelle Triggersynchronisierung](#trigger-syncing) erfordert.  
<sup>2</sup> Die Portalbearbeitung steht nur für HTTP-Trigger und Trigger mit Timer zur Verfügung (für Functions unter Linux mit dem Plan „Premium“ oder „Dediziert“).

## <a name="key-concepts"></a>Wichtige Begriffe

Einige Schlüsselkonzepte sind wichtig, um zu verstehen, wie Bereitstellungen in Azure Functions funktionieren.

### <a name="trigger-syncing"></a>Triggersynchronisierung

Wenn Sie einen Trigger ändern, muss die Infrastruktur von Functions über die vorgenommenen Änderungen informiert werden. Die Synchronisierung erfolgt bei vielen Bereitstellungstechnologien automatisch. Manchmal müssen die Trigger jedoch manuell synchronisiert werden. Eine manuelle Synchronisierung der Trigger ist erforderlich, wenn Sie Ihre Aktualisierungen über einen Verweis auf eine externe Paket-URL oder einen lokalen Git-Speicherort oder über Cloudsynchronisierung bzw. FTP bereitstellen. Trigger können auf drei Arten synchronisiert werden:

* Neustarten der Funktions-App über das Azure-Portal
* Senden einer HTTP POST-Anforderung an `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` unter Verwendung des [Hauptschlüssels](functions-bindings-http-webhook.md#authorization-keys)
* Senden einer HTTP POST-Anforderung an `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` Ersetzen Sie die Platzhalter durch Ihre Abonnement-ID, den Namen Ihrer Ressourcengruppe und den Namen Ihrer Funktions-App.

### <a name="remote-build"></a>Remotebuild

In Azure Functions können automatisch Builds für den Code ausgeführt werden, der nach ZIP-Bereitstellungen empfangen wird. Diese Builds verhalten sich etwas unterschiedlich, je nachdem, ob Ihre App unter Windows oder Linux ausgeführt wird. Remotebuilds werden nicht ausgeführt, wenn eine App bereits für die Ausführung im Modus [Run From Package](run-functions-from-deployment-package.md) (Aus Paket ausführen) festgelegt wurde. Informationen zum Verwenden von Remotebuilds finden Sie unter [ZIP-Bereitstellung](#zip-deploy).

> [!NOTE]
> Wenn bei der Verwendung eines Remotebuilds Probleme auftreten, liegt es möglicherweise daran, dass Ihre App erstellt wurde, bevor die Funktion verfügbar gemacht wurde (1. August 2019). Erstellen Sie eine neue Funktions-App.

#### <a name="remote-build-on-windows"></a>Remotebuild unter Windows

Alle Funktions-Apps, die unter Windows ausgeführt werden, enthalten eine kleine Verwaltungs-App, die SCM-Website (oder [Kudu](https://github.com/projectkudu/kudu)-Website). Auf dieser Website wird ein Großteil der Bereitstellungs- und Buildlogik für Azure Functions verarbeitet.

Bei der Bereitstellung einer App unter Windows werden sprachspezifische Befehle, z. B. `dotnet restore` (C#) oder `npm install` (JavaScript) ausgeführt.

#### <a name="remote-build-on-linux-preview"></a>Remotebuild unter Linux (Vorschau)

Zum Aktivieren des Remotebuilds unter Linux müssen Sie die folgenden [Anwendungseinstellungen](functions-how-to-use-azure-function-app-settings.md#settings) festlegen:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Wenn Apps remote unter Linux erstellt werden, werden sie [über das Bereitstellungspaket ausgeführt](run-functions-from-deployment-package.md).

> [!NOTE]
> Ein Remotebuild mit dem App Service-Plan Dedicated für Linux wird derzeit nur für Node.js und Python unterstützt.

##### <a name="consumption-preview-plan"></a>Verbrauchsplan (Vorschau)

Linux-Funktions-Apps, die im Verbrauchsplan ausgeführt werden, umfassen keine SCM/Kudu-Website, wodurch die Bereitstellungsoptionen eingeschränkt sind. Funktions-Apps unter Linux, die im Verbrauchsplan ausgeführt werden, unterstützen jedoch Remotebuilds.

##### <a name="dedicated-and-premium-preview-plans"></a>Pläne Dedicated und Premium (Vorschau)

Funktions-Apps, die unter Linux im [App Service-Plan Dedicated](functions-scale.md#app-service-plan) oder [Premium](functions-scale.md#premium-plan) ausgeführt werden, umfassen auch eine eingeschränkte SCM/Kudu-Website.

## <a name="deployment-technology-details"></a>Bereitstellungstechnologie: Details

Die folgenden Bereitstellungsmethoden sind in Azure Functions verfügbar.

### <a name="external-package-url"></a>Externe Paket-URL

Sie können eine externe Paket-URL verwenden, um auf eine Remotepaketdatei (ZIP-Datei) zu verweisen, die ihre Funktions-App enthält. Die Datei wird von der angegebenen URL heruntergeladen, und die App wird im Modus [Aus Paketdatei ausführen](run-functions-from-deployment-package.md) ausgeführt.

>__Verwendung:__ Fügen Sie Ihren Anwendungseinstellungen `WEBSITE_RUN_FROM_PACKAGE` hinzu. Der Wert dieser Einstellung muss eine URL sein (der Speicherort der spezifischen Paketdatei, die Sie ausführen möchten). Einstellungen können entweder [über das Portal](functions-how-to-use-azure-function-app-settings.md#settings) oder [mithilfe der Azure-Befehlszeilenschnittstelle](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) hinzugefügt werden. 
>
>Bei Verwendung von Blob Storage muss ein privater Container mit einer [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) verwendet werden, damit Functions auf das Paket zugreifen kann. Jedes Mal, wenn die Anwendung neu gestartet wird, ruft Sie eine Kopie des Inhalts ab. Ihr Verweis muss für die Lebensdauer der Anwendung gültig sein.

>__Einsatzgebiete:__ Eine externe Paket-URL ist die einzige Bereitstellungsmethode, die für Azure Functions unter Linux im Verbrauchsplan unterstützt wird, wenn der Benutzer ausdrücklich nicht möchte, dass ein Remotebuild ausgeführt wird. Wenn Sie die Paketdatei aktualisieren, auf die eine Funktions-App verweist, müssen Sie die [Trigger manuell synchronisieren](#trigger-syncing), um Azure darüber zu informieren, dass sich Ihre Anwendung geändert hat.

### <a name="zip-deploy"></a>ZIP-Bereitstellung

Verwenden Sie ZIP-Bereitstellung, um eine ZIP-Datei mit ihrer Funktions-APP in Azure zu pushen. Optional können Sie die App so festlegen, dass sie [über ein Paket ausgeführt wird](run-functions-from-deployment-package.md), oder Sie können angeben, dass ein [Remotebuild](#remote-build) ausgeführt wird.

>__Verwendung:__ Führen Sie die Bereitstellung mithilfe Ihres bevorzugten Clienttools aus: [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure) oder [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Eine Anleitung zum manuellen Bereitstellen einer ZIP-Datei für Ihre Funktions-App finden Sie unter [Bereitstellen mithilfe einer ZIP-Datei oder URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

Verwenden Sie zum Ausführen einer ZIP-Bereitstellung mit einem [Remotebuild](#remote-build) den folgenden [Core Tools](functions-run-local.md)-Befehl:

```bash
func azure functionapp publish <app name> --build remote
```

Alternativ können Sie in VS Code angeben, dass bei der Bereitstellung ein Remotebuild ausgeführt wird, indem Sie das Flag „azureFunctions.scmDoBuildDuringDeployment“ hinzufügen. Informationen zum Hinzufügen eines Flags in VS Code finden Sie in den Anweisungen im [Wiki zur Azure Functions-Erweiterung](https://github.com/microsoft/vscode-azurefunctions/wiki).

>Wenn die Bereitstellung mithilfe der ZIP-Bereitstellung erfolgt, können Sie die App auf [Run from Package](run-functions-from-deployment-package.md) (Aus Paket ausführen) festlegen. Zur Ausführung über ein Paket legen Sie den Wert der Anwendungseinstellung `WEBSITE_RUN_FROM_PACKAGE` auf `1` fest. Wir empfehlen die ZIP-Bereitstellung. Sie führt zu schnelleren Ladezeiten für Ihre Anwendungen und ist die Standardeinstellung für VS Code, Visual Studio und die Azure CLI. 

>__Einsatzgebiete:__ Die ZIP-Bereitstellung ist die empfohlene Bereitstellungstechnologie für Azure Functions.

### <a name="docker-container"></a>Docker-Container

Sie können ein Linux-Containerimage bereitstellen, das Ihre Funktions-App enthält.

>__Verwendung:__ Erstellen Sie eine Linux-Funktions-App im Plan „Premium“ oder „Dediziert“, und geben Sie das gewünschte Containerimage für die Ausführung an. Hierzu stehen zwei Möglichkeiten zur Verfügung:
>
>* Erstellen Sie eine Linux-Funktions-App unter einem Azure App Service-Plan über das Azure-Portal. Wählen Sie für **Veröffentlichen** das **Docker-Image** aus, und konfigurieren Sie dann den Container. Geben Sie den Speicherort ein, an dem das Image gehostet wird.
>* Erstellen Sie eine Linux-Funktions-App unter einem App Service-Plan mithilfe der Azure CLI. Eine entsprechende Anleitung finden Sie unter [Erstellen einer Funktion unter Linux mithilfe eines benutzerdefinierten Images](functions-create-function-linux-custom-image.md#create-a-premium-plan).
>
>Verwenden Sie für die Bereitstellung einer vorhandenen App mit einem benutzerdefinierten Container den Befehl [`func deploy`](functions-run-local.md#publish) der [Azure Functions Core Tools](functions-run-local.md).

>__Einsatzgebiete:__ Verwenden Sie die Option „Docker-Container“, wenn Sie mehr Kontrolle über die Linux-Umgebung benötigen, in der Ihre Funktions-App ausgeführt wird. Dieser Bereitstellungsmechanismus steht nur für Functions unter Linux zur Verfügung.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Bei der Web Deploy-Methode werden Ihre Windows-Anwendungen verpackt und auf einem beliebigen IIS-Server bereitgestellt (einschließlich Ihrer Funktions-Apps unter Windows in Azure).

>__Verwendung:__ Verwenden Sie die [Visual Studio-Tools für Azure Functions](functions-create-your-first-function-visual-studio.md). Deaktivieren Sie das Kontrollkästchen **Aus Paketdatei ausführen (empfohlen)** .
>
>Sie können auch [Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) herunterladen und `MSDeploy.exe` direkt aufrufen.

>__Einsatzgebiete:__ Web Deploy wird zwar problemlos unterstützt, inzwischen wird jedoch der Mechanismus [ZIP-Bereitstellung im Modus „Aus Paketdatei ausführen“](#zip-deploy) bevorzugt. Weitere Informationen finden Sie im [Entwicklungsleitfaden für Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Quellcodeverwaltung

Verbinden Sie Ihre Funktions-App mithilfe der Quellcodeverwaltung mit einem Git-Repository. Ein Update des Codes in diesem Repository löst die Bereitstellung aus. Weitere Informationen finden Sie im [Kudu-Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Verwendung:__ Verwenden Sie das Bereitstellungscenter im Functions-Bereich des Portals, um die auf Quellcodeverwaltung basierende Veröffentlichung einzurichten. Weitere Informationen finden Sie unter [Continuous Deployment für Azure Functions](functions-continuous-deployment.md).

>__Einsatzgebiete:__ Die Verwendung von Quellcodeverwaltung ist die bewährte Methode für Teams, die an ihren Funktions-Apps zusammenarbeiten. Quellcodeverwaltung ist eine gute Bereitstellungsoption, die anspruchsvollere Bereitstellungspipelines ermöglicht.

### <a name="local-git"></a>Lokales Git

Bei dieser Methode können Sie Code unter Verwendung von lokalem Git von Ihrem lokalen Computer aus in Azure Functions pushen.

>__Verwendung:__ Eine entsprechende Anleitung finden Sie unter [Lokale Git-Bereitstellung in Azure App Service](../app-service/deploy-local-git.md).

>__Einsatzgebiete:__ Im Allgemeinen wird empfohlen, eine andere Bereitstellungsmethode zu verwenden. Bei der Veröffentlichung über ein lokales Git müssen Sie [Trigger manuell synchronisieren](#trigger-syncing).

### <a name="cloud-sync"></a>Cloudsynchronisierung

Verwenden Sie Cloudsynchronisierung, um Ihre Inhalte aus Dropbox und OneDrive mit Azure Functions zu synchronisieren.

>__Verwendung:__ Eine entsprechende Anleitung finden Sie unter [Synchronisieren von Inhalt aus einem Cloudordner in Azure App Service](../app-service/deploy-content-sync.md).

>__Einsatzgebiete:__ Im Allgemeinen werden andere Bereitstellungsmethoden empfohlen. Bei der Veröffentlichung mit Cloudsynchronisierung müssen Sie [Trigger manuell synchronisieren](#trigger-syncing).

### <a name="ftp"></a>FTP

Die können FTP verwenden, um Dateien direkt an Azure Functions zu übertragen.

>__Verwendung:__ Eine entsprechende Anleitung finden Sie unter [Bereitstellen von Inhalten mithilfe von FTP/S](../app-service/deploy-ftp.md).

>__Einsatzgebiete:__ Im Allgemeinen werden andere Bereitstellungsmethoden empfohlen. Bei der Veröffentlichung mit FTP müssen Sie [Trigger manuell synchronisieren](#trigger-syncing).

### <a name="portal-editing"></a>Portalbearbeitung

Im portalbasierten Editor können Sie die Dateien, die sich in ihrer Funktions-App befinden, direkt bearbeiten (im Wesentlichen erfolgt die Bereitstellung bei jedem Speichern der Änderungen).

>__Verwendung:__ Wenn Sie Ihre Funktionen im Azure-Portal bearbeiten möchten, müssen [Ihre Funktionen im Portal erstellt](functions-create-first-azure-function.md) worden sein. Bei Verwendung einer anderen Bereitstellungsmethode wird Ihre Funktion schreibgeschützt und kann nicht mehr über das Portal bearbeitet werden, um eine zentrale zuverlässige Datenquelle (Single Source Of Truth, SSOT) zu gewährleisten. Sie können den Bearbeitungsmodus manuell erneut auf `Read/Write` festlegen und alle bereitstellungsbezogenen Anwendungseinstellungen (etwa `WEBSITE_RUN_FROM_PACKAGE`) entfernen, um wieder zu einem Zustand zurückzukehren, in dem Sie Ihre Dateien über das Portal bearbeiten können. 

>__Einsatzgebiete:__ Das Portal ist eine gute Möglichkeit, um erste Schritte mit Azure Functions auszuführen. Bei intensiveren Entwicklungsarbeiten empfiehlt sich die Verwendung eines der folgenden Clienttools:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools (Befehlszeile)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

Die folgende Tabelle gibt Aufschluss über die Betriebssysteme und Programmiersprachen, die Portalbearbeitung unterstützen:

| | Windows: Verbrauch | Windows Premium (Vorschau) | Windows: Dediziert | Linux: Verbrauch | Linux: Premium (Vorschau)| Linux: Dediziert |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|:---------------:|
| C# | | | | | |
| C#-Skript |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Vorschauversion) | | | | | | |
| PowerShell (Vorschauversion) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> Die Portalbearbeitung steht nur für HTTP-Trigger und Trigger mit Timer zur Verfügung (für Functions unter Linux mit dem Plan „Premium“ oder „Dediziert“).

## <a name="deployment-slots"></a>Bereitstellungsslots

Wenn Sie Ihre Funktions-App in Azure bereitstellen, können Sie als Bereitstellungsziel einen separaten Bereitstellungsslot verwenden, anstatt die Bereitstellung direkt in der Produktionsumgebung vorzunehmen. Weitere Informationen zu Bereitstellungsslots finden Sie in der Dokumentation zu [Azure Functions-Bereitstellungsslots](../app-service/deploy-staging-slots.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Bereitstellen von Funktions-Apps finden Sie in den folgenden Artikeln: 

+ [Kontinuierliche Bereitstellung für Azure Functions](functions-continuous-deployment.md)
+ [Continuous Delivery mit Azure DevOps](functions-how-to-azure-devops.md)
+ [ZIP-Bereitstellung für Azure Functions](deployment-zip-push.md)
+ [Ausführen Ihrer Azure Functions aus einem Paket](run-functions-from-deployment-package.md)
+ [Automatisieren der Ressourcenbereitstellung für Ihre Funktions-App in Azure Functions](functions-infrastructure-as-code.md)
