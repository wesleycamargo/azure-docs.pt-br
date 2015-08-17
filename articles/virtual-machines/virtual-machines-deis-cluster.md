<properties
   pageTitle="Implantar um cluster Deis de três nós no Azure"
   description="Este artigo descreve como criar um cluster Deis de três nós no Azure usando um modelo do Gerenciador de Recursos do Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="HaishiBai"
   manager="larar"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# Implantar um cluster Deis de três nós

Este artigo percorre o provisionamento de um cluster [Deis](http://deis.io/) no Azure. Ele abrange todas as etapas, desde criar os certificados necessários até implantar e dimensionar uma aplicativo **Go** de exemplo no cluster provisionado.

O diagrama a seguir mostra a arquitetura do sistema implantado. Um administrador de sistema gerencia o cluster usando ferramentas do Deis como **deis** e **deisctl**. São estabelecidas conexões por meio de um balanceador de carga do Azure, que encaminha as conexões a um dos nós membro no cluster. Os clientes também acessam aplicativos implantados por meio do balanceador de carga. Nesse caso, o balanceador de carga encaminha o tráfego para um uma malha de roteadores Deis, que encaminha o tráfego a contêineres Docker correspondentes hospedados no cluster.

  ![Diagrama da arquitetura do cluster Deis implantado](media/virtual-machines-deis-cluster/architecture-overview.png)

Para executar as etapas a seguir, você precisará de:

 * Uma assinatura ativa do Azure. Se não tiver uma, você pode obter uma versão de avaliação gratuita em [azure.com](https://azure.microsoft.com).
 * Uma ID de trabalho ou escolar para usar os grupos de recursos do Azure. Se tiver uma conta pessoal e fizer logon com uma ID da Microsoft, você precisará [criar uma ID de trabalho por meio da ID pessoal](resource-group-create-work-id-from-personal.md).
 * O [PowerShell do Azure](powershell-install-configure.md) ou a [CLI do Azure para Mac, Linux e Windows](xplat-cli-install.md), dependendo do sistema operacional cliente.
 * [OpenSSL](https://www.openssl.org/). O OpenSSL é usado para gerar os certificados necessários.
 * Um cliente Git como o [Git Bash](https://git-scm.com/).
 * Para testar o aplicativo de exemplo,você precisará também de um servidor DNS. Você pode usar quaisquer servidores DNS ou serviços que dão suporte a registros de curinga A.
 * Um computador para executar ferramentas de cliente Deis. Você pode usar um computador local ou uma máquina virtual. Você pode executar essas ferramentas em praticamente qualquer distribuição do Linux, mas as instruções a seguir usam o Ubuntu.

## Provisionar o cluster

Nesta seção, você usará um modelo do [Gerenciador de Recursos do Azure](../resource-group-overview.md) do repositório de software livre [azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates). Primeiro, você copiará o modelo. Em seguida, criará um novo par de chaves SSH para autenticação. Depois, você configurará um novo identificador para o cluster. Por fim, você usará o script de Shell ou o script de PowerShell para provisionar o cluster.

1. Clone o repositório [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).

        git clone https://github.com/Azure/azure-quickstart-templates

2. Vá para a pasta do modelo:

        cd azure-quickstart-templates\deis-cluster-coreos

3. Crie um novo par de chaves SSH usando ssh-keygen:

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. Gere um certificado usando a chave privada acima:

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. Vá para [https://discovery.etcd.io/new](https://discovery.etcd.io/new) para gerar um novo token de cluster, que é semelhante a:

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br /> Cada cluster CoreOS precisa ter um token exclusivo desse serviço gratuito. Consulte a [documentação do CoreOS](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) para obter mais detalhes.

6. Modifique o arquivo **cloud-config.yaml** para substituir o token **discovery** existente pelo novo token:

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. Modifique o arquivo **azuredeploy-parameters.json**: Abra o certificado criado na etapa 4 em um editor de texto. Copie todo o texto entre `----BEGIN CERTIFICATE-----` e `-----END CERTIFICATE-----` para o parâmetro **sshKeyData** (você precisará remover todos os caracteres de nova linha).

8. Modifique o parâmetro **newStorageAccountName**. Trata-se da conta de armazenamento para os discos do sistema operacional da VM. Este nome de conta precisa ser único globalmente.

9. Modifique o parâmetro **publicDomainName**. Ele se tornará parte do nome DNS associado ao IP público do balanceador de carga. O FQDN final terá o formato _[valor desse parâmetro]_._[região]_.cloudapp.azure.com. Por exemplo, se você especificar o nome como deishbai32 e o grupo de recursos for implantado na região oeste dos EUA, o FQDN final do balanceador de carga será deishbai32.westus.cloudapp.azure.com.

10. Salve o arquivo de parâmetro. Depois, você pode provisionar o cluster usando o PowerShell do Azure:

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  ou o CLI do Azure:

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. Após o grupo de recursos ser provisionado, você pode ver todos os recursos do grupo no Portal do Azure. Conforme mostrado na captura de tela a seguir, o grupo de recursos contém uma rede virtual com três VMs, que fazem parte do mesmo conjunto de disponibilidade. O grupo também contém um balanceador de carga, que tem um IP público associado.

  ![O grupo de recursos provisionado no portal do Azure](media/virtual-machines-deis-cluster/resource-group.png)

## Instalar o cliente

Você precisa do **deisctl** para controlar seu cluster Deis. Embora o deisctl seja instalado automaticamente em todos os nós do cluster, é uma boa prática usá-lo em uma máquina administrativa separada. Além disso, como todos os nós são configurados somente com endereços IP particulares, você precisará usar um túnel SSH no balanceador de carga, que tem um IP público, para se conectar às máquinas do nó. A seguir, estão as etapas de configuração do deisctl em uma máquina física ou virtual separada do Ubuntu.

1. Instale o deis deisctl:mkdir

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. Adicione sua chave privada ao agente SSH:

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. Configure o deisctl:

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

O modelo define regras NAT de entrada que mapeiam 2223 à instância 1, 2224 à instância 2 e 2225 à instância 3. Isso fornece redundância ao uso da ferramenta deisctl. Você pode examinar essas regras no Portal do Azure:

![Regras NAT no balanceador de carga](media/virtual-machines-deis-cluster/nat-rules.png)

> [AZURE.NOTE]Atualmente, o modelo dá suporte apenas a clusters de três nós. Isso se deve a uma limitação na definição da regra NAT do modelo do Gerenciador de Recursos do Azure, que não dá suporte à sintaxe de loop.

## Instale e inicie a plataforma Deis

Agora você pode usar o deisctl para instalar e iniciar a plataforma Deis:

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE]Iniciar a plataforma leva algum tempo (até 10 minutos). Especialmente, iniciar o serviço de construtor pode levar muito tempo. Às vezes, são necessárias algumas tentativas para ter sucesso: se parecer que a operação parou, tente digitar `ctrl+c` para interromper a execução do comando e tentar novamente.

Você pode usar `deisctl list` para verificar se todos os serviços estão em execução:

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

Parabéns! Agora, você tem um cluster Deis em execução no Azure! Em seguida, vamos implantar um aplicativo Go de exemplo para ver o cluster em ação.

## Implantar e dimensionar um aplicativo Hello World

As etapas a seguir mostram como implantar um aplicativo Go do tipo "Hello World" no cluster. As etapas são baseadas na [documentação do Deis](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Para que a malha de roteamento funcione corretamente, você precisará ter um registro A curinga para seu domínio apontando para o IP público do balanceador de carga. A captura de tela a seguir mostra o registro A para um registro de domínio de exemplo no GoDaddy:

    ![Registro A do GoDaddy](media/virtual-machines-deis-cluster/go-daddy.png)
<p />
2. Instalar e o deis:

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. Crie uma nova chave SSH e adicione a chave pública ao GitHub (é claro, você também pode reutilizar suas chaves existentes). Para criar um novo par de chaves SSH, use:

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. Adicione id\_rsa.pub ou a chave pública de sua escolha ao GitHub. Você pode fazer isso usando o botão Adicionar SSH em sua tela de configuração de chaves SSH:

  ![Chave do GitHub](media/virtual-machines-deis-cluster/github-key.png) <p /> 5. Registrar um novo usuário:

        deis register http://deis.[your domain]
<p />
6. Adicione a chave SSH:

        deis keys:add [path to your SSH public key]
  <p />
7. Crie um aplicativo.

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8. O git push disparará a construção e implantação de imagens do Docker, que o levará alguns minutos. Segundo a minha experiência, ocasionalmente, a Etapa 10 (fazer o push da imagem para o repositório privado) pode travar. Quando isso acontecer, você pode interromper o processo, remover o aplicativo usando `deis apps:destroy –a <application name>` e tentar novamente. Você pode usar `deis apps:list` para descobrir o nome do seu aplicativo. Se tudo funcionar, você verá algo semelhante ao seguinte no final das saídas de comando:

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. Verifique se o aplicativo está funcionando:

        curl -S http://[your application name].[your domain]
  Você deverá ver:

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. Dimensione o aplicativo para três instâncias:

        deis scale cmd=3
<p />
11. Você ainda pode usar informações do deis para examinar os detalhes do seu aplicativo. As saídas a seguir são da implantação do meu aplicativo:

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## Próximas etapas

Este artigo percorreu todas as etapas para provisionar um novo cluster Deis no Azure usando um modelo do Gerenciador de Recursos do Azure. O modelo dá suporte a redundância em conexões de ferramentas, bem como balanceamento de carga para aplicativos implantados. O modelo também evita o uso de IPs públicos em nós membro, o que economiza preciosos recursos de IP públicos e fornece um ambiente mais seguro para hospedar aplicativos. Para saber mais, consulte os seguintes artigos:

[Visão geral do Gerenciador de Recursos do Azure][resource-group-overview] [Como usar a CLI do Azure][azure-command-line-tools] [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure][powershell-azure-resource-manager]

[azure-command-line-tools]: ../xplat-cli.md
[resource-group-overview]: ../resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md

<!---HONumber=August15_HO6-->