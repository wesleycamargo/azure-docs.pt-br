---
title: Provisionar pools de Lote do Azure de imagens personalizadas | Microsoft Docs
description: "Você pode criar um pool de Lote de computação de uma imagem personalizada para provisionar nós que contêm o software e os dados que você precisa para seu aplicativo. Imagens personalizadas são uma maneira eficiente para configurar nós de computação para executar suas cargas de trabalho do Lote."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 1248aeeaa159789b008eb003c2cd7babe0432919
ms.contentlocale: pt-br
ms.lasthandoff: 08/09/2017

---

# <a name="use-a-custom-image-to-create-a-pool"></a>Usar uma imagem personalizada para criar um pool

Quando você cria um pool no Lote do Azure, você pode especificar uma imagem de VM (máquina virtual) que fornece a configuração do sistema operacional para cada nó de computação no pool. Você pode criar um pool usando uma imagem do Azure Marketplace ou fornecendo uma imagem personalizada que você preparou. Quando você fornece uma imagem personalizada, você tem controle sobre como o sistema operacional é configurado no momento em que cada nó de computação é provisionado. Sua imagem personalizada também pode incluir aplicativos e dados de referência que estão disponíveis no nó de computação, assim que ela é provisionada.

Usar uma imagem personalizada pode economizar tempo na obtenção do seu pool de nós de computação prontos para executar sua carga de trabalho do Lote. Embora você sempre possa usar uma imagem do Azure Marketplace e instalar o software em cada nó de computação depois de ele ser configurado, essa abordagem pode ser menos eficiente do que usar uma imagem personalizada. Se você precisar instalar aplicativos grandes, copiar quantidades significativas de dados ou reiniciar a VM durante o processo de instalação, considere usar uma imagem personalizada que está configurada para suas necessidades.  

## <a name="prerequisites"></a>Pré-requisitos

- **Uma conta do Lote criada com o modo de alocação de pools de Assinatura de Usuário.** Para usar uma imagem personalizada e provisionar os pools de Máquina Virtual, crie sua conta do Lote com o [modo de alocação de pools](batch-api-basics.md#pool-allocation-mode) de Assinatura de Usuário. Com esse modo, os pools do Lote são alocados para a assinatura onde reside a conta. Confira a seção [Conta](batch-api-basics.md#account) em [Desenvolver soluções de computação paralela em larga escala com o Lote](batch-api-basics.md) para saber mais sobre como definir o modo de alocação de pool ao criar uma conta do Lote.

- **Uma conta de Armazenamento do Azure.** Para criar um pool de configuração de máquina virtual usando uma imagem personalizada, você precisa de uma ou mais contas de Armazenamento do Azure Standard para armazenar as imagens VHD personalizadas. As imagens personalizadas são armazenadas como blobs. Para fazer referência a imagens personalizadas ao criar um pool, especifique os URIs dos blobs VHD de imagem personalizada para a propriedade [osDisk](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_osdisk) da propriedade [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_vmconf).

    Verifique se suas contas de armazenamento atendem aos seguintes critérios:   
    
    - As contas de armazenamento que contém os blobs VHD de imagem personalizada precisam estar na mesma assinatura que a conta do Lote (a assinatura de usuário).
    - As contas de armazenamento especificadas precisam estar na mesma região que a conta do Lote.
    - No momento, somente as contas de armazenamento padrão de uso geral têm suporte. O armazenamento Premium do Azure terá suporte no futuro.
    - Você pode especificar uma conta de armazenamento com vários blobs VHD personalizados ou várias contas de armazenamento, cada uma com um único blob. Recomendamos que você use várias contas de armazenamento para obter um melhor desempenho.
    - Um blob VHD de imagem personalizada pode dar suporte a até 40 instâncias de VM Linux ou 20 instâncias de VM Windows. Você pode criar cópias do blob VHD para criar pools com mais VMs. Por exemplo, um pool com 200 máquinas virtuais Windows precisa de 10 blobs VHD exclusivos especificados para a propriedade **osDisk**.
    
## <a name="prepare-a-custom-image"></a>Preparar uma imagem personalizada

Para preparar uma imagem personalizada para uso com o Lote, você deve generalizar uma instalação existente do Windows ou do Linux. Generalizar uma instalação de sistema operacional remove informações específicas do computador. O resultado é uma imagem que pode ser instalada em outros computadores ou VMs.  

Você pode usar uma imagem do Azure Marketplace como a imagem base para sua imagem personalizada. Para personalizar a imagem base, crie uma VM do Azure e adicione os aplicativos ou dados a ela. Em seguida, generalize a VM para servir como sua imagem personalizada.   

Para obter informações sobre como preparar imagens personalizadas do Linux de VMs do Azure, consulte [Como criar uma imagem de uma máquina virtual ou de um VHD](../virtual-machines/linux/capture-image.md). 

Para obter informações sobre como preparar imagens personalizadas do Windows de VMs do Azure, confira [Criar imagens de VM personalizadas com o Azure PowerShell](../virtual-machines/windows/tutorial-custom-images.md) e [Visão geral do Syprep (Preparação do Sistema)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). 

> [!IMPORTANT]
> Ao preparar sua imagem personalizada, lembre-se do seguinte:
> - Verifique se a imagem do sistema operacional base usada para provisionar os pools do Lote não têm extensões do Azure pré-instaladas, como a extensão Script Personalizado. Se a imagem contém uma extensão pré-instalada, o Azure pode ter problemas ao implantar a VM.
> - Verifique se a imagem do sistema operacional base fornecida usa a unidade temporária padrão, já que o agente de nó do Lote espera a unidade temporária padrão.
>
>
    
## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Criar um pool de uma imagem personalizada no portal

Para criar um pool de uma imagem personalizada usando o portal do Azure:

1. Navegue até sua conta do Lote no portal do Azure.
2. Na folha **Configurações**, selecione o item de menu **Pools**.
3. Na folha **Pools**, selecione o comando **Adicionar**; a folha **Adicionar pool** é exibida.
4. Selecione **Imagem personalizada (Windows/Linux)** na lista suspensa **Tipo de Imagem**. O portal exibe o seletor **Imagem Personalizada**. Escolha um ou mais VHDs do mesmo contêiner e clique no botão **Selecionar**. 
   O suporte para selecionar VHDs de diferentes contas de armazenamento e de diferentes contêineres será adicionado em uma versão futura.
5. Selecione a **oferta/publicador/SKU** correta para seus VHDs personalizados, selecione o modo **cache** e preencha todos os outros parâmetros do pool.
6. Para verificar se um pool é baseado em uma imagem personalizada, consulte a propriedade **Sistema operacional** na seção de resumo de recursos da folha **Pool**. O valor dessa propriedade deve ser **Imagem de VM personalizada**.
7. Todos os VHDs personalizados associados a um pool são exibidos na folha **propriedades** do pool.
 
## <a name="next-steps"></a>Próximas etapas

- Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).
- Para obter informações sobre como criar uma conta do Lote, confira [Criar uma conta do Lote com o Portal do Azure](batch-account-create-portal.md).
