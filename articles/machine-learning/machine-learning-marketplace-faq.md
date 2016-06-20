<properties 
	pageTitle="Perguntas Frequentes: Publicar e usar aplicativos de Aprendizado de Máquina no Azure Marketplace | Microsoft Azure" 
	description="Perguntas frequentes" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bharaths" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/06/2016" 
	ms.author="bharaths"/>

#Publicação e utilização de aplicativos de Aprendizado de Máquina no Azure Marketplace: Perguntas Frequentes

##Perguntas sobre o consumo do Marketplace


**1. Por que recebo a seguinte mensagem de erro depois que insiro a entrada para o serviço Web:**

**A solicitação resultou em um erro de back-end ou em tempo limite de back-end. A equipe está investigando o problema. Lamentamos pelo inconveniente. (500)**

Seus parâmetros de entrada podem não estar de acordo com o formato necessário para o serviço Web específico. Consulte o link de documentação correspondente para localizar o formato correto para parâmetros de entrada e as limitações do serviço Web.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**2. Se eu copiar o link da API para o serviço Web que vejo na página "Explorar esse conjunto de dados" e colá-lo em outra janela do navegador, quais credenciais devo usar para acessar os resultados e como vê-los?**

Você deve usar sua conta do Marketplace como o nome de usuário e a chave de conta principal como a senha. A chave de conta principal pode ser encontrada na página **Explorar este conjunto de dados** na descrição do serviço Web (clique no botão **mostrar**). O resultado pode ser exibido no navegador ou ele pode estar disponível para download, dependendo do navegador que você estiver usando.

**3. Por que recebo a seguinte mensagem de erro depois de inserir a entrada para o serviço Web na página "Explorar esse conjunto de dados":**

**Ocorreu um erro inesperado ao processar a sua solicitação. Tente novamente.**

Um ou mais parâmetros de entrada do serviço Web podem ter excedido o limite de tamanho ao consumir o serviço Web na página **explorar este conjunto de dados** do Marketplace. Os serviços podem ser chamados com um comprimento de entrada maior usando métodos HTTP POST. Para obter exemplos, consulte [Soluções de exemplo usando R no Aprendizado de Máquina e publicadas no Marketplace](machine-learning-r-csharp-web-service-examples.md).

**4. Por que não vejo nada na guia “GERENCIADOR DE API” no Repositório do Portal Clássico do Azure?**

Esse é um problema conhecido no Marketplace do Portal Clássico do Azure. A equipe está trabalhando para resolver esse problema.


##Perguntas sobre a publicação de Aprendizado de Máquina do Azure no Marketplace

**1. Por que as minhas transações de logotipos ou imagens não são atualizadas para o meu serviço Web?**

Os logotipos e imagens são armazenados em cache no portal de publicação, e pode levar até 10 dias para o novo logotipo ou imagem ser atualizado no portal.

**2. Por que a guia "Detalhes" do meu serviço Web no Marketplace está mostrando uma mensagem de erro?**

Há um problema conhecido no Marketplace ao se conectar ao Aprendizado de Máquina do Azure para detalhes do serviço. A equipe está trabalhando para resolver esse problema.

**3. Por que o código de exemplo R em serviços Web de Aprendizado de Máquina do Azure não funciona para consumir os serviços Web no Marketplace?**

Os sistemas de autenticação são diferentes ao se conectar aos serviços Web de Aprendizado de Máquina do Azure diretamente comparados ao conectar-se a esses serviços Web por meio do Marketplace. Os serviços no Marketplace são serviços OData e podem ser chamados com métodos GET ou POST.

**4. Por que os links de suporte das minhas ofertas de serviço Web não estão atualizando corretamente para algumas das minhas ofertas?**

Os links de suporte são globais por editor, não por oferta.

**5. Como eu publico um serviço Web com o modo de entrada em lote no Marketplace?**

No momento, não há suporte para o modo de entrada de lote em serviços Web do Marketplace.

**6. Quem devo contatar para obter ajuda se tiver dúvidas sobre como me tornar um editor de dados ou se tiver problemas durante a publicação?**

Entre em contato com a equipe do Azure Marketplace em <datamarketbd@microsoft.com> para obter mais informações.





 

<!---HONumber=AcomDC_0608_2016-->