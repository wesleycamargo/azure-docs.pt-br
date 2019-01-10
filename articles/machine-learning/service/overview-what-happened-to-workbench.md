---
title: O que aconteceu com o Machine Learning Workbench?
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre o que aconteceu com o aplicativo Machine Learning Workbench, o que mudou no Serviço do Azure Machine Learning e qual é o cronograma do suporte.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: ff6b61874363bbc869bd509174e58640a2487f56
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811300"
---
# <a name="whats-happening-to-machine-learning-workbench-in-azure-machine-learning-service"></a>O que está acontecendo com o Machine Learning Workbench no Serviço do Azure Machine Learning?

O aplicativo Azure Machine Learning Workbench e alguns outros recursos iniciais foram preteridos e substituídos na versão de setembro de 2018 para abrir caminho para uma arquitetura [aprimorada](concept-azure-machine-learning-architecture.md). A versão contém muitas atualizações significativas solicitadas pelos comentários dos clientes para melhorar sua experiência. A funcionalidade principal de execuções de experimentos para implantação de modelo não foi alterada. Mas agora, você pode usar o robusto <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> e a [CLI do Azure](reference-azure-machine-learning-cli.md) para realizar seus pipelines e tarefas de aprendizado de máquina.  

Neste artigo, você aprenderá sobre o que mudou e como isso afeta seu trabalho preexistente com Azure Machine Learning Workbench e suas APIs.

## <a name="what-changed"></a>O que mudou?

A versão mais recente do Serviço do Azure Machine Learning inclui os seguintes recursos:
+ Um [modelo simplificado de recursos do Azure](concept-azure-machine-learning-architecture.md).
+ Uma [nova interface do usuário do portal](how-to-track-experiments.md) para gerenciar suas experiências e destinos de computação.
+ Um novo, mais abrangente <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> do Python.
+ A nova [extensão de CLI do Azure](reference-azure-machine-learning-cli.md) expandida para aprendizado de máquina.

A [arquitetura](concept-azure-machine-learning-architecture.md) foi reprojetada visando facilidade de uso. Em vez de várias contas e recursos do Azure, você só precisa de um [workspace do serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace). Você pode criar workspaces rapidamente no [Portal do Azure](quickstart-get-started.md). Usando um workspace, múltiplos usuários podem armazenar destinos de computação de treinamento e de implantação, experimentos de modelo, imagens do Docker, modelos implementados e assim por diante.

Embora existam novos clientes aprimorados da CLI e do SDK na versão atual, o aplicativo Workbench de área de trabalho propriamente dito foi preterido. Agora, você pode monitorar suas experiências no [painel do workspace no portal do Azure](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Use o painel para obter o histórico de experimentos, gerenciar as metas de computação anexadas ao seu workspace, gerenciar seus modelos e imagens do Docker e até mesmo implementar serviços da web.

## <a name="how-do-i-migrate"></a>Como faço a migração?

A maioria dos artefatos criados na versão anterior do Serviço do Azure Machine Learning é armazenada em seu próprio armazenamento local ou na nuvem. Esses artefatos já não desaparecem. Para migrar, você precisa registrar os artefatos novamente com o serviço atualizado do Aprendizado de Máquina do Azure. Saiba o que você pode migrar e como neste [artigo de migração](how-to-migrate.md).

<a name="timeline"></a>

## <a name="support-timeline"></a>Linha do tempo de suporte

Você ainda pode usar suas contas de Gerenciamento de Modelos e de Experimentação do Azure Machine Learning e o aplicativo Machine Learning Workbench após setembro de 2018. O suporte para os recursos a seguir será removido progressivamente nos três ou quatro meses que sucedem esse lançamento. Você ainda pode encontrar a documentação para os recursos antigos na [seção Recursos](../desktop-workbench/tutorial-classifying-iris-part-1.md) na parte inferior do sumário.

|Fase desativação&nbsp;|Detalhes do suporte para recursos anteriores|
|:---:|----------------|
|4 de dezembro de 2018|A capacidade de criar contas de Gerenciamento de Modelos e de Experimentação do Azure Machine Learning no portal do Azure e por meio da CLI terminou. A capacidade de criar Ambientes de Computação do Machine Learning por meio da CLI também terminou. Se você tiver uma conta existente, a CLI e o Machine Learning Workbench para desktop continuarão funcionando nesta fase.|
|9 de janeiro de 2019|O suporte para todo o restante termina nessa data. Alguns exemplos são as APIs restantes e o Machine Learning Workbench para desktop.|

[Iniciar a migração](how-to-migrate.md) hoje mesmo. Todos os recursos mais recentes estão disponíveis usando os novos <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>, [CLI](reference-azure-machine-learning-cli.md) e [portal](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>E o histórico de execução?

Os históricos de execução permanecerão acessíveis por um tempo. Quando estiver pronto para migrar para a versão atualizada do Serviço do Azure Machine Learning, você poderá exportar esses históricos de execução se desejar manter uma cópia.

Os históricos de execução são chamados de **experimentos** na versão atual. Você pode coletar os experimentos do seu modelo e explorá-los usando o SDK, a CLI ou o portal do Azure.

Painel de workspace do portal é compatível apenas com os navegadores Microsoft Edge, Chrome e Firefox:

[![Portal online](./media/overview-what-happened-to-workbench/image001.png)] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>Pode eu ainda de preparação de dados?

Seus arquivos de preparação de dados preexistentes não são portáteis para a versão mais recente, porque não temos mais o Machine Learning Workbench. No entanto, você ainda pode preparar seus dados para modelagem.  

Com conjuntos de dados de qualquer tamanho, é possível usar o [SDK de preparação de dados do Azure Machine Learning](http://aka.ms/data-prep-sdk) para preparar rapidamente seus dados antes da modelagem escrevendo código Python. 

Você pode seguir [este tutorial](tutorial-data-prep.md) para saber mais sobre como usar o SDK de Preparação de Dados do Azure Machine Learning.

## <a name="will-projects-persist"></a>Projetos persistirá?

Você não perderá nenhum código ou trabalho. Na versão mais antiga, os projetos são entidades na nuvem com um diretório local. Na versão mais recente, você anexa diretórios locais ao workspace do Serviço do Azure Machine Learning usando um arquivo de configuração local. Confira um [diagrama da arquitetura mais recente](concept-azure-machine-learning-architecture.md).

Grande parte do conteúdo do projeto já estava em seu computador local. Sendo assim, você só precisa criar um arquivo de configuração nesse diretório e referenciá-lo em seu código para se conectar ao seu workspace. Saiba como [migrar seus projetos existentes](how-to-migrate.md#projects).

Saiba como começar a usar o [Python com o SDK principal](quickstart-create-workspace-with-python.md) ou usar o [portal do Azure](quickstart-get-started.md).

## <a name="what-about-my-registered-models-and-images"></a>E quanto aos meus modelos e imagens registrados?
 
Os modelos registrados em seu registro de modelo antigo devem ser migrados para o seu novo workspace se você quiser continuar a usá-los. Para migrar seus modelos, [baixe-os e registre-os novamente](how-to-migrate.md) em seu novo workspace. 

As imagens que você criou em seu registro de imagem antigo devem ser recriadas no novo workspace para continuar a usá-las. Você pode fazer isso seguindo as seções em [Configurar e criar uma imagem](how-to-deploy-and-where.md#configureimage). 

## <a name="what-about-deployed-web-services"></a>E serviços web implantados?

Os modelos implantados como serviços Web usando sua conta de Gerenciamento de Modelos de Machine Learning funcionarão enquanto o Serviço de Contêiner do Azure for compatível. Esses serviços Web funcionarão mesmo após o término do suporte para contas de Gerenciamento de Modelos de Machine Learning. No entanto, quando o suporte para o antigo CLI termina, o mesmo acontece com sua capacidade de gerenciar esses serviços da web.

Na versão mais recente, os modelos são implantados como serviços Web em clusters das Instâncias de Contêiner do Azure ou do AKS (Serviço de Kubernetes do Azure). Você também pode implantar em FPGAs e no Azure IoT Edge. Para obter mais informações, confira o artigo [Como e onde implantar](how-to-deploy-and-where.md). Sem precisar alterar nenhum dos seus arquivos, dependências e esquemas de pontuação, você pode reimplantar seus modelos usando o novo SDK ou CLI. 

## <a name="what-about-the-old-sdk-and-cli"></a>E quanto ao SDK e à CLI antigos?

Sim, eles continuarão a funcionar até janeiro. Confira a [linha do tempo](#timeline) anterior. Recomendamos que você comece a criar seus novos experimentos e modelos com o SDK ou CLI mais recente.

Usando o novo SDK do Python na versão mais recente, você pode interagir com o Serviço do Azure Machine Learning em qualquer ambiente do Python. Saiba como instalar o <a href="https://aka.ms/aml-sdk" target="_blank">SDK mais recente</a>. Você também pode usar a [extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md) atualizada com o conjunto avançado de comandos `az ml` para interagir com o serviço em qualquer ambiente de linha de comando, incluindo o Azure Cloud Shell.

## <a name="what-about-azure-machine-learning-for-visual-studio-code"></a>E o Azure Machine Learning para Visual Studio Code?

Neste último lançamento, o Azure Machine Learning para o Visual Studio Code foi expandido e aprimorado para trabalhar com os novos recursos acima.

[![Azure Machine Learning para Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png)] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>E os pacotes de domínio?

Os pacotes de domínio de [pesquisa visual computacional, de análise de texto e de previsão](../desktop-workbench/reference-python-package-overview.md) não podem ser usados com a versão mais recente do Azure Machine Learning. No entanto, você ainda pode criar e treinar modelos de pesquisa visual computacional, de texto e de previsão com o mais recente <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> do Python do Azure Machine Learning. Para saber como migrar modelos pré-existentes construídos usando os pacotes de pesquisa visual computacional, de análise de texto e de previsão, entre em contato conosco pelo telefone [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [arquitetura mais recente para o Serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md). Experimente um dos tutoriais ou guias de início rápido:

* [O que é o Serviço do Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Início Rápido: Criar um workspace com o Python](quickstart-get-started.md)
* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md)
