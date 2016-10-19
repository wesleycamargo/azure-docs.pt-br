<properties
	pageTitle="Readaptar os modelos de Aprendizado de Máquina de forma programática | Microsoft Azure"
	description="Aprenda como readaptar um modelo de forma programática e atualizar o serviço Web para usar o modelo treinado recentemente no Machine Learning do Azure."
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
	ms.date="09/28/2016"
	ms.author="raymondl;garye;v-donglo"/>


#Readaptar os modelos de Aprendizado de Máquina de forma programática  

Como parte do processo de operacionalização de modelos de aprendizado de máquina no Aprendizado de Máquina do Azure, um modelo deve ser treinado e salvo. Em seguida, use-o para criar um serviço Web predicativo. O serviço Web pode ser consumido nos sites, painéis e aplicativos móveis.

Os modelos que você cria usando o Aprendizado de Máquina geralmente não são estáticos. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser readaptado. Ou talvez seja necessário aplicar filtros para obter um subconjunto dos dados e readaptar o modelo.

A readaptação pode ocorrer com frequência. Com o recurso da API de Readaptação Programática, você pode recuperar programaticamente o modelo usando as APIs de Readaptação e atualizar o Serviço Web com o modelo treinado recentemente.

Este documento descreve o processo de readaptação e mostra como usar as APIs de Readaptação.

## Por que readaptar: definindo o problema  

Como parte do processo de treinamento do Machine Learning, um modelo é treinado usando um conjunto de dados. Os modelos que você cria usando o Aprendizado de Máquina geralmente não são estáticos. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser readaptado. Os cenários adicionais podem exigir a aplicação de filtros de obter um subconjunto dos dados e readaptar o modelo.

Nesses cenários, uma API de programação fornece uma maneira conveniente de permitir que você ou o consumidor de suas APIs criem um cliente que pode, em uma base única ou regular, readaptar o modelo usando seus próprios dados. Em seguida, podem avaliar os resultados da readaptação e atualizar a API do serviço Web para usar o modelo readaptado recentemente.

##Como readaptar: o processo ponta a ponta  

Para começar, o processo envolve os seguintes componentes: um Teste de Treinamento e um Teste de Previsão publicados como um serviço Web. Para habilitar a readaptação de um modelo treinado, o Teste de Treinamento também deve ser publicado como um serviço Web com a saída de um modelo treinado. Isso permite o acesso de API para o modelo de readaptação.

O processo para configurar a readaptação de um serviço Web Clássico envolve as seguintes etapas:

![Visão geral do processo readaptação][1]

Diagrama 1: Processo de readaptação para uma visão geral do serviço Web Clássico

O processo para configurar a readaptação de um Novo serviço Web envolve as seguintes etapas:

![Visão geral do processo readaptação][7]

Diagrama 2: Processo de readaptação para uma visão geral do Novo serviço Web

## Criar um teste de treinamento
 
Para este exemplo, você usará "Amostra 5: Treinar, testar, avaliar para classificação binária: conjunto de dados adulto" das amostras do Aprendizado de Máquina do Microsoft Azure.
	
Para criar o experimento:

1.	Entre no Estúdio de Aprendizado de Máquina do Microsoft Azure.
2.	No canto inferior direito do painel, clique em **Novo**.
3.	Nas Amostras da Microsoft, selecione a Amostra 5.
4.	Para renomear o teste, na parte superior da tela do experimento, selecione o nome do teste "Amostra 5: Treinar, testar, avaliar para classificação binária: conjunto de dados adulto".
5.	Tipo de modelo de censo.
6.	Na parte inferior da tela do experimento, clique em **Executar**.
7.	Clique em **Configurar o Serviço Web** e selecione **Readaptação do Serviço Web**.

 	![Experimento inicial.][2]

Diagrama 2: Experimento inicial.

## Criar um Teste de Pontuação e publicar como um serviço Web  

Em seguida, crie um Experimento Predicativo.

1.	Na parte inferior da tela do experimento, clique em **Configurar o Serviço Web** e selecione **Serviço Web Preditivo**. Isso salva o modelo como um Modelo Treinado e adiciona os módulos de Entrada e Saída do serviço Web.
2.	Clique em **Executar**.
3.	Após o teste em execução ter terminado, clique em **Implantar Serviço Web [Clássico]** ou **Implantar Serviço Web [Novo]**.

## Implantar o Teste de Treinamento como um serviço Web de Treinamento

Para readaptar o modelo treinado, você deve implantar o Teste de Treinamento criado como um Serviço Web de Readaptação. Este serviço Web precisa um módulo *Saída do Serviço Web* conectado ao módulo *[Modelo de Treino][train-model]* para poder produzir novos modelos treinados.

1. Para voltar para o teste de treinamento, clique no ícone de Experimentos no painel esquerdo, em seguida, clique no experimento chamado Modelo de Censo.
2. Na caixa de pesquisa Pesquisar Itens do Experimento, digite o serviço Web.
3. Arraste um módulo *Entrada do Serviço Web* para a tela do experimento e conecte sua saída ao módulo *Limpar Dados Ausentes*.
4. Arraste os dois módulos *Saída do Serviço Web* para as telas do experimento. Conecte a saída do módulo *Modelo de Treinamento* a um e a saída do módulo *Modelo de Avaliação* ao outro. A saída do serviço Web para o **Modelo de Treino** fornecerá o novo modelo treinado. A saída anexada ao **Modelo de Avaliação** retorna a saída do módulo.
5. Clique em **Executar**.

Em seguida, você deve implantar o Teste de Treinamento como um serviço Web que produz um modelo treinado e os resultados de avaliação do modelo. Para fazer isso, o próximo conjunto de ações dependem de você estar trabalhando com um serviço Web Clássico ou um Novo serviço Web.
  
**Serviço Web Clássico**

Na parte inferior da tela do experimento, clique em **Configurar o Serviço Web** e selecione **Implantar Serviço Web [Clássico]**. O **Painel** do Serviço Web é exibido com a Chave de API e a página de ajuda da API para a Execução em Lotes. Apenas o método de Execução em Lotes pode ser usado para criar Modelos Treinados.

**Novo Serviço Web**

Na parte inferior da tela do experimento, clique em **Configurar o Serviço Web** e selecione **Implantar Serviço Web [Novo]**. O portal dos Serviços Web de Machine Learning do Azure abre a página Implantar Serviço Web. Digite um nome para o serviço Web, escolha um plano de pagamento clique em **Implantar**. Apenas o método Execução em Lotes pode ser usado para criar os Modelos Treinados

Em ambos os casos, após o teste terminar a execução, o fluxo de trabalho resultante fica como a seguir:

![Fluxo de trabalho resultante após a execução.][4]

Diagrama de 3: Fluxo de trabalho resultante após a execução.

## Você quer readaptar o modelo com novos dados

Para chamar as APIs de Readaptação:

1. Crie um Aplicativo de Console do C# no Visual Studio (Novo->Projeto->Área de Trabalho do Windows->Aplicativo de Console).
2.	Entre no portal do Serviço Web de Machine Learning.
3.	Se você estiver trabalhando com um serviço Web Clássico, clique em **Serviços Web Clássicos**.
	1.	Clique no serviço Web com o qual você está trabalhando.
	2.	Clique no ponto de extremidade padrão.
	3.	Clique em **Consumo**.
	4.	Na parte inferior da página **Consumir**, na seção **Código de Exemplo**, clique em **Lote**.
	5.	Continue na etapa 5 deste procedimento.
4.	Se você estiver trabalhando com um Novo serviço Web, clique em **Serviços Web**.
	1.	Clique no serviço Web com o qual você está trabalhando.
	2.	Clique em **Consumo**.
	3.	Na parte inferior da página Consumir, na seção **Código de Exemplo**, clique em **Lote**.
5.	Copie o código C# de exemplo da execução em lotes e cole-o no arquivo Program.cs, verificando se o namespace permanece intacto.

Adicione o pacote do Nuget Microsoft.AspNet.WebApi.Client como especificado nos comentários. Para adicionar a referência a Microsoft.WindowsAzure.Storage.dll, você precisará primeiro instalar a biblioteca de cliente para os serviços de armazenamento do Microsoft Azure. Para obter mais informações, consulte [Serviços de Armazenamento do Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### Atualizar a declaração da apikey

Localize a declaração da **apikey**.

	const string apiKey = "abc123"; // Replace this with the API key for the web service

Na seção **Informações básicas de consumo** da página **Consumir**, localize a chave primária e copie-a para a declaração da **apikey**.

### Atualize as informações do Armazenamento do Azure

O código de exemplo de BES carrega um arquivo de uma unidade local (por exemplo "C:\\temp\\CensusIpnput.csv") para o armazenamento do Azure, processa e grava os resultados de volta para o armazenamento do Azure.

Para realizar essa tarefa, você deve recuperar as informações de nome da Conta de armazenamento, da chave e do contêiner para sua Conta de armazenamento no portal clássico do Azure e os valores correspondentes de atualização no código.

1. Entre no portal clássico do Azure.
1. Na coluna de navegação à esquerda, clique em **Armazenamento**.
1. Na lista de contas de armazenamento, selecione uma para armazenar o modelo recuperado.
1. Na parte inferior da página, clique em **Gerenciar Chaves de Acesso**.
1. Copie e salve a **Chave de Acesso Primária** e feche a caixa de diálogo.
1. Na parte superior da página, clique em **Contêineres**.
1. Selecione um contêiner existente ou crie um novo e salve o nome.

Localize as declarações *StorageAccountName*, *StorageAccountKey* e *StorageContainerName*, e atualize os valores salvos no portal no Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
Você também deve garantir que o arquivo de entrada está disponível no local especificado no código.

### Especificar o local de saída

Ao especificar o local de saída no Conteúdo de Solicitação, a extensão do arquivo especificado em *RelativeLocation* deve ser definido como ilearner. Veja os exemplos a seguir.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] Os nomes de seus locais de saída podem ser diferentes neste passo a passo com base na ordem em que você adicionou os módulos de saída do serviço Web. Como você configura esse Teste de Treinamento com duas saídas, os resultados incluem informações de local de armazenamento para ambos.

![Saída da readaptação][6]

Diagrama 4:Saída da readaptação.

## Avaliar os resultados da readaptação
 
Quando você executa o aplicativo, a saída inclui o token SAS e a URL necessários para acessar os resultados da avaliação.

Você pode ver os resultados do desempenho do modelo readaptado ao combinar *BaseLocation*, *RelativeLocation* e *SasBlobToken* dos resultados de saída para *output2* (como mostrado na imagem de readaptação anterior) e colando a URL completa na barra de endereço do navegador.

Examine os resultados para determinar se o modelo treinado recentemente executa bem o suficiente para substituir o existente.

Copie *BaseLocation*, *RelativeLocation* e *SasBlobToken* dos resultados de saída e use-os durante o processo de readaptação.

## Próximas etapas

[Readaptar um serviço Web Clássico](machine-learning-retrain-a-classic-web-service.md)

[Readaptar um Novo serviço Web usando os cmdlets de Gerenciamento do Machine Learning](machine-learning-retrain-new-web-service-using-powershell.md)

<!-- Retrain a New Web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0928_2016-->