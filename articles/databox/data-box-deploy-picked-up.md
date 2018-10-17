---
title: Devolver o Microsoft Azure Data Box | Microsoft Docs
description: Saiba como enviar o Azure Data Box para a Microsoft
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2018
ms.author: alkohli
ms.openlocfilehash: 05bfba9fffa7db75ef4e1a1167b3170a775e1d34
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785453"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: devolver o Azure Data Box e verificar o carregamento de dados para o Azure

Este tutorial descreve como devolver o Azure Data Box e verificar os dados carregados no Azure.

Neste tutorial, você aprenderá sobre tópicos como:

> [!div class="checklist"]
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você concluiu todo o [Tutorial: copiar dados para o Azure Data Box e verificar](data-box-deploy-copy-data.md).

## <a name="ship-data-box-back"></a>Devolver o Data Box

1. Certifique-se de que o dispositivo esteja desligado e os cabos removidos. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte posterior do dispositivo.
2. Se o envio do dispositivo estiver ocorrendo nos EUA, verifique se a etiqueta de remessa está aparecendo no papel eletrônico e agende uma retirada com a operadora. Se a etiqueta estiver danificada, perdida ou não estiver aparecendo no papel eletrônico, faça o download de uma nova etiqueta de remessa no portal do Azure e cole-a no dispositivo. Vá para **Visão geral > Baixar etiqueta de remessa**. 

    Se o envio do dispositivo está ocorrendo na Europa, a tela de papel eletrônico não mostra a etiqueta de remessa. Em vez disso, a etiqueta de remessa de devolução está incluído na bolsa transparente sob a etiqueta de remessa de encaminhamento. Remova a etiqueta de remessa antiga e verifique se a etiqueta de remessa está claramente visível.
    
3. Agende uma coleta com a UPS se devolver o dispositivo nos EUA. Se você estiver devolvendo o dispositivo na Europa com a DHL, solicite a retirada da DHL visitando o site e especificando o número da carta de porte aéreo. Vá para o site da DHL Express do país e escolha **Agendar a recolha pelo serviço de correio > Remessa de eReturn**. 

    Especifique o número da carta de porte e clique em **Agendar retirada** para organizar a retirada.

4. Após a coleta e registro do Data Box por sua operadora, o status do pedido no portal será atualizado para **Recolhido**. Uma ID de rastreamento também é exibida.

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e verificar o dispositivo, o status do pedido será atualizado para **Recebido**. Em seguida, o dispositivo passa por uma verificação física de danos ou sinais de violação. 

Após a conclusão da verificação, o Data Box será conectado à rede no datacenter do Azure. A cópia de dados começa automaticamente. Dependendo do tamanho dos dados, a operação de cópia poderá demorar de algumas horas a dias para ser concluída. Você pode monitorar o andamento do trabalho de cópia no portal.

Depois que a cópia for concluída, o status do pedido será atualizado para **Concluído**.

Verifique se seus dados estão nas contas de armazenamento antes de excluí-los da fonte. 

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box
 
 Após a conclusão do upload no Azure, o Data Box apaga os dados nos discos de acordo com as [diretrizes NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box

Avance para o seguinte artigo para aprender a gerenciar o Data Box por meio da interface do usuário da Web local.

> [!div class="nextstepaction"]
> [Usar a interface do usuário Web local para administrar o Azure Data Box](./data-box-local-web-ui-admin.md)


