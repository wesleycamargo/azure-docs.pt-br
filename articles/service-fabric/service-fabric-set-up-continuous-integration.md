<properties
   pageTitle="Integração contínua para o Service Fabric | Microsoft Azure"
   description="Obtenha uma visão geral de como configurar a integração contínua para um aplicativo do Service Fabric usando o VSTS (Visual Studio Team Services)."
   services="service-fabric"
   documentationCenter="na"
   authors="mthalman-msft"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/01/2016"
   ms.author="mthalman" />

# Configurar a integração contínua para um aplicativo do Service Fabric usando o Visual Studio Team Services

Este artigo descreve as etapas para configurar a integração contínua para um aplicativo do Azure Service Fabric usando o Visual Studio Team Services (VSTS) e assim garantir que seu aplicativo seja criado, empacotado e implantado de forma automática.

Este documento reflete o procedimento atual e deve ser alterado ao longo do tempo.

## Pré-requisitos

Siga estas etapas para começar:

1. Verifique se você tem acesso a uma conta do Team Services ou [crie uma](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) por conta própria.

2. Verifique se você tem acesso a um projeto de equipe do Team Services ou [crie um](https://www.visualstudio.com/docs/setup-admin/create-team-project) por conta própria.

3. Verifique se você tem um cluster do Service Fabric no qual você pode implantar seu aplicativo ou crie um usando o [Portal do Azure](service-fabric-cluster-creation-via-portal.md), um [o modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) ou o [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

4. Certifique-se de que você já tenha criado um projeto do Aplicativo do Service Fabric (.sfproj). Você deve ter um projeto que foi criado ou atualizado com o Service Fabric SDK 2.1 ou superior (o arquivo de .sfproj deve conter um valor da propriedade ProjectVersion 1.1 ou superior).

>[AZURE.NOTE] Agentes de build personalizado não são mais necessários. Os agentes hospedados do Team Services agora têm o software de gerenciamento de cluster do Service Fabric pré-instalado, permitindo a implantação de seus aplicativos diretamente de agentes.

## Configurar e compartilhar seus arquivos de origem

A primeira coisa que você vai querer fazer é preparar um perfil de publicação para uso pelo processo de implantação que será executado no Team Services. O perfil de publicação deve ser configurado para direcionar o cluster que você preparou anteriormente:

1.	Escolha um perfil de publicação no seu projeto de Aplicativo que você deseja usar para o fluxo de trabalho de integração contínuo e siga [publicar instruções](service-fabric-publish-app-remote-cluster.md) sobre como publicar um aplicativo para um cluster remoto. Na verdade, não é necessário publicar o aplicativo. Você pode simplesmente clicar o hyperlink **Salvar** no diálogo Publicar depois de configurar os itens corretamente.
2.	Caso deseje que seu aplicativo seja atualizado para cada implantação que ocorre no Team Services, recomendamos que você configure o perfil de publicação para habilitar a atualização. No mesmo diálogo Publicar usado na etapa 1, verifique se a caixa de seleção **Atualizar Aplicativo** está marcada. Saiba mais sobre como [definir configurações de atualização adicionais](service-fabric-visualstudio-configure-upgrade.md). Clique no hyperlink **Salvar** para salvar as configurações no perfil de publicação.
3.	Certifique-se de que você salvou as alterações para o perfil de publicação e cancele o diálogo Publicar.
4.	Agora é hora de [compartilhar seus arquivos de origem do projeto de Aplicativo](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#vs) com o Team Services. Depois que os arquivos de origem estiverem acessíveis no Team Services, você poderá passar para a próxima etapa de geração de compilações.

## Criar a definição de build

Uma definição de build do Team Services descreve um fluxo de trabalho composto por um conjunto de etapas de compilação que são executadas sequencialmente. O objetivo da definição de build que você criará é produzir um pacote de aplicativos do Service Fabric, bem como incluir outros arquivos complementares que podem ser usados para implantar eventualmente o aplicativo em um cluster. Saiba mais sobre as [definições de build](https://www.visualstudio.com/docs/build/define/create) do Team Services.

### Criar uma definição de modelo de build

1.	Abra o projeto de equipe no Visual Studio Team Services.
2.	Selecione a guia **Compilar**.
3.	Selecione o sinal **+** verde para criar uma nova definição de build.
4.	Na caixa de diálogo que é aberta, selecione o **Aplicativo do Service Fabric** dentro da categoria de modelo **Compilar**.
5.	Selecione **Avançar**.
6.	Selecione o repositório e a ramificação associada ao seu aplicativo do Service Fabric.
7.	Selecione a fila de agente que você deseja usar. Há suporte para agentes hospedados.
8.	Selecione **Criar**.
9. Salve a definição de build e dê um nome a ela.
10. A seguir está uma descrição das etapas de build geradas pelo modelo:

| Etapa de build | Descrição |
| --- | --- |
| Restauração do NuGet | Restaura os pacotes do NuGet para a solução. |
| Compilar solução *.sln | Compila a solução inteira. |
| Compilar solução *.sfproj | Gera o pacote de aplicativos do Service Fabric que será usado para implantar o aplicativo. Observe que a localização do pacote de aplicativos é especificado para estar dentro do diretório de artefato do build. |
| Atualizar Versões de Aplicativo do Service Fabric | Atualiza os valores de versão contidos nos arquivos de manifesto do pacote de aplicativos para permitir o suporte à atualização. Consulte a [página de documentação da tarefa](https://go.microsoft.com/fwlink/?LinkId=820529) para obter mais informações. |
| Copiar Arquivos | Copia os arquivos de parâmetros do aplicativo e o perfil de publicação para artefatos de build para ser consumido para implantação. |
| Publicar o Artefato | Publica os artefatos de build. Isso permite que uma definição de versão consuma os artefatos de build. |

### Verifique o conjunto de tarefas padrão

1.	Verifique o campo de entrada **Solução** para as etapas de build **Restauração NuGet** e **Solução de build**. Por padrão, essas etapas serão executadas após todos os arquivos de solução que estão contidos no repositório associado. Se você quiser que apenas a definição de build opere em um desses arquivos de solução, você precisará atualizar explicitamente o caminho para esse arquivo.
2.	Verifique o campo de entrada **Solução** para a etapa de build **Empacotar aplicativo**. Por padrão, essa etapa de build presume que existe apenas um projeto do Aplicativo do Service Fabric (.sfproj) no repositório. Se você tiver vários desses arquivos no repositório e desejar ter como alvo apenas um para essa definição de build, você precisará atualizar explicitamente o caminho desse arquivo. Se você quiser empacotar vários projetos de aplicativo em seu repositório, precisará criar etapas adicionais do **Visual Studio Build** na definição de build que cada um direciona a um projeto de Aplicativo. Então, você também precisa atualizar o campo **Argumentos de MSBuild** para cada uma dessas etapas de build para que a localização do pacote seja exclusiva para cada um deles.
3.	Verifique o comportamento de controle de versão definido na etapa de build **Atualizar Versões de Aplicativo do Service Fabric**. Por padrão, essa etapa de build acrescenta o número de build a todos os valores de versão nos arquivos de manifesto do pacote de aplicativos. Consulte a [página de documentação da tarefa](https://go.microsoft.com/fwlink/?LinkId=820529) para obter mais informações. Isso é útil para dar suporte à atualização do seu aplicativo, pois cada implantação de atualização exige valores de versão diferentes da implantação anterior. Se você não pretende usar a atualização do aplicativo em seu fluxo de trabalho, você pode considerar desabilitar esta etapa de build. Na verdade, ele deverá ser desabilitado se sua intenção é produzir um build que pode ser usado para substituir um aplicativo do Service Fabric existente, pois a implantação falhará se a versão do aplicativo produzido pelo build não coincidir com a versão do aplicativo no cluster.
4.	Se sua solução contiver um projeto .NET Core, você deverá garantir que sua definição de build contenha uma etapa de build que restaura as dependências definidas por qualquer arquivo project.json. Para fazer isso, siga estas etapas:
   1. Selecione **Adicionar etapa de build...**.
   2. Localize a tarefa **Linha de comando** dentro da guia do utilitário de tarefa e clique no botão Adicionar.
   3. Para campos de entrada da tarefa, use os seguintes valores:
      1. Ferramenta: dotnet
      2. Argumentos: restore
   4. Arraste a tarefa para que ela fique imediatamente após a etapa **restauração do NuGet**.
5.	Salve as alterações feitas à definição de build.

### Experimente

Selecione **Enfileirar Build** para iniciar um build. As compilações também serão disparadas no envio por push ou no check-in. Depois de verificar que o build está em execução com êxito, você agora pode passar a definição de uma definição de versão que implantará seu aplicativo em um cluster.

## Criar uma definição de versão

Uma definição de versão do Team Services descreve um fluxo de trabalho composto por um conjunto de tarefas que são executadas sequencialmente. O objetivo da definição de versão que você criará é usar um pacote de aplicativos e implantá-lo em um cluster. Quando usados juntos, a definição de build e a definição de lançamento podem executar todo o fluxo de trabalho, começando com arquivos de origem e terminando com um aplicativo em execução no cluster. Saiba mais sobre as [definições de versão](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) do Team Services.

### Criar uma definição de modelo de versão

1.	Abra o projeto no Visual Studio Team Services.
2.	Selecione a guia **Versão**.
3.	Selecionar o símbolo **+** em verde para criar uma nova definição de versão e selecione **Criar definição de versão** no menu.
4.	No diálogo aberto, selecione **Implantação do Azure Service Fabric** na categoria de modelo **Implantação**.
5.	Selecione **Avançar**.
6.	Selecione a definição de build que você deseja usar como a origem dessa definição de versão. A definição de lançamento fará referência aos artefatos produzidos pela definição de build selecionada.
7.	Marque a caixa de seleção **Implantação contínua** se desejar que o Team Services crie automaticamente uma nova versão e implante o aplicativo Service Fabric sempre que um build for concluído.
8.	Selecione a fila de agente que você deseja usar. Há suporte para agentes hospedados.
9.	Selecione **Criar**.
10.	Edite o nome da definição clicando no ícone de lápis na parte superior da página.
11.	Selecione o cluster no qual seu aplicativo deve ser implantado no campo de entrada **Conexão do Cluster** da tarefa. A conexão do cluster fornece as informações necessárias que permitem que a tarefa de implantação se conecte ao cluster. Se você ainda não tiver uma conexão de cluster para o cluster, selecione o hyperlink **Gerenciar** ao lado do campo para adicionar uma. Na página aberta, execute as seguintes etapas:
    1. Selecione **Novo Ponto de Extremidade de Serviço** e, em seguida, selecione **Azure Service Fabric** no menu.
    2. Selecione o tipo de autenticação usada pelo cluster direcionada por esse ponto de extremidade.
    2. Defina um nome para a conexão no campo **Nome da Conexão**. Normalmente, você poderia usar o nome do cluster.
    3. Definir a URL de ponto de extremidade de conexão do cliente no campo **Ponto de Extremidade do Cluster**. Exemplo: https://contoso.westus.cloudapp.azure.com:19000.
    4. Para as credenciais do Azure Active Directory, defina as credenciais que você deseja usar para se conectar ao cluster nos campos **Nome de Usuário** e **Senha**.
    5. Para a autenticação baseada em certificado, defina a codificação de Base64 do arquivo de certificado do cliente no campo **Certificado de Cliente**. Consulte o pop-up de ajuda nesse campo para obter informações sobre como obter esse valor. Se seu certificado for protegido por senha, defina a senha no campo **Senha**.
    6. Confirme suas alterações clicando em **OK**. Depois de navegar de volta para a definição de versão, clique no ícone para atualizar no campo **Conexão do Cluster** para ver o ponto de extremidade que você acabou de adicionar.
12.	Salve a definição da versão.

A definição que é criada consiste em uma tarefa do tipo **Implantação do Aplicativo Service Fabric**. Consulte a [página de documentação da tarefa](https://go.microsoft.com/fwlink/?LinkId=820528) para obter mais informações sobre essa tarefa.

### Verifique os padrões de modelo

1.	Verifique o campo de entrada **Perfil de Publicação** para a tarefa **Implantar Aplicativo do Service Fabric**. Por padrão, esse campo faz referência a um perfil de publicação denominado Cloud.xml contido nos artefatos do build. Se você deseja fazer referência a um perfil de publicação diferente ou se o build contiver vários pacotes de aplicativos em seus artefatos, você precisará atualizar o caminho adequadamente.
2.	Verifique o campo de entrada **Pacote de Aplicativos** para a tarefa **Implantar Aplicativo do Service Fabric**. Por padrão, isso faz referência ao caminho do pacote de aplicativos padrão usado no modelo de definição de build. Se você tiver modificado o caminho do pacote de aplicativos padrão na definição de build, também precisará atualizar o caminho adequadamente aqui.

### Experimente

Selecione **Criar Versão** no menu de botão **Versão** para criar manualmente uma versão. Na caixa de diálogo que é aberta, selecione o build no qual você deseja basear a versão e clique em **Criar**. Se você tiver habilitado a implantação contínua, versões também serão criadas automaticamente quando a definição de build associada concluir um build.

## Próximas etapas

Para saber mais sobre a integração contínua com aplicativos do Service Fabric, leia os artigos a seguir:

 - [Início da documentação do Team Services](https://www.visualstudio.com/docs/overview)
 - [Gerenciamento de build no Team Services](https://www.visualstudio.com/docs/build/overview)
 - [Gerenciamento de versão no Team Services](https://www.visualstudio.com/docs/release/overview)

<!---HONumber=AcomDC_0803_2016-->