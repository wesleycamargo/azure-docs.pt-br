<properties 
	pageTitle="Como usar o CoreOS no Azure" 
	description="Descreve o CoreOS, como criar uma máquina virtual CoreOS no Azure e seu uso básico." 
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
	ms.date="01/02/2015" 
	ms.author="rasquill"/>


<!--The next line, with one pound sign at the beginning, is the page title--> 
# Como usar o CoreOS no Azure

Este tópico descreve o [CoreOS] e mostra como criar um cluster de três máquinas virtuais CoreOS no Azure rapidamente. Ele usa os elementos mais básicos das implantações do CoreOS e os exemplos do [CoreOS com Azure], o [Tutorial de Tim Park sobre o CoreOS] e o [Tutorial de Patrick Chanezon sobre o CoreOS] a fim de demonstrar os requisitos mínimos para compreender a estrutura básica de uma implantação CoreOS e obter um cluster de três máquinas virtuais que executam sem problemas. 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
Este tópico contém estas seções:

+ [CoreOS, clusters e contêineres Linux]
+ [Considerações de segurança]
+ [Como usar o CoreOS no Azure]
+ [Próximas etapas]


## <a id='intro'>CoreOS, clusters e contêineres Linux</a>

O CoreOS é uma versão leve do Linux, criada para dar suporte à criação rápida de clusters de máquinas virtuais possivelmente muito grandes e que usam contêineres Linux como único mecanismo de empacotamento, incluindo contêineres [Docker]. O CoreOS deve dar suporte a:

+ altíssimo nível de automação
+ implantação de aplicativos mais fácil e mais consistente
+ escalabilidade dos aplicativos e sistemas

Os recursos do CoreOS que dão suporte a essas metas são:

1. Um sistema em pacote: o CoreOS só executa imagens de contêineres Linux que são executadas em contêineres Linux a fim de garantir a velocidade, a uniformidade e a facilidade da implantação
2. O sistema operacional é atualizado automaticamente; assim, os sistemas operacionais são atualizados como uma única entidade e é possível reverter as atualizações para um estado conhecido com facilidade
3. Daemons (serviços) internos [etcd](https://github.com/coreos/etcd) e [fleet](https://github.com/coreos/fleet) para gerenciamento e comunicação dinâmica da máquina virtual e do cluster

Essa é a descrição genérica do CoreOS e de seus recursos. Para obter informações mais detalhadas sobre o CoreOS, confira [Visão geral do CoreOS].

## <a id='security'>Considerações de segurança</a>
No momento, o CoreOS parte do pressuposto de que todos que podem executar SSH no cluster têm permissão para gerenciá-los. Como resultado, sem modificações, os clusters CoreOS são excelentes para ambientes de teste e desenvolvimento, mas é necessário aplicar outras medidas de segurança em qualquer ambiente de produção. 

## <a id='usingcoreos'>Como usar o CoreOS no Azure</a>

Esta seção descreve como criar um serviço de nuvem do Azure com três máquinas virtuais CoreOS por meio da [Interface de plataforma cruzada do Azure (xplat-cli)]. Estas são as etapas básicas

1. Criar certificados SSH e chaves para garantir a segurança da comunicação com a máquina virtual CoreOS
2. Obter a ID etcd do cluster para intercomunicação
3. Criar um arquivo de configuração de nuvem no formato [YAML]
4. Usar a xplat-cli para criar um novo serviço de nuvem do Azure e três máquinas virtuais CoreOS
5. Testar o cluster CoreOS na máquina virtual do Azure
6. Testar o cluster CoreOS no localhost 

### Criar chaves públicas e privadas para comunicação
 
Usar as instruções presentes em [Como usar SSH com Linux no Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/) a fim de criar chaves pública e privada para SSH. (As etapas básicas estão descritas nas instruções abaixo.) Você vai usar essas chaves para conectar-se às máquinas virtuais no cluster e verificar se elas estão funcionando e podem comunicar-se umas com as outras.

> [AZURE.NOTE] Este tópico parte do pressuposto de que você não tem essas chaves e precisa criar arquivos **`myPrivateKey.pem`** e **`myCert.pem`** para garantir a clareza. Se já salvou as chaves pública e privada em **`~/.ssh/id_rsa`**, basta digitar `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem` para obter o arquivo .pem que você precisa carregar no Azure.

1. Em um diretório de trabalho, digite `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem` para criar a chave privada e o certificado X.509 associado a ela. 

2. Para que o proprietário da chave privada possa ler ou editar o arquivo, digite  `chmod 600 myPrivateKey.key`. 

Agora você deve ter um arquivo **`myPrivateKey.key`** e um arquivo **`myCert.pem`** em seu diretório de trabalho. 


### Obter a ID etcd do cluster

O daemon **etcd** do CoreOS requer uma ID de descoberta para consultar todos os nós automaticamente no cluster. Para recuperar sua ID de descoberta e salvá-la em um arquivo **etcdid**, digite

```
curl https://discovery.etcd.io/new | grep ^http.* > etcdid
```

### Create a Create a cloud-config file

No mesmo diretório de trabalho, use o editor de texto de sua preferência para criar um arquivo com o texto exibido a seguir e salve-o como **`cloud-config.yaml`**. (Você pode atribuir o nome que desejar a ele, mas precisará fazer referência ao nome desse arquivo na opção **--custom-data** do comando **azure create vm** ao criar suas máquinas virtuais na próxima etapa.)

> [AZURE.NOTE] Lembre-se de digitar  `cat etcdid` para recuperar a ID de descoberta do etcd do arquivo  `etcdid` criado anteriormente e substituir **`<token>`** no arquivo **cloud-config.yaml** com o número geral por meio de seu arquivo  `etcdid`. Se não puder validar o cluster ao final, é possível que tenha pulado uma das etapas.

```
#cloud-config

coreos:
  etcd:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new
    discovery: https://discovery.etcd.io/<token>
    # deployments across multiple cloud services will need to use $public_ipv4
    addr: $private_ipv4:4001
    peer-addr: $private_ipv4:7001
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
```

(Para obter informações mais detalhadas sobre o arquivo de configuração de nuvem, confira [Usando a configuração de nuvem](https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/) na documentação do CoreOS.)

### Usar a xplat-cli para criar uma nova máquina virtual CoreOS
<!--Cada tópico deve ter as seguintes etapas e links para o próximo conjunto lógico de conteúdo para manter o cliente envolvido-->

1. Instale a [Interface de plataforma cruzada do Azure (xplat-cli)] se ela ainda não estiver instalada. Em seguida, faça logon usando uma ID profissional ou escolar ou baixe um arquivo .publishsettings e importe-o em sua conta.
2. Localize sua imagem CoreOS. Para localizar as imagens disponíveis a qualquer momento, digite `azure vm image list | grep CoreOS` e você deverá ver uma lista de resultados semelhante a:

	data:    2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0              Public    Linux

3. Crie um serviço de nuvem para seu cluster básico digitando
`azure service create <cloud-service-name>`, onde *<cloud-service-name>* é o nome do serviço de nuvem CoreOS. Esse exemplo usa o nome **`coreos-cluster`**. Você precisará reutilizar o nome escolhido na criação das suas instâncias de máquina virtual CoreOS no serviço de nuvem. 

Observação: Se observar o que já fez no [novo portal](https://portal.azure.com), você encontrará um nome de serviço de nuvem que é um grupo de recursos e um domínio, como mostra a imagem abaixo:

![][CloudServiceInNewPortal]  
4. Conecte-se a seu serviço de nuvem e crie uma nova máquina virtual CoreOS usando o comando **azure vm create**. Você indicará o local do certificado X.509 na opção **--ssh-cert**. Digite o código a seguir para criar a primeira imagem da máquina virtual. Lembre-se de substituir **coreos-cluster** pelo nome que você atribuiu ao serviço de nuvem:

```
azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location='West US' 2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0 core
```

5. Crie o segundo nó repetindo o comando da etapa 4, substituindo o valor de **--vm-name** por **node-2** e o valor da porta de **--ssh** por 2022. 
 
6. Crie o terceiro nó repetindo o comando da etapa 4, substituindo o valor de **--vm-name** por **node-3** e o valor da porta de **--ssh** por 3022. 
 
A imagem abaixo mostra como o cluster CoreOS aparece no novo portal.

![][EmptyCoreOSCluster]

### Testar o cluster CoreOS na máquina virtual do Azure

Para testar o cluster, você deve estar no diretório de trabalho e conectar-se a **node-1** usando **ssh**, transmitindo a chave privada com este código:

	ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key

Após a conexão, digite `sudo fleetctl list-machines` para ver se o cluster já identificou todas as máquinas virtuais que constam nele. Você deve receber uma resposta com esta aparência:


	core@node-1 ~ $ sudo fleetctl list-machines
	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-


### Testar o cluster CoreOS no localhost

Por fim, vamos testar o cluster do CoreOS no cliente Linux local instalando **fleet**. **fleet** requer**golang**, assim, pode ser necessário instalar este primeiro digitando:

`sudo apt-get install golang` 

Em seguida, clone o repositório **fleet** do github ao digitar:

`git clone https://github.com/coreos/fleet.git`

Crie o **fleet**digitando

`./build`

Por fim, disponibilize o **fleet** para que seja possível usá-lo com facilidade. Dependendo da sua configuração, você pode ou não precisar executar o **sudo**:

`cp bin/fleetctl /usr/local/bin`

Verifique se o **fleet** tem acesso a `myPrivateKey.key` no diretório de trabalho. Para isso, digite:

`ssh-add ./myPrivateKey.key`

> [AZURE.NOTE] Se você já estiver usando a chave **`~/.ssh/id_rsa`** chave, adicione-a com `ssh-add ~/.ssh/id_rsa`.

Agora você já pode fazer o teste remoto, usando o mesmo comando **fleetctl** usado em **node-1**, mas transmitindo alguns argumentos remotos:

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

Os resultados devem ser exatamente os mesmos:


	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-

## Próximas etapas

Agora você deve ter um cluster CoreOS com três nós no Azure. Deste ponto, você pode explorar como criar clusters mais complexos, usar o Docker e criar aplicativos mais interessantes. Para isso, leia o [Tutorial de Tim Park sobre o CoreOS], o [Tutorial de Patrick Chanezon sobre o CoreOS Tutorial, a documentação do ][Docker] e a [Visão geral do CoreOS].

<!--Anchors-->
[CoreOS, clusters e contêineres Linux]: #intro
[Considerações de segurança]: #security
[Como usar o CoreOS no Azure]: #usingcoreos
[Subtítulo 3]: #subheading-3
[Próximas etapas]: #next-steps

<!--Image references-->
[CloudServiceInNewPortal]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
[EmptyCoreOSCluster]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Interface de plataforma cruzada do Azure (xplat-cli)]: ../xplat-cli/

[CoreOS]: https://coreos.com/
[Visão geral do CoreOS]: https://coreos.com/using-coreos/
[CoreOS com Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tutorial de Tim Park sobre o CoreOS]: https://github.com/timfpark/coreos-azure
[Tutorial de Patrick Chanezon sobre o CoreOS]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/

<!--HONumber=45--> 
