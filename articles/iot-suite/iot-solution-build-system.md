<properties
    pageTitle="Exemplo de IoT do Azure do MyDriving: crie-o | Microsoft Azure"
    description="Crie um aplicativo que apresenta uma demonstração abrangente de como projetar um sistema de IoT usando o Microsoft Azure, incluindo o Stream Analytics, o Aprendizado de Máquina e os Hubs de Eventos."
    services=""
    documentationCenter=".net"
    suite=""
    authors="harikmenon"
    manager="douge"/>

<tags
    ms.service="multiple"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/25/2016"
    ms.author="harikm"/>



# <a name="build-and-deploy-the-mydriving-solution-to-your-environment"></a>Criar e implantar a Solução MyDriving no seu ambiente

O MyDriving é uma solução de IoT (Internet das Coisas) que reúne dados do seu carro, processa-os usando o aprendizado de máquina e apresenta-os no seu celular. O back-end consiste em uma variedade de serviços fornecidos pelo Microsoft Azure. Os clientes podem ser telefones Android, iOS ou Windows 10.

Criamos a solução MyDriving para oferecer a você um ponto de partida para a criação do seu próprio sistema de IoT. No [repositório do MyDriving no GitHub](https://github.com/Azure-Samples/MyDriving), você pode obter scripts do Azure Resource Manager para implantar a arquitetura de back-end em sua própria conta do Azure. A partir daí, você pode reconfigurar os diferentes serviços, modificar as consultas para adequá-las aos seus próprios dados e muito mais. Você pode encontrar esses scripts junto com o código para o aplicativo móvel, o projeto da API do Serviço de Aplicativo do Azure e muito mais no repositório do MyDriving.

Se você ainda não experimentou o aplicativo, examine o [Guia de introdução](iot-solution-get-started.md).

Há uma conta detalhada da arquitetura no [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs). Em resumo, há várias partes que preparamos e que você prepararia para criar um projeto semelhante:

* Um **aplicativo cliente** é executado em telefones Android, iOS e Windows 10. Podemos usar a plataforma Xamarin para compartilham grande parte do código, o qual é armazenado no GitHub em `src/MobileApp`. O aplicativo na verdade executa duas funções distintas:
 * Ele retransmite telemetria do dispositivo OBD (On-Board Diagnostics) do seu próprio serviço local para o back-end da nuvem do sistema.
 * Ele é uma interface do usuário em que usuários podem fazer consultas sobre suas viagens gravadas.
* Um **serviço de nuvem** consome os dados da viagem em tempo real e os processa. O trabalho principal de criar esse serviço é escolher, parametrizar e interligar uma variedade de serviços do Azure. Algumas das partes exigem scripts para filtrar e processar os dados de entrada. Usamos uma modelo do Azure Resource Manager para configurar todas as partes.
* Um **aplicativo de serviço móvel** é o serviço Web por trás da parte de interface do usuário do aplicativo do dispositivo. Sua principal tarefa é consultar o banco de dados processados e armazenados. Seu código está no GitHub em `src/MobileAppService`.
* **Visual Studio com Xamarin** é nosso ambiente de desenvolvimento. O Xamarin, que existe tanto como componente do Visual Studio quanto como um IDE (Ambiente de Desenvolvimento Integrado) autônomo, é usado para compilar o código do dispositivo de plataforma cruzada. Para compilar o código do iOS, é necessário ter uma instância do Xamarin em execução em um computador OS X. Se necessário, ele pode ser executado como um agente gerenciado do Visual Studio.
* **Testes de unidade** dos aplicativos do dispositivo são executados no Xamarin Test Cloud.
* **GitHub** é o repositório no qual armazenamos todos os códigos, scripts e modelos.
* **Visual Studio Team Services** é um serviço de nuvem usado para gerenciar a criação e o teste contínuos do serviço Web e aplicativos do dispositivo.
* **HockeyApp** é usado para distribuir as versões do código de dispositivo. Ele também coleta relatórios de uso e de falhas, bem como comentários do usuário.
* **Visual Studio Application Insights** monitora o serviço Web móvel.

Vejamos como configurar tudo isso. Observe que muitas das etapas são opcionais.

## <a name="sign-up-for-accounts"></a>Inscrever-se em contas

-   [Visual Studio Dev Essentials](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx). Esse programa gratuito oferece acesso fácil a muitas ferramentas e serviços de desenvolvedor, incluindo o Visual Studio, o Visual Studio Team Services e o Azure. Ele concede U$25 por mês de crédito no Azure por 12 meses. Ele também inclui assinaturas para treinamento do Pluralsight e Xamarin University. Você pode se inscrever separadamente para camadas gratuitas do [Azure](https://azure.com) e do [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx), mas eles não fornecem créditos do Azure.

-   [HockeyApp](https://rink.hockeyapp.net/) (opcional), para gerenciar a distribuição de teste de aplicativos móveis e a coleta de telemetria.

-   [Xamarin](https://xamarin.com/) (obrigatório), para criar o aplicativo móvel e executar testes e execuções de depuração no [Xamarin Test Cloud](https://xamarin.com/test-cloud).

-   [GitHub](https://github.com/Azure-Samples/MyDriving/) (opcional), para criar repositórios públicos gratuitos para seu próprio código (repositórios particulares são pagos). Como alternativa, você pode usar o plano básico no Visual Studio Team Services para repositórios particulares.

-   [Power BI](https://powerbi.microsoft.com/) (opcional), para criar visualizações avançadas dos dados em todo o sistema.

> [AZURE.NOTE] Você não precisa de uma conta do GitHub para acessar o código do MyDriving no [repositório do MyDriving](https://github.com/Azure-Samples/MyDriving).

## <a name="install-development-tools"></a>Instalar ferramentas de desenvolvimento

A configuração a seguir serve para desenvolver a solução completa: um aplicativo de plataforma cruzada do iOS, Android e Windows 10 Mobile com um back-end do Azure.

Como alternativa, você poderá usar o Xamarin Studio no Mac ou no Windows para desenvolver aplicativos móveis se não estiver trabalhando no back-end do Azure.

Há uma [descrição maior dessa configuração](https://msdn.microsoft.com/library/mt613162.aspx).

### <a name="windows-development-machine"></a>Computador de desenvolvimento do Windows

A ferramenta central no Windows é o Visual Studio para trabalhar com o aplicativo MyDriving para Android e Windows, o projeto de API do Serviço de Aplicativo e extensões de microsserviço.

Xamarin, Git, emuladores e outros componentes úteis são todos integrados ao Visual Studio.

Instalar:

-   [Visual Studio 2015 com Xamarin](https://www.visualstudio.com/products/visual-studio-community-vs) (qualquer edição – o Community é gratuito).

-   [SQLite para Plataforma Universal do Windows](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936). Necessário para compilar o código do Windows 10 Mobile.

-   [SDK do Azure para Visual Studio 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409). Oferece o SDK para aplicativos em execução no Azure e ferramentas de linha de comando para gerenciar o Azure.

-   [SDK do Azure Service Fabric](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric). Necessário para criar a extensão de [microsserviço](../service-fabric/service-fabric-get-started.md) .

Além disso, verifique se você tem as extensões do Visual Studio corretas. Verifique se, em **Ferramentas**, é exibido **Android, iOS, Xamarin...**. Caso contrário, abra o Painel de Controle e selecione **Programas e Recursos** > **Microsoft** > **Visual Studio 2015** > **Modificar**. Em **Desenvolvimento de Plataforma Cruzada**, escolha **C\#/.Net (Xamarin)**. Enquanto você está na página, verifique se o **Git para Windows** está instalado.

### <a name="mac-development-machine"></a>Computador de desenvolvimento do Mac

O Mac (Yosemite ou posterior) será necessário se você quiser desenvolver para iOS. Embora usemos o Visual Studio com Xamarin no Windows para desenvolver e gerenciar todo o código, o Xamarin usa um agente instalado em um Mac para criar e assinar o código do iOS.

![Desenvolver no Windows e compilar no Mac](./media/iot-solution-build-system/image1.png)

(Como alternativa, você pode usar o Xamarin Studio diretamente no Mac para desenvolver aplicativos de plataforma cruzada.)

O Mac não será necessário se você não desejar incluir o iOS como uma plataforma de destino.

Instalar:

-   [Xamarin Studio para iOS](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/). Você também pode configurar o Visual Studio e Xamarin em um Mac que executa uma máquina virtual do Windows. Consulte [Configuração, instalação e verificações para usuários do Mac](https://msdn.microsoft.com/library/mt488770.aspx) no MSDN.

-   [Ferramentas de desenvolvimento do Azure](https://azure.microsoft.com/downloads/) (opcionais).

Habilitar logon remoto no Mac. Abra **Preferências do Sistema** > **Compartilhamento** e selecione **Logon Remoto**.

Quando você abrir um projeto do iOS no Visual Studio no Windows, o plug-in do Xamarin solicitará a ID do Mac.

## <a name="fetch-the-github-repository"></a>Obter o repositório GitHub

Obtenha uma cópia local do [repositório do MyDriving do GitHub](https://github.com/Azure-Samples/MyDriving) usando o botão **Baixar ZIP** no GitHub, no Visual Studio ou em outro cliente Git.

Descompacte o arquivo para uma pasta com um nome de caminho curto, como C:\\código.

Como alternativa, se você quiser contribuir com nosso código ou mantê-lo atualizado, clone o repositório da seguinte maneira:

**git clone https://github.com/Azure-Samples/MyDriving.git**

## <a name="get-a-bing-maps-api-key"></a>Obter uma chave de API do Bing Mapas

[Registre-se para uma chave de API do Bing Mapas](https://msdn.microsoft.com/library/ff428642.aspx).

Você precisa substituir isso na linha 22 em `src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs`.



## <a name="build-the-demo-app"></a>Criar o aplicativo de demonstração

Abra essas soluções no Visual Studio:

-   src\MobileApps\MyDriving.sln

-   src\MobileAppService\MyDrivingService.sln

-   src\Extensions\ServiceFabric\VINLookUpApplication\VINLookUpApplication.sln

Você receberá prompts para:

-   Confiar em alguns projetos potencialmente não confiáveis. Escolher abri-los se desejar continuar.

-   Definir o Modo de desenvolvedor se você estiver trabalhando em um novo computador do Windows 10.

-   Fornecer suas credenciais do Xamarin.

-   Conectar-se ao Mac do Xamarin. Se você não tiver um Mac, clique com o botão direito do mouse no projeto do iOS no Visual Studio e selecione **Descarregar projeto**.

Recriar a solução.

Se você tiver problemas no build, experimente as soluções para peculiaridades que encontramos:

-   *O projeto VINLookupApplication não carrega*: verifique se você instalou o [SDK do Azure para Visual Studio 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

-   *O projeto do Service Fabric não é criado*: crie primeiramente os projetos de interfaces e verifique se você instalou o SDK do Service Fabric.

-   *O aplicativo do Android não cria*:

    -   Abra **Ferramentas** > **Android** > **Gerenciador de SDK** do Android e verifique se o Android 6 (API 23)/Plataforma de SDK está instalado.

    -   Exclua este diretório e recrie:<br/>
        `%LocalAppData%\Xamarin\zips`

## <a name="get-to-know-the-code"></a>Conheça o código

Na solução, você encontrará:

-   Extensões do Azure: Service Fabric.

-   Azure HDInsight: scripts para processar dados de viagem no Azure.

-   Aplicativos Móveis: os aplicativos do dispositivo.

-   MobileAppsService/MyDrivingService: o back-end da Web.

-   Power BI: a definição de relatório.

-   Scripts:

    -   Resource Manager: modelos para criar os recursos do Azure.

    -   PowerShell: scripts para executar os modelos do Resource Manager.

    -   Banco de Dados SQL do Azure: depuração de bancos de dados.

-   Banco de Dados SQL: CreateTables: definições de esquema.

-   Stream Analytics do Azure: consultas que transformam o fluxo de dados de entrada.

## <a name="run-the-apps-in-development-mode"></a>Executar os aplicativos no modo de desenvolvimento

Execute uma ação para executar os aplicativos, com base no dispositivo que você está usando:

-  Back-end: defina o MyDrivingService como o projeto de inicialização e pressione F5 para executar o serviço Web de back-end. Ele será aberto uma exibição de navegador da listagem de API.

-  Clientes móveis: os [aplicativos móveis são desenvolvidos em Xamarin](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/).
 -  Android: Para obter detalhes, consulte [Depuração do Android no Xamarin](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/).

 -  iOS: para obter detalhes, consulte [Depuração no iOS](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/).

 -  Windows Phone: para obter detalhes, consulte [Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/).

## <a name="upload-the-mobile-app-to-hockeyapp"></a>Carregar o aplicativo móvel para o HockeyApp

O HockeyApp gerencia a distribuição de seu aplicativo Android, iOS ou Windows para usuários de teste, notificando os usuários sobre novas versões. Ele também coleta relatórios de falha úteis, comentários do usuário com capturas de tela e métricas de uso.

[Comece carregando](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app) seu aplicativo de build. Em seguida, entre no [HockeyApp](https://rink.hockeyapp.net) no seu computador de desenvolvimento. No painel do desenvolvedor, clique em **Novo Aplicativo**e arrastar os arquivos criados para a janela. (Posteriormente, você pode automatizar o serviço de build para fazer isso).

Agora você está no painel do aplicativo.

![Guia de Visão Geral sobre o painel do aplicativo](./media/iot-solution-build-system/image2.png)

Repita o processo para cada plataforma em que seu aplicativo será executado. Em seguida, você pode realizar o seguinte:

-  Use a [ID do aplicativo](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) do painel para enviar comentários e dados de falha do seu aplicativo. No MyDriving, atualize as IDs em src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs.

-  [Convide os usuários de teste](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers). Você recebe uma URL para recrutar usuários de teste. Eles poderão se inscrever na sua equipe, baixar o aplicativo e enviar comentários.

-  Se você preferir uma versão beta mais aberta, defina a distribuição como pública. Clique em **Gerenciar Aplicativo** > **Distribuição** > **Download = Público**. Agora qualquer pessoa poderá baixar seu aplicativo e enviar comentários, e eles verão uma notificação quando você postar uma nova versão. E você também pode receber alguns relatórios de falha deles.

    ![Equipes no painel](./media/iot-solution-build-system/image3.png)

-  [Vincular os relatórios de falha ao Visual Studio Team Services](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs). Clique em **Gerenciar Aplicativos** > **Visual Studio Team Services**. O HockeyApp pode criar itens de trabalho automaticamente no Team Services quando há relatórios de falha ou ao receber comentários.

Leia mais no [site do HockeyApp](https://hockeyapp.net).

## <a name="test-the-mobile-app-on-xamarin-test-cloud"></a>Testar o aplicativo móvel na Nuvem de Teste do Xamarin

[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) automatiza os testes de interface do usuário em dispositivos reais na nuvem. Usando a estrutura NUnit, você escrever testes que executam seu aplicativo por meio da interface do usuário.

Para usar o Xamarin, incorpore o SDK [Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) ao seu aplicativo, que é fornecido como um pacote do NuGet. Você o encontrará no aplicativo de demonstração e ele está incluído quando você criar novos projetos de teste com os modelos do Xamarin.

![Onde encontrar o SDK de plataforma cruzada na interface](./media/iot-solution-build-system/image4.png)

Um exemplo de projeto de teste é incluído com o aplicativo no repositório. Em [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService), procure em [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/.

Se você usar um build do Visual Studio Team Services, será fácil escrever testes de unidade de interface do usuário do Xamarin e executá-los como parte do seu build.

## <a name="deploy-azure-services"></a>Implantar os serviços do Azure

Para executar uma implantação automática de serviços do Azure e dos serviços de build do Team Services, consulte as instruções detalhadas em **scripts/README.md**.

O Microsoft Azure fornece uma infinidade de serviços diferentes que você pode usar para criar aplicativos de nuvem. Embora muitos deles possam ser usados individualmente (por exemplo, Serviço de Aplicativo/Aplicativos Web), eles funcionam melhor quando interconectados para formar um sistema integrado como o que usamos no MyDriving.

É possível criar e interconectar os serviços do Azure manualmente, mas é muito mais rápido e confiável usar modelos do Azure Resource Manager. [Resource Manager](../resource-group-overview.md) automatiza a implantação dos recursos de uma solução e cria as interconexões entre eles.

Você encontrará o modelo para o sistema MyDriving no repositório do GitHub em [scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM). Ele fornece uma exibição muito abrangente e concisa de como os diferentes serviços em nossa arquitetura são interconectados. Explicamos todos eles detalhadamente no [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs), mas você pode aprender muito apenas lendo o próprio modelo.

> [AZURE.NOTE] A maioria dos serviços do Azure tem um custo associado dependendo do tipo de preço. Se ainda estiver conhecendo o Azure, você poderá [experimentá-lo gratuitamente](https://azure.microsoft.com/free/). No entanto, se você não planeja usar determinados componentes do sistema MyDriving, remova-os para evitar incorrer seus custos. A seção "Estimar os custos operacionais" mais adiante neste artigo fornece um resumo das despesas de serviço típicas.

### <a name="edit-the-template"></a>Editar o modelo

Para personalizar sua implantação, talvez visando remover componentes desnecessários ou adicionar outros, primeiro faça uma cópia do scenario\_complete.params.json e scenario\_complete.json para fazer as alterações.

Você pode usar o arquivo scenario\_complete.params.json para substituir vários valores padrão, como o SKU do serviço ou o tipo de replicação de armazenamento conforme descrito na tabela a seguir. Os valores padrão selecionam as opções de custo mais baixo.

| **Parâmetro**         | **Descrição**                | **Valor padrão** |
|--------|---------|-------|
| SKU do Hub IoT           | Camada para o serviço do Hub IoT do Azure | F1                |
| Tipo de Conta de Armazenamento  | Tipo de replicação de armazenamento       | LRS Padrão      |
| Objetivo de Serviço do SQL | Consumo de slot de simultaneidade   | DW100             |
| SKU do Plano de Hospedagem      | Plano de serviço para o Serviço de Aplicativo   | F1                |

No cenário\_complete.json:

-   Pesquise por "baseName" e altere-o para um nome que você preferir.

-   Procure por "Criar". Cada uma dessas seções cria um recurso.

-   Defina o sqlServerAdminLogin e sqlServerAdminPassword para valores adequados.

-   Antes de excluir uma seção que cria um recurso, verifique se ela tem dependentes pesquisando por seu nome em outros lugares no arquivo. Observe que cada seção que cria um serviço inclui uma seção *dependsOn* que lista suas dependências.

Aqui está o que o modelo configura. Os detalhes estão no [Guia de Referência](http://aka.ms/mydrivingdocs).

| **Serviço**                 | **Descrição e detalhes**  
|---|----
| Contas de armazenamento            | O modelo cria três contas:                                                                                                                                                                       
|| -   Um Banco de Dados SQL que recebe telemetria agregada do Stream Analytics e serve como repositório de backup para tabelas do Serviço de Aplicativo do Azure que expõem esses dados por meio de pontos de extremidade de API.                      
|| -   O armazenamento de blobs que acumula dados históricos de outro trabalho do Stream Analytics a ser processado pelo HDInsight.                                                                                         
|| -   Um Banco de Dados SQL que recebe os resultados processados pelo HDInsight para uso com o Power BI.                                                                                                                 
| Hub IoT do Azure                     | Estabelece uma conexão bidirecional para cada dispositivo conectado. Na solução MyDriving, o aplicativo móvel atua como um gateway de campo para enviar dados ao Hub IoT do Azure. O Hub IoT do Azure serve então como uma entrada para o Stream Analytics. |
| Hubs de eventos do Azure                   | Saída para um trabalho do Stream Analytics que enfileira a saída para as extensões criadas com o Azure Service Fabric.                                                                                               
| SQL Data Warehouse do Azure          |                                                                                                                                                                                                            
| Trabalhos do Stream Analytics | Conecte entradas e saídas com uma consulta, que é usada para agregar dados históricos e em tempo real às APIs de Serviço de Aplicativo, Aprendizado de Máquina do Azure, extensões e Power BI.                               
| Espaço de trabalho do Aprendizado de Máquina  | Inclui experimentos, código R e serviço de API.                                                                                                                                                              
| Fábrica de dados do Azure                | Readaptação adaptada do Aprendizado de Máquina.                                                                                                                                                                     
| Plano de hospedagem do Service Fabric | Para extensões.                                                                                                                                                                                            
| Serviço de Aplicativo (“Aplicativo Móvel”)  | Hospeda o projeto da API de Aplicativos Móveis que fornece pontos de extremidade para o aplicativo móvel. O código da API deve ser implantado no Serviço de Aplicativo do Visual Studio.                                                         
| Regras de alerta                 | Enviará um email para você se as respostas do aplicativo indicarem falhas.                                                                                                                                            
| Application Insights        | Para monitorar o desempenho das APIs no Serviço de Aplicativo. Você precisa configurar a conexão no Visual Studio.                                                                                          
| Cofre da Chave do Azure                   | Para salvar o certificado de cluster do serviço Web.                                                                                                                                                                

### <a name="run-the-template"></a>Executar o modelo

No **scripts/README.md**, há instruções detalhadas para executar o modelo.

Para provisionar todos esses serviços em sua própria conta do Azure usando o script, siga uma das seguintes opções:

-   Use o PowerShell:

    ```

    cd scripts/PowerShell;
    deploy.ps1 *location* *resourceGroupName*
    ```

 -   *local* é o [local do Azure](https://azure.microsoft.com/regions/), tal como `North Europe` ou `West US`. Use o `Get-AzureLocation` para encontrar uma lista dos locais disponíveis.

 -   *resourceGroupName* é o nome que você deseja dar ao grupo ao qual todos os recursos pertencerão. Quando você terminar com os recursos, poderá excluí-los todos juntos excluindo esse grupo.

-   Execute DeploymentScripts/Bash/deploy.sh com o Bash.

-   Abrir e criar a solução do Visual Studio em DeploymentScripts/VS/DeployARM.sln.

Observe que cada vez que o modelo é executado, ele cria um novo conjunto de recursos com novos nomes. Para excluir os recursos, vá para o portal e exclua o grupo de recursos.

Se o script falhar por algum motivo, você poderá executá-lo novamente.

O script fornece a opção de configurar a integração contínua no Visual Studio Team Services. Se você tiver configurado um projeto de Serviços para equipes, você terá uma URL: https://NomeDaSuaConta.visualstudio.com. Insira a URL completa quando solicitado. Você pode atribuir um nome novo ou existente a um projeto do Team Services.

## <a name="set-up-build-and-test-definitions-in-visual-studio-team-services"></a>Configurar o build e testar definições no Visual Studio Team Services

Usamos o Team Services neste projeto principalmente devido aos seus recursos de build e teste. Contudo, ele também dá um suporte excelente à colaboração, tal como o gerenciamento de tarefas com quadros Kanban, revisão de código integrada com tarefas e controle do código-fonte e builds restritos. Ele se integra bem com outras ferramentas como o GitHub, Xamarin, HockeyApp e, claro, o Visual Studio. Você pode acessá-lo por meio da interface da Web ou do Visual Studio, o que for mais conveniente no momento.

As etapas nas definições de build e de versão usam uma variedade de serviços de plug-in que estão disponíveis no Team Services [Marketplace](https://marketplace.visualstudio.com/VSTS). Além dos utilitários básicos para executar linhas de comando ou copiar arquivos, há serviços que invocam builds, Xamarin, Android e outros fornecedores, e que se conectam ao HockeyApp.

![Opções de build no Team Services](./media/iot-solution-build-system/image5.png)

### <a name="build-definitions"></a>Definições de build

Temos as definições de build para cada um dos principais destinos. Também temos variações de recurso e testes de regressão. Isso nos fornece:

-   MyDriving.Service (o aplicativo Web de back-end para o aplicativo móvel)

-   MyDriving.Xamarin.Android

    -   MyDriving.Xamarin.Android-Feature

    -   MyDriving.Xamarin.Android-Regression

-   MyDriving.Xamarin.iOS

    -   MyDriving.Xamarin.iOS-Feature

    -   MyDriving.Xamarin.iOS-Regression

-   MyDriving.Xamarin.UWP

    -   MyDriving.Xamarin.UWP-Feature

    -   MyDriving.Xamarin.UWP-Regression

Se você deseja ver os detalhes completos da nossa configuração, consulte a seção 4.7 do [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs), “Criação e Configuração de Build”. Eles seguem o mesmo padrão geral. O script:

1.  Restaura o pacote NuGet. Não mantemos código compilado no repositório, portanto, as primeiras etapas de cada build servem para restaurar os pacotes NuGet necessários.

2.  Ativa a licença. A compilação é executada na nuvem, por isso é necessário uma licença (especificamente para o serviço de build do Xamarin). Precisamos ativar nossa licença no computador de build atual. Podemos então desativá-la logo em seguida para permitir que ela seja usada em outro computador.

3.  Criado usando o serviço apropriado. Usamos builds do Xamarin para os aplicativos móveis e build do Visual Studio para o serviço Web de back-end.

4.  Testes de builds.

5.  Executa testes. Executamos os testes de aplicativo móvel no Xamarin Test Cloud.

6.  Publica o resultado o build para o local de destino.

O gatilho para os principais builds é definido como integração contínua. Ou seja, o build é executado sempre que o código é verificado na ramificação mestre.

![Interface na qual o gatilho é definido como integração contínua](./media/iot-solution-build-system/image6.png)

### <a name="release-definitions"></a>Definições de versão

As definições de versão são configuradas quase da mesma maneira.

Para o serviço Web, configuramos a implantação como um aplicativo Web do Azure:

![Interface para configurar a implantação como um aplicativo Web do Azure](./media/iot-solution-build-system/image7.png)

E definimos o gatilho de liberação para implantação contínua. Ou seja, cada check-in seguido por um build bem-sucedido resulta em uma atualização para o aplicativo Web.

![Interface para configurar a liberação do gatilho como implantação contínua](./media/iot-solution-build-system/image8.png)

Para aplicativos móveis, implantamos para o HockeyApp:

![Interface para implantar um aplicativo móvel para HockeyApp](./media/iot-solution-build-system/image9.png)

## <a name="explore-telemetry-by-using-application-insights"></a>Explorar telemetria usando o Application Insights

[Application Insights](../application-insights/app-insights-overview.md) coleta a telemetria sobre o desempenho e o uso de seus serviços Web. O SDK do Application Insights envia telemetria do serviço para o recurso do Application Insights no Azure.

Navegue até o recurso do Application Insights que o modelo configurou. Lá, você pode explorar os gráficos do desempenho do seu [projeto do Serviço de Aplicativo Móvel](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService). Eles mostram solicitações do servidor e tempos de resposta, falhas e contagens de exceção. Também há gráficos dos tempos de resposta da dependência, ou seja, chamadas para o banco de dados e para APIs REST, tal como o Aprendizado de Máquina. Se houver problemas de desempenho, você poderá ver qual parte do sistema os está causando.

![Exemplo de gráfico de desempenho](./media/iot-solution-build-system/image11.png)

Se você tiver um serviço Web configurado manualmente, será fácil obter os mesmos gráficos. Na folha do serviço Web, clique em **Ferramentas** > **Extensões** > **Adicionar**. Selecione **Application Insights**.

![Interface para selecionar o Application Insights para obter os gráficos](./media/iot-solution-build-system/image12.png)

O recurso funciona ao instrumentar seu aplicativos com o SDK do Application Insights.

Você pode adicionar telemetria personalizada (ou instrumentar um aplicativo em execução em algum lugar fora do Azure) [adicionando o SDK do Application Insights](../application-insights/app-insights-asp-net.md) no tempo de desenvolvimento. Isso é útil para registrar métricas em log que dependem do aplicativo, como a duração da viagem média ou a quilometragem total dos usuários. No Visual Studio, clique com o botão direito do mouse no projeto e selecione **Adicionar Application Insights**.

![Interface para selecionar Adicionar Application Insights para obter telemetria personalizada](./media/iot-solution-build-system/image10.png)

O Application Insights enviará emails de alerta caso observe números incomuns de respostas de falha. Você também pode configurar seus próprios alertas em várias métricas, como tempos de resposta.

Para garantir que seu serviço Web esteja sempre em funcionamento, você pode configurar [testes de disponibilidade](../application-insights/app-insights-monitor-web-app-availability.md). Esses testes efetuam ping no seu site em vários locais em todo o mundo a cada 15 minutos. Novamente, você receberá um email se parecer que há um problema.

## <a name="estimate-operational-costs"></a>Custos operacionais estimados

É extremamente barato executar um aplicativo como este em pequena escala. Muitos dos serviços têm tipos gratuitos iniciais, por isso os custos de desenvolvimento e operação em pequena escala são muito baixos. E, claro, seus próprios aplicativos não precisam usar todos os recursos demonstrados no MyDriving.

Aqui está uma estimativa aproximada dos custos de definir a configuração de desenvolvimento para o MyDriving. Indicamos também algumas alternativas que *não* usamos. Essas informações podem ser úteis ao estimar seus próprios custos.

Supomos que:

-   Uma equipe de até cinco membros (mais partes interessadas observadoras).

-   Execução por cerca de um mês.

-   100 usuários com quatro viagens por dia.

>[AZURE.NOTE] Caso esteja conhecendo o Azure, saiba que há um [conta gratuita](https://azure.microsoft.com/free/).

| **Serviço/componente**  | **Observações** | **Custo/mês** |
|--------|--------|----------------|
| [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs) com [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>Ambiente de desenvolvimento de plataforma cruzada| Visual Studio Community. (Precisa do [Visual Studio Professional](https://www.visualstudio.com/vs-2015-product-editions) para [Xamarin.Forms](https://xamarin.com/forms) para projetar em plataforma cruzada por meio de uma único código de base.)  | U$0   |
| [Hub IoT do Azure](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>Conexão de dados bidirecional para os dispositivos | 8 mil mensagens + 0,5 KB/mensagem gratuito. | U$0             |
| [Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/)  <br/>    Processamento de dados de fluxo de alto volume                                                                                                                                                              | Cobrança de U$0,031 por unidade de streaming por hora, enquanto estiver habilitado. Você escolhe o número de unidades de streaming desejado. Use mais para escalar verticalmente. | U$23            |
| [Aprendizado de máquina](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> Respostas adaptáveis                                                                                                                                                                              |  U$10/estação/mês. <br/>                                                                                                                                                                                 + 3 horas de teste \* U$1/hora de teste. <br/>                                                                                                                                                           + 3.5 hora de CPU da API \* U$2/hora de CPU de produção. <br/>                                                                                                                                                           O tempo de CPU da API supõe 5 min/dia de readaptação, embora isso possa aumentar com mais dados de entrada.                   <br/>                                                                                                                                                                      + 2 min/dia de pontuação para processar 400 viagens por dia.  | U$20            |
| [Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/)  <br/>  para o back-end móvel                                                                                                                                                                              | Camada B1: aplicativos Web de produção. | U$56            |
| [Visual Studio Team Services ](https://azure.microsoft.com/pricing/details/visual-studio-team-services/)  <br/>  Build, teste de unidade e gerenciamento de versão; gerenciamento de tarefas | Agentes privados, cinco usuários.| U$0             |
| [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) <br/>Monitore o desempenho e uso dos serviços Web e sites| Camada gratuita.  | U$0             |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/>  Distribuição de aplicativos beta, além de coleta de dados de comentários, de uso e de falha                                                                                                                                      | Dois aplicativos gratuitos para novos usuários.<br/>  U$30/ mês posteriormente.  | U$0    |
| [Xamarin](https://store.xamarin.com/)<br/>  Código em uma plataforma uniforme para vários dispositivos | Avaliação gratuita. <br/>U$25/mês posteriormente.| U$0    |
| [Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) para o Serviço de Aplicativo do Azure| Camada Básica; modelo banco de dados individual. | U$5             |
| [Service Fabric](https://azure.microsoft.com/pricing/details/service-fabric/) (opcional)  | Execute um cluster local. | U$0             |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/>  Exibições e investigação versáteis de dados transmitidos e estáticos| Camada gratuita: 1GB, 10 mil linhas/hora, atualização diária. <br/> U$10/usuário/mês para [limites mais altos](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/), mais opções de conexão e colaboração.    | U$0             |
| [Armazenamento](https://azure.microsoft.com/pricing/details/storage/)   | L (localmente redundante) &lt; 100 G, U$0,024/GB.  | U$3             |
| [Fábrica de dados](https://azure.microsoft.com/pricing/details/data-factory/)                                                                                                                       | U$0,60 por atividade \* (8h a 17h gratuitamente).| U$2             |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) <br/>   Cluster sob demanda para readaptação diária   | Três nós A3 por US $0,32/hora por uma hora diariamente * 31 dias. | U$30            |
| [Hubs de Evento](https://azure.microsoft.com/pricing/details/event-hubs/)  | Básico com unidade de produtividade U$11/mês + U$0,028 de entrada. | U$11            |
| Dongle OBD  || U$12            |
| **Total**|    | **U$157**       |

Para obter mais informações, consulte:

-   Resumo dos [limites e cotas do serviço do Azure](../azure-subscription-service-limits.md#iot-hub-limits)

-    [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)

## <a name="send-us-your-feedback"></a>Envie-nos seus comentários

Uma vez que criamos o MyDriving para ajudar a iniciar rapidamente seus próprios sistemas de IoT, certamente desejamos saber de você como ele funciona. Fale conosco se:

-  Você encontra dificuldades ou desafios.

-  Há um ponto de extensão que tornaria isso mais adequado ao seu cenário.

-  Você encontra uma maneira mais eficiente de atender a determinadas necessidades.

-  Você tem outras sugestões para melhorar o MyDriving ou essa documentação.

Para fazer comentários, [envie um problema no GitHub] ou deixe um comentário abaixo (edição pt-br).

Aguardamos seu contato!

## <a name="next-steps"></a>Próximas etapas

Recomendamos o [Guia de Referência do MyDriving](http://aka.ms/mydrivingdocs), que é uma descrição abrangente do design do sistema e seus componentes.



<!--HONumber=Oct16_HO2-->


