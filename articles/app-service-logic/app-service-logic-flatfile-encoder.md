<properties 
   pageTitle="Codificador de Arquivo Simples do BizTalk" 
   description="Codificador de Arquivo Simples do BizTalk" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic" 	
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/29/2015"
   ms.author="rajram"/>

# Codificador de Arquivo Simples do BizTalk

Use o conector de Codificação de Arquivo Simples do BizTalk para permitir a interoperabilidade entre os dados de arquivo simples (por exemplo, um arquivo do Excel ou CSV) e dados XML. Ele pode converter uma instância de arquivo simples em XML e vice-versa.

##Usando o Codificador de Arquivo Simples do BizTalk
Para usar o Codificador de Arquivo Simples do BizTalk, primeiro você precisa criar uma instância do aplicativo de API do Codificador de Arquivo Simples do BizTalk. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Codificador de Arquivo Simples do BizTalk no Azure Marketplace. Aqui estão as etapas para criar um aplicativo de API de Codificador de Arquivo Simples do BizTalk do Azure Marketplace: 1. Faça logon no portal do Azure (http://portal.azure.com) 2. Selecione Novo > Marketplace > Tudo 3. Pesquise "Codificador de Arquivo Simples do BizTalk" na caixa de pesquisa 4 . Selecione o Codificador de Arquivo Simples do BizTalk na lista de resultados 5. Selecione "Criar" e forneça um nome e os outros detalhes necessários 6. Selecione "Criar". Você será redirecionado à página inicial, em que poderá ver o andamento da criação. Isso pode levar algum tempo para ser concluído. Você receberá uma notificação quando o processo for concluído.

###Configurar o Codificador de Arquivo Simples do BizTalk
Depois que o aplicativo de API tiver sido criado, você poderá iniciá-lo diretamente da página inicial do portal do Azure ou da superfície do designer ao criar um aplicativo de Lógica.

Para iniciar da página inicial do Azure, você pode pesquisá-la digitando o nome que deu ao Codificador de Arquivo Simples do BizTalk ao criá-lo. Para fazer isso: 1. Insira o nome do Codificador de Arquivo Simples do BizTalk na caixa de pesquisa no portal do Azure e pesquise-o 2. Em seguida, selecione o Codificador de Arquivo Simples do BizTalk na lista. Isso abrirá a folha de Aplicativo de API, em que você pode configurar o aplicativo de API de Codificador de Arquivo Simples do BizTalk. Para iniciar a configuração, convém adicionar um esquema da seguinte forma: 1. Selecionando o componente de "Esquemas" ![Parte de esquemas do Codificador de Arquivo Simples do BizTalk][2] 2. Selecionando "Adicionar novo" na folha Esquemas que é aberta ![Lista de ações do Codificador de Arquivo Simples do BizTalk][7] 3. Selecione uma das três opções para fornecer seu esquema. As opções são CARREGAR NOVO ESQUEMA, GERAR DE JSON e GERAR DE ARQUIVO SIMPLES ![Lista de ações do Codificador de Arquivo Simples do BizTalk][8] 4. Siga as etapas para fornecer o esquema, com base em sua seleção na etapa anterior. Em seguida, você verá que o esquema foi carregado: ![Lista de ações do Codificador de Arquivo Simples do BizTalk][9]

###Usando o Codificador de Arquivo Simples do BizTalk na superfície do designer
Agora que você configurou o Codificador de Arquivo Simples do Biztalk, é hora de usá-lo em um aplicativo de Lógica. Para começar, crie um novo aplicativo de Lógica ou inicialize um existente que você criou anteriormente. Depois, siga estas etapas: 1. No cartão 'Iniciar lógica', clique em 'Executar esta lógica manualmente'. 2. Selecione o Aplicativo de API de Codificador de Arquivo Simples do BizTalk que você criou anteriormente na galeria (você encontrará o Codificador de Arquivo Simples do BizTalk criado na lista de Aplicativos da API no lado direito da tela). 3. Selecione a seta para a direita preta. As duas ações disponíveis (Arquivo Simples para Xml e para Arquivo Simples) são apresentadas: ![Lista de ações do Codificador de Arquivo Simples do BizTalk][1] ![Lista de ações do Codificador de Arquivo Simples do BizTalk][4]

Siga as etapas abaixo, com base na ação selecionada.

####Arquivo Simples para Xml

![Lista de ações do Codificador de Arquivo Simples do BizTalk][5]

Parâmetro|Tipo|Descrição do parâmetro
---|---|---
Arquivo simples|string|Conteúdo do arquivo simples de entrada
Nome do Esquema|string|Nome do esquema que representa o arquivo simples de entrada
Nome Raiz|string|Nome do nó raiz do esquema de arquivo simples


A ação retorna a saída como uma cadeia de caracteres ‒ Xml de saída. O Xml de saída contém a representação em xml do conteúdo do arquivo simples de entrada.

####Exportar para Arquivo Simples

![Lista de ações do Codificador de Arquivo Simples do BizTalk][6]

Parâmetro|Tipo|Descrição do parâmetro
---|---|---
XML de entrada|string|Conteúdo do Xml de entrada

A ação retorna a saída como uma cadeia de caracteres ‒ Arquivo Simples. A saída contém a representação de arquivo simples do conteúdo xml de entrada.

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
[7]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.addschema.PNG
[8]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.selectschemauploadoption.PNG
[9]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.shemauploaded.PNG

 

<!---HONumber=Nov15_HO2-->