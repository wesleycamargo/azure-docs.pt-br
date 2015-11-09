<properties
	pageTitle="Consumir um serviço Web de Aprendizado de Máquina com um modelo de aplicativo Web | Microsoft Azure"
	description="Use um modelo de aplicativo Web no Azure Marketplace para consumir um serviço Web preditivo no Aprendizado de Máquina do Azure."
	keywords="serviço Web, operacionalização, API REST, aprendizado de máquina"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="garye;raymondl"/>

# Consumir um serviço Web de Aprendizado de Máquina do Azure com um modelo de aplicativo Web

Uma vez você desenvolveu o modelo de previsão e o implantou como um serviço Web do Azure usando o Estúdio de Aprendizado de Máquina ou usando ferramentas como R ou Python, você pode acessar o modelo operacionalizado usando uma API REST.

Há várias maneiras para consumir a API REST e acessar o serviço Web. Por exemplo, é possível escrever um aplicativo em C#, R ou Python usando o código de exemplo gerado para você quando você implantou o serviço Web (disponível na Página de Ajuda da API no painel do serviço Web no Estúdio de Aprendizado de Máquina). Ou você pode usar a pasta de trabalho de exemplo do Microsoft Excel criada para você (também disponível no painel do serviço Web no Studio).

Mas a maneira mais rápida e fácil de acessar o serviço Web é por meio dos modelos de aplicativos Web disponíveis no [Marketplace de Aplicativos Web do Azure](https://azure.microsoft.com/marketplace/web-applications/all/).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Os Modelos de Aplicativos Web do Aprendizado de Máquina do Azure

Os modelos de aplicativo Web disponíveis no Azure Marketplace podem compilar um aplicativo Web personalizado que conhece os dados de entrada do seu serviço Web e os resultados esperados. Basta fornecer ao aplicativo Web o acesso ao serviço e aos dados da Web para que o modelo faça o resto.

Dois modelos estão disponíveis:

- [Modelo do Aplicativo Web do Serviço de Solicitação-Resposta do Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Modelo do aplicativo Web do Serviço de Execução em Lote do Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Cada modelo cria um aplicativo ASP.NET de exemplo, usando o URI da API e a Chave para o serviço Web, e é implantado como um site no Azure. O modelo RRS (Serviço de Solicitação-Resposta) cria um aplicativo Web que permite que você envie uma única linha de dados para o serviço Web para obter um único resultado. O modelo BES (Serviço de Execução em Lote) cria um aplicativo Web que permite enviar várias linhas de dados para obter vários resultados.

Nenhuma codificação é necessária para usar esses modelos. Basta fornecer o URI da API e a Chave para que o modelo compile o aplicativo para você.

## Como usar o modelo RRS (Serviço de Solicitação-Resposta)

Depois de implantar o serviço Web, você pode seguir as etapas abaixo para usar o modelo de aplicativo Web de RRS, como mostrado no diagrama a seguir.

![Processo para usar o modelo da Web RRS][image1]

1. No Estúdio de Aprendizado de Máquina, abra a guia **Serviços Web** e, em seguida, abra o serviço Web que deseja acessar. Copie a chave listada em **Chave de API** e salve-a.

	![Chave de API][image3]

2. Abra a Página de Ajuda da API **REQUEST/RESPONSE**. Na parte superior da Página de Ajuda, em **Solicitar**, copie o valor do **URI de Solicitação** e salve-o. Esse valor terá esta aparência:

		https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

	![URI da solicitação][image4]

3. Vá para [Aplicativo Web do Serviço de Solicitação-Resposta do Aprendizado de Máquina do Azure](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) e clique em **Criar Aplicativo Web**. O modelo leva você até o Portal do Microsoft Azure em que o novo aplicativo Web é criado.

	- Dê um nome exclusivo ao seu aplicativo Web. A URL do aplicativo Web será esse nome seguido de `.azurewebsites.net.` Por exemplo, `http://carprediction.azurewebsites.net.`

	- Selecione a assinatura do Azure e os serviços nos quais seu serviço Web está sendo executado.

	- Clique em **Criar**.

	![Criar um aplicativo Web][image5]

4. Quando o Azure concluir a implantação do aplicativo Web, clique na **URL** na página de configurações do aplicativo Web no Azure, ou insira a URL em um navegador da Web. Por exemplo, `http://carprediction.azurewebsites.net.`

5. Quando o aplicativo Web é executado pela primeira vez, ele solicita a **URL de Postagem da API** e a **Chave de API**. Digite os valores que você salvou anteriormente:
	- **URI de Solicitação** da Página de Ajuda da API para a **URL da Postagem da API**
	- **Chave de API** no painel do serviço Web para a **Chave de API**.

	Clique em **Enviar**.

	![Insira o URI de Postagem e a Chave de API][image6]

6. O aplicativo Web exibe sua página **Configuração de Aplicativo Web** com as configurações atuais do serviço Web. Aqui você pode alterar as configurações usadas pelo aplicativo Web.

	> [AZURE.NOTE]Alterar as configurações aqui apenas as altera para este aplicativo Web. Ele não altera as configurações padrão do serviço Web. Por exemplo, se você alterar a **Descrição** aqui, isso não alterará a descrição mostrada no painel de serviço Web no Estúdio de Aprendizado de Máquina.

	Quando terminar, clique em **Salvar alterações** e clique em **Ir para a Página Inicial**.

7. Na página inicial, é possível inserir valores para enviar para o serviço Web e clicar em **Enviar** para que o resultado seja retornado.

Para retornar à página **Configuração**, vá para a página `setting.aspx` do aplicativo Web. Por exemplo: `http://carprediction.azurewebsites.net/setting.aspx.` Você será solicitado a digitar a chave de API novamente – você precisa dela para acessar a página e atualizar as configurações.

Você pode parar, reiniciar ou excluir o aplicativo Web no Portal do Azure como qualquer outro aplicativo Web. Enquanto ele estiver em execução, você poderá navegar até o endereço web da página inicial e inserir novos valores.

## Como usar o modelo BES (Serviço de Execução em Lote)

É possível usar o modelo de aplicativo Web BES da mesma forma que o modelo RRS, exceto que o aplicativo Web criado permitirá que você envie várias linhas de dados e receba vários resultados.

Os resultados de um serviço Web de execução em lotes são armazenados em um contêiner de armazenamento do Azure; os valores de entrada podem ter como origem um arquivo local ou o armazenamento do Azure. Assim, você precisará de um contêiner de armazenamento do Azure para armazenar os resultados retornados pelo aplicativo Web e precisará preparar os dados de entrada.

![Processo para usar o modelo da Web BES][image2]

1. Siga o mesmo procedimento para criar um aplicativo Web do BES para o modelo RRS, exceto:
	- Obtenha o **URI de Solicitação** da Página de Ajuda da API **BATCH EXECUTION** do serviço Web.
	- Vá para [Modelo do Aplicativo Web do Serviço de Execução em Lote do Aprendizado de Máquina do Azure](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) para abrir o modelo BES no Azure Marketplace e clique em **Criar Aplicativo Web**.

2. Para especificar onde você deseja que os resultados sejam armazenados, insira as informações do contêiner de destino na home page do aplicativo Web. Especifique também onde o aplicativo Web pode obter os valores de entrada, em um arquivo local ou em um contêiner de armazenamento do Azure. Clique em **Enviar**.

	![Informações de armazenamento][image7]

O aplicativo Web exibirá uma página com o status do trabalho. Quando tiver concluído o trabalho, você receberá o local dos resultados no armazenamento de blob do Azure. Você também tem a opção de baixar os resultados em um arquivo local.

## Para obter mais informações

Para saber mais sobre...

- como criar uma experiência de aprendizado de máquina com o Estúdio de Aprendizado de Máquina, veja [Criar seu primeiro teste no Estúdio de Aprendizado de Máquina do Azure](machine-learning-create-experiment.md)

- como implantar sua experiência de aprendizado de máquina como um serviço Web, veja [Implantar um serviço Web de Aprendizado de Máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md)

- outras maneiras de acessar o serviço Web, veja [Como consumir um serviço Web de Aprendizado de Máquina do Azure](machine-learning-consume-web-services.md)


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png

<!---HONumber=Nov15_HO1-->