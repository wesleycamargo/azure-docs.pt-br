---
title: Registrar sua assinatura do Azure no Gerenciamento de Custo do Azure | Microsoft Docs
description: Use sua assinatura do Azure para se registrar no Gerenciamento de Custos do Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: 254a7f2904a63381a4eeec02ee32b19890703ae1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296623"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Registrar uma assinatura individual do Azure e exibir dados de custo

Use sua assinatura do Azure para se registrar no Gerenciamento de Custos do Azure. O registro fornece acesso ao portal do Cloudyn. Este guia de início rápido fornece detalhes do processo de registro necessários para criar uma assinatura de avaliação do Cloudyn e entrar no portal do Cloudyn. Também mostra como começar a exibir dados de custo imediatamente.

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em http://portal.azure.com.

## <a name="register-with-azure-cost-management"></a>Registro com o Gerenciamento de Custos do Azure

1. No portal do Azure, clique em **Gerenciamento de Custo + Cobrança** na lista de serviços.
2. Em **Visão Geral**, clique em **Gerenciamento de Custo**  
    ![Página Gerenciamento de Custo](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. Na página **Gerenciamento de Custo**, clique em **Ir para Gerenciamento de Custo** para abrir a página de registro do Cloudyn em uma nova janela.
4. Na página de registro de avaliação do portal do Cloudyn, digite o nome de sua empresa e, em seguida, selecione **Proprietário da Assinatura Individual do Azure** e, em seguida, clique em **Avançar**. O nome da conta e a ID de Locatário são automaticamente adicionados ao formulário.  
    ![registro de avaliação](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Selecione a **ID da Oferta – Nome** associada à sua assinatura. Caso não tenha certeza de qual é sua ID de Taxa da assinatura, exiba sua fatura do Azure e procure **ID da Oferta**.
6. Concorde com os Termos de Uso e valide as informações e, em seguida, clique em **Avançar**.
7. Na página **Coletar dados adicionais**, clique em **Avançar** para autorizar o Cloudyn a coletar dados do recurso do Azure. Os dados coletados incluem dados de uso, desempenho, cobrança e marcação de suas assinaturas.  
    ![reunir dados adicionais](./media/quick-register-azure-sub/gather-additional.png)
8. O navegador levará você à página de entrada do Cloudyn. Entre com suas credenciais de assinatura do Azure.
9. Clique em **Ir para o Cloudyn** para abrir o portal de Cloudyn e, em seguida, na página **Gerenciamento de Contas**, você deverá ver as informações de sua conta de assinatura do Azure.  
    ![Gerenciamento de Contas](./media/quick-register-azure-sub/accounts-mgt.png)

Para assistir a um vídeo de tutorial sobre como registrar a sua assinatura do Azure, consulte [Localizar o GUID do diretório e a ID de Taxa para uso no Gerenciamento de Custos do Azure](https://youtu.be/PaRjnyaNGMI).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você usou as informações de assinatura do Azure para se registrar no Gerenciamento de Custo. Você também entrou no portal do Cloudyn e começou a exibir os dados de custo. Para saber mais sobre o Gerenciamento de Custos do Azure, continue com o tutorial sobre Gerenciamento de Custos.

> [!div class="nextstepaction"]
> [Examinar o uso e os custos](./tutorial-review-usage.md)
