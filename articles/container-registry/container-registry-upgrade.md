---
title: "Atualizar um registro de contêiner do Azure Clássico"
description: "Aproveite o conjunto expandido de recursos dos registros de contêiner gerenciados Básico, Standard e Premium atualizando seu registro de contêiner Clássico não gerenciado."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: 19090bb69d7165c1e904450dc93b925e23e44782
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2017
---
# <a name="upgrade-a-classic-container-registry"></a>Atualizar um registro de contêiner Clássico

O ACR (Registro de Contêiner do Azure) está disponível em várias camadas de serviço, [conhecidas como SKUs](container-registry-skus.md). A versão inicial do ACR oferecia um único SKU, o Clássico, que não tinha vários recursos inerentes aos SKUs Básico, Standard e Premium (coletivamente conhecidos como registros *gerenciados*). Este artigo fornece detalhes sobre como migrar seu registro Clássico não gerenciado para um dos SKUs gerenciados para que você possa tirar proveito do seu conjunto de recursos aprimorado.

## <a name="why-upgrade"></a>Por que atualizar?

Devido às funcionalidades limitadas dos registros não gerenciados Clássicos, recomendamos a atualização de todos os registros Clássicos para registros gerenciados Básico, Standard ou Premium. Essas SKUs de nível superior integram o registro aos recursos do Azure mais profundamente.

Os registros gerenciados fornecem:

* Integração com o Azure Active Directory para [logon individual](container-registry-authentication.md#individual-login-with-azure-ad)
* Suporte a exclusão de marca e imagem
* [Replicação geográfica](container-registry-geo-replication.md)
* [Webhooks](container-registry-webhook.md)

Acima de tudo, um registro Clássico depende da conta de armazenamento que o Azure provisionou automaticamente na sua assinatura do Azure quando você criou o registro. Por outro lado, SKUs Básica, Standard e Premium SKUs se beneficiam do *armazenamento gerenciado*. Ou seja, o Azure gerencia o armazenamento das imagens para você de maneira transparente; nenhuma conta de armazenamento separada é criada na sua própria assinatura.

Um armazenamento de registro gerenciado oferece os seguintes benefícios:

* As imagens de contêiner são [criptografadas em repouso](../storage/common/storage-service-encryption.md).
* As imagens são armazenadas usando [armazenamento com redundância geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage), garantindo o backup das imagens com a replicação em várias regiões.
* Capacidade de [movimentação entre SKUs](container-registry-skus.md#changing-skus) de modo livre, permitindo uma maior taxa de transferência ao escolher um SKU de nível superior. Com cada SKU, o ACR pode atender a seus requisitos de taxa de transferência conforme a necessidade.
* O modelo de segurança unificada para o registro e seu armazenamento fornece gerenciamento de direitos simplificado. Você pode gerenciar permissões apenas para o registro de contêiner, sem a necessidade de gerenciar também permissões para uma conta de armazenamento separada.

## <a name="migration-considerations"></a>Considerações sobre a migração

Ao alterar um registro Clássico para um registro gerenciado, o Azure deve copiar todas as imagens de contêiner existentes da conta de armazenamento criada por ACR na sua assinatura para uma conta de armazenamento gerenciada pelo Azure. Dependendo do tamanho do registro, esse processo pode demorar de alguns minutos a várias horas.

Durante o processo de conversão, todas as operações `docker push` são bloqueadas, enquanto o `docker pull` continua a funcionar.

Não exclua nem modifique o conteúdo da conta de armazenamento que dá suporte ao seu registro Clássico durante o processo de conversão. Isso pode resultar na corrupção de suas imagens de contêiner.

Quando a migração é concluída, a conta de armazenamento em sua assinatura que originalmente dava suporte ao registro Clássico é mais usada pelo ACR. Depois de verificar que a migração foi bem-sucedida, considere a exclusão da conta de armazenamento para ajudar a minimizar o custo.

>[!IMPORTANT]
> A atualização do Clássico para um dos SKUs gerenciados é um **processo unidirecional**. Depois de converter um registro Clássico para Básico, Standard ou Premium, não será possível reverter para o Clássico. É possível, no entanto, mover livremente entre SKUs gerenciados com uma capacidade suficiente para o registro.

## <a name="how-to-upgrade"></a>Como atualizar

É possível atualizar um registro Clássico não gerenciado para um dos SKUs gerenciados de várias maneiras. Nas seções a seguir, descrevemos o processo para usar a [CLI do Azure][azure-cli] e o [Portal do Azure][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Atualização na CLI do Azure

Para atualizar um registro Clássico na CLI do Azure, execute o comando [az acr update][az-acr-update] e especifique o novo SKU para o registro. No exemplo a seguir, um registro Clássico denominado *myclassicregistry* é atualizado para o SKU Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Quando a migração for concluída, você verá uma saída semelhante à seguinte. Observe que o `sku` é "Premium" e o `storageAccount` é "null", o que indica que o Azure agora gerencia o armazenamento de imagens para esse registro.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Se especificar um SKU de registro gerenciado cuja capacidade máxima é menor que o tamanho do registro Clássico, você receberá uma mensagem de erro semelhante à seguinte.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Se receber um erro semelhante, execute o comando [az acr update][az-acr-update] novamente e especifique o SKU sugerido, que é o próximo SKU de nível mais alto que pode acomodar suas imagens.

## <a name="upgrade-in-azure-portal"></a>Atualizar no Portal do Azure

Ao atualizar um registro Clássico usando o Portal do Azure, o Azure seleciona automaticamente o SKU de nível mais baixo que pode acomodar suas imagens. Por exemplo, se o registro contiver 12 GiB em imagens, o Azure selecionará e converterá automaticamente o registro Clássico para o Standard (máximo de 100 GiB).

Para atualizar o registro Clássico usando o Portal do Azure, navegue até o registro de contêiner **Visão geral** e selecione **Atualizar para o registro gerenciado**.

![Botão de atualização do registro Clássico na interface do usuário do Portal do Azure][update-classic-01-upgrade]

Selecione **OK** para confirmar que você deseja atualizar um registro gerenciado.

![Confirmação da atualização do registro Clássico na interface do usuário do Portal do Azure][update-classic-02-confirm]

Durante a migração, o portal indica que o **estado de provisionamento** do registro é *Atualizando*. Como mencionado anteriormente, as operações `docker push` são desabilitadas durante a migração e você não deve excluir nem atualizar a conta de armazenamento usada pelo registro Clássico enquanto a migração estiver em andamento. Isso poderá resultar na corrupção da imagem.

![Progresso da atualização do registro Clássico na interface do usuário do Portal do Azure][update-classic-03-updating]

Quando a migração for concluída, o **Estado de provisionamento** indicará *Êxito*, e você poderá `docker push` novamente no registro.

![Estado de conclusão da atualização do registro Clássico na interface do usuário do Portal do Azure][update-classic-04-updated]

## <a name="next-steps"></a>Próximas etapas

Depois que você tiver atualizado um registro Clássico para Básico, Standard ou Premium, o Azure não usará a conta de armazenamento que originalmente deu suporte ao registro Clássico. Para reduzir o custo, considere a possibilidade de excluir a conta de armazenamento ou o contêiner de Blob dentro da conta que contém as imagens do contêiner antigo.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com