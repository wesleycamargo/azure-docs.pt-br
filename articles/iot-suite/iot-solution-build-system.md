<properties 
	pageTitle="Exemplo de IoT do Azure do MyDriving – Crie-o | Microsoft Azure" 
	description="Crie um aplicativo que é uma demonstração abrangente de como arquitetar um sistema de IoT com o Microsoft Azure, incluindo o Stream Analytics, o Aprendizado de Máquina e os Hubs de Eventos." 
	services="" 
    documentationCenter=""
    suite="iot-suite"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="iot-suite" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2016" 
	ms.author="awills"/>


# Criar e implantar a Solução MyDriving no seu ambiente

O MyDriving é uma solução de IoT (Internet das Coisas) que reúne dados do seu carro, processa-os usando o aprendizado de máquina e apresenta-os no seu celular. O back-end consiste em uma variedade de serviços fornecidos pelo Microsoft Azure e os clientes podem ser telefones Android, iOS ou Windows 10.

Criamos a solução MyDriving para oferecer a você um ponto de partida para a criação do seu próprio sistema de IoT. No repositório você poderá obter scripts do Azure Resource Manager para implantar a arquitetura de back-end em sua própria conta do Azure, por meio da qual você pode reconfigurar serviços diferentes, modificar as consultas para adequá-las aos seus próprios dados e assim por diante. Você pode encontrar esses scripts junto com o código para o aplicativo móvel, o projeto da API do Serviço de Aplicativo do Azure e muito mais em <https://github.com/Azure-Samples/MyDriving>.

Se você ainda não experimentou o aplicativo, examine o [Guia de introdução](iot-solution-get-started.md).

## O que você precisa criar?

Há uma conta detalhada da arquitetura no [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs). Em resumo, há várias partes que preparamos e que você prepararia para criar um projeto semelhante:

* **Aplicativo cliente**. Ele é executado em telefones Android, iOS e Windows 10. Podemos usar a plataforma Xamarin para compartilham grande parte do código, o qual é armazenado no GitHub em `src/MobileApp`. O aplicativo na verdade executa duas funções distintas:
 * **Telemetria de retransmissão** do dispositivo OBD do seu próprio serviço local para o back-end da nuvem do sistema.
 * **Interface do usuário** que permite aos usuários consultar sobre suas viagens gravadas.
* **Serviço de nuvem** que consome os dados da viagem em tempo real e os processa. O trabalho principal de criar esse serviço é escolher, parametrizar e interligar uma variedade de serviços do Azure. Algumas das partes exigem scripts para filtrar e processar os dados de entrada. Usamos uma modelo do **ARM (Azure Resource Management)** para configurar todas as partes.
* **Aplicativo de serviço móvel**. Este é o serviço Web por trás da parte de interface do usuário do aplicativo do dispositivo. Sua principal tarefa é consultar o banco de dados processados e armazenados. Seu código está no GitHub em `src/MobileAppService`.
* O **Visual Studio com Xamarin** é nosso ambiente de desenvolvimento. O Xamarin, que existe tanto como componente do Visual Studio quanto como um IDE autônomo, é usado para compilar o código do dispositivo de plataforma cruzada. Para compilar o código do iOS, é necessário ter uma instância de Xamarin em execução em um computador com OSX, contudo se necessário ele pode ser executado como um agente gerenciado do Visual Studio.
* **Testes de unidade** dos aplicativos do dispositivo são executados no Xamarin Test Cloud.
* O **GitHub** é o repositório no qual armazenamos todos os códigos, scripts e modelos.
* O **Visual Studio Team Services** é um serviço de nuvem usado para gerenciar criação e teste contínuos do serviço Web e aplicativos do dispositivo.
* O **HockeyApp** é usado para distribuir as versões do código de dispositivo. Além de gerenciar a distribuição, ele coleta relatórios de uso e falhas, bem como comentários do usuário.
* O **Application Insights** monitora o serviço Web móvel.


Vejamos como configurar tudo isso. Observe que muitas das etapas são opcionais.

## Inscrever-se em contas

-   [**Visual Studio Dev Essentials**](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx). Esse programa gratuito oferece acesso fácil a várias ferramentas e serviços de desenvolvedor, incluindo o Visual Studio, o Visual Studio Team Services e o Azure, concedendo U$25/mês de crédito no Azure por 12 meses. Ele também inclui assinaturas para treinamento do Pluralsight e Xamarin University. Você pode se inscrever separadamente para camadas gratuitas do [Azure](https://azure.com) e do [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx), mas eles não fornecem créditos do Azure.

-   [**HockeyApp**](https://rink.hockeyapp.net/) (opcional), para gerenciar a distribuição de teste de aplicativos móveis e a coleta de telemetria.

-   [**Xamarin**](https://xamarin.com/) (obrigatório), para criar o aplicativo móvel e executar testes e execuções de depuração no [Xamarin Test Cloud](https://xamarin.com/test-cloud).

-   [**GitHub**](https://github.com/Azure-Samples/MyDriving/) (opcional), para criar repositórios públicos gratuitos para seu próprio código (repositórios particulares são pagos). Como alternativa, use o plano básico no Visual Studio Team Services para repositórios particulares.

-   [**Power BI**](https://powerbi.microsoft.com/) (opcional), para criar visualizações avançadas dos dados em todo o sistema.

> [AZURE.NOTE] Você não precisa de uma conta do GitHub para acessar o código do MyDriving em [https://github.com/Azure-Samples/MyDriving](https://github.com/Azure-Samples/MyDriving).

## Instalar ferramentas de desenvolvimento

A configuração a seguir serve para desenvolver a solução completa: um aplicativo de plataforma cruzada do iOS, Android e Windows Phone 10 com um back-end do Azure.

Como alternativa, você poderá usar o Xamarin Studio no Mac ou no Windows para desenvolver aplicativos móveis se não estiver trabalhando no back-end do Azure.

Há uma [descrição maior dessa configuração aqui](https://msdn.microsoft.com/library/mt613162.aspx).

### Computador de desenvolvimento do Windows

A ferramenta central no Windows é o Visual Studio para trabalhar com o aplicativo MyDriving para Android e Windows, o projeto de API do Serviço de Aplicativo e extensões de microsserviço.

Xamarin, Git, emuladores e outros componentes úteis são todos integrados ao Visual Studio.

Instalar:

-   [**Visual Studio 2015 com Xamarin**](https://www.visualstudio.com/products/visual-studio-community-vs) (qualquer edição – o Community é gratuito)

-   [**SQLite para Plataforma Universal do Windows**](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936). Necessário para compilar o código do Windows Phone 10.

-   [SDK do Azure para VS2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) – Oferece o SDK para aplicativos em execução no Azure e ferramentas de linha de comando para gerenciar o Azure.

-   [SDK do Azure Service Fabric](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric). Necessário para criar a extensão de [microsserviço](../service-fabric/service-fabric-get-started.md).

Extensões do Visual Studio: verifique se em Ferramentas exibe Android, iOS, Xamarin... Se não, abra o **Painel de Controle**, **Programas e Recursos**, **Microsoft** **Visual Studio 2015**, **Modificar**. Em **Desenvolvimento de Plataforma Cruzada**, escolha **C#/.Net (Xamarin).** Enquanto você está na página, verifique se o **GitHub** está instalado.

### Computador de desenvolvimento do Mac

O Mac (Yosemite ou posterior) será necessário se você quiser desenvolver em iOS. Embora usemos o Visual Studio com Xamarin no Windows para desenvolver e gerenciar todo o código, o Xamarin usa um agente instalado em um Mac para criar e assinar o código do iOS.

![](./media/iot-solution-build-system/image1.png)

(Como alternativa, você pode usar o Xamarin Studio diretamente no Mac para desenvolver aplicativos de plataforma cruzada.)

O Mac não será necessário se você não desejar incluir o iOS como uma plataforma de destino.

Instalar:

-   [**Xamarin Studio** para iOS](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/). Você também pode configurar o Visual Studio e Xamarin em um Mac executando uma máquina virtual do Windows. Consulte [Configuração, instalação e verificações para usuários do Mac](https://msdn.microsoft.com/library/mt488770.aspx) no MSDN.

-   (Opcional) [Ferramentas de desenvolvimento do Azure](https://azure.microsoft.com/downloads/).

Habilitar logon remoto no Mac. Abra Preferências do Sistema, Compartilhamento e marque Logon Remoto.

Quando você abrir um projeto do iOS no Visual Studio no Windows, o plug-in do Xamarin solicitará a ID do Mac.

## Obter o repositório do GitHub

Obtenha uma cópia local do <https://github.com/Azure-Samples/MyDriving> usando o botão **Baixar ZIP** no GitHub, no Visual Studio ou em outro cliente Git.

Descompacte o arquivo para uma pasta com um nome de caminho curto, como C:\\código.

Como alternativa, se você quiser contribuir com nosso código ou mantê-lo atualizado, clone o repositório da seguinte maneira:

> clone do git <https://github.com/Azure-Samples/MyDriving.git>

## Código do desenvolvedor do Bing Mapas

[Registre-se para uma Chave de API do Bing Mapas](https://msdn.microsoft.com/library/ff428642.aspx).

Você precisa substituir isso na linha 22 em `src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs`



## Criar o aplicativo de demonstração

Abra essas soluções no Visual Studio:

-   src\\MobileApps\\MyDriving.sln
-   src\\MobileAppService\\MyDrivingService.sln
-   src\\Extensions\\ServiceFabric\\VINLookUpApplication\\VINLookUpApplication.sln

Você receberá prompts para:

-   Confiar em alguns projetos potencialmente não confiáveis. Escolher abri-los se desejar continuar.

-   Definir o Modo de desenvolvedor se você estiver trabalhando em um novo computador do Windows 10.

-   Fornecer suas credenciais do Xamarin.

-   Conectar-se ao Mac do Xamarin. Se você não tiver um Mac, clique com o botão direito do mouse no projeto do iOS no Visual Studio e escolha **Descarregar projeto**.

Recriar a solução.

### Problemas de criação?

Estas são algumas peculiaridades que às vezes encontramos:

-   O projeto VINLookupApplication não carrega: verifique se você instalou o [SDK do Azure para VS2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

-   O Service Fabric não cria: crie primeiramente os projetos de interfaces e verifique se você instalou o SDK do Service Fabric.

-   O aplicativo do Android não cria:

    -   Abra Ferramentas, Android, Gerenciador de SDK do Android e verifique se o Android 6 (API 23)/Plataforma de SDK está instalado.

    -   Exclua este diretório e recrie:<br/> `%LocalAppData%\Xamarin\zips`

## Conheça o código

Na solução, você encontrará:

-   Extensões do Azure

    -   Service Fabric

-   HDInsight – scripts para processar dados de viagem no Azure

-   Aplicativos Móveis – os aplicativos de dispositivo

-   MobileAppsService/MyDrivingService – o back-end da Web

-   Power BI – definição de relatório do Power BI

-   Scripts

    -   ARM – modelos para criar os recursos do Azure

    -   PowerShell-scripts para executar os modelos do ARM

    -   SQLDatabases – bancos de dados de depuração

-   SQLDatabase

    -   CreateTables – definições de esquema

-   StreamingAnalytics – consultas que transformam o fluxo de dados de entrada

## Executar os aplicativos no modo de desenvolvimento

### Back-end

Defina o MyDrivingService como o projeto de inicialização e pressione F5 para executar o serviço Web de back-end. Ele será aberto uma exibição de navegador da listagem de API.

### Clientes móveis

Os [aplicativos móveis são desenvolvidos em Xamarin](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/).

-   [Depurando Android no Xamarin](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/)

### iOS

-   [Depurando no iOS](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/)

### Windows Phone

-   [Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/)


## HockeyApp

O HockeyApp gerencia a distribuição de seu aplicativo de dispositivos Android, iOS Windows para usuários de teste e notificar os usuários sobre novas versões. Ele também coleta relatórios de falha úteis, comentários do usuário com capturas de tela e métricas de uso.

[Comece carregando](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app) seu aplicativo de build. Em seguida, conecte-se ao HockeyApp (<https://rink.hockeyapp.net>) no seu computador de desenvolvimento. No painel do desenvolvedor, clique em **Novo Aplicativo** e arrastar os arquivos criados para a janela. (Posteriormente, você pode automatizar o serviço de build para fazer isso).

Agora você está no painel do aplicativo.

![](./media/iot-solution-build-system/image2.png)

Repita o processo para cada plataforma em que seu aplicativo será executado.

- Use a [ID do aplicativo](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) do painel para enviar comentários e dados de falha do seu aplicativo. No MyDriving, atualize as IDs em src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs 
-   [**Convide os usuários de teste**](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers). Você recebe uma URL para recrutar usuários de teste. Eles poderão se inscrever na sua equipe, baixar o aplicativo e enviar comentários.

-   Ou então, se você preferir uma versão beta aberta, defina a distribuição para pública: clique em **Gerenciar Aplicativo, Distribuição, Download = Público.** Agora qualquer pessoa poderá baixar seu aplicativo e enviar comentários, e eles verão uma notificação quando você postar uma nova versão. E você pode também obter alguns relatórios de falha deles. ![](./media/iot-solution-build-system/image3.png)

-   [**Vincular relatórios de falhas ao Visual Studio Team Services**](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs): clique em **Gerenciar Aplicativo, Visual Studio Team Services**. O HockeyApp pode criar itens de trabalho automaticamente no Team Services quando há relatórios de falha ou ao receber comentários.

> Saiba mais em <https://hockeyapp.net>.

## Xamarin Test Cloud

O [Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) automatiza os testes de interface do usuário em dispositivos reais na nuvem. Usando a estrutura NUnit, você escrever testes que executam seu aplicativo por meio da interface do usuário.

Para usar o Xamarin, incorpore o SDK [Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) ao seu aplicativo, que é fornecido como um pacote do NuGet. Você o encontrará no aplicativo de demonstração e ele está incluído quando você criar novos projetos de teste com os modelos do Xamarin.

![](./media/iot-solution-build-system/image4.png)

Um projeto de teste de exemplo está incluído com o aplicativo no repositório: em [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService), procure em [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/

Se você usar a compilação do Visual Studio Team Services, será fácil escrever testes de unidade de interface do usuário do Xamarin e executá-los como parte da sua build.

## Implantar os serviços do Azure

Consulte as instruções detalhadas em **scripts/README.md** para executar uma implantação automática de serviços do Azure e do Team Services Build.

### O que o script de implantação faz?

O Microsoft Azure fornece uma infinidade de serviços diferentes que você pode usar para criar aplicativos de nuvem. Embora muitos deles possam ser usados individualmente (por exemplo, Serviço de Aplicativo/Aplicativos Web), eles funcionam melhor quando interconectados para formar um sistema integrado como o que usamos no MyDriving.

É possível criar e interconectar os serviços do Azure manualmente, mas é muito mais rápido e confiável usar modelos do ARM (Azure Resource Manager). O [ARM](../resource-group-overview.md) automatiza a implantação dos recursos de uma solução e cria as interconexões entre eles.

Você encontrará o modelo para o sistema MyDriving no repositório do GitHub em [Scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM). Ele fornece uma exibição muito abrangente e concisa de como os diferentes serviços em nossa arquitetura são interconectados. Explicamos todos eles detalhadamente no [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs), mas você pode aprender muito apenas lendo o próprio modelo.

> [AZURE.NOTE] A maioria dos serviços do Azure tem um custo associado dependendo do tipo de preço. Se ainda estiver conhecendo o Azure, você poderá [experimentá-lo gratuitamente](https://azure.microsoft.com/free/). No entanto, se você não planeja usar determinados componentes do sistema MyDriving, remova-os para evitar incorrer seus custos. Consulte os *Custos estimados de operações* abaixo para ver um resumo das despesas típicas de serviço.

#### Editar o modelo

Para personalizar sua implantação, talvez visando remover componentes desnecessários ou adicionar outros, primeiro faça uma cópia do scenario\_complete.params.json e scenario\_complete.json para fazer as alterações.

O arquivo scenario\_complete.params.json permite substituir vários valores padrão, como o SKU do serviço ou o tipo de replicação de armazenamento conforme descrito na tabela a seguir. Os valores padrão selecionam as opções de custo mais baixo.

| **Parâmetro** | **Descrição** | **Valor padrão** |
|--------|---------|-------|
| SKU do Hub IoT | Camada para o serviço do Hub IoT do Azure | F1 |
| Tipo de Conta de Armazenamento | Tipo de replicação de armazenamento | LRS Padrão |
| Objetivo de Serviço do SQL | Consumo de slot de simultaneidade | DW100 |
| SKU do Plano de Hospedagem | Plano de serviço para o Serviço de Aplicativo | F1 |

No scenario\_complete.json:

-   Pesquise por "baseName" e altere-o para um nome que você deseja usar.

-   Pesquise em "Create" – cada uma dessas seções cria um recurso.

-   Defina o sqlServerAdminLogin e sqlServerAdminPassword para valores adequados.

-   Antes de excluir uma seção que cria um recurso, verifique se ela tem dependentes pesquisando por seu nome em outros lugares no arquivo. Observe que cada seção que cria um serviço inclui uma seção *dependsOn* que lista as suas dependências.

Aqui está o que o modelo configura; os detalhes estão no [Guia de Referência](http://aka.ms/mydrivingdocs):

| **Serviço** | **Descrição e detalhes**  
|---|----
| Contas de armazenamento | O modelo cria três contas:                                                                                                                                                                       
||- Um Banco de Dados SQL que recebe telemetria agregada do Stream Analytics e serve como repositório de backup para tabelas do Serviço de Aplicativo do Azure que expõem esses dados por meio de pontos de extremidade de API.                      
| | - O armazenamento de blobs que acumula dados históricos de outro trabalho do Stream Analytics a ser processado pelo HDInisight.                                                                                         
| | - Um Banco de Dados SQL que recebe os resultados processados pelo HDInsight para uso com o Power BI.                                                                                                                 
| Hub IoT | Estabelece uma conexão bidirecional para cada dispositivo conectado. Na solução MyDriving, o aplicativo móvel atua como um gateway de campo para enviar dados ao Hub IoT. O Hub IoT serve então como uma entrada para o Stream Analytics. |
| Hub de evento | Saída para um trabalho do Stream Analytics que enfileira a saída para as extensões criadas com o Azure Service Fabric.                                                                                               
| SQL Data Warehouse |                                                                                                                                                                                                            
| Trabalhos do ASA (Stream Analytics) | Conecte entradas e saídas com uma consulta, que é usada para agregar dados históricos e em tempo real às APIs de Serviço de Aplicativo, Aprendizado de Máquina, extensões e Power BI.                               
| Espaço de trabalho do Aprendizado de Máquina | Inclui experimentos, código R e serviço de API.                                                                                                                                                              
| Fábrica de dados | Readaptação adaptada do Aprendizado de Máquina.                                                                                                                                                                     
| Plano de hospedagem do Service Fabric | Para extensões.                                                                                                                                                                                            
| Serviço de Aplicativo (“Aplicativo Móvel”) | Hospeda o projeto da API de Aplicativos Móveis que fornece pontos de extremidade para o aplicativo móvel. O código da API deve ser implantado no Serviço de Aplicativo do Visual Studio.                                                         
| Regras de alerta | Enviará um email para você se falhas forem indicadas pelas respostas do aplicativo.                                                                                                                                            
| Application Insights | Para monitorar o desempenho das APIs no Serviço de Aplicativo. Você precisa configurar a conexão no Visual Studio.                                                                                          
| Cofre da Chave | Para salvar o certificado de cluster do serviço Web.                                                                                                                                                                

#### Executar o modelo

Há **instruções detalhadas no scripts/README.md**

Para provisionar todos esses serviços em sua própria conta do Azure usando o script, siga uma das seguintes opções:

-   Usar o PowerShell:

    ```

    cd scripts/PowerShell;
    deploy.ps1 *location* *resourceGroupName*
    ```

 -   *local* é o [local do Azure](https://azure.microsoft.com/regions/) como “Europa Setentrional” ou “Oeste dos EUA”. Use o `Get-AzureLocation` para encontrar uma lista dos locais disponíveis.

 -   *resourceGroupName* é o nome que você deseja dar ao grupo ao qual todos os recursos pertencerão. Quando você terminar com os recursos, poderá excluí-los todos juntos excluindo esse grupo.

-   Execute DeploymentScripts/Bash/deploy.sh com o Bash.

-   Abrir e criar a solução do Visual Studio em DeploymentScripts/VS/DeployARM.sln

Observe que cada vez que o modelo é executado, ele cria um novo conjunto de recursos com novos nomes. Para excluir os recursos, vá para o portal e exclua o grupo de recursos.

Se o script falhar por algum motivo, você poderá executá-lo novamente.

O script fornece a opção de configurar a integração contínua no Visual Studio Team Services. Se você tiver configurado um projeto do Team Services, terá uma URL https://yourAccountName.visualstudio.com. Insira a URL completa quando solicitado. Você pode atribuir um nome novo ou existente a um projeto do Team Services.



## Visual Studio Team Services

Usamos o Team Services neste projeto principalmente devido aos seus recursos de build e teste. Contudo, ele também dá um suporte excelente à colaboração, tal como o gerenciamento de tarefas com quadros Kanban, revisão de código integrada com tarefas e controle do código-fonte, builds restritos e muito mais. Ele se integra bem com outras ferramentas como o GitHub, Xamarin, HockeyApp e, claro, o Visual Studio. Você pode acessá-lo por meio da interface da Web ou do Visual Studio, o que for mais conveniente no momento.

As etapas nas definições de build e de versão usam uma variedade de serviços de plug-in que estão disponíveis no Team Services [Marketplace](https://marketplace.visualstudio.com/VSTS). Além dos utilitários básicos para executar linhas de comando ou copiar arquivos, há serviços que invocam builds, Xamarin, Android e outros fornecedores, e que fazem interface com o HockeyApp.

![](./media/iot-solution-build-system/image5.png)

### Definições de build

Temos as definições de build para cada um dos principais destinos e variações para testes de recursos e de regressão. Isso nos fornece:

-   MyDriving.Services – o aplicativo Web de back-end para o aplicativo móvel

-   MyDriving.Xamarin.Android

    -   MyDriving.Xamarin.Android-Feature

    -   MyDriving.Xamarin.Android-Regression

-   MyDriving.Xamarin.iOS

    -   MyDriving.Xamarin.iOS-Feature

    -   MyDriving.Xamarin.iOS-Regression

-   MyDriving.Xamarin.UWP

    -   MyDriving.Xamarin.UWP-Feature

    -   MyDriving.Xamarin.UWP-Regression

Se você gostaria de ver os detalhes completos da nossa configuração, consulte a seção 4.7 do [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs), *Criação e Configuração de Build.* Eles seguem o mesmo padrão geral:

1.  Restauração do pacote do NuGet. Não mantemos código compilado no repositório, portanto, as primeiras etapas de cada build servem para restaurar os pacotes NuGet necessários.

2.  Ativação de licença. A compilação é executada na nuvem, por isso é necessário uma licença (especificamente para o serviço de build do Xamarin). Precisamos ativar nossa licença na máquina de build atual. Podemos então desativá-la logo em seguida para permitir que ela seja usada em outro computador.

3.  Crie usando o serviço apropriado. Usamos builds do Xamarin para os aplicativos móveis e build do Visual Studio para o serviço Web de back-end.

4.  Criar testes

5.  Execute os testes. Executamos os testes de aplicativo móvel no Xamarin Test Cloud.

6.  Publique os resultados de build no local de destino.

O gatilho para as builds principais é definido para Integração Contínua, ou seja, a build é executada sempre que o código for verificado na ramificação mestre.

![](./media/iot-solution-build-system/image6.png)

### Definições de versão

As definições de versão são configuradas quase da mesma maneira.

Para o serviço Web, configuramos a implantação como um aplicativo Web do Azure:

![](./media/iot-solution-build-system/image7.png)

E defina o gatilho de versão para implantação contínua, ou seja, cada check-in seguido por uma build bem-sucedida resulta em uma atualização para o aplicativo Web:

![](./media/iot-solution-build-system/image8.png)

Para aplicativos móveis, implantamos para o HockeyApp: ![](./media/iot-solution-build-system/image9.png)

## Application Insights

O [Application Insights](../application-insights/app-insights-overview.md) coleta a telemetria sobre o desempenho e o uso de seus serviços Web. O SDK do Application Insights envia telemetria do serviço para o recurso do Application Insights no Azure.

Navegue até o recurso do Application Insights que o modelo configurou. Lá você pode explorar os gráficos do desempenho do seu [projeto do Serviço de Aplicativo Móvel](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService). Eles mostram solicitações do servidor e tempos de resposta, falhas e contagens de exceção. Também há gráficos dos tempos de resposta da dependência, ou seja, chamadas para o banco de dados e para APIs REST, tal como o Aprendizado de Máquina. Se houver problemas de desempenho, você poderá ver qual parte do sistema os está causando.

![Exemplo de gráfico de desempenho](./media/iot-solution-build-system/image11.png)

Se você tiver um serviço Web configurado manualmente, será fácil obter os mesmos gráficos:

![Na folha do serviço Web, escolha Ferramentas, Extensões e Adicionar. Selecione o Application Insights.](./media/iot-solution-build-system/image12.png)

O recurso funciona ao instrumentar seu aplicativos com o SDK do Application Insights.

Você pode adicionar telemetria personalizada (ou instrumentar um aplicativo em execução em algum lugar fora do Azure) [adicionando o SDK do Application Insights](../application-insights/app-insights-asp-net.md) no tempo de desenvolvimento. Isso é útil para registrar métricas em log que dependem do aplicativo, como a duração da viagem média ou a quilometragem total dos usuários.

![No Visual Studio, clique com o botão direito do mouse no projeto e escolha Adicionar Application Insights](./media/iot-solution-build-system/image10.png)

O Application Insights enviará emails de alerta se identificar números incomuns de respostas de falhas e você também pode configurar seus próprios alertas sobre várias métricas, tal como tempos de resposta.

Para garantir que seu serviço Web esteja sempre em funcionamento, você pode configurar [testes de disponibilidade](../application-insights/app-insights-monitor-web-app-availability.md), que executa ping no seu site em vários locais em todo o mundo a cada quinze minutos. Novamente, você receberá um email se parecer que há um problema.



## Custos operacionais estimados

É extremamente barato executar um aplicativo como este em pequena escala. Muitos dos serviços têm tipos gratuitos iniciais, por isso os custos de desenvolvimento e operação em pequena escala são muito baixos. E, claro, seus próprios aplicativos não precisam usar todos os recursos demonstrados no MyDriving.

Aqui está uma estimativa aproximada dos custos de definir a configuração de desenvolvimento para o MyDriving.

Indicamos também algumas alternativas que *não* usamos. #

Supomos que:

-   Uma equipe de até cinco membros (+ stakeholders observadores).

-   Execução por cerca de um mês.

-   100 usuários com quatro viagens por dia.

>[AZURE.NOTE] Caso esteja conhecendo o Azure, há um [conta de avaliação gratuita](https://azure.microsoft.com/free/).

| **Serviço/Componente** | **Observações** | **Custo/mês** |
|--------|--------|----------------|
| [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs) com [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>Ambiente de desenvolvimento de plataforma cruzada.| Visual Studio Community. <br/>(# [VS Pro](https://www.visualstudio.com/vs-2015-product-editions) para [Xamarin.Forms](https://xamarin.com/forms) é necessário para projetar em plataforma cruzada por meio de uma base de código único.) | U$0 |
| [Hub IoT do Azure](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>Conexão de dados bidirecional para os dispositivos. | 8 mil mensagens + 0,5 KB/mensagem Gratuito | U$0 |
| [Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/) <br/> Processamento de dados de fluxo de alto volume. | Cobre U$0,031 por unidade de streaming por hora, enquanto está habilitado. Você escolhe o número de unidades de streaming desejado. Use mais para escalar verticalmente. | U$23 |
| [Aprendizado de Máquina do Azure](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> Respostas adaptáveis. | U$10/assento/mês <br/> + 3h experimentais* U$1 / hora experimental <br/> + 3,5h de CPU de API * U$2 / hora de CPU de produção <br/> O tempo de CPU de API supõe readaptação de 5 min/dia, embora isso aumentaria com mais dados de entrada; <br/> + 2 min/dia de pontuação para processar 400 viagens por dia. | U$20 |
| [Serviço de Aplicativo do Azure](https://azure.microsoft.com/pricing/details/app-service/) <br/> hospedar o back-end móvel | Camada B1 - aplicativos Web de produção | U$56 |
| [Visual Studio Team Services ](https://azure.microsoft.com/pricing/details/visual-studio-team-services/) <br/> Build, teste de unidade e gerenciamento de versão. Gerenciamento de tarefas. | Agentes Privados, cinco usuários| U$0 |
| [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) <br/>Monitore o desempenho e uso dos serviços Web e sites.| Camada gratuita | U$0 |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/> Distribua aplicativos beta e colete dados de comentários, uso e falhas. | Dois aplicativos gratuitos para novos usuários.<br/> U$30/ mês posteriormente. | U$0 |
| [Xamarin](https://store.xamarin.com/)<br/> Código em uma plataforma uniforme para vários dispositivos. | Avaliação gratuita. <br/>U$25/ mês posteriormente.| U$0 |
| [Instância SQL](https://azure.microsoft.com/pricing/details/sql-database/) para o Serviço de Aplicativo do Azure| Camada Básica; modelo banco de dados individual | U$5 |
| [Service Fabric](../service-fabric/services/service-fabric.md) (opcional) | Execute um cluster local | U$0 |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/> Exibições e investigação versáteis de dados transmitidos e estáticos.| Camada gratuita: 1GB, 10 mil linhas/hora, atualização diária <br/> # U$10/usuário/mês para [limites mais altos](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/), mais opções de conexão e colaboração | U$0 |
| [Armazenamento](https://azure.microsoft.com/pricing/details/storage/) | L (Localmente Redundante) &lt; 100G U$0,024/GB | U$3 |
| [Fábrica de dados](https://azure.microsoft.com/pricing/details/data-factory/) | U$0,60 por atividade* (8 - 5 FOC)| U$2 |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) (cluster sob demanda) <br/> Para readaptação diária | 3 nós A3 a U$ 0,32/h por 1 hora por dia X 31 dias | U$30 |
| [Hub de evento](https://azure.microsoft.com/pricing/details/event-hubs/) | Básico com unidade de produtividade U$11/mês + U$0,028 de entrada | U$11 |
| Dongle OBD || U$12 |
| **Total**| | **U$157** |

Consulte também:

-   Resumo dos [limites e cotas do serviço do Azure](../azure-subscription-service-limits/#iot-hub-limits).

-   [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) do Azure



## Guia de Referência

Recomendamos o [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs), que é uma descrição abrangente do design do sistema e seus componentes.


## Comentários 

Uma vez que criamos o MyDriving para ajudar a iniciar rapidamente seus próprios sistemas de IoT, certamente desejamos saber sua opinião sobre como ele funciona. Fale conosco se você enfrentar problemas ou desafios, se houver um ponto de extensão que seria mais adequado para o seu cenário, se você encontrar uma maneira mais eficiente para cumprir com determinadas necessidades ou se tiver sugestões para melhorar o MyDriving ou essa documentação.

Para fazer comentários, [emita um problema no GitHub] ou deixe um comentário abaixo (edição pt-br).

Aguardamos seu contato!

<!---HONumber=AcomDC_0406_2016-->