---
title: Registrar seu Contrato Enterprise do Azure no Gerenciamento de Custo do Azure | Microsoft Docs
description: Use seu Contrato Enterprise para se registrar no Gerenciamento de Custo do Azure pelo Cloudyn.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 0f157b465a9da266481be8d208fc18307cd3bb16
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registrar um Contrato Enterprise do Azure e exibir dados de custo

Use seu Contrato Enterprise do Azure para se registrar no Gerenciamento de Custo do Azure pelo Cloudyn. O registro fornece acesso ao portal do Cloudyn. Este guia de início rápido fornece detalhes do processo de registro necessários para criar uma assinatura de avaliação do Cloudyn e entrar no portal do Cloudyn. Também mostra como começar a exibir dados de custo imediatamente.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

- Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Criar um registro de avaliação

1. No portal do Azure, clique em **Gerenciamento de Custo + Cobrança** na lista de serviços.
2. Em **Visão Geral**, clique em **Gerenciamento de Custo**  
    ![Página Gerenciamento de Custo](./media/quick-register-ea/cost-mgt-billing-service.png)
3. Na página **Gerenciamento de Custo**, clique em **Ir para Gerenciamento de Custo** para abrir a página de registro do Cloudyn em uma nova janela.
4. Na página de registro de avaliação do portal do Cloudyn, digite o nome de sua empresa e, em seguida, selecione **Administrador de Registro do Azure Enterprise**.  
    ![registro de avaliação](./media/quick-register-ea/trial-reg.png)
5. Insira sua chave de API de registro do Enterprise Portal. Caso não tenha a chave em mãos, clique no link do [Enterprise Portal](https://ea.azure.com) e siga as seguintes etapas:
  1. Entre no site do Azure Enterprise e clique em **Relatórios**, clique em **Chave de Acesso da API** e, em seguida, copie a chave primária.  
    ![Chave de API do EA](./media/quick-register-ea/ea-key.png)
  3. Volte para a página de registro e cole-a em sua chave de API.
6. Concorde com os Termos de Uso e valide a chave. Clique em **Avançar** para autorizar o Cloudyn a coletar dados do recurso do Azure. Os dados coletados incluem dados de uso, desempenho, cobrança e marcação de suas assinaturas.  
    ![validação de chave](./media/quick-register-ea/ea-key-validated.png)
7. Em **Convidar outros stakeholders**, adicione usuários digitando seus endereços de email. Quando concluir, clique em **Avançar**. Leva cerca de duas horas para que todos os seus dados de cobrança sejam adicionados ao Cloudyn.
8. Clique em **Ir para o Cloudyn** para abrir o portal de Cloudyn e, em seguida, na página **Gerenciamento de Contas de Nuvem**, você deverá ver as informações de sua conta do EA registrada.

Para assistir a um vídeo de tutorial sobre como registrar seu Contrato Enterprise, consulte [Como localizar sua ID de registro de EA e chave de API para uso no Gerenciamento de Custo do Azure por Cloudyn](https://youtu.be/u_phLs_udig).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você usou as informações do Contrato Enterprise do Azure para se registrar no Gerenciamento de Custo. Você também entrou no portal do Cloudyn e começou a exibir os dados de custo. Para saber mais sobre o Gerenciamento de Custo do Azure pelo Cloudyn, continue com o tutorial sobre o Gerenciamento de Custo.

> [!div class="nextstepaction"]
> [Examinar o uso e os custos](./tutorial-review-usage.md)
