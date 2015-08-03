<properties
	pageTitle="Usando a extensão de máquina virtual Docker para Linux no Azure"
	description="Descreve as extensões de máquinas virtuais Docker e Azure Virtual, além de mostrar como criar máquinas virtuais que sejam hosts do Docker programaticamente no Azure, por meio da linha de comando, usando a CLI do Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="05/25/2015"
	ms.author="rasquill"/>


# Usando a extensão de máquina virtual Docker com o Portal do Azure

O [Docker](https://www.docker.com/) é uma das abordagens de virtualização mais populares que utiliza [contêineres Linux](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como forma de isolar dados e computar recursos compartilhados. Você pode usar a extensão da VM Docker gerenciada pelo [Agente Linux do Azure] para criar uma VM Docker que hospede diversos contêineres para seus aplicativos no Azure.

> [AZURE.NOTE]Este tópico descreve como criar uma VM Docker no Portal do Azure. Para ver como criar uma máquina virtual Docker na linha de comando, confira [Como usar a extensão de máquina virtual do Docker na interface de linha de comando (CLI do Azure)]. Para participar de um debate de alto nível sobre contêineres e suas vantagens, confira o [Quadro de comunicações de alto nível do Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## Criar uma nova máquina virtual na Galeria de Imagens
A primeira etapa requer uma máquina virtual do Azure obtida por meio de uma imagem Linux compatível com a extensão de máquina virtual Docker, usando uma imagem Ubuntu 14.04 LTS da Galeria de Imagens como exemplo de imagem do servidor e um desktop Ubuntu 14.04 como cliente. No portal, clique em **+ Novo** no canto inferior esquerdo para criar uma nova instância de máquina virtual e selecione uma imagem Ubuntu 14.04 LTS dentre as opções disponíveis ou na Galeria de Imagens, como mostramos abaixo.

> [AZURE.NOTE]Atualmente, apenas imagens do Ubuntu 14.04 LTS mais recentes de julho de 2014 dão suporte à extensão VM Docker.

![Criar uma nova imagem Ubuntu](./media/virtual-machines-docker-with-portal/ChooseUbuntu.png)

## Criar certificados Docker

Depois de criar a máquina virtual, verifique se o Docker está instalado no computador cliente. Para obter detalhes, confira [Instruções de instalação do Docker](https://docs.docker.com/installation/#installation).

Crie os arquivos de certificado e de chave para comunicação do Docker de acordo com as instruções em [Executando Docker com https] e coloque-os no diretório **`~/.docker`** do computador cliente.

> [AZURE.NOTE]A extensão de VM Docker no portal atualmente requer credenciais que são codificadas na base 64.

Na linha de comando, use **`base64`** ou outra ferramenta de codificação de sua preferência para criar tópicos codificados em base64. Para executar essas ações com um conjunto simples de arquivos de certificado e de chave, use um código parecido com este:

```
 ~/.docker$ l
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ l
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## Adicionar a extensão de máquina virtual Docker
Para adicionar a extensão de máquina virtual Docker, localize a instância de máquina virtual criada e clique em **Extensões** para visualizar as extensões de máquina virtual, como mostramos abaixo.
> [AZURE.NOTE]Essa funcionalidade somente tem suporte no portal de visualização: https://portal.azure.com/

![](./media/virtual-machines-docker-with-portal/ClickExtensions.png)
### Adicionar uma extensão
Clique em **+ Adicionar** para visualizar as extensões de máquina virtual que podem ser adicionadas à máquina em questão.

![](./media/virtual-machines-docker-with-portal/ClickAdd.png)
### Selecionar a extensão de máquina virtual Docker
Escolha a extensão de máquina virtual Docker, que apresenta a descrição do Docker e links importantes. Em seguida, clique em **Criar** na parte inferior da página para iniciar o procedimento de instalação.

![](./media/virtual-machines-docker-with-portal/ChooseDockerExtension.png)

![](./media/virtual-machines-docker-with-portal/CreateButtonFocus.png)
### Adicionar seus arquivos de certificado e de chaves:

Nos campos do formulário, insira as versões codificadas em base64 do certificado de autoridade de certificação, do certificado do servidor e da chave do servidor, como mostramos na imagem abaixo.

![](./media/virtual-machines-docker-with-portal/AddExtensionFormFilled.png)

> [AZURE.NOTE]Observe que (como na imagem anterior) 2376 é preenchido por padrão. Você pode inserir qualquer ponto de extremidade nesse campo, mas precisará abrir o ponto de extremidade correspondente na próxima etapa. Se alterar o padrão, lembre-se de abrir o ponto de extremidade correspondente na etapa seguinte.

## Adicionar o ponto de extremidade de comunicação do Docker
Ao visualizar sua máquina virtual no grupo de recursos que você criou, navegue para clicar em **Pontos de extremidade** e visualizar os pontos na máquina virtual, como mostramos abaixo.

![](./media/virtual-machines-docker-with-portal/AddingEndpoint.png)

Clique em **+ Adicionar** para adicionar outro ponto de extremidade. No caso padrão, insira o nome do ponto de extremidade (neste exemplo, **docker**) e informe 2376 como porta privada e pública. Selecione **TCP** como valor do protocolo e clique em **OK** para criar o ponto de extremidade.

![](./media/virtual-machines-docker-with-portal/AddEndpointFormFilledOut.png)


## Testar o cliente Docker e o host Docker do Azure
Localize e copie o nome do domínio da sua máquina virtual. Na linha de comando do computador cliente, insira `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:2376 info` (onde *dockerextension* é substituído pelo subdomínio da máquina virtual).

O resultado deve ter esta aparência:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Depois de concluir as etapas acima, você terá um host de Docker totalmente funcional em execução em uma VM do Azure, configurado para ser conectado remotamente a outros clientes.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Você está pronto para conferir o [Guia do usuário do Docker] e usar sua máquina virtual Docker. Se quiser automatizar a criação hosts de Docker em VMs do Azure por meio da interface de linha de comando, confira [Como usar a extensão de VM Docker da Interface de Linha de Comando (CLI do Azure)]

<!--Anchors-->
[Create a new VM from the Image Gallery]: #createvm
[Create Docker Certificates]: #dockercerts
[Add the Docker VM Extension]: #adddockerextension
[Test Docker Client and Azure Docker Host]: #testclientandserver
[Next steps]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Como usar a extensão de VM Docker da Interface de Linha de Comando (CLI do Azure)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Como usar a extensão de máquina virtual do Docker na interface de linha de comando (CLI do Azure)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Agente Linux do Azure]: virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

[Executando Docker com https]: http://docs.docker.com/articles/https/
[Guia do usuário do Docker]: https://docs.docker.com/userguide/
 

<!---HONumber=July15_HO4-->