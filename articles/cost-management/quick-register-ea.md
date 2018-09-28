---
title: Registrar seu Contrato Enterprise do Azure no Gerenciamento de Custo do Azure | Microsoft Docs
description: Use o seu Enterprise Agreement para se registrar na Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/14/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: bed1ea3785c35d1053a0ff2147c3bdd797e28581
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996616"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registrar um Contrato Enterprise do Azure e exibir dados de custo

Você usa seu contrato do Azure Enterprise para registrar-se na Cloudyn. O registro fornece acesso ao portal do Cloudyn. Este guia de início rápido fornece detalhes do processo de registro necessários para criar uma assinatura de avaliação do Cloudyn e entrar no portal do Cloudyn. Também mostra como começar a exibir dados de custo imediatamente.

O Azure Cost Management oferece funcionalidade semelhante ao Cloudyn. O Azure Cost Management é uma solução nativa de gerenciamento de custos do Azure. Ele ajuda você a analisar custos, criar e gerenciar orçamentos, exportar dados e rever e agir de acordo com recomendações de otimização para economizar dinheiro. Para obter mais informações, consulte [ Gerenciamento de custos do Azure ](overview-cost-mgt.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em http://portal.azure.com.

## <a name="register-with-cloudyn"></a>Registre-se com Cloudyn

1. No portal do Azure, clique em **Gerenciamento de Custo + Cobrança** na lista de serviços.
2. Em **Visão geral**, clique em **Cloudyn**  
    ![Página do Cloudyn](./media/quick-register-ea/cost-mgt-billing-service.png)
3. Sobre o **Cloudyn** página, **ir para o Cloudyn** para abrir a página de registro do Cloudyn em uma nova janela.
4. Na página de registro de avaliação do portal do Cloudyn, digite o nome de sua empresa e, em seguida, selecione **Administrador de Registro do Azure Enterprise**.  
    ![registro de avaliação](./media/quick-register-ea/trial-reg.png)
5. Insira sua chave de API de registro do Enterprise Portal. Caso não tenha a chave em mãos, clique no link do [Enterprise Portal](https://ea.azure.com) e siga as seguintes etapas:
  1. Entre no site do Azure Enterprise e clique em **Relatórios**, clique em **Chave de Acesso da API** e, em seguida, copie a chave primária.  
    ![Chave de API do EA](./media/quick-register-ea/ea-key.png)
  3. Volte para a página de registro e cole-a em sua chave de API.
6. Concorde com os Termos de Uso e valide a chave. Clique em **Avançar** para autorizar o Cloudyn a coletar dados do recurso do Azure. Os dados coletados incluem dados de uso, desempenho, cobrança e marcação de suas assinaturas.  
    ![validação de chave](./media/quick-register-ea/ea-key-validated.png)
7. Em **Convidar outros stakeholders**, adicione usuários digitando seus endereços de email. Quando concluir, clique em **Avançar**. Dependendo do tamanho do seu registro do Azure, pode levar até 24 horas para todos os seus dados de cobrança serem adicionados ao Cloudyn.
8. Clique em **Ir para o Cloudyn** para abrir o portal de Cloudyn e, em seguida, na página **Gerenciamento de Contas de Nuvem**, você deverá ver as informações de sua conta do EA registrada.

Para assistir a um vídeo tutorial sobre como registrar seu Enterprise Agreement, consulte [Como encontrar seu ID de inscrição EA e chave de API para usar no Cloudyn](https://youtu.be/u_phLs_udig).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou as informações de contrato Enterprise do Azure para registrar com Cloudyn. Você também entrou no portal do Cloudyn e começou a exibir os dados de custo. Para saber mais sobre a Cloudyn, continue o tutorial para o Cloudyn.

> [!div class="nextstepaction"]
> [Examinar o uso e os custos](./tutorial-review-usage.md)
