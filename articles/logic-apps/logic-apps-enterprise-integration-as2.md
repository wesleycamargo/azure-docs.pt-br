---
title: "Criar um contrato AS2 em Aplicativo Lógico do Azure | Microsoft Docs"
description: "Criar um contrato AS2 para o Enterprise Integration Pack | Aplicativo Lógico do Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 6a947ea997bbcfe1b6b28c7cbb49911836750e6a
ms.openlocfilehash: a490b89c5420aecdfb3f79289979faab9a4630e9


---
# <a name="enterprise-integration-with-as2"></a>Integração corporativa com o AS2
Para usar os recursos corporativos em aplicativos lógicos, primeiro crie os contratos.

## <a name="prerequisites"></a>Pré-requisitos
* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) deve ser definida em sua assinatura do Azure.  
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) já devem estar definidos na sua conta de integração.  

> [!NOTE]
> Ao criar um contrato, o conteúdo do arquivo do contrato deve corresponder ao tipo de contrato.    

Depois de criar uma conta de integração e adicionar os parceiros, você pode criar um contrato usando os procedimentos nas seções a seguir.  

## <a name="create-an-agreement"></a>Criação de um contrato

1. Entre no [portal do Azure](http://portal.azure.com "portal do Azure").  
2. Selecione **Mais serviços**, acesse **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados.

 ![Selecione "Contas de Integração" na lista de resultados](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
3. Selecione a conta de integração à qual você deseja adicionar o certificado.

 ![Selecione a conta de integração](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Selecione o bloco **Contratos** . Se o bloco não for exibido, adicione-o.

 ![Selecione o bloco "Contratos"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
5. Na folha **Contratos**, selecione **Adicionar**.

 ![Selecione "Adicionar"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
6. Insira um nome para o contrato, selecione **AS2** na lista de **Tipo de contrato**, e insira as informações adequadas nas listas **Parceiro do Host**, **Identidade do Host**, **Parceiro Convidado** e **Identidade do Convidado**.

 ![Insira um nome para o contrato](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

 A tabela a seguir descreve as propriedades disponíveis na caixa de diálogo **Adicionar**:

    | Propriedade | Descrição |
    | --- | --- |
    | Parceiro de Host | Um contrato precisa dos parceiros host e convidado. O parceiro host representa a organização que está configurando o contrato. |
    | Identidade do Host | Um identificador para o parceiro host. |
    | Parceiro Convidado | Um contrato precisa dos parceiros host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro host. |
    | Identidade do Convidado | Um identificador para o parceiro convidado. |
    | Configurações de Recebimento | As propriedades que se aplicam a todas as mensagens que são recebidas pelo contrato. |
    | Configurações de Envio | As propriedades que se aplicam a todas as mensagens que são enviadas pelo contrato. |

7. Siga as instruções a seguir para configurar como as mensagens recebidas por este contrato devem ser manipuladas:

 a. Selecione **Configurações de Recebimento**.

 b. Você também pode substituir as propriedades de mensagens de entrada selecionando **Substituir as propriedades de mensagem**.

 c. Para que todas as mensagens de entrada sejam assinadas, selecione **A mensagem deve ser assinada**. Caso selecione essa opção, valide a assinatura na mensagem selecionando **certificado público de parceiro convidado** na lista **Certificado**.

 d. Para exigir que todas as mensagens de entrada sejam criptografadas, selecione a **Mensagem deve ser criptografada**. Caso selecione essa opção, selecione **certificado público de parceiro convidado** para descriptografar as mensagens de entrada na lista **Certificado**.

 e. Para exigir que as mensagens sejam compactadas, selecione **Mensagem deve ser compactada**.    

 f. Para enviar uma notificação de disposição de mensagens (MDN) síncrona das mensagens recebidas, selecione **enviar MDN**.

 g. Selecione **Enviar MDN assinada** para enviar MDNs assinadas para as mensagens de entrada.

 h. Selecione **Enviar MDN assíncrona** para enviar MDNs assíncronas para as mensagens de entrada.

 ![Definição das propriedades de "Configurações de Recebimento"](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

 A tabela a seguir descreve as propriedades das **Configurações de Recebimento**:  

 | Propriedade | Descrição |
 | --- | --- |
 | Substituir as propriedades de mensagem | Indica que as propriedades nas mensagens recebidas podem ser substituídas. |
 | Mensagem deve ser assinada | Exige que as mensagens sejam assinadas digitalmente. Configura o certificado público do parceiro convidado para verificação de assinatura.  |
 | Mensagem deve ser criptografada | Exige que as mensagens sejam criptografadas. Mensagens não criptografadas serão rejeitadas. Configura o certificado particular do parceiro do host para descriptografar as mensagens.  |
 | Mensagem deve ser compactada | Exige que as mensagens sejam compactadas. Mensagens não compactadas serão rejeitadas. |
 | Texto MDN | A notificação de disposição de mensagem (MDN) padrão a ser enviada para o remetente da mensagem. |
 | Enviar MDN | Exige que MDNs sejam enviadas. |
 | Enviar MDN assinada | Exige que MDNs sejam assinadas. |
 | Algoritmo de Controle de Integridade de Credenciais | |
 | Enviar MDN assíncrona | Exige que as mensagens sejam enviadas de forma assíncrona. |
 | URL | O URL cujas MDNs devem ser enviadas. |

8. Siga as instruções a seguir para configurar como as mensagens enviadas por este contrato devem ser manipuladas:

 a. Selecione **Configurações de Envio**.  

 b. Selecione **Ativar a assinatura de mensagens** para enviar mensagens assinadas para o parceiro. Se você selecionar essa opção, assine as mensagens selecionando o **Algoritmo MIC do certificado particular do parceiro do host** na lista **Algoritmo MIC** e o **Certificado particular do parceiro do host** na lista **Certificado**.

 c. Selecione **Ativar a criptografia de mensagens** para enviar mensagens criptografadas para o parceiro. Se você selecionar essa opção, criptografe as mensagens selecionando o **algoritmo do certificado público do parceiro do host** na lista **Algoritmo de Criptografia** e o **Certificado público do parceiro do host** na lista **Certificado**.

 d. Para compactar a mensagem, selecione **Habilitar a compactação de mensagem**.

 e. Selecione **Desdobrar cabeçalhos HTTP** para desdobrar o cabeçalho de tipo de conteúdo HTTP em uma única linha.

 f. Selecione **Solicitar MDN** para receber MDNs síncronas para mensagens enviadas.

 g. Selecione **Solicitar MDN assinada** para receber MDNs assinadas para mensagens enviadas.

 h. Selecione **Solicitar MDN assíncrona** para receber MDNs assíncronas para mensagens enviadas. Caso selecione essa opção, insira o URL cujas MDNs devem ser enviadas.  

 i. Selecione **Habilitar NRR** para exigir o não-repúdio do recebimento.

 j. Selecione **OK**.

 ![Definição das propriedades de "Configurações de Envio"](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

 A tabela a seguir descreve as propriedades das **Configurações de Envio**:  

 | Propriedade | Descrição |
 | --- | --- |
 | Habilitar a assinatura de mensagens | Exige que todas as mensagens enviadas do contrato sejam assinadas. |
 | Algoritmo de Controle de Integridade de Credenciais | O algoritmo a ser usado para assinar as mensagens. Configura o Algoritmo MIC do certificado particular do parceiro do host para assinar as mensagens. |
 | Certificado | O certificado a ser usado para assinar as mensagens. Configura o certificado particular do parceiro do host para assinar as mensagens. |
 | Habilitar a criptografia de mensagem | Exige a criptografia de todas as mensagens enviadas deste contrato. Configura o algoritmo do certificado público do parceiro convidado para criptografar as mensagens. |
 | Algoritmo de Criptografia | O algoritmo de criptografia a ser usado na criptografia de mensagens. Configura o certificado público do parceiro convidado para criptografar as mensagens. |
 | Certificado | O certificado a ser usado para criptografar as mensagens. Configura o certificado privado do parceiro convidado para criptografar as mensagens. |
 | Habilitar a compactação de mensagem | Exige a compressão de todas as mensagens enviadas deste contrato. |
 | Desdobrar cabeçalhos HTTP | Posiciona o cabeçalho de tipo de conteúdo HTTP em uma única linha. |
 | Solicitar MDN | Exige uma MDN de todas as mensagens enviadas deste contrato. |
 | Solicitar MDN assinada | Exige que todas as MDNs enviadas para este contrato sejam assinadas. |
 | Solicitar MDN assíncrona | Exige que as MDNs assíncronas sejam enviadas para este contrato. |
 | URL | O URL cujas MDNs devem ser enviadas. |
 | Habilitar NRR | Requer o não-repúdio de recebimento (NRR), um atributo de comunicação que fornece evidência de que os dados foram recebidos conforme endereçados. |

## <a name="view-the-agreements-list"></a>Exibir lista de contratos
Selecione o bloco **Contratos** na folha da **Conta de Integração** para ver o contrato recém-adicionado.

![Exibir lista de "Contratos"](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  



<!--HONumber=Feb17_HO1-->


