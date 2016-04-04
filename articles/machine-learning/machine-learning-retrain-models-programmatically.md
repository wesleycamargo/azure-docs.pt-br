<properties
	pageTitle="Readaptar os modelos de Aprendizado de Máquina de forma programática | Microsoft Azure"
	description="Aprenda como readaptar um modelo de forma programática e atualizar o serviço Web para usar o modelo treinado recentemente no Aprendizado de Máquina do Azure."
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
	ms.date="03/17/2016"
	ms.author="raymondl;garye"/>


#Readaptar os modelos de Aprendizado de Máquina de forma programática  

Como parte do processo de operacionalização de modelos de aprendizado de máquina no Aprendizado de Máquina do Azure, um modelo deve ser treinado e salvo e, em seguida, usado para criar um serviço Web preditivo. O serviço Web pode ser consumido em sites, painéis e aplicativos móveis.

Normalmente, é necessário readaptar o modelo criado na primeira etapa com novos dados. Anteriormente, isso só era possível com a interface do usuário do Azure ML, mas com a introdução do recurso de API de Readaptação de Programação. Agora você pode readaptar o modelo e atualizar o serviço Web com o modelo treinado recentemente, de forma programática, usando as APIs de Readaptação.

Este documento descreve o processo acima e mostra como usar as APIs de Readaptação.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Por que readaptar: definindo o problema  
Como parte do processo de treinamento ML, um modelo é treinado usando um conjunto de dados. Os modelos devem ser readaptados em cenários em que novos dados se tornam disponíveis, ou quando o consumidor da API tem seus próprios dados para treinar o modelo ou quando os dados precisam ser filtrados e o modelo treinado com o subconjunto de dados, etc.

Nesses cenários, uma API de programação fornece uma maneira conveniente de permitir que você ou o consumidor de suas APIs criem um cliente que pode, em uma base única ou regular, readaptar o modelo usando seus próprios dados. Em seguida, eles podem avaliar os resultados da readaptação e atualizar a API do serviço Web para usar o modelo readaptado recentemente.

##Como readaptar: o processo ponta a ponta  
Para começar, o processo envolve os seguintes componentes: um Teste de Treinamento e um Experimento Preditivo publicado como um serviço Web. Para habilitar a readaptação de um modelo treinado, o Experimento de Treinamento deve também ser publicado como um serviço Web com a saída de um modelo treinado. Isso permite o acesso de API para o modelo de readaptação. O processo para configurar a readaptação envolve as seguintes etapas:

![][1]

Diagrama 1: visão geral do processo readaptação

1. *Criar um Experimento de Treinamento* Usaremos o experimento "Exemplo 5 (Treinamento, Teste, Avaliação de Classificação Binária: Conjunto de Dados Adulto)" dos experimentos de exemplo do Azure ML para este exemplo. Como você verá abaixo, eu simplifiquei o exemplo removendo alguns módulos. O experimento também foi nomeado como "Modelo de Censo".

 	![][2]

	Com essas peças no lugar, agora é possível clicar em Executar na parte inferior da tela para executar este experimento.  
2. *Criar um Experimento de Pontuação e publicar como um Serviço Web*  

	![][3]

	Após a conclusão da execução do experimento, clicamos em Criar Experimento Preditivo. Isso cria um Experimento Preditivo, salva o modelo como um Modelo Treinado e adiciona os módulos de Entrada e Saída do serviço Web, conforme mostrado abaixo. Em seguida, clicamos em Executar.

	Após concluir a execução do experimento, clicar em "Publicar Serviço Web" publicará o Experimento Preditivo como um Serviço Web e criará um ponto de extremidade padrão. O modelo treinado neste serviço Web é atualizável, conforme mostrado abaixo. Os detalhes desse ponto de extremidade serão exibidos na tela.
3. *Publicar o Experimento de Treinamento como um Serviço Web*: para treinar novamente o modelo treinado, é necessário publicar o Experimento de Treinamento que criamos na etapa 1 acima como um Serviço Web. Este serviço Web precisará de um módulo de Saída do Serviço Web conectado ao [Modelo de Treino][train-model] para poder produzir novos modelos treinados. Clique no ícone de Experimentos no painel esquerdo, em seguida, clique no experimento chamado Modelo de Censo para voltar para o experimento de treinamento.  

	Em seguida, adicionamos uma Entrada de Serviço Web e dois módulos de Saída do Serviço Web para o fluxo de trabalho. A saída do Serviço Web para o Modelo de Treinamento nos dará o novo modelo treinado. A saída anexada ao Modelo de Avaliação retornará a saída do Modelo de Avaliação do módulo.

	É possível, agora, clique em Executar. Depois de concluir a execução do experimento, o fluxo de trabalho resultante deve ser conforme mostrado abaixo:

	![][4]

	Em seguida, clicamos no botão Implantar Serviço Web e clicamos em Sim. Essa ação implantará o Teste de Treinamento como um Serviço Web que produz um modelo treinado e resultados de avaliação do modelo. O Painel do Serviço Web será exibido com a Chave de API e a página de ajuda da API para a Execução em Lotes. Observe que apenas o método de Execução em Lotes pode ser usado para criar Modelos Treinados.  
4. *Adicionar um novo Ponto de Extremidade* O Serviço Web Preditivo que publicamos na Etapa 2 acima é o ponto de extremidade de pontuação padrão. Os pontos de extremidade padrão são mantidos em sincronização com o experimento de origem e o treinamento original; e um modelo treinado do ponto de extremidade padrão não pode ser substituído. Para criar um novo ponto de extremidade de pontuação com um modelo capaz de atualizar, visite o Portal Clássico do Azure e clique em Adicionar Ponto de Extremidade (mais detalhes [aqui](machine-learning-create-endpoint.md)). Você também pode adicionar pontos de extremidade de pontuação usando o código de exemplo fornecido [aqui](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

5. *Readaptar o modelo com novos dados e BES* Para chamar as APIs de Recuperação, criamos um novo Aplicativo de Console C# no Visual Studio (Novo -> Projeto -> Windows Desktop -> Aplicativo de Console).

	Em seguida, copiamos o código C# de exemplo da página de ajuda da API do Serviço Web de Treinamento para a execução em lotes (criada na Etapa 3 acima) e o colamos no arquivo Program.cs, certificando de que o namespace permanece intacto.

	Observe que o código de exemplo tem comentários que indicam as partes do código que precisam de atualizações. Além disso, ao especificar o local “output1” na Carga de Solicitação, a extensão de arquivo do “RelativeLocation” deve ser alterada para “.ilearner” como em:

	```c#
	Outputs = new Dictionary<string, AzureBlobDataReference>()
	{
		{
			"output1",
			new AzureBlobDataReference()
			{
				ConnectionString = "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==",
				RelativeLocation = "mycontainer/output1results.ilearner"
			}
		},
	},
	```
	1. Fornecer informações de Armazenamento do Azure. O código de exemplo para BES carregará um arquivo de uma unidade local (por exemplo, "C:\\temp\\CensusIpnput.csv") para o Armazenamento do Azure, irá processá-lo e gravar os resultados no Armazenamento do Azure.  

		Para fazer isso, você precisa recuperar o nome da conta de Armazenamento, a chave e as informações de contêiner do Portal Clássico do Azure para a sua conta de Armazenamento e, em seguida, atualizar o código aqui. Você também precisa garantir que o arquivo de entrada está disponível no local especificado no código.

		Configuramos esse Experimento de Treinamento com duas saídas, portanto os resultados incluirão informações do local de armazenamento para ambos, conforme mostrado abaixo. "output1" é a saída do Modelo Treinado e "output2" a saída do Modelo de Avaliação. Lembre-se também de que a extensão de arquivo da Saída para o Modelo Treinado (Output1) é “.ileaner” e não “.csv”.

		![][6]

6. *Avaliar os Resultados da Recuperação* Usando a combinação de BaseLocation, RelativeLocation e SasBlobToken dos resultados de saída acima para "output2" podemos ver os resultados de desempenho do modelo recuperado colando a URL completa na barra de endereço do navegador.

	Isso nos informará se o modelo treinado recentemente executa bem o suficiente para substituir o existente.

7. *Atualizar o Modelo Treinado do Ponto de Extremidade agregado* Para concluir o processo, precisamos atualizar o modelo treinado do ponto de extremidade preditivo (pontuação) que criamos na Etapa 4 acima.

	(Se tiver adicionado um novo ponto de extremidade usando o Portal do Azure, você pode clicar no nome do novo do ponto de extremidade, em seguida, no link UpdateResource para obter a URL que você precisa para atualizar o modelo do ponto de extremidade. Se você tiver adicionado o ponto de extremidade usando código, a saída dessa chamada terá a URL do ponto de extremidade).

	A saída de BES acima mostra as informações para o resultado de recuperação para "output1", que contém as informações de local do modelo recuperado. Agora precisamos pegar esse modelo treinado e atualizar o ponto de extremidade da pontuação (criado na etapa 4 acima). Veja abaixo o exemplo de código:

	```C#
	private async Task OverwriteModel()
	{
		var resourceLocations = new
		{
			Resources = new[]
			{
				new
				{
					Name = "Census Model [trained model]",
					Location = new AzureBlobDataReference()
					{
						BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
						RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
						SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
					}
				}
			}
		};

		using (var client = new HttpClient())
		{
			client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

			using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
			{
				request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
				HttpResponseMessage response = await client.SendAsync(request);

				if (!response.IsSuccessStatusCode)
				{
					await WriteFailedResponse(response);
				}

				// Do what you want with a successful response here.
			}
		}
	}
	```

	"apiKey" e "endpointUrl" ficam visíveis no painel do ponto de extremidade para esta chamada. O parâmetro "Nome" em Recursos deve corresponder ao nome do Modelo Treinado Salvo no Teste Preditivo.
	
	Observe que o token SAS expira depois de uma hora (55 minutos). Você precisa fazer um GET com a Id de Trabalho para obter um novo token.

	Com o sucesso desta chamada, o novo ponto de extremidade será iniciado usando um modelo recuperado aproximadamente dentro de 15 segundos.

##Resumo  
Usando as APIs de Recuperação podemos atualizar o modelo treinado de um de Serviço Web previsto que possibilita cenários como modelo periódico de recuperação com novos dados ou a distribuição de modelos para os clientes com o objetivo de permitir que eles recuperem o modelo usando seus próprios dados.

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0323_2016-->