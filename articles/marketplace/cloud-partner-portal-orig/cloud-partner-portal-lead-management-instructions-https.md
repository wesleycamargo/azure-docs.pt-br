---
title: Ponto de extremidade HTTPS | Microsoft Docs
description: Configure o gerenciamento de cliente potencial para Https.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804966"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurar o gerenciamento de cliente potencial usando um ponto de extremidade HTTPS

É possível usar um ponto de extremidade HTTPS para tratar clientes potenciais do AppSource e Microsoft Azure Marketplace que podem ser gravados em um sistema CRM. Este artigo descreve como configurar o gerenciamento de cliente potencial usando o serviço de automação do Microsoft Flow.


## <a name="create-a-flow-using-microsoft-flow"></a>Criar um fluxo usando o Microsoft Flow

1.  Abra a página da Web do [Flow](https://flow.microsoft.com/). Selecione **Inscrever-se** ou selecione**Inscreva-se gratuitamente** para criar uma conta gratuita do Flow.

2.  Inscreva-se e selecione **Meus fluxos** na barra de menus.

    ![Meus fluxos](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  Selecione **Criar em branco**.

    ![Criar do zero](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  Selecione o conector **Solicitação/Resposta** e, em seguida, procure o gatilho de solicitação. 

    ![Criar do zero](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. Selecione o gatilho **Solicitação**.
    ![Gatilho de solicitação](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  Copie o **exemplo JSON** no final deste artigo no **Esquema JSON do corpo da solicitação**.

7.  Adicione uma nova etapa e escolha o sistema CRM de sua escolha com a ação para criar um novo registro. A próxima captura de tela mostra **Dynamics 365 - Criar um novo registro** como um exemplo.

    ![Criar um novo registro](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  Forneça as entradas de conexão ao conector e selecione a entidade **Clientes Potenciais**.

    ![Selecionar clientes potenciais](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  Fluxos mostram um formulário para fornecer informações de clientes potenciais. É possível mapear itens da solicitação de entrada, escolhendo adicionar conteúdo dinâmico.

    ![Adicionar conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  Mapeie os campos desejados e, em seguida, selecione **Salvar** para salvar o fluxo.

11. Uma URL de HTTP POST é criada na Solicitação. Copie essa URL e use-a como o ponto de extremidade HTTPS.

    ![URL de HTTP POST](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurar a oferta para enviar clientes potenciais ao ponto de extremidade HTTPS

Ao configurar as informações de gerenciamento de cliente potencial para a oferta, selecione **Ponto de extremidade HTTPS** do Destino do Cliente Potencial e cole a URL de HTTP POST copiada na etapa anterior.  

![Adicionar conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

Quando clientes potenciais forem gerados, a Microsoft enviará clientes potenciais ao Flow, que serão roteados para o sistema CRM que você configurou.


## <a name="json-example"></a>Exemplo JSON

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "definitions": {},
  "id": "http://example.com/example.json",
  "properties": {
    "ActionCode": {
      "id": "/properties/ActionCode",
      "type": "string"
    },
    "OfferTitle": {
      "id": "/properties/OfferTitle",
      "type": "string"
    },
    "LeadSource": {
      "id": "/properties/LeadSource",
      "type": "string"
    },
    "UserDetails": {
      "id": "/properties/UserDetails",
      "properties": {
        "Company": {
          "id": "/properties/UserDetails/properties/Company",
          "type": "string"
        },
        "Country": {
          "id": "/properties/UserDetails/properties/Country",
          "type": "string"
        },
        "Email": {
          "id": "/properties/UserDetails/properties/Email",
          "type": "string"
        },
        "FirstName": {
          "id": "/properties/UserDetails/properties/FirstName",
          "type": "string"
        },
        "LastName": {
          "id": "/properties/UserDetails/properties/LastName",
          "type": "string"
        },
        "Phone": {
          "id": "/properties/UserDetails/properties/Phone",
          "type": "string"
        },
        "Title": {
          "id": "/properties/UserDetails/properties/Title",
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "type": "object"
}
```
