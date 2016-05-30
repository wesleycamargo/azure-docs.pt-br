<properties
   pageTitle="Criar hosts do Docker no Azure com o computador Docker | Microsoft Azure"
   description="Descreve o uso do computador Docker para criar hosts do Docker no Azure."
   services="visual-studio-online"
   documentationCenter="na"
   authors="SteveLas"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="05/16/2016"
   ms.author="stevelas" />

# Criar hosts do Docker no Azure com docker-machine

A execução de contêineres do [Docker](https://www.docker.com/) exige uma VM host executando o daemon do docker. Este tópico descreve como usar o comando [docker-machine](https://docs.docker.com/machine/) para criar novas VMs Linux, configuradas com o daemon do Docker, em execução no Azure.

**Observação**
- *Este artigo depende versão 0.7.0 ou posterior do docker-machine*
- *Em breve, os contêineres do Windows terão suporte por meio do docker-machine*

## Criar VMs com o computador Docker

Crie VMs host do docker no Azure com o comando `docker-machine create` usando o driver `azure`.

O driver do Azure precisará da id de sua assinatura. Você pode usar a [CLI do Azure](xplat-cli-install.md) ou [Portal do Azure](https:/portal.azure.com) para recuperar sua assinatura do Azure.

**Usando o Portal do Azure**
- Selecione Assinaturas na página de navegação à esquerda e copie a id da assinatura.

**Usando a CLI do Azure**
- Digite ```azure account list``` e copie a id da assinatura.


Digite `docker-machine create --driver azure` para ver as opções e seus valores padrão. Você também consultar [Documentação do Driver do Docker Azure](https://docs.docker.com/machine/drivers/azure/) para saber mais.

O exemplo a seguir conta com os valores padrão, mas ele abre opcionalmente a porta 80 na VM para acesso à internet.

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## Escolha um host docker com docker-machine
Quando houver uma entrada no docker-machine para seu host, será possível definir o host padrão ao executar comandos do docker.
##Usando o PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##Como usar o Bash
```bash
eval $(docker-machine env MyDockerHost)
```

Agora você pode executar comandos do docker no host especificado

```
docker ps
docker info
```

## Executar um contêiner

Com um host configurado, agora é possível executar um servidor Web simples para testar se o host foi configurado corretamente. Aqui usamos uma imagem padrão do nginx, especificamos que ela deve escutar na porta 80 e, que se a VM host for reiniciada, o contêiner será reiniciado também (`--restart=always`).

```bash
docker run -d -p 80:80 --restart=always nginx
```

A saída deve ter uma aparência semelhante ao seguinte:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## Testar o contêiner

Examine os contêineres em execução usando `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

E verifique o contêiner em execução, digitando `docker-machine ip <VM name>` para localizar o endereço IP a ser inserido no navegador:
```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Contêiner ngnix em execução](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##Resumo
Com o docker-machine você pode provisionar facilmente hosts do docker no Azure para suas validações individuais de host do docker. Para produção de hospedagem de contêineres, consulte o [Serviço de Contêiner do Azure](http://aka.ms/AzureContainerService)

Para desenvolver aplicativos de núcleo do .NET com o Visual Studio, consulte [Ferramentas do Docker para Visual Studio](http://aka.ms/DockerToolsForVS)

<!---HONumber=AcomDC_0518_2016-->