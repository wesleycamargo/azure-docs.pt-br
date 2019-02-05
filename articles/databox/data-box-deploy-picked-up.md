---
title: Devolver o Microsoft Azure Data Box | Microsoft Docs
description: Saiba como enviar o Azure Data Box para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 158c2a8919bccea03f5c7b67aef23cd07022c259
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900503"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o Azure Data Box e verificar o upload de dados para o Azure

Este tutorial descreve como devolver o Azure Data Box e verificar os dados carregados no Azure.

Neste tutorial, você aprenderá sobre tópicos como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para o envio
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

- Você concluiu o [Tutorial: Copiar dados para o Azure Data Box e verificá-los](data-box-deploy-copy-data.md). 
- Trabalhos de cópia estão concluídos. A Preparação para o envio não poderá ser executada se os trabalhos de cópia estiverem em andamento.

## <a name="prepare-to-ship"></a>Preparar para o envio

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Devolver o Data Box

1. Certifique-se de que o dispositivo esteja desligado e os cabos removidos. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte posterior do dispositivo.
2. Se o envio do dispositivo estiver ocorrendo nos EUA, verifique se a etiqueta de remessa está aparecendo no papel eletrônico e agende uma retirada com a operadora. Se o rótulo estiver danificado ou perdido ou não for exibido na tela E-ink, acesse **Visão geral > Baixar etiqueta de remessa** no portal do Azure. Baixe a etiqueta de remessa e afixe-a ao dispositivo.

    Se o envio do dispositivo está ocorrendo na Europa, a tela de papel eletrônico não mostra a etiqueta de remessa. Em vez disso, a etiqueta de remessa de devolução está incluído na bolsa transparente sob a etiqueta de remessa de encaminhamento. Remova a etiqueta de remessa antiga e verifique se a etiqueta de remessa está claramente visível.
    
3. Agende uma retirada com a UPS se estiver devolvendo o dispositivo. Para agendar uma retirada, ligue para a UPS local (número de chamada gratuita específico do país) ou entregue o Data Box na localização de entrega mais próxima.

4. Após a coleta e registro do Data Box por sua operadora, o status do pedido no portal será atualizado para **Recolhido**. Uma ID de rastreamento também é exibida.

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e verificar o dispositivo, o status do pedido será atualizado para **Recebido**. Em seguida, o dispositivo passa por uma verificação física de danos ou sinais de violação.

Após a conclusão da verificação, o Data Box será conectado à rede no datacenter do Azure. A cópia de dados começa automaticamente. Dependendo do tamanho dos dados, a operação de cópia poderá demorar de algumas horas a dias para ser concluída. Você pode monitorar o andamento do trabalho de cópia no portal.

Depois que a cópia for concluída, o status do pedido será atualizado para **Concluído**.

Verifique se seus dados estão nas contas de armazenamento antes de excluí-los da fonte. Quando você copia os dados para o Data Box, dependendo do tipo, eles são carregados em um dos caminhos a seguir em sua conta do Armazenamento do Azure.

- Para blobs de página e blobs de bloco: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
- Para Arquivos do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

Como alternativa, você pode acessar sua conta de Armazenamento do Azure no portal do Azure e navegar de lá.

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box
 
Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para o envio
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box

Avance para o seguinte artigo para aprender a gerenciar o Data Box por meio da interface do usuário da Web local.

> [!div class="nextstepaction"]
> [Usar a interface do usuário Web local para administrar o Azure Data Box](./data-box-local-web-ui-admin.md)


