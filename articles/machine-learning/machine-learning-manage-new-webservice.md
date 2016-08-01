<properties
	pageTitle="Gerenciar um novo serviço Web de Aprendizado de Máquina | Microsoft Azure"
	description="Gerencie o acesso aos espaços de trabalho de aprendizado de máquina do Azure e implante e gerencie serviços Web da API ML"
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="v-donglo"/>


# Gerenciar um novo serviço Web de Aprendizado de Máquina

> [AZURE.NOTE] Os procedimentos neste artigo são relevantes para novos serviços Web do Aprendizado de Máquina do Azure. Para obter informações sobre como gerenciar os serviços Web clássicos, veja [Gerenciar um espaço de trabalho do Aprendizado de Máquina do Azure](machine-learning-manage-workspace.md).

Usando o portal de serviços Web de Aprendizado de Máquina do Microsoft Azure, você pode gerenciar seus novos serviços Web de Aprendizado de Máquina. Você pode:

- Monitorar como o serviço Web está sendo usado.
- Configurar a descrição, atualizar as chaves para o serviço Web, atualizar sua chave de conta de armazenamento e habilitar ou desabilitar dados de exemplo.
- Excluir o serviço Web.
- Criar, excluir ou atualizar planos de cobranças.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> [AZURE.TIP] No Estúdio de Aprendizado de Máquina do Azure, na guia **SERVIÇOS WEB**, você pode adicionar, atualizar ou excluir um serviço Web do Aprendizado de Máquina.

Para gerenciar seus serviços Web:

1.	Entre no [Portal do serviço Web de Aprendizado de Máquina do Microsoft Azure](https://services.azureml.net/quickstart) usando sua conta do Microsoft Azure. Use a conta que está associada à assinatura do Azure.
2.	No menu, clique em **serviços Web**.

Isso exibe uma lista de serviços Web implantados para sua assinatura. Para gerenciar um serviço Web, clique no nome na lista para abrir a página do serviço Web.

Na página Serviços Web, você pode:

- Clicar no serviço Web para gerenciá-lo.
- Clicar no Plano de Faturamento para o serviço Web para atualizá-lo.
- Excluir um serviço Web.
- Copiar um serviço Web para outra região.
- Copiar um serviço Web e implantá-lo em outra região.

Quando você clica em um serviço Web, abre a página de início rápido do serviço Web. A página de início rápido do serviço Web tem duas opções de menu que permitem que você gerencie seu serviço Web:

- **PAINEL** -permite exibir o uso do serviço Web.
- **CONFIGURAR** - permite adicionar um texto descritivo, ativar e desativar o log de erros, atualizar a chave da conta de armazenamento associada ao serviço Web e habilitar e desabilitar dados de exemplo.

## Monitorando como o serviço Web está sendo usado

Clique na guia **PAINEL**.

No painel, você pode exibir o uso geral do serviço Web em um período de tempo. Você pode selecionar o período para exibir no menu suspenso Período no canto superior direito dos gráficos de uso. O painel mostra as seguintes informações:

- **Solicitações ao longo do tempo** exibe um gráfico de etapa do número de solicitações ao longo do período selecionado. Ele pode ajudar a identificar se houver picos de uso.
- **Solicitações de solicitação-resposta** exibe o número total de chamadas de solicitação-resposta que o serviço recebeu no período de tempo selecionado e quantos deles falharam.
- **Tempo médio de computação de solicitação-resposta** exibe uma média do tempo necessário para executar as solicitações recebidas.
- **Solicitações de lote** exibe o número total de chamadas de lote que o serviço recebeu no período de tempo selecionado e quantos deles falharam.
- **Latência média de trabalho** exibe uma média do tempo necessário para executar as solicitações recebidas.
- **Erros** exibe o número total de erros que ocorreram em chamadas para o serviço Web.
- **Custos de serviços** exibe os encargos para o plano de faturamento associado ao serviço.

## Configurando o serviço Web ##

Clique na opção de menu **CONFIGURAR**.

Você pode atualizar as seguintes propriedades:

* **Descrição** permite inserir uma descrição para o serviço Web.
* **Título** permite inserir um título para o serviço Web
* **Chaves** permite girar as chaves de API principais e secundárias.
* **Chave da conta de armazenamento** permite atualizar a chave da conta de armazenamento associada às alterações de serviço Web.
* **Habilitar dados de exemplo** permite que você forneça dados de exemplo que podem ser usados para testar o seu serviço de solicitação-resposta. Se você criou o serviço Web no Estúdio de Aprendizado de Máquina, os dados de exemplo são retirados dos dados usados para treinar seu modelo. Se você criou o serviço programaticamente, os dados foram extraídos dos dados de exemplo fornecidos como parte do pacote JSON.

## Gerenciando planos de cobranças

Clique na opção de menu **Planos** da página de início rápido de serviços Web. Você também pode clicar no plano associado com o serviço Web específico para gerenciar o plano.

* **Novo** permite que você crie um novo plano.
* **Adicionar/remover instância de plano** permite que você "escale horizontalmente "um plano existente para aumentar a capacidade.
* **Atualização/Downgrade** permite que você "escale verticalmente" um plano existente para aumentar a capacidade.
* **Excluir** permite que você exclua um plano.

Clique em um plano para exibir o painel. O painel fornece um instantâneo ou plano de uso durante um determinado período de tempo. Clique no menu suspenso **Período** na parte superior direita das informações do painel para selecionar o período de tempo.

O painel de plano fornece as seguintes informações:

* **Descrição do plano** exibe informações sobre os custos e a capacidade associada ao plano.
* **Uso do plano** exibe o número de transações e as horas de computação que serão cobradas em relação ao plano.
* **Serviços Web** exibe o número de serviços Web que estão usando este plano.
* **Principais serviços Web por chamadas** exibe os quatro principais serviços Web que estão fazendo chamadas que são cobradas em relação ao plano.
* **Principais serviços Web por horas de computação** exibe os quatro principais serviços Web que estão usando recursos de computação que são cobrados em relação ao plano.

<!---HONumber=AcomDC_0720_2016-->