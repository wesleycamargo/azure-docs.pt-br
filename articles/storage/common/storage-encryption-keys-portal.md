---
title: Configurar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure do portal do Azure
description: Saiba como usar o portal do Azure para configurar as chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure. Chaves gerenciadas pelo cliente permitem que você criar, girar, desabilitar e revogar os controles de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 47dd38d9b5fd99431c8661c4891c4ebcdf5341d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593661"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Configurar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure do portal do Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando o [portal do Azure](https://portal.azure.com/). Para saber como criar um cofre de chaves usando o portal do Azure, consulte [guia de início rápido: Definir e recuperar um segredo do Cofre de chaves do Azure usando o portal do Azure](../../key-vault/quick-create-portal.md). 


> [!IMPORTANT]
> Usar chaves gerenciadas pelo cliente com a criptografia de armazenamento do Azure requer que o Cofre de chaves tem duas propriedades obrigatórias configuradas, **exclusão reversível** e **não limpar**. Essas propriedades são habilitadas por padrão, quando você cria um novo cofre de chaves no portal do Azure. No entanto, se você precisar habilitar essas propriedades em um cofre de chaves existente, você deve usar o PowerShell ou CLI do Azure.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Para habilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento.
1. Na folha **Configurações** da conta de armazenamento, clique em **Criptografia**. Selecione a opção **Usar sua própria chave**, conforme a imagem a seguir.

    ![Portal de captura de tela mostrando a opção de criptografia](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Especifique uma chave

Depois de habilitar chaves gerenciadas pelo cliente, você terá a oportunidade de especificar uma chave para associar a conta de armazenamento.

### <a name="specify-a-key-as-a-uri"></a>Especificar uma chave como URI

Para especificar uma chave como um URI, siga estas etapas:

1. Para localizar o URI da chave no portal do Azure, navegue até seu Cofre de chaves e selecione o **chaves** configuração. Selecione a chave desejada e clique na tecla para exibir suas configurações. Copie o valor da **identificador de chave** campo, que fornece o URI.

    ![URI da chave de Cofre de chaves do mostrando de captura de tela](media/storage-encryption-keys-portal/key-uri-portal.png)

1. No **criptografia** configurações de sua conta de armazenamento, escolha o **digitar a chave URI** opção.
1. No campo **Chave URI**, especifique o URI.

   ![Captura de tela mostrando como inserir URI da chave](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Especificar uma chave de um cofre de chaves

Para especificar uma chave do Cofre de chaves, primeiro certifique-se de que você tenha um cofre de chaves que contém uma chave. Para especificar uma chave do Cofre de chaves, siga estas etapas:

1. Escolha a opção **Selecionar do Cofre de chaves**.
2. Escolha o Cofre de chaves que contém a chave que deseja usar.
3. Escolha a chave do Cofre de chaves.

   ![Captura de tela mostrando a opção de chave gerenciada pelo cliente](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Atualizar a versão da chave

Quando você cria uma nova versão de uma chave, você precisará atualizar a conta de armazenamento para usar a nova versão. Siga estas etapas:

1. Navegue até sua conta de armazenamento e exibir o **criptografia** configurações.
1. Especifique o URI para a nova versão da chave. Como alternativa, você pode selecionar o Cofre de chaves e a chave novamente para atualizar a versão.

## <a name="next-steps"></a>Próximas etapas

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [O que é o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
