<properties
   pageTitle="Configurar o ambiente de desenvolvimento | Microsoft Azure"
   description="Instale o tempo de execução, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir a instalação, você estará pronto para criar aplicativos."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="seanmck"/>

# Preparar seu ambiente de desenvolvimento
 Para compilar e executar os [aplicativos do Service Fabric][1] em seu computador de desenvolvimento, você precisará instalar as ferramentas de tempo de execução, o SDK e configurar um cluster local.

## Pré-requisitos
### Versões de sistema operacional com suporte
Há suporte para as seguintes versões de sistema operacional:

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

As ferramentas do Service Fabric dependem do Visual Studio 2015, que pode ser encontrado [aqui][2].

> [AZURE.NOTE]Se não estiver executando uma das versões de sistema operacional com suporte ou se preferir não instalar o Visual Studio 2015 no seu computador, você poderá configurar uma máquina virtual do Azure com o Windows Server 2012 R2 e o Visual Studio 2015 pré-instalados usando uma imagem da galeria de máquinas virtuais do Azure.

## Instalar o tempo de execução, o SDK e as ferramentas

A instalação dos componentes da Malha do Serviço é feita pelo Web Platform Installer. Siga estas instruções de instalação:

1. [Baixar o SDK][3] usando o Web Platform Installer.

2. Clique em **Instalar** para iniciar o processo de instalação.

3. Leia e aceite o EULA.

A instalação continuará automaticamente.

## Habilitar a execução de script do PowerShell

A Malha do Serviço usa scripts do Windows PowerShell para criar um cluster de desenvolvimento local e implantar aplicativos do Visual Studio. Por padrão, o Windows bloqueia a execução desses scripts. Para habilitá-los, você deve modificar sua política de execução do PowerShell. Abra o PowerShell com um administrador e insira o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Próximas etapas
Agora que seu ambiente de desenvolvimento está configurado, você pode começar a criar e executar aplicativos.

- [Criar seu primeiro aplicativo do Service Fabric no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Saiba como implantar e gerenciar aplicativos no seu cluster local](service-fabric-get-started-with-a-local-cluster.md)
- [Saiba mais sobre os modelos de programação: Atores Confiáveis e Serviços Confiáveis](service-fabric-choose-framework.md)
- [Confira os exemplos da Malha do Serviço no GitHub](https://aka.ms/servicefabricsamples)
- [Visualizar o cluster usando o Gerenciador da Malha do Serviço](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Página de campanha da Malha do Serviço"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Link do WebPI"

<!---HONumber=AcomDC_1125_2015-->