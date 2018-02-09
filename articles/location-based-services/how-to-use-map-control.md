---
title: "Como usar o Controle de Mapeamento dos Serviços do Azure Baseados na Localização | Microsoft Docs"
description: "Saiba como usar a biblioteca Javascript do lado do cliente do Controle de Mapeamento dos Serviços do Azure Baseados na Localização."
services: location-based-services
keywords: "Não adicione ou edite palavras-chave sem consultar seu especialista em SEO."
author: philmea
ms.author: philmea
ms.date: 11/22/2017
ms.topic: article
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: 494a8308a5ed4ae37ed9561d051155e7433e6193
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>Como usar o Controle de Mapeamento dos Serviços do Azure Baseados na Localização
A biblioteca Javascript do lado do cliente do Controle de Mapeamento permite renderizar mapas e funcionalidade inserida nos Serviços do Azure Baseados na Localização em seu aplicativo Web ou móvel. 

## <a name="prerequisites"></a>pré-requisitos
Uma chave e conta dos Serviços do Azure Baseados na Localização. Para obter mais informações sobre como criar uma conta e recuperar uma chave, consulte [Como gerenciar sua conta e as chaves dos Serviços do Azure Baseados na Localização](how-to-manage-account-keys.md). 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>Criar um novo mapa em uma página da Web usando a API de Controle de Mapeamento
Você pode inserir um mapa em uma página da Web usando a biblioteca Javascript do lado do cliente do Controle de Mapeamento.

1. Crie um novo arquivo e nomeie-o como MapSearch.html.

2. Adicione as referências de origem de scirpt e de folha de estilos dos Serviços do Azure Baseados na Localização ao elemento `<head>` do arquivo:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. Para renderizar um novo mapa em seu navegador, adicione uma referência **#map** ao elemento `<style>`.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Para inicializar o controle de mapa, defina uma nova seção no corpo html e crie um script. Use sua própria chave de conta dos Serviços do Azure Baseados na Localização no script. 

    ```html
    <div id="map">
        <script>
            var LBSAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": LBSAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Abra o arquivo no seu navegador da Web e exiba o mapa renderizado.