<properties 
	pageTitle="Visão geral de X12 e do Enterprise Integration Pack | Serviço de Aplicativo do Microsoft Azure" 
	description="Saiba como usar contratos X12 para criar aplicativos lógicos" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>

# Integração corporativa com o X12 

## Criar um contrato X12 
Para poder trocar mensagens X12, você precisará criar um contrato X12 e armazená-lo em sua conta de integração. As etapas a seguir orientarão você pelo processo de criação de um contrato X12.

### Veja o que você precisa antes de começar
- Um [conta de integração](./app-service-logic-enterprise-integration-accounts.md) definida na sua assinatura do Azure
- Pelo menos dois [parceiros](./app-service-logic-enterprise-integration-partners.md) já definidos na sua conta de integração

>[AZURE.NOTE]Ao criar um contrato, o conteúdo do arquivo do contrato deve corresponder ao tipo de contrato.


Depois de você [criar uma conta de integração](./app-service-logic-enterprise-integration-accounts.md) e [adicionar os parceiros](./app-service-logic-enterprise-integration-partners.md), poderá criar um contrato X12 executando estas etapas:

### Na página inicial do Portal do Azure

Depois de fazer logon no [Portal do Azure](http://portal.azure.com "Portal do Azure"):
1. Selecione **Procurar** no menu à esquerda.

>[AZURE.TIP]Se você não vir o link **Procurar**, talvez seja necessário expandir o menu primeiro. Faça isso selecionando o link **Mostrar menu** que está localizado na parte superior esquerda do menu recolhido.

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Digite *integração* na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Na folha **Contas de Integração** que é aberta, selecione a conta de integração na qual você criará o contrato. Se você não vir nenhuma lista de contas de integração, [crie uma primeiro](./app-service-logic-enterprise-integration-accounts.md "Tudo sobre contas de integração"). ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Selecione o bloco **Contratos**. Se você não vir o bloco de contratos, adicione-o primeiro. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)
5. Selecione o botão **Adicionar** na folha Contratos que se abre. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)
6. Insira um **Nome** para seu contrato, selecione **Tipo de Contrato**, **Parceiro Host**, **Identidade do Host** e **Parceiro Convidado**, **Identidade do Convidado** na folha Contratos que se abre. ![](./media/app-service-logic-enterprise-integration-x12/x12-1.png)

Aqui estão alguns detalhes que podem ser úteis ao configurar as definições para o seu contrato:
  
|Propriedade|Descrição|
|----|----|
|Parceiro de Host|Um contrato precisa dos parceiros host e convidado. O parceiro host representa a organização que está configurando o contrato.|
|Identidade do Host|Um identificador para o parceiro host. |
|Parceiro Convidado|Um contrato precisa dos parceiros host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro host.|
|Identidade do Convidado|Um identificador para o parceiro convidado.|
|Configurações de Recebimento|Essas propriedades se aplicam a todas as mensagens recebidas por um contrato|
|Configurações de Envio|Essas propriedades se aplicam a todas as mensagens enviadas por um contrato|  
Continuando:
7. Selecione **Configurações de Recebimento** para configurar como as mensagens recebidas por este contrato devem ser manipuladas.
 
 - Outra opção é substituir as propriedades na mensagem de entrada. Para fazer isso, marque a caixa de seleção **Substituir as propriedades de mensagem**.
  - Marque a caixa de seleção **Mensagens devem ser assinadas** se desejar exigir que todas as mensagens recebidas sejam assinadas. Se você selecionar essa opção, também precisará selecionar o **Certificado** que será usado para validar a assinatura nas mensagens.
  - Outra opção é exigir que as mensagens também sejam criptografados. Para fazer isso, marque a caixa de seleção **Mensagens devem ser criptografadas**. Será necessário então selecionar o **Certificado** que será usado para decodificar as mensagens de entrada.
  - Você também pode exigir que as mensagens sejam compactadas. Para fazer isso, marque a caixa de seleção **Mensagens devem ser compactadas**. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)

Veja a tabela abaixo se você gostaria de saber mais sobre o que o processo de recebimento proporciona.

|Propriedade|Descrição|
|----|----|
|Substituir as propriedades da mensagem|Selecione esta opção para indicar que as propriedades nas mensagens recebidas podem ser substituídas |
|Mensagens devem ser assinadas|Habilite esta opção para exigir que as mensagens sejam assinadas digitalmente|
|Mensagens devem ser criptografadas|Habilite esta opção para exigir que as mensagens sejam criptografadas. Mensagens não criptografadas serão rejeitadas.|
|Mensagens devem ser compactadas|Habilite esta opção para exigir que as mensagens sejam compactadas. Mensagens não compactadas serão rejeitadas.|
|Texto MDN|Este é um padrão MDN a ser enviado para o remetente da mensagem|
|Enviar MDN|Habilite esta opção para permitir que MDNs sejam enviadas.|
|Enviar MDN assinada|Habilite esta opção para exigir que MDNs sejam assinadas.|
|Algoritmo de Controle de Integridade de Credenciais||
|Enviar MDN assíncrona|Habilite esta opção para exigir que as mensagens sejam enviadas de forma assíncrona.|
|URL|Esta é a URL para o qual as mensagens serão enviadas.|
Agora, vamos continuar:
8. Selecione **Configurações de Envio** para configurar como as mensagens enviadas por este contrato devem ser manipuladas. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)

Veja a tabela abaixo se você gostaria de saber mais sobre o que o processo de envio proporciona.

|Propriedade|Descrição|
|----|----|
|Habilitar a assinatura de mensagens|Marque essa caixa de seleção para habilitar que todas as mensagens enviadas do contrato sejam assinadas.|
|Algoritmo de Controle de Integridade de Credenciais|Selecione o algoritmo a ser usado na assinatura de mensagens|
|Certificado|Selecione o certificado a ser usado na assinatura de mensagens|
|Habilitar a criptografia de mensagem|Marque essa caixa de seleção para criptografar todas as mensagens enviadas deste contrato.|
|Algoritmo de Criptografia|Selecione o algoritmo de criptografia a ser usado na criptografia de mensagens|
|Desdobrar cabeçalhos HTTP|Marque esta caixa de seleção para desdobrar o cabeçalho de tipo de conteúdo HTTP em uma única linha.|
|Solicitar MDN|Marque essa caixa de seleção para solicitar uma MDN de todas as mensagens enviadas deste contrato|
|Solicitar MDN assinada|Habilite para solicitar que todas as MDNs enviadas a este contrato sejam assinadas|
|Solicitar MDN assíncrona|Habilite para solicitar que MDN assíncrona seja enviada para este contrato|
|URL|A URL para a qual as MDNs serão enviadas|
|Habilitar NRR|Marque essa caixa de seleção para habilitar o Não Repúdio do Recebimento|
Estamos quase lá!
9. Selecione o bloco **Contratos** na folha da Conta de Integração e você verá o contrato recém-adicionado listado. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)



## Saiba mais
- [Saiba mais sobre o Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

<!---HONumber=AcomDC_0706_2016-->