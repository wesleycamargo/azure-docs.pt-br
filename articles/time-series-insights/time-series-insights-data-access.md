---
title: Configurar a segurança para acessar e gerenciar o Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a segurança e as permissões como políticas de gerenciamento de acesso e políticas de acesso de dados para proteger o Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: c9bddf8e20524433b31793e277efd954a5d1320e
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423367"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Conceder acesso a dados em um ambiente de Análise de Séries Temporais usando o portal do Azure

Este artigo aborda os dois tipos de políticas de acesso do Time Series Insights.

## <a name="video"></a>Vídeo: 

### <a name="in-this-video-we-cover-creating-and-managing-access-policies-within-time-series-insights-br"></a>Neste vídeo, abordaremos a criação e o gerenciamento de políticas de acesso no Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/5zTIdyHMJW8]

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

## <a name="provide-guest-access-to-a-user-from-another-aad-tenant"></a>Fornecer acesso de convidado a um usuário de outro locatário do AAD

"Convidado" não é uma função de gerenciamento, é um termo usado para uma conta que foi convidada de um locatário para outro. Depois que a conta é convidada no diretório do locatário, ela pode ter o mesmo controle de acesso aplicado que qualquer outra conta, para conceder acesso de gerenciamento a um ambiente TSI usando a folha Controle de Acesso (IAM) ou para permitir acesso aos dados no ambiente por meio da folha Políticas de Acesso a Dados. Para obter mais informações sobre o acesso de convidado do locatário do AAD, confira este [documento](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Siga estas etapas para conceder acesso de convidado a um ambiente do Time Series Insights para um usuário do AAD de outro locatário:

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Localize o seu ambiente do Time Series Insights. Digite **Time Series** na caixa **pesquisar**. Selecione **Ambiente do Time Series** nos resultados da pesquisa.

3. Selecione o seu ambiente de Análise de Séries Temporais na lista.

4. Selecione **Políticas de Acesso a Dados**, em seguida, selecione + **Convidar**.

    ![Gerenciar a fonte do Time Series Insights – convidar usuário](media/data-access/getstarted-grant-data-access6.png)

5. Forneça o email do usuário que você deseja convidar. Observe que esse email deve estar associado ao AAD. Opcionalmente, você pode incluir uma mensagem pessoal com o convite.

    ![Gerenciar a fonte das Análise de Séries Temporais - selecionar usuário](media/data-access/getstarted-grant-data-access7.png)

6. Uma bolha de confirmação deverá aparecer na tela.

    ![Gerenciar a fonte do Time Series Insights – confirmar usuário](media/data-access/getstarted-grant-data-access8.png)

7. Selecione **Selecionar usuário**. Pesquise o endereço de email do usuário convidado que você acabou de convidar, para localizar o usuário que deseja adicionar. Clique em **Selecionar** para confirmar a seleção.
  
    ![Gerenciar a fonte do Time Series Insights – confirmar usuário](media/data-access/getstarted-grant-data-access9.png)

8. Selecione **Selecionar função**. Escolha a função de acesso apropriada para o usuário convidado:

    * Selecione **Colaborador** se você desejar permitir que o usuário altere os dados de referência e compartilhe consultas e perspectivas salvas com outros usuários do ambiente.

    * Caso contrário, selecione **Leitor** para permitir que o usuário consulte dados no ambiente e salve as consultas pessoais (não compartilhadas) no ambiente.

    Selecione **OK** para confirmar a escolha da função.

    ![Gerenciar a fonte das Análise de Séries Temporais - selecionar função](media/data-access/getstarted-grant-data-access10.png)

9. Selecione **Ok** na página **Selecionar Função do Usuário**.

10. A página **Políticas de Acesso a Dados** agora lista o usuário convidado e suas funções para cada usuário convidado.

    ![Gerenciar a fonte do Time Series Insights – confirmar função](media/data-access/getstarted-grant-data-access11.png)

11. Agora o usuário convidado precisará executar determinadas etapas para acessar o ambiente localizado no locatário do Azure para o qual você o convidou. Primeiro, ele precisa aceitar o convite que você acabou de enviar. Esse convite é enviado por email para o endereço de email convidado na Etapa 5. Ele deve clicar em 'Iniciar', para aceitar.

    ![Gerenciar a fonte do Time Series Insights – convidar usuário](media/data-access/getstarted-grant-data-access12.png)

12. Em seguida, o usuário convidado precisará aceitar as permissões associadas à organização do administrador.

    ![Gerenciar a fonte do Time Series Insights – aceitar as permissões](media/data-access/getstarted-grant-data-access13.png)

13. Quando o usuário convidado for conectado ao endereço de email convidado e aceitar o convite, ele será encaminhado para insights.azure.com. Ao chegar lá, ele deverá clicar no avatar ao lado do email no canto superior direito da tela. 

    ![Gerenciar a fonte do Time Series Insights – aceitar as permissões](media/data-access/getstarted-grant-data-access14.png)

14. Em seguida, o usuário convidado selecionará seu locatário do Azure no menu suspenso de diretório. Esse é o locatário para o qual você o convidou. 

    ![Gerenciar a fonte do Time Series Insights – aceitar as permissões](media/data-access/getstarted-grant-data-access15.png)

15. Por fim, quando o usuário convidado selecionar seu locatário, ele verá o ambiente do Time Series Insights para o qual você o forneceu acesso. Agora, ele terá todas as funcionalidades associadas à função fornecida na Etapa 8.

## <a name="next-steps"></a>Próximas etapas
* Saiba [como adicionar uma fonte de evento do Hub de Eventos ao ambiente do Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
* Exibir seu ambiente no [explorador do Time Series Insights](https://insights.timeseries.azure.com).
