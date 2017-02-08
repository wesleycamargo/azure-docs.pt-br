---
title: "Gerenciar um espaço de trabalho do Machine Learning | Microsoft Docs"
description: "Gerencie o acesso aos espaços de trabalho de aprendizado de máquina do Azure e implante e gerencie serviços Web da API ML"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: c5479dd817e8929ff5e9129d4643d49758e7ab16
ms.openlocfilehash: 32e74821309507fed4a20b005dbfbba12ffb0703


---
# <a name="manage-an-azure-machine-learning-workspace"></a>Gerenciar um espaço de trabalho de Aprendizado de Máquina do Azure
> [!NOTE]
> Os procedimentos neste artigo são relevantes para serviços Web clássicos do Azure Machine Learning. Para obter informações sobre como gerenciar serviços Web no portal de serviços Web do Machine Learning, veja [Gerenciar um serviço Web usando o portal de serviços Web do Azure Machine Learning](machine-learning-manage-new-webservice.md).
> 
> 

Com o Portal Clássico do Azure, você pode gerenciar seus espaços de trabalho do Aprendizado de Máquina para:

* Monitorar como o espaço de trabalho está sendo usado
* Configurar o espaço de trabalho para permitir ou negar acesso
* Gerenciar serviços Web criados no espaço de trabalho
* Excluir o espaço de trabalho

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Além disso, a guia do painel fornece uma visão geral do uso do espaço de trabalho e uma breve explicação das informações do espaço de trabalho.  

> [!TIP]
> No Azure Machine Learning Studio, na guia **SERVIÇOS WEB**, você pode adicionar, atualizar ou excluir um serviço Web do Machine Learning.
> 
> 

Para gerenciar um espaço de trabalho:

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com/) usando sua conta do Microsoft Azure – use a conta que está associada à assinatura do Azure.
2. No painel de serviços do Microsoft Azure, clique em **APRENDIZADO DE MÁQUINA**.
3. Clique no espaço de trabalho que você deseja gerenciar.

A página do espaço de trabalho tem três guias:

* **PAINEL** - permite que você exiba o uso e as informações do espaço de trabalho
* **CONFIGURAR** - permite que você gerencie o acesso ao espaço de trabalho
* **SERVIÇOS WEB** - permite que você gerencie serviços Web que foram publicados deste espaço de trabalho

## <a name="to-monitor-how-the-workspace-is-being-used"></a>Para monitorar como o espaço de trabalho está sendo usado
Clique na guia **PAINEL** .

No painel, você pode exibir o uso geral do espaço de trabalho e ter uma visão rápida das informações do espaço de trabalho.

* O gráfico **COMPUTAR** mostra os recursos de computação que estão sendo usados pelo espaço de trabalho. Você pode alterar o modo de exibição para exibir valores relativos ou absolutos e pode alterar o período de tempo exibido no gráfico.
* **Visão geral de uso** exibe o armazenamento do Azure que está sendo usado pelo espaço de trabalho.
* **Visão rápida** fornece um resumo das informações do espaço de trabalho e links úteis.

> [!NOTE]
> O link **Entrar no Estúdio AM** abre o Estúdio de Aprendizado de Máquina usando a conta da Microsoft com a qual você está conectado no momento. A Conta da Microsoft que você usou para entrar no Portal Clássico do Azure para criar um espaço de trabalho não tem automaticamente permissão para abrir esse espaço de trabalho. Para abrir um espaço de trabalho, você deve estar conectado à Conta da Microsoft que foi definido como proprietária do espaço de trabalho ou você precisa receber um convite do proprietário para ingressar no espaço de trabalho.
> 
> 

## <a name="to-grant-or-suspend-access-for-users"></a>Para conceder ou suspender acesso para usuários
Clique na guia **CONFIGURAR** .

Na guia de configuração, você pode:

* Suspender o acesso ao espaço de trabalho do Aprendizado de Máquina clicando em NEGAR. Os usuários não poderão mais abrir o espaço de trabalho no Estúdio de Aprendizado de Máquina. Para restaurar o acesso, clique em PERMITIR.

Para gerenciar as contas adicionais quem têm acesso ao espaço de trabalho no Machine Learning Studio, clique em **Entrar no Estúdio AM** na guia **PAINEL** (consulte a observação acima sobre **Entrar no Estúdio AM**). Isso abre o espaço de trabalho no Estúdio de Aprendizado de Máquina. Daqui, clique na guia **CONFIGURAÇÕES** e, em seguida, em **USUÁRIOS**. Você pode clicar em **CONVIDAR MAIS USUÁRIOS** para dar acesso aos usuários ao espaço de trabalho, ou selecionar um usuário e clicar em **REMOVER**.

## <a name="to-manage-web-services-in-this-workspace"></a>Para gerenciar os serviços Web neste espaço de trabalho
Clique na  guia **SERVIÇOS WEB** .

Isso exibe uma lista de serviços Web publicados desse espaço de trabalho.
Para gerenciar um serviço Web, clique no nome na lista para abrir a página do serviço Web.

Um serviço Web pode ter um ou mais pontos de extremidade definidos.

* Você pode definir mais pontos de extremidade além do ponto de extremidade "Padrão". Para adicionar o ponto de extremidade, clique em **Gerenciar Pontos de Extremidade** na parte inferior do painel para abrir o portal de serviços Web do Azure Machine Learning.
* Para excluir um ponto de extremidade (você não pode excluir o ponto de extremidade "Padrão"), clique na caixa de seleção no início da linha do ponto de extremidade e clique em **EXCLUIR**. Isso remove o ponto de extremidade do serviço Web.
  
  > [!NOTE]
  > Se um aplicativo estiver usando o ponto de extremidade do serviço Web quando o ponto de extremidade for excluído, o aplicativo receberá um erro na próxima vez que tentar acessar o serviço.
  > 
  > 

Clique no nome de um ponto de extremidade de serviço Web para abri-lo. 

No painel, você pode exibir o uso geral do serviço Web em um período de tempo. Você pode selecionar o período para exibir no menu suspenso Período no canto superior direito dos gráficos de uso. O painel mostra as seguintes informações:

* **Solicitações ao longo do tempo** exibe um gráfico de etapa do número de solicitações ao longo do período selecionado. Ele pode ajudar a identificar se houver picos de uso.
* **Solicitações de solicitação-resposta** exibe o número total de chamadas de solicitação-resposta que o serviço recebeu no período de tempo selecionado e quantos deles falharam.
* **Tempo médio de computação de solicitação-resposta** exibe uma média do tempo necessário para executar as solicitações recebidas.
* **Solicitações de lote** exibe o número total de chamadas de lote que o serviço recebeu no período de tempo selecionado e quantos deles falharam.
* **Latência média de trabalho** exibe uma média do tempo necessário para executar as solicitações recebidas.
* **Erros** exibe o número total de erros que ocorreram em chamadas para o serviço Web.
* **Custos de serviços** exibe os encargos para o plano de faturamento associado ao serviço.

Na página Configurar, você pode atualizar as seguintes propriedades:

* **Descrição** permite inserir uma descrição para o serviço Web. Descrição é um campo obrigatório.
* **Registrar em log** permite habilitar ou desabilitar o registro de erros em log no ponto de extremidade. Para obter mais informações sobre Registrar em Log, veja Habilitar [registro em log de serviços Web do Machine Learning](machine-learning-web-services-logging.md).
* **Habilitar dados de Exemplo** permite que você forneça dados de exemplo que podem ser usados para testar o seu serviço de Solicitação-Resposta. Se você criou o serviço Web no Estúdio de Aprendizado de Máquina, os dados de exemplo são retirados dos dados usados para treinar seu modelo. Se você criou o serviço programaticamente, os dados foram extraídos dos dados de exemplo fornecidos como parte do pacote JSON.




<!--HONumber=Jan17_HO1-->


