<properties
   pageTitle="Implantar um contêiner ASP.NET em um host remoto do Docker | Microsoft Azure"
   description="Saiba como usar as Ferramentas do Visual Studio para Docker para publicar um aplicativo Web ASP.NET 5 em um contêiner do Docker executado em uma máquina do host do Docker no Azure"   
   services="visual-studio-online"
   documentationCenter=".net"
   authors="tomarcher"
   manager="douge"
   editor=""/>

<tags
   ms.service="visual-studio-online"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/08/2016"
   ms.author="tarcher"/>

# Implantar um contêiner ASP.NET em um host remoto do Docker

## Visão geral
O Docker é um mecanismo de contêiner leve, semelhante em alguns pontos a uma máquina virtual, que você pode usar para hospedar aplicativos e serviços. O Visual Studio oferece suporte ao Docker no Ubuntu, no CoreOS e no Windows. Este tutorial explica como usar a extensão [Ferramentas do Visual Studio 2015 para Docker](http://aka.ms/DockerToolsForVS) para publicar um aplicativo ASP.NET 5 em um host do Docker no Azure.

## 1\. Pré-requisitos
Para concluir este tutorial, você precisará:

- Criar uma VM de Host do Docker no Azure, conforme descrito em [How to use docker-machine with Azure](./virtual-machines/virtual-machines-linux-classic-docker-machine.md) (Como usar uma máquina Docker com o Azure).
- Instalar o [Visual Studio 2015](https://www.visualstudio.com/pt-BR/downloads/download-visual-studio-vs.aspx)
- Instalar as [Ferramentas do Visual Studio 2015 para Docker - Prévia](http://aka.ms/DockerToolsForVS)

## 2\. Criar um aplicativo Web ASP.NET 5
As etapas a seguir guiarão você na criação de um aplicativo básico ASP.NET 5 que será usado neste tutorial.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 3\. Adicionar suporte ao Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 4\. Apontar para o host remoto do Docker

1.  No **Gerenciador de Soluções** do Visual Studio, localize a pasta **Propriedades** e expanda-a.
1.  Abra o arquivo *Docker.props*.

    ![Abrir o arquivo Docker.props][0]

1.  Altere o valor de **DockerMachineName** para o nome do seu host remoto do Docker. Se você não souber o nome do seu host remoto do Docker, execute ```docker-machine ls``` no prompt do Windows PowerShell. Use o valor listado na coluna **Nome** para o host desejado.

    ![Alterar nome da máquina do Docker][1]

1.  Reinicie o Visual Studio.

## 5\. Configurar o ponto de extremidade do host do Docker no Azure
Antes de implantar o aplicativo do Visual Studio no Azure, adicione o ponto de extremidade 80 à Máquina Virtual do Host do Docker para que depois você possa exibir o aplicativo no navegador. Isso pode ser feito no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885) ou pelo Windows PowerShell:

- **Use o [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885) para configurar o ponto de extremidade do Host do Docker do Azure**

    1.  Navegue até o [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885). 
    
    1.  Toque em **MÁQUINAS VIRTUAIS**.
    
    1.  Selecione a máquina virtual do Host do Docker.
    
    1.  Toque na guia **PONTOS DE EXTREMIDADE**.
    
    1.  Toque em **ADICIONAR** (na parte inferior da página).
    
    1.  Siga as instruções para expor a porta 80, que é usada pelo script de implantação por padrão.

- **Usar o Windows PowerShell para configurar o ponto de extremidade do Host do Docker no Azure**

    1. Abra o Windows PowerShell
    1. Insira o comando a seguir no prompt do Windows PowerShell (alterando os valores entre os colchetes angulares para que correspondam ao seu ambiente):  

        ```PowerShell
        C:\PS>Get-AzureVM -ServiceName "<your_cloud_service_name>" -Name "<your_vm_name>" | Add-AzureEndpoint -Name "<endpoint_name>" -Protocol "tcp" -PublicPort 80 -LocalPort 80 | Update-AzureVM
        ```

## 6\. Criar e executar o aplicativo
Ao implantar em hosts remotos, o recurso de mapeamento de volume usado para Editar e Atualizar o desenvolvimento não funcionará. Portanto, você precisará usar a *configuração de versão* ao criar seu aplicativo para evitar a configuração de mapeamento de volume. Siga estas etapas para executar o aplicativo.

1.  Na barra de ferramentas do Visual Studio, selecione a configuração **Versão**

1.  Altere o destino de inicialização para **Docker**.

1.  Toque no ícone do **Docker** para criar e executar o aplicativo.

![Iniciar aplicativo][2]

Você deverá ver resultados semelhantes ao seguinte.

![Exibir aplicativo][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0511_2016-->