---
title: O que acontece quando você adiciona os Serviços Móveis a um aplicativo do Javascript usando os Serviços Conectados do Visual Studio | Microsoft Docs
description: Descreve o que aconteceu com seu projeto dos Serviços Móveis do Azure no Visual Studio
services: mobile-services
documentationcenter: ''
author: mlhoop
manager: douge
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: JavaScript
ms.topic: article
ms.date: 07/21/2016
ms.author: mlearned

---
# O que acontece com o meu projeto do Javascript quando adiciono os Serviços Móveis do Azure usando os Serviços Conectados do Visual Studio?
## Pacote NuGet adicionado
O pacote NuGet **WindowsAzure.MobileServices.WinJS** foi instalado, incluindo a biblioteca do Serviço Móvel do Azure no arquivo `js\MobileServices.js`.

## Valores de cadeia de conexão para Serviços Móveis
Na pasta `services\mobileServices\settings`, um novo arquivo JavaScript (.js) com um **MobileServiceClient** foi gerado contendo a URL do aplicativo do serviço móvel selecionado e uma chave do aplicativo.

## Referências adicionadas ao default.html
As referências a `MobileServices.js` e o arquivo de configurações foram adicionados a `default.html`.

## Arquivos de serviços conectados adicionados
Na pasta de serviços, arquivos de configuração de Serviços Conectados foram adicionados.

<!---HONumber=AcomDC_0727_2016-->