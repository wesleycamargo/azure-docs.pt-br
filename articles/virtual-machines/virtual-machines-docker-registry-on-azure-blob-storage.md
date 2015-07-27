<properties 
    pageTitle="Implantando seu próprio Registro do Docker privado no Azure"
    description="Descreve como você pode usar o Registro do Docker para hospedar suas imagens de contêiner no serviço de Armazenamento de Blob do Azure."
    services="virtual-machines"
    documentationCenter="virtual-machines"
    authors="ahmetalpbalkan"
    editor="squillace"
    manager=""
	tags=""/>

<tags
    ms.service="virtual-machines"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="06/17/2015" 
    ms.author="ahmetb" />

# Implantando seu próprio Registro do Docker privado no Azure

Este documento descreve o que é um Registro do Docker privado e mostra como você pode implantar uma imagem de contêiner do Registro do Docker 2.0 em um registro privado do Docker no Microsoft Azure usando o Armazenamento Blob do Azure.

Este documento supõe que:

1. você sabe como usar o Docker e tem imagens Docker para armazenar. (Não concorda? [Saiba mais sobre o Docker](https://www.docker.com).
2. Você tem um servidor com o mecanismo Docker instalado. (Não concorda? [Faça isso rapidamente no Azure.](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## O que é um Registro do Docker privado?

Para enviar seus aplicativos em recipientes para a nuvem, você constrói uma imagem de contêiner do Docker e a armazena em algum lugar para que possa ser usada por você e por outras pessoas.

Embora a criação e entrega de uma imagem de contêiner para a nuvem seja fácil, é um desafio armazenar a imagem gerada de forma confiável. Por esse motivo, o Docker oferece um serviço centralizado chamado [Docker Hub][docker-hub], a fim de armazenar imagens de contêiner na nuvem e permitir que você crie contêineres a qualquer momento usando essas imagens.

Embora o [Docker Hub][docker-hub] seja um serviço pago para armazenamento de suas imagens de contêiner de aplicativo privado, o Docker respeita as necessidades do desenvolvedor e fornece um conjunto de ferramentas de código aberto para armazenar as imagens no seu próprio registro privado do Docker, atrás de um firewall ou localmente sem precisar acessar a Internet pública. Como o armazenamento de Blob do Azure é fácil de proteger, você pode usá-lo rapidamente para criar e utilizar um registro privado do Docker no Azure controlado por você mesmo.

## Por que você deveria hospedar um registro do Docker no Azure?

Ao hospedar sua instância do Registro do Docker no Microsoft Azure e armazenar imagens no Armazenamento de Blob do Azure, você pode ter vários benefícios:

**Segurança:** as imagens do Docker não saem dos datacenters do Azure, portanto, não cruzam a Internet pública como fariam se você estivesse usando o Docker Hub.
  
**Desempenho:** suas imagens do Docker são armazenadas no mesmo datacenter ou região que seus aplicativos. Isso significa que as imagens serão recebidas com mais rapidez e de forma mais confiável em comparação ao Docker Hub.

**Confiabilidade:** ao usar o Armazenamento de Blob do Microsoft Azure, você pode usar várias propriedades de armazenamento como alta disponibilidade, redundância, armazenamento premium (SSDs) e assim por diante.

## Configuração do Registro do Docker para uso do Armazenamento de Blob do Azure

(Recomenda-se a leitura de [Documentação do Registro do Docker 2.0][registry-docs] antes de continuar).

Você pode [configurar][registry-config] o Registro do Docker de duas maneiras diferentes. Você pode:

1. Usar um arquivo `config.yml`. Nesse caso, será necessário criar uma imagem separada do Docker na parte superior da imagem do `registry`.
2. Substituir o arquivo de configuração padrão por meio de variáveis de ambiente: realize suas tarefas sem criar e manter uma imagem separada do Docker.

Para simplificar, este tópico segue a opção 2, usando as variáveis de ambiente.

Para executar uma instância do Registro do Docker que: * usa sua Conta de Armazenamento do Azure para armazenar as imagens * escuta na porta 5000 da Máquina virtual * não tem qualquer autenticação configurada (não recomendado, consulte a observação abaixo)

você precisa executar o seguinte comando do Docker em seu terminal bash (substituir `<storage-account>` e `<storage-key>` por suas credenciais):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Depois que o comando for encerrado, você poderá ver o contêiner hospedando sua instância privada do Registro do Docker executando o comando `docker ps` em seu host:

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT]A configuração da segurança do Registro do Docker não é abordada neste documento, e seu registro poderá ser acessado por qualquer pessoa sem autenticação se você abrir a porta para a porta do registro no ponto de extremidade da Máquina virtual ou balanceador de carga, caso você use o comando de implantação acima.
>
> Leia a documentação [Configuração do Registro do Docker][registry-config] para saber como proteger a instância do Registro e suas imagens.

## Próximas etapas

Depois de configurar o Registro, você o usará novamente. Comece com o [registry-docs] do docker.

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[registry-docs]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 

<!---HONumber=July15_HO2-->