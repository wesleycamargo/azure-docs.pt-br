---
title: Problemas conhecidos e solução de problemas do serviço Azure Machine Learning
description: Obter uma lista com problemas conhecidos, soluções alternativas e solução de problemas
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 02cee5a3e088c919ec94aee6f46ef6f428b9bb48
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249410"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemas conhecidos e solução de problemas do serviço Azure Machine Learning
 
Este artigo ajuda a localizar e corrigir os erros ou as falhas encontrados ao usar o serviço Azure Machine Learning. 

## <a name="sdk-installation-issues"></a>Problemas de instalação do SDK

**Mensagem de erro: não é possível desinstalar 'PyYAML'** 

O PyYAML é um projeto de distutils instalado. Portanto, não é possível determinar com precisão quais arquivos pertencem a ele no caso de uma desinstalação parcial. Para continuar a instalação do SDK ignorando esse erro, use:
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="image-building-failure"></a>Falha na criação da imagem

Falha na criação da imagem ao implantar o serviço Web. Uma solução alternativa é adicionar “pynacl==1.2.1” como uma dependência de pip para o arquivo Conda da imagem de configuração.  

## <a name="pipelines"></a>Pipelines
Ocorrerá um erro ao chamar PythonScriptStep várias vezes consecutivas sem alterar o script ou os parâmetros. Uma solução alternativa é recriar o objeto PipelineData.

## <a name="fpgas"></a>FPGAs
Não será possível implantar modelos em FPGAs até que você tenha solicitado e recebido aprovação para cota de FPGA. Para solicitar acesso, preencha o formulário de solicitação de cota: https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Problemas do Databricks e do Azure Machine Learning.

1. Recomendação de cluster do Databricks:
   
   Crie seu cluster do Azure Databricks como v4.x com Python 3. Recomendamos um cluster de alta simultaneidade.
 
1. O SDK do AML falha na instalação no Databricks quando mais pacotes são instalados.

   Alguns pacotes, como `psutil upgrade libs`, podem causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão lib. Esse problema está relacionado ao Databricks e não ao SDK do AML. Exemplo:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="diagnostic-logs"></a>Logs de diagnóstico
Às vezes, pode ser útil fornecer informações de diagnóstico ao pedir ajuda. Este é o local em que ficam os arquivos de log:

## <a name="resource-quotas"></a>Cotas de recursos

Saiba mais sobre as [cotas de recursos](how-to-manage-quotas.md) que você pode encontrar ao trabalhar com o Azure Machine Learning.

## <a name="get-more-support"></a>Obter mais suporte

Você pode enviar solicitações de suporte e obter ajuda do suporte técnico, de fóruns e muito mais. [Saiba mais...](support-for-aml-services.md)
