---
title: O que aconteceu com o Azure Machine Learning Workbench? | Microsoft Docs
description: Saiba mais sobre o que aconteceu com o aplicativo Workbench, o que mudou no Azure Machine Learning e qual é o cronograma do suporte.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: d90f1791a05d575caedfdd5c696bb1e6d6c29376
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091909"
---
# <a name="what-is-happening-to-workbench-in-azure-machine-learning-preview"></a>O que está acontecendo com o Workbench no Azure Machine Learning (versão prévia)?

O aplicativo Workbench e alguns outros recursos iniciais foram descontinuados na versão de setembro de 2018 para dar espaço a uma arquitetura [aprimorada](concept-azure-machine-learning-architecture.md). A versão contém muitas atualizações significativas solicitadas pelo feedback do cliente para melhorar sua experiência. A funcionalidade principal das execuções experimentais para a implantação do modelo não foi alterada, mas agora você pode usar o <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> e [CLI](reference-azure-machine-learning-cli.md) robusto para realizar tarefas e pipelines de aprendizado de máquina.  

Neste artigo, você aprenderá sobre o que mudou e como isso afeta seu trabalho preexistente com o serviço de Aprendizado de Máquina do Azure.

## <a name="what-changed"></a>O que mudou?

A versão mais recente do serviço do Azure Machine Learning inclui:
+ Um [modelo simplificado de recursos do Azure](concept-azure-machine-learning-architecture.md)
+ [Nova interface do usuário do portal](how-to-track-experiments.md) para gerenciar suas experiências e os destinos de computação
+ Um novo, mais abrangente Python <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>
+ Um novo expandido [extensão de CLI do Azure](reference-azure-machine-learning-cli.md) para aprendizado de máquina

O [arquitetura](concept-azure-machine-learning-architecture.md) foi reprojetado com facilidade de uso em mente. Em vez de várias contas e recursos do Azure, você só precisa de um [workspace do serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace).  Você pode criar workspaces rapidamente no [Portal do Azure](quickstart-get-started.md).  Um workspace pode ser usado por vários usuários para armazenar alvos de computação de treinamento e implantação, experimentos de modelo, imagens do Docker, modelos implementados e assim por diante.

Embora existam novos clientes CLI e SDK aprimorados na liberação atual, o próprio aplicativo Workbench da área de trabalho está obsoleto. Agora, você pode monitorar suas experiências no painel do [workspace no portal da Web do Azure](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Use o painel para obter o histórico de experimentos, gerenciar as metas de computação anexadas ao seu workspace, gerenciar seus modelos e imagens do Docker e até mesmo implementar serviços da web.

## <a name="how-do-i-migrate"></a>Como faço a migração?

A maioria dos artefatos criados na versão anterior do serviço do Azure Machine Learning é armazenada em seu próprio armazenamento local ou na nuvem. Esses artefatos já não desaparecem. Para migrar, você precisa registrar os artefatos novamente com o serviço atualizado do Aprendizado de Máquina do Azure. Saiba o que você pode migrar e como neste [artigo de migração](how-to-migrate.md).

<a name="timeline"></a>

## <a name="support-timeline"></a>Linha do tempo de suporte

Você pode continuar usando suas contas de gerenciamento de experimentos e modelos, bem como o aplicativo Workbench por mais algum tempo após setembro de 2018. O suporte para os seguintes recursos será removido progressivamente nos 3-4 meses após esse lançamento. Você ainda pode encontrar a documentação para os recursos antigos na [seção Recursos](../desktop-workbench/tutorial-classifying-iris-part-1.md) na parte inferior do sumário.

|Fase|Detalhes do suporte para recursos anteriores|
|:---:|----------------|
|1|A capacidade de criar _conta de Experimentação do Azure Machine Learning_ e _conta de Gerenciamento de Modelo_ no portal do Azure e das extremidades da CLI. A capacidade de criar Ambientes de Computação ML a partir da CLI também termina. Se você tiver uma conta existente, a CLI e o Workbench de área de trabalho continuarão trabalhando nesta fase.|
|2|Suporte para todo o resto, incluindo as APIs restantes e o final do Workbench da área de trabalho nesta fase.|

[Iniciar a migração](how-to-migrate.md) hoje mesmo. Todos os recursos mais recentes estão disponíveis usando o novo <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>, [CLI](reference-azure-machine-learning-cli.md), e [portal](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>E o histórico de execução?

As histórias de execução permanecerão acessíveis por um tempo. Quando estiver pronto para migrar para a versão atualizada do serviço Azure Machine Learning, você poderá exportar esses históricos de execução se desejar manter uma cópia.

Os históricos de execução agora são chamados de _experimentos_ na versão atual. Você pode coletar os experimentos do seu modelo e explorá-los usando o SDK, o CLI ou o portal da web.

Painel de workspace do portal tem suporte nos navegadores Microsoft Edge, Chrome e Firefox apenas.

[![ Portal on-line ](./media/overview-what-happened-to-workbench/image001.png)] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>Pode eu ainda de preparação de dados?

Seus arquivos de preparação de dados preexistentes não são portáveis para a versão mais recente, já que não temos mais o Workbench. No entanto, você ainda pode preparar seus dados para modelagem.  

Com conjuntos de dados menores, é possível usar o <a href="http://aka.ms/aml-sdk" target="_blank">SDK de preparação de dados do Azure Machine Learning</a> para preparar rapidamente seus dados antes da modelagem. 

Você pode usar esse mesmo <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> para grandes conjuntos de dados ou usar o Azure Databricks para preparar conjuntos de Big Data. 

## <a name="will-projects-persist"></a>Projetos persistirá?

Você não perderá nenhum código ou trabalho. Na versão mais antiga, os projetos são entidades na nuvem com um diretório local. Na versão mais recente, você anexa diretórios locais ao workspace do serviço do Azure Machine Learning usando um arquivo de configuração local. [Veja um diagrama da arquitetura do mais recente](concept-azure-machine-learning-architecture.md).

Como grande parte do conteúdo do projeto já estava em sua máquina local, você só precisa criar um arquivo de configuração nesse diretório e referenciá-lo em seu código para se conectar ao seu workspace. [Saiba como migrar seus projetos existentes.](how-to-migrate.md#projects)

Saiba como começar a usar [no Python com o SDK principal](quickstart-get-started.md).

## <a name="what-about-my-registers-models-and-images"></a>E quanto aos meus modelos de registradores e imagens?
 
Os modelos registrados em seu registro de modelo antigo devem ser migrados para o novo workspace se você quiser continuar a usá-los. Você pode fazer isso [fazendo o download dos modelos e registrando-os novamente](how-to-migrate.md) em seu novo workspace. 

As imagens que você criou em seu registro de imagem antigo devem ser recriadas no novo workspace para continuar a usá-las. Você pode fazer isso seguindo a seção [create docker image](how-to-deploy-to-aci.md#configure-an-image). 

## <a name="what-about-deployed-web-services"></a>E serviços web implantados?

Os modelos implantados como serviços da Web usando sua conta de Modelo de Gerenciamento continuarão a funcionar enquanto o ACS (Azure Container Service) for suportado. Esses serviços da Web funcionarão mesmo após o término do suporte para contas do Modelo de Gerenciamento. No entanto, quando o suporte para o antigo CLI termina, o mesmo acontece com sua capacidade de gerenciar esses serviços da web.

Na versão mais recente, os modelos são implantados como serviços da Web em [instâncias do Contêiner do Azure](how-to-deploy-to-aci.md) (ACI) ou [clusters do Serviço do Azure Kubernetes](how-to-deploy-to-aks.md) (AKS). Você também pode [implantar em FPGAs e para o IoT edge](how-to-deploy-and-where.md). Sem precisar alterar nenhum dos seus arquivos, dependências e esquemas de pontuação, você pode reimplantar seus modelos usando o novo SDK ou CLI. 

## <a name="what-about-the-old-sdk--cli"></a>E quanto ao antigo SDK e CLI?

Sim, eles continuarão a trabalhar por um tempo (veja o [cronograma](#timeline) acima). Recomendamos que você comece a criar seus novos experimentos e modelos com o SDK e / ou CLI mais recente.

Na versão mais recente, o novo SDK do Python permite que você interaja com o serviço de Aprendizado de Máquina do Azure em qualquer ambiente do Python. Saiba como instalar o <a href="http://aka.ms/aml-sdk" target="_blank">SDK mais recente</a>.  Você também pode usar a [extensão de aprendizado de máquina atualizada do CLI do Azure](reference-azure-machine-learning-cli.md) com o conjunto avançado de comandos `az ml` para interagir com o serviço em qualquer ambiente de linha de comando, incluindo o shell da nuvem do portal do Azure.

## <a name="what-about-vs-code-tools-for-ai"></a>O que acontece com o VS Code Tools for AI?

Com este último lançamento, as Ferramentas de Código do Visual Studio (VS) para extensão AI foram expandidas e aprimoradas para trabalhar com os novos recursos acima.

[![ Ferramentas de código do Visual Studio para AI ](./media/overview-what-happened-to-workbench/vscode.png)] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>E os pacotes de domínio?

Os pacotes de domínio para [Visão Computacional, Análise de Texto e Previsão](../desktop-workbench/reference-python-package-overview.md) não podem ser usados com a versão mais recente do Aprendizado de Máquina do Azure. No entanto, você ainda pode criar e treinar modelos de visão computacional, texto e previsão com o <a href="http://aka.ms/aml-sdk" target="_blank">SDK mais recente do Azure Machine Learning</a>. Para saber como migrar modelos pré-existentes construídos usando os pacotes Computer Vision, Text Analytics e Forecasting, entre em contato conosco pelo telefone [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [a arquitetura mais recente para o serviço de Azure Machine Learning](concept-azure-machine-learning-architecture.md) e tente um dos tutoriais ou guias de início rápido:

* [O que é o serviço Azure Machine Learning](overview-what-is-azure-ml.md)
* [Início Rápido: Criar um espaço de trabalho com o Python](quickstart-get-started.md)
* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md)
