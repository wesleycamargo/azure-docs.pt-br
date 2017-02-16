---
title: Aprenda a criar um contrato AS2 para o Enterprise Integration Pack | Microsoft Docs
description: "Aprenda a criar um contrato AS2 para o Enterprise Integration Pack | Aplicativo Lógico do Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 47a1c5653d84a55224c18222bc547e1c863ea442
ms.openlocfilehash: ea256557a38b3ce6cb457d195fa4b48cfd8879b8


---
# <a name="enterprise-integration-with-as2"></a>Integração corporativa com o AS2
## <a name="create-an-as2-agreement"></a>Criar um contrato AS2
Para usar os recursos corporativos em aplicativos Lógicos, é necessário criar primeiro os contratos. 

### <a name="heres-what-you-need-before-you-get-started"></a>Veja o que você precisa antes de começar
* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) definida em sua assinatura do Azure  
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) já definidos em sua conta de integração  

> [!NOTE]
> Ao criar um contrato, o conteúdo do arquivo do contrato deve corresponder ao tipo de contrato.    
> 
> 

Depois de você [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) e [adicionar os parceiros](logic-apps-enterprise-integration-partners.md), será possível criar um contrato seguindo estas etapas:  

### <a name="from-the-azure-portal-home-page"></a>Na página inicial do Portal do Azure
Depois de fazer logon no [portal do Azure](http://portal.azure.com "portal do Azure"):  

1. Selecione **Mais serviços** e digite **integração** na caixa de pesquisa de filtro. Selecione **Contas de Integração** na lista de resultados    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
2. Selecione a conta de integração à qual você deseja adicionar o certificado. 
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
3. Selecione o bloco **Contratos** . Se você não vir o bloco de contratos, adicione-o primeiro.   
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
4. Selecione o botão **Adicionar** na folha Contratos que se abre.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
5. Insira um **Nome** para seu contrato, selecione **AS2** em **Tipo de Contrato**, **Parceiro de Host**, **Identidade do Host**, **Parceiro Convidado**, **Identidade do Convidado** na folha Contratos.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

Aqui estão alguns detalhes que podem ser úteis ao configurar as definições para o seu contrato: 

| Propriedade | Descrição |
| --- | --- |
| Parceiro de Host |Um contrato precisa dos parceiros host e convidado. O parceiro host representa a organização que está configurando o contrato. |
| Identidade do Host |Um identificador para o parceiro host. |
| Parceiro Convidado |Um contrato precisa dos parceiros host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro host. |
| Identidade do Convidado |Um identificador para o parceiro convidado. |
| Configurações de Recebimento |Essas propriedades se aplicam a todas as mensagens recebidas por um contrato |
| Configurações de Envio |Essas propriedades se aplicam a todas as mensagens enviadas por um contrato |

Continuando:  

1. Selecione **Configurações de Recebimento** para configurar como as mensagens recebidas por este contrato devem ser manipuladas.  
   
   * Outra opção é substituir as propriedades na mensagem de entrada. Para fazer isso, selecione a opção **Substituir as propriedades de mensagem** .
   * Selecione a opção **Mensagem deve ser assinada** se desejar exigir que todas as mensagens recebidas sejam assinadas. Se você selecionar essa opção, precisará selecionar o *certificado público de parceiro convidado* que será usado para validar a assinatura nas mensagens.
   * Selecione a opção **Mensagem deve ser criptografada** se desejar exigir que todas as mensagens recebidas sejam criptografadas.  Se você selecionar essa opção, precisará selecionar o *certificado público de parceiro convidado* que será usado para validar a assinatura nas mensagens de entrada.
   * Você também pode exigir que as mensagens sejam compactadas. Para fazer isso, selecione a opção **Mensagem deve ser compactada** .    
   * Selecione **Enviar MDN** a fim de enviar MDN de sincronização para as mensagens recebidas
   * Selecione **Enviar MDN assinada** a fim de enviar MDN assinada para as mensagens recebidas
   * Selecione **Enviar MDN assíncrona** a fim de enviar MDN assíncrona para as mensagens recebidas     
    ![](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

Veja a tabela abaixo se você gostaria de saber mais sobre o que o processo de recebimento proporciona.  

| Propriedade | Descrição |
| --- | --- |
| Substituir as propriedades de mensagem |Selecione esta opção para indicar que as propriedades nas mensagens recebidas podem ser substituídas |
| Mensagem deve ser assinada |Habilite esta opção para exigir que as mensagens sejam assinadas digitalmente.  Configure o certificado público do parceiro convidado para verificação de assinatura  |
| Mensagem deve ser criptografada |Habilite esta opção para exigir que as mensagens sejam criptografadas. Mensagens não criptografadas serão rejeitadas. Configure o certificado particular do parceiro de host para descriptografar as mensagens  |
| Mensagem deve ser compactada |Habilite esta opção para exigir que as mensagens sejam compactadas. Mensagens não compactadas serão rejeitadas. |
| Texto MDN |Este é um padrão MDN a ser enviado para o remetente da mensagem |
| Enviar MDN |Habilite esta opção para permitir que MDNs sejam enviadas. |
| Enviar MDN assinada |Habilite esta opção para exigir que MDNs sejam assinadas. |
| Algoritmo de Controle de Integridade de Credenciais | |
| Enviar MDN assíncrona |Habilite esta opção para exigir que as mensagens sejam enviadas de forma assíncrona. |
| URL |Esta é a URL para o qual as MDNs serão enviadas. |

Agora, vamos continuar:  

1. Selecione **Configurações de Envio** para configurar como as mensagens enviadas por este contrato devem ser manipuladas.  

   * Selecione **Ativar a assinatura de mensagens** para enviar mensagens assinadas para o parceiro. Se você selecionar essa opção, precisará selecionar o *Algoritmo MIC do certificado particular do parceiro de host* e o *Certificado particular do parceiro de host* para assinar as mensagens.
   * Selecione **Ativar a criptografia de mensagens** para enviar mensagens criptografadas para o parceiro. Se você selecionar essa opção, precisará selecionar o *Algoritmo do certificado público do parceiro de host* e o *Certificado público do parceiro de host* para criptografar as mensagens.
   * Selecione a opção **Mensagem deve ser compactada** para compactar a mensagem 
   * Selecione a opção **Desdobrar cabeçalhos HTTP** para desdobrar o cabeçalho de tipo de conteúdo HTTP em uma única linha 
   * Selecione a opção **Solicitar MDN** para receber sincronização MDN das mensagens enviadas
   * Selecione a opção **Solicitar MDN assinada** para receber MDN assinada das mensagens enviadas
   * Selecione a opção **Solicitar MDN assíncrona** para receber MDN assíncrona das mensagens enviadas. Se você selecionar essa opção, precisará fornecer uma URL para a qual as MDNs serão enviadas  
   * Selecione **Habilitar NRR** para habilitar o não repúdio do recebimento    
   ![](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

Veja a tabela abaixo se você gostaria de saber mais sobre o que o processo de envio proporciona.  

| Propriedade | Descrição |
| --- | --- |
| Habilitar a assinatura de mensagens |Selecione essa opção para permitir que todas as mensagens enviadas do contrato sejam assinadas. |
| Algoritmo de Controle de Integridade de Credenciais |Selecione o algoritmo a ser usado na assinatura de mensagens. Configure o algoritmo MIC do certificado particular do parceiro de host para assinar as mensagens |
| Certificado |Selecione o certificado a ser usado na assinatura de mensagens. Configure o certificado particular do parceiro de host para assinar as mensagens |
| Habilitar a criptografia de mensagem |Selecione essa opção para criptografar todas as mensagens enviadas deste contrato. Configure o algoritmo de certificado público do parceiro convidado para criptografar as mensagens |
| Algoritmo de Criptografia |Selecione o algoritmo de criptografia a ser usado na criptografia de mensagens. Configure o certificado público do parceiro convidado para criptografar as mensagens |
| Desdobrar cabeçalhos HTTP |Selecione essa opção para desdobrar o cabeçalho de tipo de conteúdo HTTP em uma única linha |
| Solicitar MDN |Selecione essa opção para solicitar uma MDN de todas as mensagens enviadas deste contrato |
| Solicitar MDN assinada |Habilite para solicitar que todas as MDNs enviadas a este contrato sejam assinadas |
| Solicitar MDN assíncrona |Habilite para solicitar que MDN assíncrona seja enviada para este contrato |
| URL |A URL para a qual as MDNs serão enviadas |
| Habilitar NRR |Selecione essa opção para habilitar o Não Repúdio do Recebimento |

Selecione o bloco **Contratos** na folha da Conta de Integração e você verá o contrato recém-adicionado listado.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  


<!--HONumber=Jan17_HO4-->


