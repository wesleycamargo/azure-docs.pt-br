<properties 
	pageTitle="Criando soluções de B2B com o Enterprise Integration Pack | Serviço de Aplicativo do Microsoft Azure | Microsoft Azure" 
	description="Saiba mais sobre o recebimento de dados usando os recursos de B2B do Enterprise Integration Pack" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Saiba mais sobre o recebimento de dados usando os recursos de B2B do Enterprise Integration Pack#

## Visão geral ##

Este documento faz parte do Enterprise Integration Pack de Aplicativos Lógicos. Veja a visão geral para saber mais sobre as [funcionalidades do Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md).

## Pré-requisitos ##

Para usar as ações AS2 e X12, você precisará de uma conta do Enterprise Integration

[Como criar uma conta do Enterprise Integration](./app-service-logic-enterprise-integration-accounts.md)

## Como usar os conectores de B2B dos Aplicativos Lógicos ##

Depois de ter criado uma conta de integração e ter adicionado parceiros e contratos a ele, você estará pronto para criar um Aplicativo Lógico que implementa um fluxo de trabalho entre empresas (B2B).

Neste passo a passo, você aprenderá a usar ações AS2 e X12 para criar um Aplicativo Lógico entre empresas que recebe dados de um parceiro comercial.

1. Crie um novo Aplicativo Lógico e [vincule-o à sua conta de integração](./app-service-logic-enterprise-integration-accounts.md).
2. Adicione um gatilho **Solicitação - Quando uma solicitação HTTP é recebida** ao seu Aplicativo Lógico ![](./media/app-service-logic-enterprise-integration-b2b/flatfile-1.png)
3. Adicione a ação **Decodificar AS2** selecionando primeiro **Adicionar uma ação** ![](./media/app-service-logic-enterprise-integration-b2b/transform-2.png)
4. Insira a palavra **as2** na caixa de pesquisa para filtrar todas as ações para aquela que você deseja usar ![](./media/app-service-logic-enterprise-integration-b2b/b2b-5.png)
6. Selecione a ação **AS2 - Decodificar mensagem AS2** ![](./media/app-service-logic-enterprise-integration-b2b/b2b-6.png)
7. Conforme mostrado, adicione o **Corpo** que será utilizado como entrada. Neste exemplo, selecione o corpo da solicitação HTTP que disparou o Aplicativo Lógico. Como alternativa, você pode inserir uma expressão para inserir os cabeçalhos no campo **CABEÇALHOS**:

    @triggerOutputs()['headers']

8. Adicione os **cabeçalhos** que são necessários para AS2. Eles estarão nos cabeçalhos de solicitação HTTP. Neste exemplo, selecione o cabeçalho da solicitação HTTP que disparou o Aplicativo Lógico.
9. Agora, adicione a ação Decodificar mensagem X12 novamente selecionando **Adicionar uma ação** ![](./media/app-service-logic-enterprise-integration-b2b/b2b-9.png)
10. Insira a palavra **x12** na caixa de pesquisa para filtrar todas as ações para aquela que você deseja usar ![](./media/app-service-logic-enterprise-integration-b2b/b2b-10.png)
11. Selecione a ação **X12 – Decodificar mensagem X12** para adicioná-lo ao Aplicativo Lógico ![](./media/app-service-logic-enterprise-integration-b2b/b2b-11.png)
12. Agora, você precisa especificar a entrada para esta ação que será a saída da ação AS2 acima. O conteúdo real da mensagem está em um objeto JSON e é codificado em base64. Portanto, você precisa especificar uma expressão como a entrada; então, insira a seguinte expressão no campo de entrada **X12 FLAT FILE MESSAGE TO DECODE** (MENSAGEM DO ARQUIVO SIMPLES X12 A SER DECODIFICADA)

    @base64ToString(body('Decode\_AS2\_message')?['Message']?['Content'])

13. Esta etapa decodificará os dados X12 recebidos de um parceiro comercial e produzirão um número de itens em um objeto JSON. Para informar o parceiro do recebimento de dados, você pode enviar de volta uma resposta que contém o MDN (Notificação de Disposição de Mensagem) do AS2 em uma Ação de Resposta HTTP
14. Adicione a ação **Resposta** selecionando **Adicionar uma ação** ![](./media/app-service-logic-enterprise-integration-b2b/b2b-14.png)
15. Insira a palavra **response** na caixa de pesquisa para filtrar todas as ações para aquela que você deseja usar ![](./media/app-service-logic-enterprise-integration-b2b/b2b-15.png)
16. Selecione a ação **Response** para adicioná-la ![](./media/app-service-logic-enterprise-integration-b2b/b2b-16.png)
17. Defina o campo **CORPO** usando a expressão a seguir para acessar a MDN da saída da ação **Decodificar mensagem X12**

    @base64ToString(body('Decode\_AS2\_message')?['OutgoingMdn']?['Content'])

![](./media/app-service-logic-enterprise-integration-b2b/b2b-17.png)
18. Salve seu trabalho ![](./media/app-service-logic-enterprise-integration-b2b/transform-5.png)

Neste ponto, você já configurou seu Aplicativo Lógico B2B. Em um aplicativo real, convém armazenar os dados X12 decodificados em um aplicativo LOB ou no armazenamento de dados. Você pode facilmente adicionar mais ações a ele ou gravar APIs personalizadas para se conectar a seus próprios aplicativos de LOB e usar essas APIs em seu Aplicativo Lógico.

## Recursos e casos de uso ##

- As ações decodificar e codificar AS2 e X12 permitem que você receba e envie dados a parceiros comerciais usando os protocolos padrão do setor usando os Aplicativos Lógicos
- Você pode usar X12 e AS2 uns com os outros ou separadamente para trocar dados com parceiros comerciais conforme necessário
- As ações de B2B facilitam a criação de parceiros e acordos na conta do Integration e os consomem em um Aplicativo Lógico
- Estendendo o seu Aplicativo Lógico com outras ações, você pode enviar e receber dados de e para outros aplicativos e serviços, como SalesForce

## Saiba mais ##

[Saiba mais sobre o Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md)

<!---HONumber=AcomDC_0803_2016-->