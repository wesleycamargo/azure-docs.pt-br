---
title: "SKUs do Registro de Contêiner do Azure"
description: "Comparações entre as diferentes camadas de serviço disponíveis no Registro de Contêiner do Azure"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: mmacy
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: stevelas
ms.openlocfilehash: dae97084bdaab77efd38169cdf7e70c827b0b5ab
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2017
---
# <a name="azure-container-registry-skus"></a>SKUs do Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) está disponível em várias camadas de serviço, conhecidas como SKUs. Essas SKUs fornecem preços previsíveis e várias opções de como você deseja usar o registro de Docker privado no Azure. A escolha de uma SKU de nível mais alto oferece mais desempenho e escala. No entanto, todas as SKUs fornecem os mesmos recursos de programação, permitindo que um desenvolvedor comece com o Básico e converta para Standard e Premium conforme o uso do registro vai aumentando.

## <a name="basic"></a>Basic
Um ponto de entrada de otimização de custo para desenvolvedores aprendendo sobre o Registro de Contêiner do Azure. Registros básicos têm os mesmos recursos de programação que Standard e Premium (integração da autenticação do Azure Active Directory, exclusão de imagem e ganchos Web), no entanto, há restrições de tamanho e uso.

## <a name="standard"></a>Standard
Os registros Standard oferecem os mesmos recursos que o Básico, com limites de armazenamento e taxa de transferência de imagem maiores. Registros Standard devem atender às necessidades da maioria dos cenários de produção.

## <a name="premium"></a>Premium
Os registros Premium fornecem limites mais altos nas restrições, como armazenamento e operações simultâneas, permitindo o uso em cenários de alto volume. Além de maior capacidade de taxa de transferência de imagem, o Premium adiciona recursos como [replicação geográfica](container-registry-geo-replication.md) para gerenciar um único registro em várias regiões, mantendo um registro de fechamento de rede para cada implantação.

## <a name="classic"></a>Clássico
A SKU de registro Clássico permitiu a versão inicial do serviço Registro de Contêiner do Azure no Azure. Os registros Clássicos contam com uma conta de armazenamento que o Azure cria em sua assinatura, o que limita a capacidade do ACR de fornecer recursos de níveis mais altos, como uma maior taxa de transferência e a replicação geográfica. Devido aos recursos limitados, estamos planejando substituir a SKU Clássica no futuro.

> [!NOTE]
> Por causa da substituição planejada da SKU de registro Clássico, recomendamos que você use Básico, Standard ou Premium para todos os registros novos. Para obter informações sobre como converter um registro Clássico existente, consulte [Alterando SKUs](#changing-skus).
>

## <a name="registry-sku-feature-matrix"></a>Matriz de recurso de SKU de registro

A tabela a seguir fornece detalhes sobre os recursos e os limites das camadas de serviço Básico, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="manage-registry-size"></a>Gerenciar o tamanho do registro
As restrições de armazenamento de cada SKU devem se alinhar com um cenário típico: Básico para introdução, Standard para a maioria dos aplicativos de produção e Premium para o desempenho de hiperescala e [georeplicação](container-registry-geo-replication.md). Durante a vida útil do registro, você deve gerenciar o tamanho periodicamente excluindo conteúdo não utilizado.

Você pode encontrar o uso atual de um registro na **Visão geral** do registro de contêiner no portal do Azure:

![Informações de uso do registro no portal do Azure](media/container-registry-skus/registry-overview-quotas.png)

Você pode gerenciar o tamanho do registro excluindo repositórios no portal do Azure.

Em **SERVIÇOS**, selecione **Repositórios**, clique com o botão direito do mouse no repositório que você deseja excluir e selecione **Excluir**.

![Excluir um repositório no portal do Azure](media/container-registry-skus/delete-repository-portal.png)

## <a name="changing-skus"></a>Alterando SKUs

Você pode alterar a SKU do registro no portal do Azure.

Na **Visão geral** do registro no portal do Azure, selecione **Atualizar** e selecione uma nova **SKU** na lista suspensa SKU.

![Atualizar SKU do registro de contêiner no portal do Azure](media/container-registry-skus/update-registry-sku.png)

## <a name="changing-from-classic"></a>Alterando do Clássico
Quando você altera um registro Clássico para Básico, Standard ou Premium, o Azure copia a as imagens de contêiner da conta de armazenamento associada na sua assinatura para uma conta de armazenamento gerenciada pelo Azure. Esse processo pode levar algum tempo.

Durante a conversão, `docker pull` continua a funcionar; no entanto, `docker push` será bloqueado até que a conversão seja concluída.

Depois de concluído, a conta de armazenamento da assinatura não é mais usada pelo ACR.

### <a name="why-change-from-classic-to-basic-standard-or-premium"></a>Por que alterar de Clássico para Básico, Standard ou Premium?

Devido aos recursos limitados de registros Clássicos, recomendamos que você altere seus registros de Clássico para camadas Básica, Standard ou Premium. Essas SKUs de nível superior integram o registro aos recursos do Azure mais profundamente. Alguns desses recursos incluem:

* Integração com o Azure Active Directory para autenticação individual (confira [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login))
* Suporte a exclusão de marca e imagem
* [Replicação geográfica](container-registry-geo-replication.md)
* [Webhooks](container-registry-webhook.md)

Acima de tudo, um registro Clássico depende da conta de armazenamento que o Azure provisionou automaticamente na sua assinatura do Azure quando você criou o registro. Por outro lado, SKUs Básica, Standard e Premium SKUs se beneficiam do *armazenamento gerenciado*. Ou seja, o Azure gerencia o armazenamento das imagens para você de maneira transparente; nenhuma conta de armazenamento separada é criada na sua própria assinatura.

Alguns dos benefícios de armazenamento gerenciado fornecido por registros Básico, Standard e Premium:

* As imagens de contêiner são [criptografadas em repouso](../storage/common/storage-service-encryption.md).
* As imagens são armazenadas usando [armazenamento com redundância geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage), garantindo o backup das imagens com a replicação em várias regiões.
* Capacidade de [mudar entre SKUs](#changing-skus), permitindo uma maior taxa de transferência quando você escolhe uma SKU de nível superior. Com cada SKU, o ACR pode atender a seus requisitos de taxa de transferência conforme a necessidade. A implementação subjacente de como o ACR atinge a taxa de transferência desejada é expressa como *intenção* (pela escolha de SKUs superiores), sem que você precise gerenciar os detalhes da implementação.

## <a name="pricing"></a>Preços

Para saber mais sobre os preços de cada SKU de Registro de Contêiner do Azure, confira [Preços do registro de contêiner](https://azure.microsoft.com/pricing/details/container-registry/).

## <a name="next-steps"></a>Próximas etapas

**Roteiro do Registro de Contêiner do Azure**

Visite o [Roteiro do ACR](https://aka.ms/acr/roadmap) no GitHub para encontrar informações sobre os próximos recursos do serviço.

**UserVoice do Registro de Contêiner do Azure**

Envie e vote em sugestões de novos recursos no [UserVoice do ACR](https://feedback.azure.com/forums/903958-azure-container-registry).