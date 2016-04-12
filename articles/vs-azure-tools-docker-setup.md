<properties
   pageTitle="Configurar um host do Docker com o VirtualBox | Microsoft Azure"
   description="Instruções passo a passo para configurar uma instância de Docker padrão usando a máquina Docker e o VirtualBox"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="03/27/2016"
   ms.author="tarcher" />

# Configurar um host do Docker com o VirtualBox

## Visão geral
Este artigo orienta você pela configuração de uma instância de Docker padrão usando a máquina Docker e o VirtualBox. Se você estiver usando o [Docker para Windows beta](http://beta.docker.com/), essa configuração não é necessária.

## Pré-requisitos
As ferramentas a seguir precisam ser instaladas.

- [Caixa de Ferramentas do Docker](https://www.docker.com/products/overview#/docker_toolbox)

## Configurando o cliente Docker com o Windows PowerShell

Para configurar um cliente Docker, apenas abra o Windows PowerShell e execute as seguintes etapas:

1. Crie uma instância de host do docker padrão.

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. Verifique se a instância padrão está configurada e em execução. (Você deve ver uma instância chamada “padrão” em execução.

		docker-machine ls 
		
	![][0]
 
1. Defina o padrão como o host atual e configure seu shell.

        docker-machine env default | Invoke-Expression

1. Exiba os contêineres do Docker ativo. A lista deve estar vazia.

		docker ps

	![][1]
 
> [AZURE.NOTE] Sempre que você reinicializar o computador de desenvolvimento, precisará reiniciar o host do Docker local. Para fazer isso, execute o seguinte comando no prompt de comando: `docker-machine start default`

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png

<!-----------HONumber=AcomDC_0330_2016-->