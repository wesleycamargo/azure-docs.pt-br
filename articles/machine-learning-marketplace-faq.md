<properties title="FAQ for publishing and using Machine Learning apps in the Azure Marketplace" pageTitle="Perguntas frequentes sobre a publicação e utilização de aplicativos de aprendizado de máquina no Azure Marketplace | Azure" description="Perguntas frequentes" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 

#Perguntas frequentes sobre a publicação e utilização de aplicativos de aprendizado de máquina no Azure Marketplace

##Para o consumo do Marketplace


###Pergunta 1: Posso ver o seguinte erro depois de inserir a entrada para o serviço Web. Por que recebo esse erro?
A solicitação resultou em um erro de back-end ou tempo limite de back-end. A equipe está investigando o problema. Lamentamos pelo inconveniente. (500)
Resposta: Seus parâmetros de entrada podem não estar de acordo com o formato necessário para o serviço Web específico. Consulte o link de documentação correspondente para localizar o formato correto para parâmetros de entrada e as limitações do serviço Web.

###Pergunta 2: Se eu copiar o link da API do serviço Web que vejo em 'Explorar esse conjunto de dados' e colá-lo em outra janela/guia do navegador, quais são as credenciais que devo usar para acessar os resultados e como vê-los?
Resposta: Você deve usar sua conta do Marketplace como o nome de usuário e a chave de conta principal como a senha. A chave de conta principal pode ser encontrada na página 'explorar este conjunto de dados' na descrição do serviço Web (clique no botão 'mostrar'). O resultado pode ser exibido no navegador ou estar disponível para download, dependendo do navegador que você estiver usando.

###Pergunta 3: Eu vejo o seguinte erro depois de inserir a entrada para o serviço Web em 'explorar este conjunto de dados'. Por que recebo esse erro?
Ocorreu um erro inesperado ao processar sua solicitação. Tente novamente.
Resposta: Um ou mais parâmetros de entrada do serviço Web podem ter excedido o limite de tamanho ao consumir o serviço Web na página do marketplace 'explorar este conjunto de dados'. Os serviços podem ser chamados com um tamanho de entrada maior usando métodos HTTP POST; o exemplo de código é postado sob os serviços de exemplo descritos aqui: http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-web-service-examples/.

###

##Para a publicação do AM do Azure no Marketplace

###Pergunta 1: Por que meus logotipos/imagens/nº de transações não são atualizados para meu serviço Web? 
Resposta: Há um cache de logotipos/imagens no portal de publicação, e pode levar até 10 dias para o novo logotipo/imagem ser atualizado no portal.

###Pergunta 2: Por que a "guia Detalhes" do meu serviço Web na oferta do Marketplace está mostrando um erro?
Resposta: Há um problema conhecido no Marketplace ao se conectar ao AM do Azure para detalhes do serviço. A equipe está trabalhando em solucionar esse problema.

###Pergunta 3: Por que o código de exemplo R em serviços Web de AM do Azure não funciona para consumir os serviços Web no Marketplace?
Resposta: Os sistemas de autenticação são diferentes ao se conectar aos serviços Web de AM do Azure diretamente comparados ao conectar-se a esses serviços Web por meio do Marketplace. Os serviços no Marketplace são serviços OData e podem ser chamados com métodos GET ou POST. 

###Pergunta 4: Por que os links de suporte das minhas ofertas de serviço Web não estão atualizando corretamente para algumas das minhas ofertas?
Resposta: Os links de suporte são globais por editor, e não por oferta. 

###Pergunta 5: Como eu publico um serviço Web com o modo de entrada em lote no Marketplace?
Resposta: No momento, não há suporte para o modo de entrada de lote em serviços Web do Marketplace.

###Pergunta 6: Quem devo contatar para obter ajuda se tiver dúvidas sobre como me tornar um editor de dados ou se tiver problemas durante a publicação?
Resposta: Entre em contato com a equipe do Marketplace em datamarketbd@microsoft.com para obter mais informações.





