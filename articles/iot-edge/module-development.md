---
title: Entwickeln von Modulen für Azure IoT Edge | Microsoft-Dokumentation
description: Entwickeln von benutzerdefinierten Modellen für Azure IoT Edge zur Kommunikation mit Runtime und IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9d983dc4a2623e7f2a272ea2a320d2658d784dee
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003585"
---
# <a name="develop-your-own-iot-edge-modules"></a>Entwickeln eigener IoT Edge-Module

Azure IoT Edge-Module können mit anderen Azure-Diensten eine Verbindung herstellen und zu Ihrer größeren Clouddatenpipeline beitragen. In diesem Artikel wird beschrieben, wie Sie Module für die Kommunikation mit der IoT Edge-Runtime und IoT Hub und damit mit dem Rest der Azure-Cloud entwickeln können. 

## <a name="iot-edge-runtime-environment"></a>IoT Edge-Laufzeitumgebung
Die IoT Edge-Laufzeit bietet die Infrastruktur, um die Funktionalität mehrerer IoT Edge-Module zu integrieren und auf IoT Edge-Geräten bereitstellen. Theoretisch kann jedes Programm als IoT Edge-Modul verpackt werden. Um die Kommunikations- und Verwaltungsfunktionalitäten von IoT Edge jedoch umfassend zu nutzen, kann ein in einem Modul ausgeführtes Programm eine Verbindung mit dem lokalen IoT Edge-Hub, der in die IoT Edge-Laufzeit integriert ist, herstellen.

## <a name="using-the-iot-edge-hub"></a>Verwenden des IoT Edge-Hubs
Der IoT Edge-Hub bietet zwei Hauptfunktionalitäten: Proxy für IoT Hub und lokale Kommunikation.

### <a name="iot-hub-primitives"></a>IoT Hub-Primitive
IoT Hub behandelt eine Modulinstanz analog zu einem Gerät. Dies bedeutet Folgendes:

* Die Modulinstanz hat einen Modulzwilling, der vom [Gerätezwilling](../iot-hub/iot-hub-devguide-device-twins.md) und den anderen Modulzwillingen des Geräts getrennt und isoliert ist.
* Sie kann [Gerät-zu-Cloud-Nachrichten](../iot-hub/iot-hub-devguide-messaging.md) senden.
* Sie kann [direkte Methoden](../iot-hub/iot-hub-devguide-direct-methods.md) empfangen, die speziell an ihre Identität gerichtet sind.

Derzeit kann ein Modul keine C2D-Nachrichten empfangen und keine Dateien hochladen.

Wenn Sie ein Modul schreiben, können Sie das [Azure IoT-Geräte-SDK](../iot-hub/iot-hub-devguide-sdks.md) verwenden, um eine Verbindung mit dem IoT Edge-Hub herzustellen, und die oben beschriebene Funktionalität auf die gleiche Weise verwenden, wie IoT Hub für eine Geräteanwendung verwendet wird. Der einzige Unterschied ist, dass Sie vom Anwendungs-Back-End auf die Modulidentität statt auf die Geräteidentität verweisen müssen.

### <a name="device-to-cloud-messages"></a>D2C-Nachrichten
Um die komplexe Verarbeitung von D2C-Nachrichten zu ermöglichen, stellt der IoT Edge-Hub deklaratives Routing von Nachrichten zwischen Modulen und zwischen Modulen und IoT Hub bereit. Durch deklaratives Routing können Module Nachrichten abfangen und verarbeiten, die von anderen Modulen gesendet wurden, und in komplexen Pipelines verteilen. Weitere Informationen finden Sie unter [Bereitstellen von Modulen und Einrichten von Routen in IoT Edge](module-composition.md).

Ein IoT Edge-Modul kann im Gegensatz zu einer normalen IoT Hub-Geräteanwendung Gerät-zu-Cloud-Nachrichten empfangen, für deren Übertragung der lokale IoT Edge-Hub als Proxy fungiert, um sie zu verarbeiten.

Der IoT Edge-Hub verteilt die Nachrichten an Ihr Modul auf der Grundlage deklarativer Routen, die im [Bereitstellungsmanifest](module-composition.md) beschrieben werden. Beim Entwickeln eines IoT Edge-Moduls können Sie diese Nachrichten empfangen, indem Sie Meldungshandler festlegen.

Um die Erstellung von Routen zu vereinfachen, wird in IoT Edge das Konzept von *Eingangs*- und *Ausgangs*-Modulendpunkten eingeführt. Ein Modul kann alle an es weitergeleiteten D2C-Nachrichten empfangen, ohne einen Eingang anzugeben, und D2C-Nachrichten senden, ohne einen Ausgang anzugeben. Die Verwendung expliziter Eingänge und Ausgänge erleichtert jedoch das Verständnis der Routingregeln. 

Schließlich werden vom Edge-Hub behandelte D2C-Nachrichten mit den folgenden Eigenschaften gekennzeichnet:

| Eigenschaft | BESCHREIBUNG |
| -------- | ----------- |
| $connectionDeviceId | Die Geräte-ID des Clients, der die Nachricht gesendet hat. |
| $connectionModuleId | Die Modul-ID des Moduls, das die Nachricht gesendet hat. |
| $inputName | Der Eingang, an dem die Nachricht empfangen wurde. Kann leer sein. |
| $outputName | Der zum Senden der Nachricht verwendete Ausgang. Kann leer sein. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Herstellen einer Verbindung von einem Modul mit dem IoT Edge Hub
Die Herstellung einer Verbindung mit dem lokalen IoT Edge-Hub von einem Modul umfasst zwei Schritte: 
1. Erstellen Sie eine ModuleClient-Instanz in Ihrer Anwendung.
2. Sicherstellen, dass Ihre Anwendung das vom IoT Edge-Hub auf diesem Gerät vorgelegte Zertifikat akzeptiert.

Erstellen Sie eine ModuleClient-Instanz, um Ihr Modul mit dem IoT Edge-Hub zu verbinden, der auf dem Gerät ausgeführt wird. Dies erfolgt auf ähnliche Weise wie DeviceClient-Instanzen eine Verbindung zwischen IoT-Geräten und IoT Hub herstellen. Weitere Informationen zur ModuleClient-Klasse und ihre Kommunikationsmethoden finden Sie in der API-Referenz für Ihre bevorzugte SDK-Sprache: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable) oder [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Unterstützung für Sprache und Architektur

IOT Edge unterstützt mehrere Betriebssysteme, Gerätearchitekturen und Entwicklungssprachen, sodass Sie das Szenario erstellen können, das Ihren Anforderungen entspricht. Erfahren Sie in diesem Abschnitt, welche Optionen Ihnen zum Entwickeln von benutzerdefinierten IOT Edge-Modulen zur Verfügung stehen. Weitere Informationen zur Toolunterstützung und Anforderungen für die einzelnen Sprachen finden Sie unter [Vorbereiten Ihrer Entwicklungs- und Testumgebung für IOT Edge](development-environment.md).

### <a name="linux"></a>Linux

Für alle Sprachen in der folgenden Tabelle unterstützt IOT Edge die Entwicklung für AMD64- und ARM32-Linux-Geräte. 

| Programmier-/Entwicklungssprache | Entwicklungstools |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Die Unterstützung für das Entwickeln und Debuggen von ARM64-Linux-Geräten befindet sich in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Weitere Informationen finden Sie unter [Develop and debug ARM64 IoT Edge modules in Visual Studio Code (preview) (Entwickeln und Debuggen von ARM64 IoT Edge-Modulen in Visual Studio Code (Vorschauversion))](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Für alle Sprachen in der folgenden Tabelle unterstützt IOT Edge die Entwicklung für AMD64-Windows-Geräte.

| Programmier-/Entwicklungssprache | Entwicklungstools |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (keine Debuggingfunktionen)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Nächste Schritte

[Vorbereiten Ihrer Entwicklungs- und Testumgebung für IoT Edge](development-environment.md)

[Verwenden von Visual Studio zum Entwickeln von C#-Modulen für IoT Edge](how-to-visual-studio-develop-module.md)

[Verwenden von Visual Studio Code zum Entwickeln von Modulen für IoT Edge](how-to-vs-code-develop-module.md)

[Grundlegendes zu Azure IoT Hub-SDKs und deren Verwendung](../iot-hub/iot-hub-devguide-sdks.md)