---
title: "Melhores práticas no Registro de Contêiner do Azure"
description: "Saiba como usar o registro de contêiner do Azure efetivamente seguindo estas melhores práticas."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: d94c6801f96ce684ebb912667dc4aa381c171216
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="best-practices-for-azure-container-registry"></a>Melhores práticas para o Registro de Contêiner do Azure

Ao seguir essas melhores práticas, você pode ajudar a maximizar o desempenho e o uso econômico de seu registro de Docker privado no Azure.

## <a name="network-close-deployment"></a>Implantação de fechamento de rede

Crie o registro de contêiner na mesma região do Azure em que você implantar contêineres. Colocar o registro em uma região de fechamento de rede para seus hosts de contêiner pode ajudar a reduzir a latência e o custo.

A implantação de fechamento de rede é um dos principais motivos para usar um registro de contêiner particular. As imagens do docker têm uma [construção de camadas](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) eficiente, que permite implantações incrementais. No entanto, os novos nós precisam efetuar o pull de todas as camadas necessárias para uma determinada imagem. Este `docker pull` inicial pode acumular até vários gigabytes. Ter um registro privado perto de sua implantação minimiza a latência de rede.
Além disso, todas as nuvens públicas, incluindo do Azure, implementam taxas de saída de rede. Extrair imagens de um datacenter para outro adiciona taxas de saída de rede, além da latência.

## <a name="geo-replicate-multi-region-deployments"></a>Replicar geograficamente implantações em várias regiões

Use o recurso de [replicação geográfica](container-registry-geo-replication.md) do Registro de Contêiner do Azure se estiver implantando contêineres em várias regiões. Se você estiver atendendo clientes globais de centros de dados locais ou se sua equipe de desenvolvimento estiver em locais diferentes, é possível simplificar o gerenciamento de registro e minimizar a latência por meio da replicação geográfica do registro. Atualmente na versão prévia, esse recurso está disponível com registros [Premium](container-registry-skus.md).

Para saber como usar a replicação geográfica, consulte o tutorial em três partes, [Replicação geográfica no Registro de Contêiner do Azure](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Namespaces do repositório

Ao aproveitar os namespaces de repositório, você pode permitir o compartilhamento de um único registro em vários grupos em sua organização. Os registros podem ser compartilhados entre equipes e implantações. O Registro de Contêiner do Azure oferece suporte a namespaces aninhados, habilitando o isolamento de grupo.

Por exemplo, considere as seguintes marcas de imagem de contêiner. Imagens que são usadas em toda a empresa, como `aspnetcore`, são colocadas no namespace raiz, no caso de imagens de contêiner que pertencem a grupos de Produção e de Marketing, cada uma usa seus próprios namespaces.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Grupo de recursos dedicados

Como os registros de contêiner são recursos que são usados em vários hosts de contêiner, um registro deve residir seu próprio grupo de recursos.

Embora seja possível fazer experiências com um tipo de host específico, como Instâncias de Contêiner do Azure, você provavelmente desejará excluir a instância de contêiner quando terminar. No entanto, você também poderá manter a coleção de imagens que enviou por push para o Registro de Contêiner do Azure. Ao colocar o registro em seu próprio grupo de recursos, você minimiza o risco de excluir acidentalmente a coleção de imagens no registro quando exclui o grupo de recursos de instância do contêiner.

## <a name="authentication"></a>Autenticação

Ao se autenticar com um registro de contêiner do Azure, há dois cenários principais: autenticação individual e autenticação de serviço (ou "sem periférico"). A tabela a seguir fornece uma visão geral sobre esses cenários e o método de autenticação recomendado para cada.

| type | Cenário de exemplo | Método recomendado |
|---|---|---|
| Identidade individual | Um desenvolvedor que efetua o pull de imagens de ou para seu computador de desenvolvimento. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| Sem periférico/serviço identidade | Pipelines de implantação e compilação em que o usuário não está diretamente envolvido. | [Entidade de serviço](container-registry-authentication.md#service-principal) |

Para obter informações detalhadas sobre a autenticação de Registro de Contêiner do Azure, consulte [Autenticar com um registro de contêiner do Azure](container-registry-authentication.md).

## <a name="manage-registry-size"></a>Gerenciar o tamanho do registro

As restrições de armazenamento de cada [SKU de registro de contêiner][container-registry-skus] devem se alinhar com um cenário típico: **Básico** para introdução, **Standard** para a maioria dos aplicativos de produção e **Premium** para o desempenho de hiperescala e [replicação geográfica][container-registry-geo-replication]. Durante a vida útil do registro, você deve gerenciar o tamanho periodicamente excluindo conteúdo não utilizado.

Você pode encontrar o uso atual de um registro na **Visão geral** do registro de contêiner no portal do Azure:

![Informações de uso do registro no portal do Azure][registry-overview-quotas]

Você pode gerenciar o tamanho do registro usando a [CLI do Azure][azure-cli] ou o [Portal do Azure][azure-portal]. Somente SKUs gerenciados (Básico, Standard e Premium) são compatíveis com a exclusão de repositórios e imagens – você não pode excluir repositórios, imagens nem marcações em um registro Clássico.

### <a name="delete-in-azure-cli"></a>Exclusão na CLI do Azure

Use o comando [az acr repository delete][az-acr-repository-delete] para excluir um repositório ou conteúdo dentro de um repositório.

Para excluir um repositório, incluindo todas as marcas e os dados da camada de imagem no repositório, especifique apenas o nome do repositório ao executar [az acr repository delete][az-acr-repository-delete]. No exemplo a seguir, excluímos o repositório *myapplication* e todas as marcações e os dados de camada de imagem no repositório:

```azurecli
az acr repository delete --name myregistry --repository myapplication
```

Você também pode excluir dados de imagem de um repositório usando os argumentos `--tag` e `--manifest`. Para obter detalhes sobre esses argumentos, consulte a [referência do comando az acr repository delete][az-acr-repository-delete].

### <a name="delete-in-azure-portal"></a>Exclusão no Portal do Azure

Para excluir um repositório de um registro no Portal do Azure, primeiro navegue para o registro de contêiner. Então, em **SERVIÇOS**, selecione **Repositórios** e clique com o botão direito do mouse no repositório que você deseja excluir. Selecione **Excluir** para excluir o repositório e as imagens do Docker que ele contém.

![Excluir um repositório no portal do Azure][delete-repository-portal]

De maneira semelhante, você também pode excluir marcações de um repositório. Navegue até o repositório, clique com o botão direito do mouse na marcação que você deseja excluir em **MARCAÇÕES**e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

O Registro de Contêiner do Azure está disponível em várias camadas, chamadas de SKUs e cada uma delas fornece funcionalidades diferentes. Para obter detalhes sobre as SKUs disponíveis, consulte [SKUs de Registro de Contêiner do Azure](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[azure-cli]: /cli/azure/overview
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
