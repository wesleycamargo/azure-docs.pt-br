---
title: "Configurar a segurança para acessar e gerenciar o Azure Time Series Insights | Microsoft Docs"
description: "Este artigo descreve como configurar a segurança e as permissões como políticas de gerenciamento de acesso e políticas de acesso de dados para proteger o Azure Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: c7d4079c9106226e0d07aa97c4a52c16ddb257c3
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2018
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Conceder acesso a dados em um ambiente de Análise de Séries Temporais usando o portal do Azure

Os ambientes de Análise de Séries Temporais possuem dois tipos independentes de políticas de acesso:

* Políticas de acesso de gerenciamento
* Políticas de acesso de dados

Os dois tipos de políticas concedem às entidades de segurança (usuários e aplicativos) do Azure Active Directory várias permissões em um ambiente específico. As entidades de segurança (usuários e aplicativos) devem pertencer ao active directory (conhecido como locatário do Azure) associado à assinatura que contém o ambiente.

As políticas de acesso de gerenciamento concedem permissões relacionadas à configuração do ambiente, como
*   Criação e exclusão do ambiente, origens de evento, conjuntos de dados de referência e
*   Gerenciamento das políticas de acesso de dados.

As políticas de acesso a dados concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e salvar consultas compartilhadas e perspectivas associadas ao ambiente.

Os dois tipos de políticas permitem uma separação clara entre o acesso ao gerenciamento do ambiente e o acesso aos dados dentro do ambiente. Por exemplo, é possível configurar um ambiente de modo que o proprietário/criador do ambiente seja removido do acesso aos dados. Além disso, usuários e serviços que têm permissão para ler dados do ambiente podem não receber permissão de acesso à configuração do ambiente.

## <a name="grant-data-access"></a>Conceder acesso a dados
Siga estas etapas para conceder acesso a dados para uma entidade de usuário:

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Localize o seu ambiente do Time Series Insights. Digite **Time Series** na caixa **pesquisar**. Selecione **Ambiente do Time Series** nos resultados da pesquisa. 

3. Selecione o seu ambiente de Análise de Séries Temporais na lista.
   
4. Selecione **Políticas de Acesso a Dados** e depois **+Adicionar**.
  ![Gerenciar a fonte do Time Series Insights – ambiente](media/data-access/getstarted-grant-data-access1.png)

5. Selecione **Selecionar usuário**.  Pesquise o nome de usuário ou endereço de email para localizar o usuário que deseja adicionar. Clique em **Selecionar** para confirmar a seleção. 

   ![Gerenciar a fonte das Análise de Séries Temporais - adicionar](media/data-access/getstarted-grant-data-access2.png)

6. Selecione **Selecionar função**. Escolha a função de acesso apropriada para o usuário:
   - Selecione **Colaborador** se quiser permitir que o usuário altere os dados de referência e compartilhe consultas salvas e perspectivas com outros usuários do ambiente. 
   - Caso contrário, selecione **Leitor** para permitir que o usuário consulte dados no ambiente e salve as consultas pessoais (não compartilhadas) no ambiente.

   Selecione **OK** para confirmar a escolha da função.

   ![Gerenciar a fonte das Análise de Séries Temporais - selecionar usuário](media/data-access/getstarted-grant-data-access3.png)

8. Selecione **Ok** na página **Selecionar Função do Usuário**.

   ![Gerenciar a fonte das Análise de Séries Temporais - selecionar função](media/data-access/getstarted-grant-data-access4.png)

9. A página **Políticas de Acesso de Dados** lista os usuários e suas funções.

   ![Gerenciar a fonte das Análise de Séries Temporais - resultados](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba [como adicionar uma fonte de evento do Hub de Eventos ao ambiente do Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
* Exibir seu ambiente no [explorador do Time Series Insights](https://insights.timeseries.azure.com).
