<properties
   pageTitle="Configurar o ambiente de desenvolvimento da Malha do Serviço | Microsoft Azure"
   description="Instale o tempo de execução da Malha do Serviço, o SDK e as ferramentas e crie um cluster de desenvolvimento local."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/10/2015"
   ms.author="seanmck"/>

# Configurar o ambiente de desenvolvimento da Malha do Serviço
 Este artigo aborda tudo o que você precisa para começar a criar aplicativos da [Malha do Serviço][1], incluindo a instalação do tempo de execução, do SDK e das ferramentas, bem como a configuração de um cluster local.

 >[AZURE.NOTE]Estas instruções têm como objetivo a configuração de novos PCs. Se você instalou uma versão anterior do Service Fabric em seu computador, siga as [instruções para atualizar seu ambiente de desenvolvimento](service-fabric-update-your-development-environment.md).

## Pré-requisitos
### Versões de sistema operacional com suporte
Há suporte para as seguintes versões de sistema operacional:

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

As ferramentas da Malha de Serviço dependem do Visual Studio 2015, que pode ser encontrado [aqui][2].

> [AZURE.NOTE]Se não estiver executando uma das versões de sistema operacional com suporte ou prefere não instalar o Visual Studio 2015 no seu computador, você poderá configurar uma máquina virtual do Azure com o Windows Server 2012 R2 e o Visual Studio 2015 pré-instalados usando uma imagem da Galeria da VM.

## Instalar o tempo de execução, o SDK e as ferramentas

A instalação dos componentes da Malha do Serviço é feita pelo Web Platform Installer. Siga estas instruções de instalação:

1. [Baixe o SDK][3] usando o Web Platform Installer.

2. Clique em **Instalar** para iniciar o processo de instalação.

3. Leia e aceite o EULA.

A instalação continuará automaticamente.

## Habilitar a execução de script do PowerShell

A Malha do Serviço usa scripts do Windows PowerShell para criar um cluster de desenvolvimento local e implantar aplicativos do Visual Studio. Por padrão, o Windows bloqueia a execução desses scripts. Para habilitá-los, você deve modificar sua política de execução do PowerShell. Abra o PowerShell com um administrador e insira o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Instalar e iniciar um cluster local
Um cluster local representa a topologia de vários computadores que você, no fim, usará em produção em um único computador de desenvolvimento. Para configurar o cluster local, siga estas etapas:


1. Feche todas as outras janelas do PowerShell e inicie uma nova como Administrador.

2. Navegue até o diretório de instalação do cluster

    ```powershell
    cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"
    ```
    
3. Executar

    ```powershell
    .\DevClusterSetup.ps1
    ```

Em alguns segundos, você deve ver a saída que mostra as informações do nó e a confirmação de que o cluster foi criado com êxito. Em alguns casos, você pode ver avisos durante a inicialização dos Serviços de Nomenclatura e do Serviço de Host da Malha do Serviço. Esses avisos são normais e serão seguidos momentaneamente por algumas informações básicas sobre o cluster.

> [AZURE.NOTE]O cluster local usa exatamente o mesmo tempo de execução que será executado no Azure. Ele não é simulado nem emulado. A única diferença é que todos os nós são executados em um único computador, em vez de serem distribuídos entre vários computadores como serão no Azure.

## Validar a configuração do cluster

Você pode verificar se o cluster foi criado com êxito usando a ferramenta Gerenciador da Malha do Serviço fornecida com o SDK.

1. Inicie o Gerenciador do Service Fabric executando 

    ```powershell
    . "$env:ProgramW6432\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe"
    ```

2. Expanda o nó Onebox/Cluster Local no canto superior esquerdo.

3. Verifique se os modos de exibição Aplicativo e Nó estão verdes.

Se algum elemento não estiver verde ou um erro for exibido, aguarde alguns segundos e clique no botão Atualizar. Se ainda estiver com problemas, confira as [etapas da solução de problemas de configuração](service-fabric-troubleshoot-local-cluster-setup.md).

## Próximas etapas
Agora que seu ambiente de desenvolvimento está configurado, você pode começar a criar e executar aplicativos.

- [Saiba mais sobre os modelos de programação: Atores Confiáveis e Serviços Confiáveis](service-fabric-choose-framework.md)
- [Introdução à API de Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
- [Introdução à API de Atores Confiáveis](service-fabric-reliable-actors-get-started.md)
- [Confira os exemplos da Malha do Serviço no GitHub](https://github.com/azure/servicefabric-samples)
- [Visualizar o cluster usando o Gerenciador da Malha do Serviço](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Página de campanha da Malha do Serviço"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Link do WebPI"

<!---HONumber=Sept15_HO2-->