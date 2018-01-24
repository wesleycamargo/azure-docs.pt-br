---
title: "SKUs do Registro de Contêiner do Azure"
description: "Compare as diferentes camadas de serviço disponíveis no Registro de Contêiner do Azure."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: 15179fa3e3567f92a5eae69ba9a684addc3138dd
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="azure-container-registry-skus"></a>SKUs do Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) está disponível em várias camadas de serviço, conhecidas como SKUs. Essas SKUs fornecem preços previsíveis e várias opções de alinhamento aos padrões de capacidade e uso de seu registro de Docker privado no Azure.

| SKU | Gerenciada | DESCRIÇÃO |
| --- | :-------: | ----------- |
| **Básico** | sim | Um ponto de entrada de otimização de custo para desenvolvedores aprendendo sobre o Registro de Contêiner do Azure. Registros básicos têm os mesmos recursos de programação que Standard e Premium (integração da autenticação do Azure Active Directory, exclusão de imagem e ganchos Web), no entanto, há restrições de tamanho e uso. |
| **Standard** | sim | Os registros Standard oferecem os mesmos recursos que o Básico, com limites de armazenamento e taxa de transferência de imagem maiores. Registros Standard devem atender às necessidades da maioria dos cenários de produção. |
| **Premium** | sim | Os registros Premium fornecem limites mais altos nas restrições, como armazenamento e operações simultâneas, permitindo o uso em cenários de alto volume. Além de maior capacidade de taxa de transferência de imagem, o Premium adiciona recursos como [replicação geográfica][container-registry-geo-replication] para gerenciar um único registro em várias regiões, mantendo um registro de fechamento de rede para cada implantação. |
| Clássico | Não  | A SKU de registro Clássico permitiu a versão inicial do serviço Registro de Contêiner do Azure no Azure. Os registros Clássicos contam com uma conta de armazenamento que o Azure cria em sua assinatura, o que limita a capacidade do ACR de fornecer recursos de níveis mais altos, como uma maior taxa de transferência e a replicação geográfica. Devido aos recursos limitados, estamos planejando substituir a SKU Clássica no futuro. |

Escolher um SKU de nível mais alto fornece mais desempenho e escalabilidade, no entanto, todos os SKUs gerenciados fornecem os mesmos recursos de programação. Com várias camadas de serviço, pode começar com o Básico e depois mudar para o Padrão e o Premium à medida que crescer o uso do registro.

> [!NOTE]
> Por causa da substituição planejada da SKU de registro Clássico, recomendamos que você use Básico, Standard ou Premium para todos os registros novos. Para obter informações sobre como converter um registro Clássico existente, consulte [Atualizar um registro clássico][container-registry-upgrade].
>

## <a name="managed-vs-unmanaged"></a>Gerenciado e não gerenciado

Os SKUs básicos, padrão e premium são coletivamente conhecidos como registros *gerenciados* e os registros clássicos como *não gerenciados*. A principal diferença entre os dois é como as imagens de contêiner são armazenadas.

### <a name="managed-basic-standard-premium"></a>Gerenciado (básico, padrão e premium)

Os registros gerenciados têm o suporte de uma conta de Armazenamento do Microsoft Azure gerenciada pelo Azure. Ou seja, a conta de armazenamento que armazena as imagens não apareça na assinatura do Azure. O uso dos SKUs de registro gerenciado oferece vários benefícios, discutidos mais detalhadamente em [Atualizar um registro clássico][container-registry-upgrade]. Este artigo enfoca os SKUs de registro gerenciado e seus recursos.

### <a name="unmanaged-classic"></a>Não gerenciado (clássico)

Os registros clássicos são "não gerenciados" no sentido de que a conta de armazenamento que dá suporte a um registro clássico reside na *sua* assinatura do Azure. Assim, você é responsável pelo gerenciamento da conta de armazenamento na qual as imagens de contêiner são armazenadas. Com registros não gerenciados, não é possível alternar entre os SKUs conforme suas necessidades mudarem (diferente da [atualização][container-registry-upgrade] para um registro gerenciado), e vários recursos de registros gerenciados não estão disponíveis (por exemplo, exclusão de imagem de contêiner, [replicação geográfica][container-registry-geo-replication] e [webhooks][container-registry-webhook]).

Para obter mais informações sobre como atualizar um registro clássico para um dos SKUs gerenciados, consulte [Atualizar um registro clássico][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>Matriz de recurso de SKU

A tabela a seguir fornece detalhes sobre os recursos e os limites das camadas de serviço Básico, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Alterando SKUs

Você pode alterar a SKU do registro com o CLI do Azure ou no portal do Azure. Você pode trocar livremente os SKUs gerenciados, contanto que o SKU para o qual muda tenha a capacidade de armazenamento máxima necessária. Se você alternar para um dos SKUs do gerenciado do clássico, não poderá voltar para o clássico. É uma conversão sem volta.

### <a name="azure-cli"></a>CLI do Azure

Para mover entre os SKUs do CLI do Azure, use o comando [az acr update][az-acr-update]. Por exemplo, para alternar para o Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal do Azure

Na **Visão geral** do registro de contêiner no portal do Azure, selecione **Atualizar** e selecione uma nova **SKU** na lista suspensa SKU.

![Atualizar SKU do registro de contêiner no portal do Azure][update-registry-sku]

Se você tiver um registro clássico, não poderá selecionar uma SKU gerenciada no portal do Azure. Em vez disso, primeiro deve [atualizar][container-registry-upgrade] para um registro gerenciado (consulte [Como mudar do clássico](#changing-from-classic)).

## <a name="changing-from-classic"></a>Alterando do Clássico

Há considerações adicionais que se devem considerar ao migrar um registro clássico não gerenciado para os SKUs básicos, padrão ou premium gerenciados. Se o registro clássico contiver um grande número de imagens e muitos gigabytes de tamanho, o processo de migração poderá demorar um pouco. Além disso, as operações do `docker push` ficam desabilitadas até a conclusão da migração.

Para obter mais detalhes sobre como atualizar um registro clássico para um dos SKUs gerenciados, consulte [Atualizar um registro de contêiner clássico][container-registry-upgrade].

## <a name="pricing"></a>Preços

Para saber mais sobre os preços de cada SKU de Registro de Contêiner do Azure, confira [Preços do registro de contêiner][container-registry-pricing].

## <a name="next-steps"></a>Próximas etapas

**Roteiro do Registro de Contêiner do Azure**

Visite o [Roteiro do ACR][acr-roadmap] no GitHub para encontrar informações sobre os próximos recursos do serviço.

**UserVoice do Registro de Contêiner do Azure**

Envie e vote em sugestões de novos recursos no [UserVoice do ACR][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-webhook]: container-registry-webhook.md
