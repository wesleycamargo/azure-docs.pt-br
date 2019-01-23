---
title: Problemas conhecidos e soluções
titleSuffix: Azure Machine Learning service
description: Obter uma lista dos problemas conhecidos, soluções alternativas e solução de problemas do Serviço do Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e143c0c8ef09af49aed656d479bcad4dd35e2211
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351791"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemas conhecidos e solução de problemas do serviço Azure Machine Learning
 
Este artigo ajuda a localizar e corrigir os erros ou as falhas encontrados ao usar o serviço Azure Machine Learning. 

## <a name="sdk-installation-issues"></a>Problemas de instalação do SDK

**Mensagem de erro: não é possível desinstalar 'PyYAML'** 

SDK do Azure Machine Learning para Python: O PyYAML é um projeto de distutils instalado. Portanto, não é possível determinar com precisão quais arquivos pertencem a ele no caso de uma desinstalação parcial. Para continuar a instalação do SDK ignorando esse erro, use:
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemas ao criar a computação do Azure Machine Learning
Há uma chance rara que alguns usuários que criaram seu espaço de trabalho do Machine Learning do Azure no portal do Azure antes da versão GA não consigam criar a computação do Azure Machine Learning nesse espaço de trabalho. Você pode gerar uma solicitação de suporte no serviço ou criar um novo espaço de trabalho por meio do Portal ou do SDK para desbloqueio imediato. 

## <a name="image-building-failure"></a>Falha na criação da imagem

Falha na criação da imagem ao implantar o serviço Web. Uma solução alternativa é adicionar “pynacl==1.2.1” como uma dependência de pip para o arquivo Conda da imagem de configuração.  

## <a name="fpgas"></a>FPGAs
Não será possível implantar modelos em FPGAs até que você tenha solicitado e recebido aprovação para cota de FPGA. Para solicitar acesso, preencha o formulário de solicitação de cota: https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Problemas do Databricks e do Azure Machine Learning.
 
1. O SDK do AML falha na instalação no Databricks quando mais pacotes são instalados.

   Alguns pacotes, como `psutil`, podem causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão lib. Esse problema está relacionado a Databricks e não ao SDK do Azure ML, você também pode encará-lo com outras bibliotecas. Exemplo:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
   Como alternativa, você pode usar scripts de inicialização se continuar tendo problemas de instalação com as bibliotecas de Python. Essa abordagem não é uma abordagem oficialmente com suporte. Você pode consultar [este documento](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

2. Ao usar o Machine Learning Automatizado no Databricks, se você quiser cancelar uma execução e iniciar um novo teste em execução, reinicie o seu cluster do Azure Databricks.

3. Nas configurações de ML automatizado, quando você tiver mais de dez iterações, defina show_output como False ao enviar sua execução.


## <a name="azure-portal"></a>Portal do Azure
Se você for diretamente exibir seu espaço de trabalho a partir de um link de compartilhamento do SDK ou do portal, não poderá exibir a página de Visão Geral normal com as informações de assinatura na extensão. Você também não poderá alternar para outro espaço de trabalho. Se você precisar exibir outro workspace, a solução alternativa será ir diretamente para o [portal do Azure](https://portal.azure.com) e procurar o nome do workspace.

## <a name="diagnostic-logs"></a>Logs de diagnóstico
Às vezes, pode ser útil fornecer informações de diagnóstico ao pedir ajuda. Este é o local em que ficam os arquivos de log:

## <a name="resource-quotas"></a>Cotas de recursos

Saiba mais sobre as [cotas de recursos](how-to-manage-quotas.md) que você pode encontrar ao trabalhar com o Azure Machine Learning.

## <a name="get-more-support"></a>Obter mais suporte

Você pode enviar solicitações de suporte e obter ajuda do suporte técnico, de fóruns e muito mais. [Saiba mais...](support-for-aml-services.md)
