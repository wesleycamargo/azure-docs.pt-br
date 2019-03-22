---
title: SKUs do Registro de Contêiner do Azure
description: Compare as diferentes camadas de serviço disponíveis no Registro de Contêiner do Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2018
ms.author: danlep
ms.openlocfilehash: 8fc364eac619c2f23ad2db1051a9b3ffa1bd38d6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138075"
---
# <a name="azure-container-registry-skus"></a>SKUs do Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) está disponível em várias camadas de serviço, conhecidas como SKUs. Essas SKUs fornecem preços previsíveis e várias opções de alinhamento aos padrões de capacidade e uso de seu registro de Docker privado no Azure.

| SKU | Gerenciada | DESCRIÇÃO |
| --- | :-------: | ----------- |
| **Básico** | Sim | Um ponto de entrada de otimização de custo para desenvolvedores aprendendo sobre o Registro de Contêiner do Azure. Os registros Básicos têm os mesmos recursos programáticos do Standard e Premium (integração de autenticação do Azure Active Directory, exclusão de imagens e web hooks). No entanto, o armazenamento incluído e a taxa de transferência de imagem são mais apropriados para cenários de uso mais baixos. |
| **Standard** | Sim | Os registros Standard oferecem os mesmos recursos do Básico, com maior armazenamento incluído e taxa de transferência de imagem. Registros Standard devem atender às necessidades da maioria dos cenários de produção. |
| **Premium** | Sim | Os registros Premium fornecem a maior quantidade de armazenamento incluído e operações simultâneas, permitindo cenários de alto volume. Além de maior taxa de transferência de imagem, o Premium adiciona recursos, incluindo [replicação geográfica] [ container-registry-geo-replication] para gerenciar um único registro em várias regiões, [confiança (visualização)deconteúdo](container-registry-content-trust.md) para a assinatura de marca de imagem, e [firewalls e redes virtuais (versão prévia)](container-registry-vnet.md) para restringir o acesso ao registro. |
| Clássico<sup>1</sup> | Não  | Esta SKU permitiu a versão inicial do serviço Registro de Contêiner do Azure no Azure. Os registros Clássicos contam com uma conta de armazenamento que o Azure cria em sua assinatura, o que limita a capacidade do ACR de fornecer recursos de níveis mais altos, como uma maior taxa de transferência e a replicação geográfica. |

<sup>1</sup> O SKU clássico será **preterido** em **março de 2019**. Use Básico, Standard ou Premium para todos os novos registros de contêiner.

Escolher um SKU de nível mais alto fornece mais desempenho e escalabilidade, no entanto, todos os SKUs gerenciados fornecem os mesmos recursos de programação. Com várias camadas de serviço, pode começar com o Básico e depois mudar para o Padrão e o Premium à medida que crescer o uso do registro.

## <a name="managed-vs-unmanaged"></a>Gerenciado e não gerenciado

Os SKUs básicos, padrão e premium são coletivamente conhecidos como registros *gerenciados* e os registros clássicos como *não gerenciados*. A principal diferença entre os dois é como as imagens de contêiner são armazenadas.

### <a name="managed-basic-standard-premium"></a>Gerenciado (básico, padrão e premium)

Os registros gerenciados se beneficiam de armazenamento de imagens totalmente gerenciado pelo Azure. Ou seja, uma conta de armazenamento que armazena as imagens não apareça na assinatura do Azure. O uso dos SKUs de registro gerenciado oferece vários benefícios, discutidos mais detalhadamente em [Armazenamento de imagens de contêiner no Registro de Contêiner do Azure][container-registry-storage]. Este artigo enfoca os SKUs de registro gerenciado e seus recursos.

### <a name="unmanaged-classic"></a>Não gerenciado (clássico)

> [!IMPORTANT]
> O SKU clássico está sendo preterido e não estará disponível depois de março de 2019. Use Básico, Standard ou Premium para todos os novos registros.

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

Para obter detalhes sobre os preços das transferências de dados, confira [Detalhes de Preços de Largura de Banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

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
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md
