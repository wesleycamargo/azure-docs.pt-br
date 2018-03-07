---
title: "Proteger as mensagens de B2B com certificados nos Aplicativos Lógicos do Azure | Microsoft Docs"
description: Adicionar certificados para proteger mensagens de B2B com o Enterprise Integration Pack
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 708bdcddede71186c48ae7d4034cc9df0bd61391
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="secure-b2b-messages-with-certificates"></a>Proteger as mensagens de B2B com certificados

Às vezes você precisa manter a comunicação B2B confidencial. Para ajudá-lo a proteger a comunicação B2B para seus aplicativos de integração corporativa, especificamente os aplicativos lógicos, você pode adicionar certificados à sua conta de integração. Certificados são documentos digitais que verificam a identidade dos participantes de comunicações eletrônicas.
Certificados ajudam você a proteger a comunicação das seguintes maneiras:

* Criptografa o conteúdo da mensagem
* Assina digitalmente as mensagens  

Você pode usar esses tipos de certificados em seus aplicativos de integração corporativa:

* Certificados públicos, que devem ser adquiridos de uma autoridade de certificação (CA).
* Certificados privados, você pode emitir por conta própria. Esses certificados são chamados de certificados autoassinados.

## <a name="upload-a-public-certificate"></a>Carregar um certificado público

Para usar um *certificado público* em aplicativos lógicos com recursos de B2B, você deve carregá-lo em sua conta de integração. Depois de definir as propriedades nos [contratos](logic-apps-enterprise-integration-agreements.md) criados, o certificado fica disponível para ajudá-lo a proteger suas mensagens B2B.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu principal do Azure, selecione **Todos os serviços**. Insira “integração” na caixa de pesquisa e, em seguida, selecione **Contas de Integração**.

   ![Localizar sua conta de integração](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. Em **Contas de Integração**, selecione a conta de integração onde você deseja adicionar ao certificado.

   ![Selecione a conta de integração à qual você deseja adicionar o certificado](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. Escolha o bloco **Certificados**.  

   ![Escolher “Certificados”](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. Em **Certificados**, escolha **Adicionar**.

   ![Escolha "Adicionar"](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. Em **Adicionar Certificado**, forneça os detalhes do certificado.
   
   1. Insira o **Nome** do certificado. Para o tipo de certificado, selecione **Público**.

   2. No lado direito da caixa **Certificado**, escolha o ícone de pasta. 
   Localize e selecione o arquivo de certificado que você deseja carregar. 
   Quando terminar, escolha **OK**.

      ![Carregar um certificado público](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   O Azure carrega o certificado depois de validar sua seleção.

   ![Consultar o novo certificado](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>Carregar um certificado privado

Para usar um *certificado privado* em aplicativos lógicos com recursos de B2B, você deve carregá-lo em sua conta de integração. Você também precisa ter uma chave privada que seja adicionada primeiro ao [Azure Key Vault](../key-vault/key-vault-get-started.md). 

Depois de definir as propriedades nos [contratos](logic-apps-enterprise-integration-agreements.md) criados, o certificado fica disponível para ajudá-lo a proteger suas mensagens B2B.

> [!NOTE]
> Para certificados privados, certifique-se de adicionar um certificado público correspondente para aparecer no [contrato AS2](logic-apps-enterprise-integration-as2.md) Enviar e receber as configurações para assinar e criptografar mensagens.

1. [Adicionar sua chave privada no Azure Key Vault](../key-vault/key-vault-get-started.md#add) e **Nome de Chave**.
   
2. Autorize os Aplicativos Lógicos do Azure a executarem operações no Azure Key Vault. Para conceder acesso à entidade de serviço de Aplicativos Lógicos, use o comando do PowerShell, [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), por exemplo:

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Entre no [Portal do Azure](https://portal.azure.com).

4. No menu principal do Azure, selecione **Todos os serviços**. Insira “integração” na caixa de pesquisa e, em seguida, selecione **Contas de Integração**.

   ![Localizar sua conta de integração](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. Em **Contas de Integração**, selecione a conta de integração onde você deseja adicionar ao certificado.

6. Escolha o bloco **Certificados**.  

   ![Escolher o bloco Certificados](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. Em **Certificados**, escolha **Adicionar**.   

   ![Selecionar o botão Adicionar](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. Em **Adicionar Certificado**, forneça os detalhes do certificado.
   
   1. Insira o **Nome** do certificado. Para o tipo de certificado, selecione **Privado**.

   2. No lado direito da caixa **Certificado**, escolha o ícone de pasta. 
   Localize e selecione o arquivo de certificado que você deseja carregar. 
   Além disso, selecione **Grupo de Recursos**, **Key Vault** e **Nome da Chave**. 
   Quando terminar, escolha **OK**.

      ![Adicionar certificado](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   O Azure carrega o certificado depois de validar sua seleção.

   ![Consultar o novo certificado](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>Próximas etapas

* [Criar um contrato de B2B](logic-apps-enterprise-integration-agreements.md)
