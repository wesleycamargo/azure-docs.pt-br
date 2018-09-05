---
title: Início Rápido do Microsoft Azure Data Box Disk | Microsoft Docs
description: Use este guia de início rápido para implantar rapidamente o disco do Azure Data Box no portal do Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/28/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: 596c4b15ea6ef76d4471bca6994377bf4d5ddc01
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143416"
---
# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal-preview"></a>Início Rápido: Implantar o disco do Azure Data Box usando o portal do Azure (visualização)

Este início rápido descreve como implantar o disco do Azure Data Box usando o portal do Azure. As etapas incluem como, rapidamente, criar um pedido, receber discos, descompactar, conectar e copiar dados para discos de modo que eles sejam carregados no Azure. 

Para implantação passo a passo detalhada e instruções de acompanhamento, acesse [Tutorial: solicitar disco do Azure Data Box](data-box-disk-deploy-ordered.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> O Data Box Disk está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de implantar essa solução.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Verifique se sua assinatura é habilitada para o serviço Azure Data Box. Para ativar sua assinatura para esse serviço, [Inscreva-se no serviço](http://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [http://aka.ms/azuredataboxfromdiskdocs](http://aka.ms/azuredataboxfromdiskdocs).

## <a name="order"></a>Classificar

Esta etapa leva aproximadamente 5 minutos.

1. Criar um novo recurso do Azure Data Box no portal do Azure. 
2. Selecione uma assinatura habilitada para esse serviço e escolha o tipo de transferência como **Importação**. Forneça o **País de origem** onde os dados residem e a **região de destino do Azure** para a transferência de dados.
3. Selecione **Disco do Data Box**. A capacidade máxima da solução é de 35 TB, e você pode criar vários pedidos de disco para tamanhos maiores de dados.  
4. Insira os detalhes do pedido e as informações de envio. Se o serviço estiver disponível em sua região, forneça endereços de email de notificação, examine o resumo e, em seguida, crie o pedido. 

Depois que o pedido é criado, os discos estão preparados para remessa. 

## <a name="unpack"></a>Desempacotar

Esta etapa leva aproximadamente 5 minutos.

O disco do Data Box é enviado em uma caixa expressa da UPS. Abra a caixa e verifique se a ela contém:

- 1 a 5 discos de USB embalados em plástico-bolha.
- Um cabo de conexão por disco. 
- Uma etiqueta de remessa para remessa de devolução.

## <a name="connect-and-unlock"></a>Conectar e desbloquear

Esta etapa leva aproximadamente 5 minutos.

1. Use o cabo incluído para conectar o disco em um computador Windows executando uma versão com suporte. Para obter mais informações sobre versões de sistema operacional com suporte, vá até [Requisitos de sistema do disco do Azure Data Box](data-box-disk-system-requirements.md). 
2. Para desbloquear o disco:

    1. No portal do Azure, acesse **Geral > Detalhes do Dispositivo** e obtenha a chave de acesso.
    2. Baixe e extraia a ferramenta de desbloqueio do disco do Data Box no computador usado para copiar os dados para discos. 
    3. Execute *DataBoxDiskUnlock.exe* e forneça a chave de acesso. Para qualquer nova inserção de disco, execute a ferramenta de desbloqueio novamente e forneça a chave de acesso. **Não use a caixa de diálogo BitLocker nem a chave do BitLocker para desbloquear o disco.** 
    4. A letra da unidade atribuída ao disco é exibida pela ferramenta. Anote a letra da unidade de disco. Isso é usado nas etapas subsequentes.

## <a name="copy-data-and-verify"></a>Copiar dados e verificar

O tempo para concluir essa operação depende do tamanho dos dados. 

1. A unidade contém as pastas *PageBlob*, *BlockBlob*, *AzureImportExport*. Arraste e solte para copiar os dados que precisam ser importados como blobs de blocos para a pasta *BlockBlob*. Da mesma forma, arraste e solte os dados como VHD/VHDX para a pasta *PageBlob*.

    Um contêiner é criado na conta de armazenamento do Azure para cada subpasta em *BlockBlob* e a pasta *PageBlob*. Todos os arquivos nas pastas *BlockBlob* e *PageBlob* são copiados para um contêiner padrão `$root` sob a conta de Armazenamento do Azure.

    > [!NOTE] 
    > - Todos os contêineres e blobs devem obedecer às [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). Se essas regras não forem seguidas, o carregamento de dados do Azure falhará.
    > - Verifique se os arquivos não excedem ~4,7 TiB para blobs de bloco e ~8 TiB para blobs de página.

2. (Opcional) Depois que a cópia for concluída, é recomendável que você execute o `AzureExpressDiskService.cmd` fornecido na pasta *AzureImportExport* para gerar somas de verificação para validação. Dependendo do tamanho dos dados, esta etapa pode demorar. 
3. Desconecte a unidade. 


## <a name="ship-to-azure"></a>Enviar para o Azure

Essa etapa demora cerca de 5 a 7 minutos para ser concluída.

1. Coloque todos os discos em conjunto no pacote original. Use a etiqueta de remessa inclusa. Se a etiqueta for danificada ou perdida, baixe-a partir do portal. Vá para **Visão geral** e clique em **Baixar etiqueta de remessa** na barra de comandos.
2. Entregue o pacote lacrado no local de remessa.  

O serviço do disco do Data Box envia uma notificação por email e atualiza o status do trabalho no portal do Azure.


## <a name="verify-your-data"></a>Verifique seus dados

O tempo para concluir essa operação depende do tamanho dos dados.

1. Quando o disco do Data Box estiver conectado à rede de datacenter do Azure, o carregamento de dados do Azure é iniciado automaticamente. 
2. O serviço do Azure Data Box notifica que a cópia de dados foi concluída por meio do portal do Azure. 
    
    1. Verifique se há logs de erros para quaisquer falhas e tome as medidas apropriadas.
    2. Verifique se seus dados estão nas contas de armazenamento antes de excluí-los da fonte.

## <a name="clean-up-resources"></a>Limpar recursos

Esta etapa leva de 2 a 3 minutos para ser concluída.

Para limpar, você pode cancelar o pedido do Data Box e, em seguida, excluí-lo.

- Você pode cancelar o pedido do Data Box no portal do Azure antes de o pedido ser processado. Depois que o pedido tiver sido processado, ele não pode ser cancelado. O pedido progride até atingir o estágio concluído. 

    Para cancelar o pedido, vá para **Visão geral** e clique em **Cancelar** na barra de comandos.  

- Você pode excluir o pedido depois que o status é mostrado como **Completo** ou **Cancelado** no portal do Azure. 

    Para excluir o pedido, vá para **Visão geral** e clique em **Excluir** na barra de comandos.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou o disco do Azure Data Box para ajudar a importar seus dados no Azure. Para saber mais sobre o gerenciamento de disco do Azure Data Box, avance para o tutorial a seguir: 

> [!div class="nextstepaction"]
> [Usar o portal do Azure para administrar o disco do Data Box](data-box-portal-ui-admin.md)


