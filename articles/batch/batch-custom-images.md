---
title: Provisionar pools de Lote do Azure de imagens personalizadas | Microsoft Docs
description: "Você pode criar um pool de Lote de computação de uma imagem personalizada para provisionar nós que contêm o software e os dados que você precisa para seu aplicativo. Imagens personalizadas são uma maneira eficiente para configurar nós de computação para executar suas cargas de trabalho do Lote."
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 10/11/2017
ms.author: v-dotren
ms.openlocfilehash: d62abd673f89fd51edba721119d1680762a60c76
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2017
---
# <a name="use-a-managed-custom-image-to-create-a-pool-of-virtual-machines"></a>Usar uma imagem personalizada gerenciada para criar um pool de máquinas virtuais 

Ao criar um pool no Lote do Azure usando a Configuração de Máquina Virtual, você especifica uma imagem de VM que fornece o sistema operacional para cada nó de computação no pool. Você pode criar um pool de máquinas virtuais com uma imagem do Azure Marketplace ou com uma imagem personalizada (uma imagem de VM que você mesmo criou e configurou). A imagem personalizada deve ser um recurso de *imagem gerenciada* na mesma assinatura e região do Azure que a conta de Lote.

## <a name="why-use-a-custom-image"></a>Por que usar uma imagem personalizada?
Quando você fornece uma imagem personalizada, tem controle sobre a configuração do sistema operacional e o tipo de sistema operacional e discos de dados a serem usados. Sua imagem personalizada pode incluir aplicativos e dados de referência ficam disponíveis em todos os nós do pool do Lote assim que eles são provisionados.

Usar uma imagem personalizada economiza tempo ao preparar os nós de computação do seu pool para executar sua carga de trabalho em Lotes. Embora você possa usar uma imagem do Azure Marketplace e instalar o software em cada nó de computação após o provisionamento, talvez seja mais eficiente usar uma imagem personalizada.

Usar uma imagem personalizada configurada para o seu cenário pode fornecer várias vantagens:

- **Configurar o SO (sistema operacional)**. Você pode executar configuração especial do sistema operacional no disco do sistema operacional da imagem personalizada. 
- **Pré-instalação aplicativos.** Você pode criar uma imagem personalizada com aplicativos pré-instalados no disco do SO, o que é mais eficiente e menos propenso a que instalar aplicativos após o provisionamento de nós de computação usando StartTask.
- **Economize tempo de reinicialização em VMs.** A instalação de aplicativo normalmente requer a reinicialização da VM, o que é demorado. Você pode economizar tempo de reinicialização pré-instalando aplicativos. 
- **Copie grandes quantidades de dados de uma só vez.** Você pode tornar dados estáticos parte da imagem personalizada gerenciada copiando-os para os discos de dados de uma imagem gerenciada. Isso só precisa ser feito uma vez e disponibiliza dados para cada nó do pool.
- **Opção de tipos de disco.** Você pode criar uma imagem personalizada de um VHD, de um disco gerenciado de uma VM do Azure, de um instantâneo de discos ou da sua própria instalação do Windows ou do Linux que você configurou. Você tem a opção de usar o armazenamento premium para o disco do SO e o disco de dados.
- **Expanda os pools para qualquer tamanho.** Quando você usar uma imagem personalizada gerenciada para criar um pool, o pool pode ser expandido para qualquer tamanho que você solicitar. Você não precisa fazer cópias de VHDs do blob de imagem para acomodar o número de VMs. 


## <a name="prerequisites"></a>Pré-requisitos

- **Um recurso de imagem gerenciada**. Para criar um pool de máquinas virtuais usando uma imagem personalizada, você precisa criar um recurso de imagem gerenciada na mesma assinatura e região do Azure que a conta do Lote. Para opções para preparar uma imagem gerenciada, consulte a seção a seguir.
- **Autenticação do AAD (Azure Active Directory)**. A API do cliente de Lote deve usar a autenticação do AAD. O suporte ao Lote do Azure para AAD está documentado em [Autenticar soluções do serviço de Lote com o Active Directory](batch-aad-auth.md).

    
## <a name="prepare-a-custom-image"></a>Preparar uma imagem personalizada
Você pode preparar uma imagem gerenciada de um VHD, de uma VM do Azure com discos gerenciados ou de um instantâneo de VM. 

Ao preparar sua imagem, tenha em mente os seguintes pontos:

* Verifique se a imagem do sistema operacional base usada para provisionar os pools do Lote não têm extensões do Azure pré-instaladas, como a extensão Script Personalizado. Se a imagem contém uma extensão pré-instalada, o Azure pode ter problemas ao implantar a VM.
* Verifique se a imagem do sistema operacional base que você forneceu usa unidade temporária padrão. O agente do nó de Lote no momento espera unidade temporária padrão.
* O recurso de imagem gerenciada referenciado por um Pool de Lote não pode ser excluído pelo tempo de vida do pool. Se o recurso de imagem gerenciada for excluído, o pool não poderá crescer mais. 

### <a name="to-create-a-managed-image"></a>Para criar uma imagem gerenciada
Você pode usar qualquer disco de sistema operacional Windows ou Linux preparado existente para criar uma imagem gerenciada. Por exemplo, se você quiser usar uma imagem local, carregue o disco local para uma conta de Armazenamento do Azure que esteja na mesma assinatura e região que sua conta do Lote usando AzCopy ou outra ferramenta de upload. Para ver as etapas detalhadas para carregar um VHD e criar uma imagem gerenciada, consulte as diretrizes para VMs [Windows](../virtual-machines/windows/upload-generalized-managed.md) ou [Linux](../virtual-machines/linux/upload-vhd.md).

Você também pode preparar uma imagem gerenciada de uma VM do Azure nova ou existente ou do instantâneo de VM. 

* Se estiver criando uma nova VM, você poderá usar uma imagem do Azure Marketplace como a imagem base para sua imagem gerenciada e então personalizá-la. 

* Se você planeja capturar a imagem usando o portal, verifique se a VM for criada com um disco gerenciado. Essa é a configuração de armazenamento padrão quando você cria uma VM.

* Quando a VM estiver em execução, conecte-se a ela via RDP (para Windows) ou SSH (para Linux). Instale o software necessário ou copie os dados desejados e, em seguida, generalize a VM.  

Para ver as etapas para generalizar uma VM do Azure e criar uma imagem gerenciada, consulte as diretrizes para VMs [Windows](../virtual-machines/windows/capture-image-resource.md) ou [Linux](../virtual-machines/linux/capture-image.md).

Dependendo de como você planeja criar um pool de Lote com a imagem, será necessário o seguinte identificador para a imagem:

* Se você planeja criar um pool com a imagem usando as APIs de Lote, a **ID de recurso** da imagem, que tem a forma `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. 
* Se você planeja usar o portal, o **nome** da imagem. 





## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Criar um pool de uma imagem personalizada no portal

Após ter salvo sua imagem personalizada e saber o nome ou a ID do recurso, você pode criar um pool do Lote dessa imagem. As etapas a seguir mostram como criar um pool do portal do Azure.

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
 
## <a name="next-steps"></a>Próximas etapas

- Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).
