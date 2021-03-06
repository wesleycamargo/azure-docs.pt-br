---
title: Devolver o Microsoft Azure Data Box Disk | Microsoft Docs
description: Usar este tutorial para aprender como enviar o Azure Data Box Disk para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 0dd0474ad1ad360fd82cfdf746d2e9837f74833a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108368"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o Azure Data Box Disk e verificar o upload de dados para o Azure

Este é o último tutorial da série: Implantar o Azure Data Box Disk. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box Disk

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você concluiu o [Tutorial: copiar dados para o Azure Data Box Disk e verificar](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Devolver o Data Box Disk

1. Após a conclusão da validação de dados, desconecte os discos. Remova os cabos de conexão.
2. Encapsule todos os discos e os cabos de conexão com plástico bolha e coloque-os na caixa de remessa.
3. Use a etiqueta de remessa de devolução localizada na capa de plástico transparente que está afixada à caixa. Se a etiqueta estiver danificada ou perdida, faça o download de uma nova etiqueta de remessa no portal do Azure e cole-a no dispositivo. Vá para **Visão geral > Faça o download da etiqueta de remessa**.

    ![Faça o download da etiqueta de remessa](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    Essa ação faz o download de uma etiqueta de remessa de devolução, conforme mostrado abaixo.

    ![Exemplo de etiqueta de remessa](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)

4. Lacre a caixa de envio e verifique se a etiqueta de remessa de devolução está visível.
5. Agende uma coleta com a UPS se devolver o dispositivo nos EUA. Se você estiver devolvendo o dispositivo na Europa com a DHL, solicite a retirada da DHL visitando o site e especificando o número da carta de porte aéreo. Vá para o site da DHL Express do país e escolha **Agendar a recolha pelo serviço de correio > Remessa de eReturn**.

    ![Devolução de remessa da DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
    Especifique o número da carta de porte e clique em **Agendar retirada** para organizar a retirada.

      ![Agendar retirada](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

7. Depois que os discos forem recolhidos pelo serviço de correio, o status do pedido no portal de atualizações será exibido como **Recolhidos**. Uma ID de rastreamento também é exibida.

    ![Discos escolhidos](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e verificar o disco, o status do trabalho será atualizado para **Recebido**. 

![Discos recebidos](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Os dados são copiados automaticamente quando os discos são conectados a um servidor no datacenter do Azure. Dependendo do tamanho dos dados, a operação de cópia poderá demorar de algumas horas a dias para ser concluída. Você pode monitorar o andamento do trabalho de cópia no portal.

Depois que a cópia for concluída, o status do pedido será atualizado para **Concluído**.

![Cópia de dados concluída](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Verifique se seus dados estão nas contas de armazenamento antes de excluí-los da fonte. Seus dados podem estar em:

- Suas contas de Armazenamento do Azure. Quando você copia os dados para o Data Box, dependendo do tipo, eles são carregados em um dos caminhos a seguir em sua conta do Armazenamento do Azure.

  - Para blobs de página e blobs de bloco: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Arquivos do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Como alternativa, você pode acessar sua conta de Armazenamento do Azure no portal do Azure e navegar de lá.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como blobs de páginas e, em seguida, convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido.

  - Se a cópia para discos gerenciados no Azure tiver êxito, você poderá acessar os **Detalhes do pedido** no portal do Azure e anotar o grupo de recursos especificado para discos gerenciados.

      ![Exibir detalhes do pedido](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Vá para o grupo de recursos anotado e localize os discos gerenciados.

      ![Grupo de recursos para discos gerenciados](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Se você copiou um VHDX, ou um VHD diferencial/dinâmico, o VHDX/VHD será carregado para a conta de armazenamento de preparo como um blob de blocos. Acesse sua **conta de armazenamento de preparo > Blobs** e selecione o contêiner apropriado - StandardSSD, StandardHDD ou PremiumSSD. Os VHDX/VHDs devem aparecer como blobs de blocos na conta de armazenamento de preparo.

Para verificar se os dados foram carregados no Azure, execute as seguintes etapas:

1. Vá para a conta de armazenamento associada ao pedido do disco.
2. Vá para **serviço Blob > Procurar blobs**. A lista de contêineres é apresentada. Correspondente à subpasta que você criou nas pastas *BlockBlob* e *PageBlob*, contêineres com o mesmo nome são criados em sua conta de armazenamento.
    Se os nomes das pastas não estiverem em conformidade com as convenções de nomenclatura do Azure, o carregamento de dados para o Azure falhará.

4. Para verificar se todo o conjunto de dados foi carregado, use o Gerenciador de Armazenamento do Microsoft Azure. Anexe a conta de armazenamento correspondente ao pedido de aluguel do disco e, em seguida, observe a lista de contêineres de blob. Escolha um contêiner, clique em **...Mais** e, em seguida, clique em **Estatísticas da pasta**. No painel **Atividades**, as estatísticas dessa pasta, incluindo o número de blobs e o tamanho total do blob são exibidos. O tamanho total do blob em bytes deve corresponder ao tamanho do conjunto de dados.

    ![Estatísticas da pasta no Gerenciador de Armazenamento](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Eliminar dados do Data Box Disk

Depois que a cópia for concluída e você tiver verificado que os dados estão na conta de armazenamento do Azure, os discos serão apagados com segurança seguindo o padrão do NIST.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box Disk


Avance para a próxima instrução para saber como gerenciar o Data Box Disk por meio do portal do Azure.

> [!div class="nextstepaction"]
> [Usar o portal do Azure para administrar o Azure Data Box Disk](./data-box-portal-ui-admin.md)


