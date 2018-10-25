---
title: Provisionar um pool de Lote do Azure de uma imagem personalizada | Microsoft Docs
description: Crie um pool de Lote de computação de uma imagem personalizada para provisionar nós que contêm o software e os dados que você precisa para seu aplicativo. Imagens personalizadas são uma maneira eficiente para configurar nós de computação para executar suas cargas de trabalho do Lote.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 10/04/2018
ms.author: danlep
ms.openlocfilehash: 7d0526dd233afd3976b22d257300681db0bfcead
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885198"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Usar uma imagem personalizada para criar um pool de máquinas virtuais 

Ao criar um pool no Lote do Azure usando a Configuração de Máquina Virtual, você especifica uma imagem de VM que fornece o sistema operacional para cada nó de computação no pool. Você pode criar um pool de máquinas virtuais com uma imagem do Azure Marketplace compatível ou com uma imagem personalizada (uma imagem de VM que você mesmo criou e configurou). A imagem personalizada deve ser um recurso de *imagem gerenciada* na mesma assinatura e região do Azure que a conta de Lote.

## <a name="why-use-a-custom-image"></a>Por que usar uma imagem personalizada?

Quando você fornece uma imagem personalizada, tem controle sobre a configuração do sistema operacional e o tipo de sistema operacional e discos de dados a serem usados. Sua imagem personalizada pode incluir aplicativos e dados de referência ficam disponíveis em todos os nós do pool do Lote assim que eles são provisionados.

Usar uma imagem personalizada economiza tempo ao preparar os nós de computação do seu pool para executar sua carga de trabalho em Lotes. Embora você possa usar uma imagem do Azure Marketplace e instalar o software em cada nó de computação após o provisionamento, talvez seja mais eficiente usar uma imagem personalizada.

Usar uma imagem personalizada configurada para o seu cenário pode fornecer várias vantagens:

- **Configurar o SO (sistema operacional)**. Você pode personalizar a configuração do disco do sistema operacional da imagem. 
- **Pré-instalação aplicativos.** Faça a pré-instalação de aplicativos no disco do SO, o que é mais eficiente e menos propenso a que instalar aplicativos após o provisionamento de nós de computação usando uma tarefa start.
- **Economize tempo de reinicialização em VMs.** A instalação de aplicativo normalmente requer a reinicialização da VM, o que é demorado. Você pode economizar tempo de reinicialização pré-instalando aplicativos. 
- **Copie grandes quantidades de dados de uma só vez.** Torne os dados estáticos parte da imagem personalizada gerenciada copiando-os para os discos de dados de uma imagem gerenciada. Isso só precisa ser feito uma vez e disponibiliza dados para cada nó do pool.
- **Opção de tipos de disco.** Você tem a opção de usar o armazenamento premium para o disco do SO e o disco de dados.
- **Expanda os pools para tamanhos grandes.** Quando você usa uma imagem personalizada gerenciada para criar um pool, ele pode crescer sem que seja necessário que você faça cópias de VHDs do blob de imagem. 


## <a name="prerequisites"></a>Pré-requisitos

- **Um recurso de imagem gerenciada**. Para criar um pool de máquinas virtuais usando uma imagem personalizada, você precisa ter ou criar um recurso de imagem gerenciada na mesma assinatura e região do Azure que a conta do Lote. A imagem deve ser criada de instantâneos de disco do sistema operacional da VM e, opcionalmente, dos discos de dados anexados. Para obter mais informações e etapas para preparar uma imagem gerenciada, veja a seção a seguir. 
  - Use uma imagem personalizada exclusiva para cada pool que criar.
  - Para criar um pool com a imagem usando as APIs de Lote, especifique a **ID de recurso** da imagem, que tem a forma `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Para usar o portal, use o **nome** da imagem.  
  - O recurso de imagem gerenciada deve existir pelo tempo de vida do pool para permitir a expansão e pode ser removido após a exclusão do pool.

- **Autenticação do AAD (Azure Active Directory)**. A API do cliente de Lote deve usar a autenticação do AAD. O suporte ao Lote do Azure para AAD está documentado em [Autenticar soluções do serviço de Lote com o Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Preparar uma imagem personalizada

No Azure, você pode preparar uma imagem gerenciada de instantâneos de discos de SO e de dados de uma VM do Azure, de uma VM do Azure generalizada com discos gerenciados ou de um VHD local generalizado carregado por você. Para dimensionar os pools de lote de forma confiável com uma imagem personalizada, é recomendável criar uma imagem gerenciada usando *apenas* o primeiro método: usando instantâneos de discos da VM. Consulte as etapas a seguir para preparar uma VM, gerar um instantâneo e criar uma imagem do instantâneo. 

### <a name="prepare-a-vm"></a>Preparar uma VM 

Se estiver criando uma nova VM para a imagem, você poderá usar uma imagem do Azure Marketplace compatível com o Lote como a imagem base para sua imagem gerenciada, para então personalizá-la.  Para obter uma lista de referências de imagem do Azure Marketplace compatíveis com o Lote do Azure, veja a operação [Listar SKUs do agente de nó](/rest/api/batchservice/account/listnodeagentskus). Você não pode usar uma imagem de terceiros como sua imagem base.

* Verifique se a VM é criada com um disco gerenciado. Essa é a configuração de armazenamento padrão quando você cria uma VM.
* Não instale extensões do Azure, tais como a extensão de Script personalizado, na VM. Se a imagem contém uma extensão pré-instalada, o Azure pode ter problemas ao implantar a o pool do Lote.
* Verifique se a imagem do sistema operacional base que você forneceu usa unidade temporária padrão. O agente do nó de Lote no momento espera unidade temporária padrão.
* Quando a VM estiver em execução, conecte-se a ela via RDP (para Windows) ou SSH (para Linux). Instale o software necessário ou copie os dados desejados.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo da VM

Um instantâneo é uma cópia completa somente leitura de um VHD. Para criar um instantâneo de discos de dados e do SO de uma VM, você pode usar o portal do Azure ou as ferramentas de linha de comando. Para etapas e opções para criar um instantâneo, veja as diretrizes para VMs [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem de um ou mais instantâneos

Para criar uma imagem gerenciada de um instantâneo, use ferramentas de linha de comando do Azure como o comando [az image create](/cli/azure/image#az_image_create). Você pode criar uma imagem especificando um instantâneo do disco do sistema operacional e, opcionalmente, um ou mais instantâneos de disco de dados.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Criar um pool de uma imagem personalizada no portal

Após ter salvo sua imagem personalizada e saber o nome ou a ID do recurso, crie um pool do Lote dessa imagem. As etapas a seguir mostram como criar um pool do portal do Azure.

> [!NOTE]
> Se você estiver criando o pool usando uma das APIs de Lote, verifique se a identidade que você usa para autenticação do AAD tem permissões para o recurso de imagem. Consulte [Autenticar soluções do serviço do Lote no Active Directory](batch-aad-auth.md).
>

1. Navegue até sua conta do Lote no portal do Azure. Esta conta deve estar na mesma assinatura e na mesma região que o grupo de recursos que contém a imagem personalizada. 
2. Na janela **Configurações** à esquerda, selecione o item de menu **Pools**.
3. Na janela **Pools**, selecione o comando **Adicionar**.
4. Na janela **Adicionar Pool**, selecione **Imagem Personalizada (Windows/Linux)** na lista suspensa **Tipo de Imagem**. Da lista suspensa **Imagem personalizada de VM**, selecione o nome da imagem (forma abreviada da ID de recurso).
5. Selecione o **Editor/Oferta/SKU** correto para sua imagem personalizada.
6. Especifique o restante das configurações necessárias, incluindo o **Tamanho do nó**, os **Nós dedicados de destino** e os **Nós de baixa prioridade**, bem como qualquer configuração opcional desejada.

    Por exemplo, para uma imagem personalizada do Microsoft Windows Server Datacenter 2016, a janela **Adicionar Pool** aparece como mostrado abaixo:

    ![Adicionar pool de uma imagem personalizada do Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Para verificar se um pool existente é baseado em uma imagem personalizada, consulte a propriedade **Sistema Operacional** na seção de resumo de recursos da folha **Pool**. Se o pool tiver sido criado de uma imagem personalizada, ela estará definida como **Imagem de VM personalizada**.

Todas as imagens personalizadas associadas a um pool são exibidas na janela **Propriedades** do pool.

## <a name="considerations-for-large-pools"></a>Considerações sobre pools grandes

Se você planeja criar um pool com centenas de VMs ou mais usando uma imagem personalizada, é importante seguir as diretrizes anteriores para usar uma imagem criada de um instantâneo de VM.

Além disso, observe o seguinte:

- **Limites de tamanho** – o Lote limita o tamanho do pool a 2.500 nós de computação dedicados, ou 1.000 nós de baixa prioridade, quando você usa uma imagem personalizada.

  Se você usar a mesma imagem (ou várias imagens com base no mesmo instantâneo subjacente) para criar vários pools, os nós de computação totais nos pools não poderão exceder os limites anteriores. Não recomendamos o uso de uma imagem ou do respectivo instantâneo subjacente para mais de um único pool.

  Os limites poderão ser reduzidos se você configurar o pool com [pools NAT de entrada](pool-endpoint-configuration.md).

- **Tempo limite de redimensionamento** – se o pool contém um número fixo de nós (não dimensiona automaticamente), aumente propriedade resizeTimeout do pool para um valor como 20 ou 30 minutos. Se o seu pool não alcançar seu tamanho de destino dentro do período de tempo limite, execute outra [operação de redimensionamento](/rest/api/batchservice/pool/resize).

  Se você planejar um pool com mais de 300 nós de computação, você poderá precisar redimensionar o pool várias vezes para alcançar o tamanho de destino.

## <a name="next-steps"></a>Próximas etapas

- Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).
