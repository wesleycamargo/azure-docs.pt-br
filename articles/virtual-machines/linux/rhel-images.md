---
title: Imagens do Red Hat Enterprise Linux no Azure | Microsoft Docs
description: Saiba mais sobre imagens do Red Hat Enterprise Linux no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/18/2019
ms.author: borisb
ms.openlocfilehash: fd73863dacd7914e23199ba92211ea67e68f7d0d
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402142"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Imagens do Red Hat Enterprise Linux no Azure
Este artigo descreve as imagens de Red Hat Enterprise Linux (RHEL) disponíveis no Azure Marketplace, juntamente com as políticas em torno de seus nomes e retenção.

Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

>[!Important]
> Atualmente, as imagens RHEL disponíveis no Azure Marketplace dão suporte aos modelos de licenciamento BYOS (Bring-Your-Own-Subscription) ou PAYG (Pagamento Conforme o Uso). Não há suporte para o [Benefício de Uso Híbrido do Azure](../windows/hybrid-use-benefit-licensing.md) a troca dinâmica entre BYOS e PAYG. O modo de licenciamento de troca exige a reimplantação da VM com base na imagem correspondente.

>[!Note]
> Em caso de problemas relacionados a imagens RHEL na Galeria do Azure Marketplace, registre um tíquete de suporte na Microsoft.

## <a name="images-available-in-the-ui"></a>Imagens disponíveis na interface do usuário
Quando você procurar por "Red Hat" no Marketplace ou quando você cria um recurso na interface do usuário do portal do Azure, você verá um subconjunto de imagens RHEL disponíveis e os produtos relacionados do Red Hat. Você sempre pode obter o conjunto completo de imagens VM disponíveis usando a CLI/PowerShell/API do Azure.

Para ver o conjunto completo de imagens disponíveis do Red Hat no Azure, execute o seguinte comando

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Convenção de nomenclatura
Imagens de VM no Azure são organizadas por Editor, oferta, SKU e versão. A combinação de Publisher:Offer:SKU:Version é a imagem URN e identifica exclusivamente a imagem a ser usada.

Por exemplo, `RedHat:RHEL:7-RAW:7.6.2018103108` se refere a uma imagem raw particionada RHEL 7.6 compilada em 31 de outubro de 2018.

Abaixo está um exemplo de como criar uma VM do RHEL 7.6.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>O moniker "mais recente"
A API REST do Azure permite o uso do moniker "mais recente" para a versão em vez da versão específica. Usar "mais recente" provisionará a imagem mais recente disponível para determinado publicador, oferta e SKU.

Por exemplo, `RedHat:RHEL:7-RAW:latest` refere-se à imagem raw particionada da família RHEL 7 disponível.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> Em geral, a comparação de versões para determinar a versão mais recente segue as regras do [método CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Convenção de nomenclatura atual
Todas as imagens RHEL publicadas atualmente usam o modelo pago conforme o uso e estão conectadas à [atualização de infra-estrutura RHUI (Red Hat) no Azure](https://aka.ms/rhui-update). Devido a uma limitação de design do RHUI, uma nova convenção de nomenclatura tem sido adotada para as imagens de família do RHEL 7. A nomenclatura família do RHEL 6 não foi alterada no momento.

A limitação é o fato de que, quando um não-seletivo `yum update` for executado em uma VM a versão do RHEL conectada ao RHUI, é atualizada para a versão mais recente da família atual. Para saber mais, confira [este link](https://aka.ms/rhui-udate). Isso pode resultar em confusão quando uma imagem RHEL 7.2 provisionada torna-se RHEL 7.6 após uma atualização. Você ainda pode provisionar de uma imagem mais antiga, conforme ilustrado nos exemplos anteriores especificando explicitamente a versão necessária. Se a versão necessária não for especificada durante o provisionamento de uma nova imagem RHEL 7, a imagem mais recente será provisionada.

>[!NOTE]
> O RHEL para conjunto SAP de imagens, a versão do RHEL permanece fixa. Assim, sua convenção de nomenclatura inclui uma versão específica na SKU.

>[!NOTE]
> O conjunto de imagens RHEL 6 não foi movido para a nova convenção de nomenclatura.

As ofertas a seguir são SKUs que estão atualmente disponíveis para uso geral:
Oferta| SKU | Particionamento | Provisionamento | Observações
:----|:----|:-------------|:-------------|:-----
RHEL | 7-RAW | RAW | Agente do Linux | Família RHEL 7 de imagens
| | 7-LVM | LVM | Agente do Linux | Família RHEL 7 de imagens
| | 7-RAW-CI | RAW-CI | Cloud-init | Família RHEL 7 de imagens
| | 6.7 | RAW | Agente do Linux | Imagens do RHEL 6.7, convenção de nomenclatura antiga
| | 6,8 | RAW | Agente do Linux | Mesmo como acima para RHEL 6.8
| | 6.9 | RAW | Agente do Linux | Mesmo como acima para RHEL 6.9
| | 6.10 | RAW | Agente do Linux | Mesmo como acima para RHEL 6.10
| | 7,2 | RAW | Agente do Linux | Mesmo como acima para RHEL 7.2
| | 7.3 | RAW | Agente do Linux | Mesmo como acima para RHEL 7.3
| | 7.4 | RAW | Agente do Linux | Mesmo como acima para RHEL 7.4
| | 7.5 | RAW | Agente do Linux | Mesmo como acima para RHEL 7.5
RHEL-SAP | 7.4 | LVM | Agente do Linux | RHEL 7.4 para SAP HANA e aplicativos de negócios
| | 7.5 | LVM | Agente do Linux | RHEL 7.5 para SAP HANA e aplicativos de negócios
RHEL-SAP-HANA | 6.7 | RAW | Agente do Linux | RHEL 6.7 para SAP HANA
| | 7,2 | LVM | Agente do Linux | RHEL 7.2 para SAP HANA
| | 7.3 | LVM | Agente do Linux | RHEL 7.3 para SAP HANA
RHEL-SAP-APPS | 6,8 | RAW | Agente do Linux | RHEL 6.8 para SAP Business Applications
| | 7.3 | LVM | Agente do Linux | RHEL 7.3 para SAP Business Applications

### <a name="old-naming-convention"></a>Convenção de nomenclatura antiga
A família RHEL 7 de imagens e a família RHEL 6 de imagens usou versões específicas em suas SKUs até a alteração de convenção de nomenclatura explicada acima.

Você encontrará SKUs numéricas na lista de imagem completa. A Microsoft e a Red Hat criarão novas SKUs numéricas quando uma nova versão secundária for lançada.

### <a name="other-available-offers-and-skus"></a>Outras SKUs e ofertas disponíveis
A lista completa de ofertas disponíveis e SKUs pode incluir imagens adicionais além do que está listado na tabela acima, por exemplo, `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Essas ofertas podem ser usadas para fornecer suporte a soluções específicas do marketplace, ou devem ser publicadas para fins de teste e visualizações. Podem ser alteradas ou removidas a qualquer momento sem aviso. Não use, a menos que sua presença foi documentada publicamente pela Microsoft ou Red Hat.

## <a name="publishing-policy"></a>Política de publicação
A Microsoft e Red Hat atualizam as imagens quando novas versões secundárias são lançadas, conforme necessário para resolver CVEs específicos ou para alterações/atualizações ocasionais de configuração. Nos esforçamos para fornecer imagens atualizadas assim que possível, em três dias úteis após uma versão ou uma correção CVE.

Podemos atualizar somente a versão secundária atual em uma família de determinada imagem. Com o lançamento de uma versão secundária mais recente, podemos parar de atualizar a versão secundária mais antiga. Por exemplo, com o lançamento do RHEL 7.6, imagens de RHEL 7.5 não serão atualizadas.

>[!NOTE]
> As máquinas virtuais do Active Azure provisionadas das imagens com pagamento Conforme o Uso são conectadas ao RHUI do Azure e podem receber atualizações e correções assim que forem liberadas pela Red Hat e replicadas para o RHUI do Azure (geralmente em menos de 24 horas após a liberação oficial pela Red Hat). Essas VMs não exigem uma nova imagem publicada para obter as atualizações e os clientes têm controle total sobre quando iniciar a atualização.

## <a name="image-retention-policy"></a>Política de retenção de imagem
Nossa política atual é manter todas as imagens publicadas anteriormente. Nós nos reservamos ao direito de remover as imagens que são conhecidas por causar problemas de qualquer tipo. Por exemplo, imagens com configurações incorretas devido à plataforma subsequente ou atualizações de componentes, podem ser removidas. Imagens que podem ser removidas seguirão a política atual do Marketplace para fornecer notificações de até 30 dias antes da remoção da imagem.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a Infraestrutura de atualização do Red Hat do Azure [aqui](https://aka.ms/rhui-update).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).