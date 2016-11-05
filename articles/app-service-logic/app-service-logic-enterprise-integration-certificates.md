
---
title: Usar certificados com o Enterprise Integration Pack | Microsoft Docs
description: Saiba como usar certificados com o Enterprise Integration Pack e Aplicativos lógicos
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: deonhe

---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>Saiba mais sobre certificados e o Enterprise Integration Pack
## <a name="overview"></a>Visão geral
A integração corporativa usa certificados para proteger as comunicações B2B. Você pode usar dois tipos de certificados em seus aplicativos de integração corporativa:

* Certificados públicos, que devem ser adquiridos de uma autoridade de certificação (CA).
* Certificados privados, você pode emitir por conta própria. Esses certificados são chamados de certificados autoassinados.

## <a name="what-are-certificates?"></a>O que são certificados?
Certificados são documentos digitais que verificam a identidade dos participantes de comunicações eletrônicas, e também servem para proteger as comunicações eletrônicas.

## <a name="why-use-certificates?"></a>Por que usar certificados?
Às vezes, as comunicações B2B devem ser mantidas confidenciais. A integração corporativa usa certificados para proteger essas comunicações de duas formas:

* Criptografando o conteúdo das mensagens
* Assinando digitalmente as mensagens  

## <a name="how-do-you-upload-certificates?"></a>Como você carrega os certificados?
### <a name="public-certificates"></a>Certificados públicos
Para usar um *certificado público* em seus aplicativos lógicos com recursos de B2B, primeiro você precisa carregá-lo em sua conta de integração. Para usar um *certificado autoassinado*, primeiro ele deve ser carregado no [Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md "Saiba mais sobre o Cofre de Chaves").

Depois de carregar um certificado, ele fica disponível para ajudar você com a proteção de suas mensagens B2B durante a definição de suas propriedades nos [contratos](app-service-logic-enterprise-integration-agreements.md) criados.  

Estas são as etapas detalhadas para carregar os certificados públicos em sua conta de integração depois de entrar no Portal do Azure:

1. Selecione **Procurar**.  
    ![Selecionar Procurar](./media/app-service-logic-enterprise-integration-overview/overview-1.png)  
2. Insira **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados.     
    ![Selecionar Contas de Integração](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Selecione a conta de integração à qual você deseja adicionar o certificado.  
    ![Selecione a conta de integração à qual você deseja adicionar o certificado](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Escolha o bloco **Certificados** .  
   ![Escolha o bloco Certificados](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. Na folha **Certificados** que é aberta, selecione o botão **Adicionar**.
    ![Selecionar o botão Adicionar](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Insira um **Nome** para o certificado e selecione o tipo de certificado. (Neste exemplo, usamos o tipo de certificado público). Em seguida, selecione o ícone de pasta no lado direito da caixa de texto **Certificado**.
7. Quando o seletor de arquivos for aberto, encontre e selecione o arquivo de certificado que você deseja carregar em sua conta de integração.
8. Selecione o certificado, e selecione **OK** no seletor de arquivo. Isso valida e carrega o certificado em sua conta de integração.
9. Por fim, volte à folha **Adicionar certificado** e selecione o botão **OK**.  
    ![Selecionar o botão OK](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)  
10. Em cerca de um minuto, você verá uma notificação indicando que o carregamento do certificado foi concluído.
11. Escolha o bloco **Certificados** . Você deverá ver o certificado recém-adicionado.  
    ![Consulte o novo certificado](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)  

### <a name="private-certificates"></a>Certificados privados
Você pode carregar certificados privados em sua conta de integração executando as seguintes etapas:  

1. [Carregar sua chave privada no Cofre de Chaves](../key-vault/key-vault-get-started.md "Saiba mais sobre o Cofre de Chaves").  
   
   > [!TIP]
   > Você deve autorizar o recurso Aplicativos Lógicos do Serviço de Aplicativo do Azure para executar operações no Cofre de Chaves. Você pode conceder acesso à entidade de serviço de Aplicativos Lógicos usando o comando a seguir do PowerShell: `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 
2. Crie um certificado privado.  
3. Carregue os certificados privados em sua conta de integração.

Depois de concluir as etapas acima, você poderá usar o certificado privado para criar acordos.

Veja a seguir as etapas detalhadas para carregar os certificados privados em sua conta de integração depois de entrar no Portal do Azure:  

1. Selecione **Procurar**.  
    ![Carregar seus certificados privados em sua conta de integração](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Insira **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados.     
    ![Selecionar Contas de Integração](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Selecione a conta de integração à qual você deseja adicionar o certificado.  
    ![Selecione a conta de integração à qual você deseja adicionar o certificado](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Escolha o bloco **Certificados** .  
    ![Escolher o bloco Certificados](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)  
5. Na folha **Certificados** que é aberta, selecione o botão **Adicionar**.
    ![Selecionar o botão Adicionar](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Insira um **Nome** para o certificado e selecione o tipo de certificado. (Neste exemplo, usamos o tipo de certificado público). Em seguida, selecione o ícone de pasta no lado direito da caixa de texto **Certificado**.
7. Quando o seletor de arquivos for aberto, encontre e selecione o arquivo de certificado que você deseja carregar em sua conta de integração.
8. Depois de selecionar o certificado, escolha **OK** no seletor de arquivo. Esta ação valida o certificado e o carrega em sua conta de integração.
9. Por fim, volte à folha **Adicionar certificado** e selecione o botão **OK**.  
    ![Adicionar certificado](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)  
10. Em cerca de um minuto, você verá uma notificação indicando que o carregamento do certificado foi concluído.
11. Escolha o bloco **Certificados** . Você deverá ver o certificado recém-adicionado.
    ![Consulte o novo certificado](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)  

Depois de carregar um certificado, ele fica disponível para ajudar você com a proteção de suas mensagens B2B durante a definição das propriedades nos [contratos](app-service-logic-enterprise-integration-agreements.md).  

## <a name="next-steps"></a>Próximas etapas
* [Criar um aplicativo lógico que use recursos de B2B](app-service-logic-enterprise-integration-b2b.md)  
* [Criar um contrato de B2B](app-service-logic-enterprise-integration-agreements.md)  
* [Saiba mais sobre o Cofre de Chaves](../key-vault/key-vault-get-started.md "Saiba mais sobre o Cofre de Chaves")  

<!--HONumber=Oct16_HO2-->


