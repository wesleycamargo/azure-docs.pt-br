<properties
    pageTitle="Publicar um aplicativo em um cluster remoto com o VS | Microsoft Azure"
    description="Saiba mais sobre as etapas necessárias para publicar um aplicativo em um cluster do Service Fabric remoto usando o Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawa"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Publicar um aplicativo em um cluster remoto usando o Visual Studio

As Ferramentas do Service Fabric do Visual Studio fornecem uma maneira simples e que permite a repetição e execução por script de publicação de um aplicativo em um cluster do Service Fabric. Isso é feito usando um script de implantação do PowerShell com perfis de publicação. Os perfis de publicação são arquivos no formato .XML que armazenam informações essenciais sobre a publicação. A caixa de diálogo **Publicar Aplicativo do Service Fabric** também foi adicionada para permitir que os usuários publiquem com facilidade um aplicativo em um cluster local ou remoto. Quaisquer alterações de configuração feitas por meio da caixa de diálogo Publicar são capturadas nos perfis de publicação. Isso permite que, posteriormente, você use as configurações de publicação editadas de forma manual em um processo de automação.

## Artefatos necessários para publicar um aplicativo em um cluster do Service Fabric

### Deploy-FabricApplication.ps1

Este é um script do PowerShell que usa um caminho de perfil de publicação como um parâmetro para a publicação de aplicativos do Service Fabric.

### Perfis de publicação

Uma pasta no projeto de Aplicativo do Service Fabric chamado **PublishProfiles** contém os arquivos de **Cloud.XML** e **Local.XML**. Esses são "perfis de publicação" que armazenam informações essenciais para publicação de um aplicativo, como: - parâmetros de conexão de cluster do Service Fabric - Caminho para um arquivo de parâmetro do aplicativo - Configurações de atualização.

### Arquivos de parâmetros do aplicativo

Uma pasta no projeto de aplicativo do Service Fabric chamada **ApplicationParameters** contém arquivos XML para valores de parâmetro do manifesto do aplicativo especificados pelo usuário. Os arquivos de manifesto do aplicativo podem ser parametrizados, de modo que você possa usar valores diferentes para configurações de implantação.

Por exemplo, você pode alterar a contagem de partições para ajustar a ambientes diferentes para cada implantação. Quando você cria um projeto, as configurações no manifesto do aplicativo, tais como **TargetReplicaSetSize** e **PartitionCount**, são convertidas em parâmetros. O valor padrão desses parâmetros é especificado na seção **Parâmetros** do arquivo de manifesto do aplicativo (ApplicationManifest.XML, localizado no projeto de aplicativo do Service Fabric). Quaisquer valores adicionados ao arquivo de parâmetro do aplicativo substituirá os valores padrão no arquivo de manifesto do aplicativo.

>[AZURE.NOTE]Para os serviços do Ator, você deve compilar o projeto primeiro a fim de gerar os parâmetros no arquivo de manifesto.

Veja um exemplo de arquivo de manifesto do aplicativo.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application2Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Actor1ActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="Actor1ActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="Actor1ActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Actor1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Actor1ActorService">
         <StatefulService ServiceTypeName="Actor1ActorServiceType" TargetReplicaSetSize="[Actor1ActorService_TargetReplicaSetSize]" MinReplicaSetSize="[Actor1ActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[Actor1ActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## Publicar um aplicativo em um cluster do Service Fabric usando a caixa de diálogo Publicar Aplicativo do Service Fabric

As etapas a seguir demonstram como publicar um aplicativo usando a caixa de diálogo **Publicar Aplicativo do Service Fabric** fornecida pelas Ferramentas do Service Fabric do Visual Studio.

1. No menu de atalho do projeto de Aplicativo do Service Fabric, escolha **Publicar...** para exibir a caixa de diálogo **Publicar Aplicativo do Service Fabric**.

    ![][0]

    O arquivo selecionado na lista suspensa **Perfil de destino** é onde todas as configurações, exceto as **Versões do manifesto**, são salvas. Você pode reutilizar um perfil existente ou criar um novo escolhendo **<Gerenciar Perfis... >** na lista suspensa **Perfil de destino**. Quando você escolhe um perfil de publicação, seu conteúdo aparece nos campos correspondentes da caixa de diálogo. Para salvar suas alterações a qualquer momento, escolha o link **Salvar Perfil**.

1. A seção **Ponto de extremidade da conexão** permite que você especifique um ponto de extremidade de publicação um cluster do Service Fabric local ou remoto. Para adicionar ou alterar o ponto de extremidade de conexão, escolha o botão **Selecionar...**. A caixa de diálogo **Selecionar Cluster do Service Fabric** mostra os pontos de extremidade de conexão do cluster do Service Fabric disponíveis nos quais você pode publicar. (Se ainda não estiver conectado a uma assinatura do Azure, você receberá uma solicitação para fazê-lo.) Escolha um ponto de extremidade de conexão.

    ![][1]

    Depois de escolher um ponto de extremidade, o Visual Studio valida a conexão com o cluster do Service Fabric selecionado. Se o cluster não for seguro, o Visual Studio poderá conectar-se a ele imediatamente. No entanto, se o cluster estiver protegido, será necessário instalar um certificado em seu computador local antes de continuar. Confira [Como configurar conexões seguras](service-fabric-visualstudio-configure-secure-connections.md) para saber mais. Ao terminar, escolha o botão **OK**. O cluster selecionado aparece na caixa de diálogo **Publicar Aplicativo do Service Fabric**.

1. A lista suspensa **Arquivo de Parâmetros do Aplicativo** permite que você navegue até um arquivo de parâmetros do aplicativo. Um arquivo de parâmetros do aplicativo contém valores especificados pelo usuário para os parâmetros no arquivo de manifesto do aplicativo. Para adicionar ou alterar um parâmetro, escolha o botão **Editar**. Insira ou altere o valor do parâmetro na grade **Parâmetros**. Quando terminar, escolha o botão **Salvar**.

    ![][2]

1. A caixa de seleção **Atualizar o Aplicativo** permite que você especifique se essa ação de publicação é uma atualização. Ações de publicação de atualização são diferentes das ações de publicação normais. Confira [Atualização de Aplicativo do Service Fabric](service-fabric-application-upgrade.md) para obter uma lista das diferenças. Para definir as configurações de atualização, escolha o link **Definir Configurações de Atualização**. O editor de parâmetros de atualização é exibido. Confira [Configurar a atualização de um aplicativo do Service Fabric](service-fabric-visualstudio-configure-upgrade.md) para saber mais sobre os parâmetros de atualização.

1. Escolha o botão **Versões do Manifesto...** para exibir a caixa de diálogo **Editar Versões**. Você precisa atualizar as versões do aplicativo e do serviço para que uma atualização ocorra. Confira [Tutorial de atualização de aplicativo do Service Fabric](service-fabric-application-upgrade-tutorial.md) para saber como as versões do aplicativo e do manifesto de serviço afetam um processo de atualização.

    ![][3]

    Se o aplicativo e as versões de serviço usarem o controle de versão semântico, por exemplo, 1.0.0, ou valores numéricos no formato 1.0.0.0, selecione a opção **Atualizar automaticamente as versões do aplicativo e do serviço**. Quando você escolhe essa opção, os números de versão do serviço e do aplicativo são atualizados automaticamente sempre que um código, uma configuração ou a versão do pacote de dados é atualizado. Se você preferir editar as versões manualmente, desmarque a caixa de seleção para desativar esse recurso.

    >[AZURE.NOTE]Para que todas as entradas do pacote apareçam em um projeto de Ator, primeiro compile o projeto a fim de gerar as entradas nos arquivos de Manifesto do Serviço.

1. Quando você terminar de especificar todas as configurações necessárias, escolha o botão **Publicar** para publicar seu aplicativo no cluster do Service Fabric selecionado. As configurações especificadas são aplicadas ao processo de publicação.

## Próximas etapas

Para saber como automatizar o processo de publicação em um ambiente de Integração Contínua, confira [Configuração da integração contínua no Service Fabric](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png

<!---HONumber=Nov15_HO4-->