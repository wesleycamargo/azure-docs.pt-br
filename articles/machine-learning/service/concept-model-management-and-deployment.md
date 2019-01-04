---
title: Gerenciar, registrar, implantar e monitorar modelos de ML
titleSuffix: Azure Machine Learning service
description: Saiba como usar os Serviços do Azure Machine Learning para implantar, gerenciar e monitorar seus modelos para melhorá-los continuamente. Você pode implantar os modelos que treinou com os Serviços do Azure Machine Learning no computador local ou de outras fontes.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 25f149ad4df43a7e5b443d6abd72be91072cb47f
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250189"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Gerenciar, implantar e monitorar modelos com os Serviços do Azure Machine Learning

Neste artigo, você pode aprender a usar os Serviços do Azure Machine Learning para implantar, gerenciar e monitorar seus modelos para melhorá-los continuamente. Você pode implantar os modelos que treinou com o Azure Machine Learning em seu computador local ou de outras fontes. 

O diagrama a seguir ilustra o fluxo de trabalho de implantação completo: [ ![Fluxo de trabalho de implantação do Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png) ](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

O fluxo de trabalho de implantação inclui as seguintes etapas:
1. **Registrar o modelo** em um registro hospedado no espaço de trabalho dos Serviços do Azure Machine Learning
1. **Registrar uma imagem** que emparelhe um modelo com um script de pontuação e suas dependências em um contêiner portátil 
1. **Implantar** a imagem como um serviço Web na nuvem ou em dispositivos de borda
1. **Monitorar e coletar dados**

Cada etapa pode ser executada independentemente ou como parte de um comando de implantação única. Além disso, você pode integrar a implantação em um **Fluxo de trabalho de CI/CD** conforme ilustrado neste gráfico.

[ !['Ciclo CI/CD (integração contínua/implantação contínua) do Azure Machine Learning'](media/concept-model-management-and-deployment/model-ci-cd.png) ](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)


## <a name="step-1-register-model"></a>Etapa 1: Registrar modelo

O registro de modelo mantém o registro de todos os modelos no espaço de trabalho dos Serviços do Azure Machine Learning.
Os modelos são identificados por nome e versão. Cada vez que você registra um modelo com o mesmo nome de um já existente, o Registro incrementa a versão. Você também pode fornecer marcas de metadados adicionais durante o registro, que podem ser usadas ao pesquisar modelos.

Você não pode excluir modelos que estão sendo usados por uma imagem.

## <a name="step-2-register-image"></a>Etapa 2: Registrar a imagem

Imagens permitem a implantação confiável de modelo, juntamente com todos os componentes necessários para usar o modelo. Uma imagem contém os seguintes itens:

* O modelo
* O mecanismo de pontuação
* O arquivo ou aplicativo de pontuação
* Quaisquer dependências necessárias para pontuar o modelo

A imagem também pode incluir componentes do SDK para registro em log e monitoramento. Os dados de logs do SDK podem ser usados para ajustar ou treinar novamente seu modelo, incluindo entrada e saída do modelo.

O Azure Machine Learning dá suporte para as estruturas mais populares, porém, em geral, qualquer estrutura que possa ser instalada com pip poderá funcionar.

Quando seu workspace foi criado, também foram criados vários outros recursos do Azure usados pelo workspace.
Todos os objetos usados para criar a imagem são armazenados na conta de Armazenamento do Azure no seu workspace. A imagem é criada e armazenada no Registro de Contêiner do Azure. Você pode fornecer as marcas de metadados adicionais durante a criação da imagem, que também são armazenadas pelo Registro de imagem e podem ser consultadas para localizar a imagem.

## <a name="step-3-deploy-image"></a>Etapa 3: Implantar a imagem

Você pode implantar imagens registradas na nuvem ou em dispositivos de borda. O processo de implantação cria os recursos necessários para monitorar, balancear carga e dimensionar seu modelo automaticamente. O acesso aos serviços implantados pode ser protegido com a autenticação baseada em certificado, fornecendo os ativos de segurança durante a implantação. Você também pode atualizar uma implantação existente para usar uma imagem mais recente.

Implantações de serviço Web também podem ser pesquisadas. Por exemplo, você pode pesquisar todas as implantações de uma imagem ou de um modelo específico.

[ ![Destinos de inferência](media/concept-model-management-and-deployment/inferencing-targets.png) ](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

Você pode implantar suas imagens nos seguintes [destinos de implantação](how-to-deploy-and-where.md) na nuvem:

* Azure Container Instance
* Serviço de Kubernetes do Azure
* Computador FPGA do Azure
* Dispositivos do Azure IoT Edge

Conforme seu serviço é implantado, a solicitação de inferência automaticamente recebe balanceamento de carga e o cluster é dimensionado para atender a quaisquer picos de demanda. A [Telemetria sobre seu serviço pode ser capturada](how-to-enable-app-insights.md) no serviço do Azure Application Insights associado ao espaço de trabalho.

## <a name="step-4-monitor-models-and-collect-data"></a>Etapa 4: Monitorar modelos e coletar dados

Um SDK para a captura de dados e registro em log de modelo está disponível para que você possa monitorar a entrada, a saída e outros dados relevantes do seu modelo. Os dados são armazenados como um blob na conta de Armazenamento do Azure para seu workspace.

Para usar o SDK com seu modelo, você pode importar o SDK para seu aplicativo ou script de pontuação. Em seguida, pode usar o SDK para registrar em log dados como parâmetros, resultados ou detalhes de entrada.

Se você decidir [habilitar a coleta de dados de modelo](how-to-enable-data-collection.md) sempre que implantar a imagem, os detalhes necessários para capturar os dados, como as credenciais para o seu armazenamento de blobs pessoal, serão provisionados automaticamente.

> [!Important]
> A Microsoft não vê os dados que você coleta do seu modelo. Os dados são enviados diretamente para a conta de Armazenamento do Azure para seu workspace.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como e em que local você pode implantar modelos](how-to-deploy-and-where.md) com o serviço do Azure Machine Learning.
