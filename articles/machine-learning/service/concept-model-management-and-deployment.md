---
title: 'MLOps: Gerenciar, implantar e monitorar modelos de ML'
titleSuffix: Azure Machine Learning service
description: 'Saiba como usar o serviço Azure Machine Learning para MLOps: implantar, gerenciar e monitorar seus modelos para melhorar continuamente-los. Você pode implantar os modelos que treinou com os Serviços do Azure Machine Learning no computador local ou de outras fontes.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 18a80af8422e30ce3e87395449fca7b5f6a73762
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025038"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Gerenciar, implantar e monitorar modelos com os Serviços do Azure Machine Learning

Neste artigo, você pode aprender a usar os Serviços do Azure Machine Learning para implantar, gerenciar e monitorar seus modelos para melhorá-los continuamente. Você pode implantar os modelos que treinou com o Azure Machine Learning em seu computador local ou de outras fontes. 

O diagrama a seguir ilustra o fluxo de trabalho de implantação completo: [![Fluxo de trabalho de implantação para o Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

O MLOps / fluxo de trabalho de implantação inclui as seguintes etapas:
1. **Registrar o modelo** em um registro hospedado no espaço de trabalho dos Serviços do Azure Machine Learning
1. **Use** o modelo em um serviço web na nuvem, em um dispositivo IoT ou para análise com o Power BI.
1. **Monitorar e coletar dados**
1. **Atualizar** uma implantação para usar uma nova imagem.

Cada etapa pode ser executada independentemente ou como parte de um único comando. Além disso, você pode criar uma **fluxo de trabalho de CI/CD** conforme ilustrado neste gráfico.

[![' Ciclo/integração contínua (CI/CD) de implantação do azure Machine Learning'](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

> [!VIDEO https://www.youtube.com/embed/0MaHb070H_8]

## <a name="step-1-register-model"></a>Etapa 1: Registrar modelo

O registro de modelo permite que você armazene e controle a versão de seus modelos na nuvem do Azure em seu workspace. O registro de modelo torna mais fácil organizar e manter o controle de seus modelos treinados.
 
Modelos registrados são identificados por nome e versão. Cada vez que você registra um modelo com o mesmo nome de um já existente, o Registro incrementa a versão. Você também pode fornecer marcas de metadados adicionais durante o registro, que podem ser usadas ao pesquisar modelos. O serviço de Azure Machine Learning dá suporte a qualquer modelo que pode ser carregado usando o Python 3.5.2 ou superior.

Você não pode excluir modelos que estão sendo usados em uma implantação ativa.

Para obter mais informações, consulte a seção sobre registro de modelos em [Implantar modelos](how-to-deploy-and-where.md#registermodel).

Para obter um exemplo de registro de um modelo armazenado no formato pickle, consulte [Tutorial: treinar um modelo de classificação de imagem](tutorial-deploy-models-with-aml.md).

## <a name="step-2-use-the-model"></a>Etapa 2: Usar o modelo

Modelos de aprendizado de máquina podem ser usados como um serviço web, em dispositivos IoT Edge, ou para a análise de serviços como o Power BI.

### <a name="web-service"></a>Serviço Web

Você pode usar seus modelos no **serviços web** destinos de computação com o seguinte:

* Azure Container Instance
* Serviço de Kubernetes do Azure

Para implantar o modelo como um serviço web, você deve fornecer o seguinte:

* O modelo ou um ensemble de modelos.
* Dependências necessárias para usar o modelo. Por exemplo, um script que aceita solicitações e invoca o modelo, as dependências de conda, etc.
* Configuração de implantação que descreve como e onde implantar o modelo.

Para obter mais informações, consulte [implantar modelos](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>Dispositivos do IoT Edge

Você pode usar modelos com dispositivos IoT por meio **módulos do Azure IoT Edge**. Módulos do IoT Edge são implantados em dispositivos de hardware, que permite que a inferência no dispositivo.

Para obter mais informações, consulte [implantar modelos](how-to-deploy-and-where.md).

### <a name="analytics"></a>Análise

Microsoft Power BI dá suporte a usando modelos de aprendizado de máquina para análise de dados. Para obter mais informações, consulte [integração do Azure Machine Learning no Power BI (visualização)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="step-3-monitor-models-and-collect-data"></a>Etapa 3: Monitorar modelos e coletar dados

Monitoramento permite que você entenda quais dados estão sendo enviados para o seu modelo e as previsões que ele retorna.

Essas informações ajudam você a entender como seu modelo está sendo usado. Os dados coletados de entrada também podem ser útil em versões futuras do treinamento do modelo.

Para obter mais informações, consulte [Como habilitar a coleta de dados de modelo](how-to-enable-data-collection.md).

## <a name="step-4-update-the-deployment"></a>Etapa 4: Atualizar a implantação

As implantações devem ser atualizadas explicitamente. Para obter mais informações, consulte a seção sobre atualização em [Implantar modelos](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como e em que local você pode implantar modelos](how-to-deploy-and-where.md) com o serviço do Azure Machine Learning. Para obter um exemplo de implantação, consulte [Tutorial: Implantar um modelo de classificação de imagem em instâncias de contêiner do Azure](tutorial-deploy-models-with-aml.md).

Saiba como criar aplicativos cliente e serviços que [Consomem um modelo implantado como um serviço Web](how-to-consume-web-service.md).
