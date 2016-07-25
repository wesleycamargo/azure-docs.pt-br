<properties 
	pageTitle="Aprenda a codificar ou decodificar arquivos simples usando o Enterprise Integration Pack e Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure | Microsoft Azure" 
	description="Use os recursos do Enterprise Integration Pack e Aplicativos Lógicos para codificar ou decodificar arquivos simples" 
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
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Integração corporativa com arquivos simples

## Visão geral

Você usará o conector de codificação de arquivo simples de dentro de um Aplicativo Lógico que codifica o conteúdo XML. Talvez você queira codificar o conteúdo XML antes de enviá-lo para um parceiro de negócios em um cenário B2B. O Aplicativo lógico criado por você pode obter seu conteúdo XML de diversas fontes, incluindo de um gatilho de solicitação HTTP ou de outro aplicativo, ou até mesmo de um dos vários [conectores](../connectors/apis-list.md). Confira a [Documentação de aplicativos lógicos](./app-service-logic-what-are-logic-apps.md "Saiba mais sobre os Aplicativos Lógicos") para saber mais sobre o poder dos Aplicativos lógicos.

## Como criar o conector de codificação de arquivo simples

Execute estas etapas para criar um Aplicativo lógico e adicionar um conector de codificação de arquivo simples ao Aplicativo lógico

1. Crie um Aplicativo lógico e [vincule-o à sua conta de integração](./app-service-logic-enterprise-integration-accounts.md "Saiba como vincular uma conta de integração a um Aplicativo lógico") que contém o esquema que você usará para codificar os dados XML.
2. Adicione um gatilho **Solicitação - Quando uma solicitação HTTP é recebida** ao seu Aplicativo Lógico ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
3. Adicione uma ação de codificação de arquivo simples fazendo o seguinte:
-  Selecione o sinal de **adição**
-  Selecione o link **Adicionar uma ação** que aparece após a seleção do sinal de adição
-  Insira *Simples* na caixa de pesquisa para filtrar todas as ações para aquela que você deseja usar
-  Escolha a ação **Codificação de Arquivo Simples** na lista ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
6. Selecione a caixa de texto **CONTEÚDO** no controle de Codificação de Arquivo Simples que aparece ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)
7. Selecione a marca body como o conteúdo que será codificado. A marca body preencherá o campo de conteúdo. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)
8. Escolha a caixa de listagem **NOME DO ESQUEMA** e escolha o esquema que você deseja usar para codificar a entrada *conteúdo* acima ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)
9. Salve seu trabalho ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

Neste ponto, você já configurou seu conector de codificação de arquivo simples. Em um aplicativo real, convém armazenar os dados codificados em um aplicativo LOB, como o SalesForce, ou enviar esses dados codificados a um parceiro comercial. Você pode adicionar facilmente uma ação para enviar a saída da ação de codificação para o Salesforce ou seu parceiro comercial usando qualquer um dos outros conectores fornecido.

Agora você pode testar seu conector fazendo uma solicitação ao ponto de extremidade HTTP e incluindo o conteúdo XML no corpo da solicitação.

## Como criar o conector de decodificação de arquivo simples

### Pré-requisito
Você precisa ter um arquivo de esquema já carregado na conta de integração para concluir estas etapas.

1. Adicione um gatilho **Solicitação - Quando uma solicitação HTTP é recebida** ao seu Aplicativo Lógico ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
2. Adicione uma ação de codificação de arquivo simples fazendo o seguinte:
-  Selecione o sinal de **adição**
-  Selecione o link **Adicionar uma ação** que aparece após a seleção do sinal de adição
-  Insira *Simples* na caixa de pesquisa para filtrar todas as ações para aquela que você deseja usar
-  Escolha a ação **Decodificação de Arquivo Simples** na lista ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
- Selecione o controle **CONTEÚDO**. Isso produzirá uma lista do conteúdo das etapas anteriores que você pode usar como o conteúdo para decodificação. Você perceberá que o *corpo* da solicitação HTTP de entrada estará disponível para uso como o conteúdo para decodificação. Observe que você também pode inserir o conteúdo para decodificação diretamente no controle **CONTEÚDO**. Neste exemplo, eu selecionei *Corpo* para usar o corpo da solicitação HTTP de entrada da etapa 1 das etapas de decodificação.
- Selecione a marca *Body*. Observe que agora a marca body está no controle CONTEÚDO.
- Selecione o nome do Esquema que você deseja usar para decodificar o conteúdo. Neste exemplo, eu selecionei *OrderFile*. Observe que OrderFile é o nome do esquema que eu carreguei em minha conta de integração em algum momento anterior. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)
- Selecione o link **Salvar** no menu para salvar seu trabalho ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

Neste ponto, você já configurou seu conector de decodificação de arquivo simples. Em um aplicativo real, convém armazenar os dados decodificados em um aplicativo LOB, como o SalesForce. Você pode adicionar facilmente uma ação para enviar a saída da ação de decodificação para o Salesforce.

Agora você pode testar seu conector fazendo uma solicitação ao ponto de extremidade HTTP e incluindo o conteúdo XML que você deseja decodificar no corpo da solicitação.

## Saiba mais
- [Saiba mais sobre o Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

<!---HONumber=AcomDC_0713_2016-->