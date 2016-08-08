<properties 
	pageTitle="Visão geral da validação de XML no Enterprise Integration Pack | Serviço de Aplicativo do Microsoft Azure | Microsoft Azure" 
	description="Saiba como funciona a validação no Enterprise Integration Pack e nos Aplicativos lógicos" 
	services="app-service\logic" 
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

# Integração corporativa com a validação XML

## Visão geral
Geralmente, em cenários de B2B, os parceiros em um acordo precisam validar que as mensagens trocadas entre si são válidas antes de começar o processamento dos dados. No Enterprise Integration Pack, você pode usar o conector de Validação de XML para validar documentos com um esquema predefinido.

## Como validar um documento com o conector de Validação de XML
1. Crie um aplicativo lógico e [vincule-o à sua conta de integração](./app-service-logic-enterprise-integration-accounts.md "Saiba como vincular uma conta de integração a um Aplicativo lógico") que contém o esquema que você usará para validar os dados XML.
2. Adicione um gatilho **Solicitação - Quando uma solicitação HTTP é recebida** ao seu Aplicativo Lógico ![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)
3. Adicione a ação **Validação de XML** selecionando primeiro **Adicionar uma ação**
4. Insira *xml* na caixa de pesquisa para filtrar todas as ações para aquela que você deseja usar
5. Escolha **Validação de XML** ![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)
6. Escolha a caixa de texto **CONTEÚDO** ![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. Selecione a marca body como o conteúdo que será validado. ![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)
8. Escolha a caixa de listagem **NOME DO ESQUEMA** e escolha o esquema que você deseja usar para validar a entrada *conteúdo* acima ![](./media/app-service-logic-enterprise-integration-xml/xml-4.png)
9. Salve seu trabalho ![](./media/app-service-logic-enterprise-integration-xml/xml-5.png)

Neste ponto, você já configurou seu conector de validação. Em um aplicativo real, convém armazenar os dados validados em um aplicativo LOB, como o SalesForce. Você pode adicionar facilmente uma ação para enviar a saída da validação para o SalesForce.

Agora você pode testar a ação de validação fazendo uma solicitação ao ponto de extremidade HTTP.

## Próximas etapas

[Saiba mais sobre o Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

<!---HONumber=AcomDC_0727_2016-->