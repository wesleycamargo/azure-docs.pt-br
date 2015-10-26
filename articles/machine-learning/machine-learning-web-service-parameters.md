<properties 
	pageTitle="Usar os Parâmetros de Serviço Web de Aprendizado de Máquina do Azure | Microsoft Azure" 
	description="Como usar Parâmetros de Serviço Web de Aprendizado de Máquina do Azure para modificar o comportamento do seu modelo quando o serviço Web é acessado." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/04/2015" 
	ms.author="raymondl;garye"/>

#Usar os parâmetros do serviço Web de Aprendizado de Máquina do Azure

Um serviço Web de Aprendizado de Máquina do Azure é criado pela publicação de um experimento com módulos com parâmetros configuráveis. Em alguns casos, talvez você queira alterar o comportamento do módulo durante a execução do serviço Web. Os *Parâmetros de serviço Web* permitem que você faça isso.

Um exemplo comum é a configuração do módulo [Leitor][reader] para que o usuário do serviço Web publicado possa especificar outra fonte de dados quando o serviço Web é acessado. Ou então, configurar o módulo [Gravador][writer] para que um destino diferente possa ser especificado. Alguns exemplos incluem a alteração do número de bits para o módulo [Hash de Recurso][feature-hashing] ou o número de recursos desejados para o módulo [Seleção de Recursos Baseada em Filtros][filter-based-feature-selection].

Você pode definir os Parâmetros do Serviço Web e associá-los a um ou mais parâmetros de módulo no seu teste, podendo também especificar se eles são obrigatórios ou opcionais. O usuário do serviço Web pode então fornecer valores para esses parâmetros quando chamar o serviço Web.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Como definir e usar parâmetros de serviço Web

Você pode definir um parâmetro de serviço Web, clicando no ícone ao lado do parâmetro para um módulo e selecionando "Definir como parâmetro de serviço Web". Isso cria um novo Parâmetro de Serviço Web e o conecta a esse parâmetro do módulo. Em seguida, quando o serviço Web for acessado, o usuário poderá especificar um valor para o Parâmetro de Serviço Web e ele será aplicado ao parâmetro do módulo.

Depois que você definir um Parâmetro de Serviço Web, ele estará disponível para qualquer outro parâmetro de módulo do experimento. Se você definir um parâmetro de serviço da Web associado a um parâmetro para um módulo, poderá usar esse mesmo parâmetro de serviço da Web para qualquer outro módulo, desde que o parâmetro espere o mesmo tipo de valor. Por exemplo, se o Parâmetro de Serviço Web for um valor numérico, só poderá ser usado para parâmetros do módulo que esperem um valor numérico. Quando o usuário definir um valor para o Parâmetro de Serviço Web, ele será aplicado a todos os parâmetros do módulo associados.

Você pode optar por fornecer um valor padrão para o Parâmetro de Serviço Web. Nesse caso, o parâmetro será opcional para o usuário do serviço da Web. Se você não fornecer um valor padrão, o usuário deverá inserir um valor quando o serviço Web for acessado.

A documentação para o serviço Web (fornecido por meio do link **página da Ajuda da API** no serviço Web **PAINEL** no Estúdio de Aprendizado de Máquina) incluirá informações para o usuário do serviço Web sobre como especificar o Parâmetro de Serviço Web programaticamente ao acessar o serviço Web.


##Exemplo

Por exemplo, vamos supor que temos um experimento com um módulo [Gravador][writer] que envia informações para o armazenamento de blobs do Azure. Definiremos um Parâmetro de Serviço Web denominado "Caminho do blob", que permite que o usuário do serviço Web altere o caminho para o armazenamento de blobs quando o serviço for acessado.

1.	No Estúdio de Aprendizado de Máquina, clique no módulo [Gravador][writer] para selecioná-lo. Suas propriedades são mostradas no painel Propriedades à direita da tela do experimento.

2.	Especifique a conta de armazenamento:

    - Em **Especifique o destino de dados**, selecione "Armazenamento de Blobs do Azure".
    - Em **Especifique o tipo de autenticação**, selecione "Conta".
    - Insira as informações de conta para o armazenamento de blobs do Azure. 
    <p />

3.	Clique no ícone à direita de **Caminho para o blob que começa com o parâmetro contêiner**. Ele tem esta aparência:

	![Ícone do Parâmetro de Serviço Web][icon]

    Selecione "Definir como parâmetro de serviço Web".

    Uma entrada é adicionada sob **Parâmetros de Serviço Web** na parte inferior do painel Propriedades com o nome "Caminho para o blob que começa com contêiner". Esse é o Parâmetro de Serviço Web que agora está associado a esse parâmetro do módulo [Gravador][writer].

4.	Para renomear o Parâmetro do Serviço Web, clique no nome, insira "Caminho do blob" e pressione a tecla **Enter**.
 
5.	Para fornecer um valor padrão para o Parâmetro do Serviço Web, clique no ícone à direita do nome, selecione "Fornecer o valor padrão", insira um valor (por exemplo, "contêiner1/saída1.csv") e pressione a tecla **Enter**.

	![Parâmetro de Serviço Web][parameter]

6.	Clique em **Executar**.

7.	Clique em **PUBLICAR SERVIÇO WEB** para publicar o serviço Web.

O usuário do serviço Web agora pode especificar um novo destino para o módulo [Gravador][writer] ao acessar o serviço Web.

##Mais informações

Para obter um exemplo mais detalhado, consulte a entrada [Parâmetros de Serviço Web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) no [Blog de Aprendizado de Máquina](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Para obter mais informações sobre como acessar um serviço Web de Aprendizado de Máquina, consulte [Como consumir um serviço Web de aprendizado de máquina publicado](machine-learning-consume-web-services.md).



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 

<!---HONumber=Oct15_HO3-->