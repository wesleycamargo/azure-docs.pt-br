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
ms.date: 09/24/2018
ms.openlocfilehash: d84040dc440c373ae9bae6dbac7a95109a387ba7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162739"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemas conhecidos e solução de problemas do serviço Azure Machine Learning
 
Este artigo ajuda a localizar e corrigir os erros ou as falhas encontrados ao usar o serviço Azure Machine Learning. 


## <a name="databricks"></a>Databricks

Problemas do Databricks e do Azure Machine Learning.

1. Recomendação de cluster do Databricks:
   
   Crie seu cluster do Azure Databricks como v4.x com Python 3. Recomendamos um cluster de alta simultaneidade.
 
1. O SDK do AML falha na instalação no Databricks quando mais pacotes são instalados.

   Alguns pacotes, como `psutil upgrade libs`, podem causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão lib. Esse problema está relacionado ao Databricks e não ao SDK do AML. Exemplo:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="gather-diagnostics-information"></a>Coletar informações de diagnóstico
Às vezes, pode ser útil fornecer informações de diagnóstico ao pedir ajuda. Este é o local em que ficam os arquivos de log:

## <a name="resource-quotas"></a>Cotas de recursos

Saiba mais sobre as [cotas de recursos](how-to-manage-quotas.md) que você pode encontrar ao trabalhar com o Azure Machine Learning.

## <a name="get-more-support"></a>Obter mais suporte

Você pode enviar solicitações de suporte e obter ajuda do suporte técnico, de fóruns e muito mais. [Saiba mais...](support-for-aml-services.md)
