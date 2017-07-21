---
title: "Requer transferência segura no Armazenamento do Microsoft Azure | Microsoft Docs"
description: "Saiba mais sobre o recurso \"Requer transferência segura\" para o Armazenamento do Microsoft Azure e como habilitá-lo."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 516618653064fd4e334197bba767a013a805260a
ms.contentlocale: pt-br
ms.lasthandoff: 06/21/2017

---
# <a name="require-secure-transfer"></a>Requer transferência segura

A opção "Transferência segura obrigatória" melhora a segurança da sua conta de armazenamento, permitindo apenas solicitações para a conta de armazenamento de conexões seguras. Por exemplo, ao chamar APIs REST para acessar sua conta de armazenamento, será necessário estar conectado utilizando o HTTPS. Todas as solicitações utilizando HTTP serão rejeitadas quando a "Transferência segura obrigatória" estiver habilitada.

Quando você estiver usando o serviço de Arquivos do Azure, qualquer conexão sem criptografia falhará quando "Transferência segura obrigatória" estiver habilitada. Isso inclui cenários usando SMB 2.1, SMB 3.0 sem criptografia e alguns tipos do cliente Linux SMB. 

Por padrão, a opção "Transferência segura obrigatória" está desabilitada.

> [!NOTE]
> Como o armazenamento do Azure não dá suporte a HTTPS para nomes de domínio personalizado, essa opção não será aplicada ao usar um nome de domínio personalizado.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Habilitar "Transferência segura obrigatória" no portal do Azure

Você pode habilitar a "Transferência segura obrigatória" configurando ambos ao criar uma conta de armazenamento no [Portal do Azure](https://portal.azure.com)e para contas de armazenamento existentes.

### <a name="require-secure-transfer-when-you-create-a-storage-account"></a>Requerer transferência segura ao criar uma conta de armazenamento

1. Abra a folha **Criar conta de armazenamento** no portal do Azure.
1. Em **Transferência segura obrigatória**, selecione **habilitado**.

  ![captura de tela](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Requer transferência segura de uma conta de armazenamento existente

1. Selecionar uma conta de armazenamento existente no portal do Azure.
1. Selecione **Configuração** em **CONFIGURAÇÕES** na folha do menu da conta de armazenamento.
1. Em **Transferência segura obrigatória**, selecione **habilitado**.

  ![captura de tela](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="next-steps"></a>Próximas etapas
O Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que, juntos, permitem aos desenvolvedores criar aplicativos seguros. Para obter mais detalhes, visite o [Guia de segurança do armazenamento](storage-security-guide.md).

