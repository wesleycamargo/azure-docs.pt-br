---
title: Implantar o Avere vFXT para Azure
description: Etapas para implantar o cluster do Avere vFXT no Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: c8bad3642f1e98cac3857d536f539554235e1a51
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578620"
---
# <a name="deploy-the-vfxt-cluster"></a>Implantar cluster vFXT

A maneira mais fácil para criar um cluster vFXT no Azure é usar um controlador de cluster. O controlador de cluster é uma VM que inclui os scripts, modelos e a infraestrutura de software necessários para criar e gerenciar o cluster vFXT.

A implantação de um novo cluster vFXT inclui as seguintes etapas:

1. [Crie o cluster de controlador](#create-the-cluster-controller-vm).
1. Se estiver usando o Armazenamento de Blobs do Azure, [crie um ponto de extremidade de armazenamento](#create-a-storage-endpoint-if-using-azure-blob) em sua rede virtual.
1. [Conecte-se ao controlador de cluster](#access-the-controller). As etapas restantes são realizadas na VM do controlador de cluster. 
1. [Crie a função de acesso](#create-the-cluster-node-access-role) para os nós de cluster. Um protótipo é fornecido.
1. [Personalize o script de criação de cluster](#edit-the-deployment-script) para o tipo de cluster vFXT que você deseja criar.
1. [Execute o script de criação de cluster](#run-the-script).

Para obter mais informações sobre as etapas e o planejamento de implantação de cluster, leia [Plan your Avere vFXT system](avere-vfxt-deploy-plan.md) (Planejar seu sistema vFXT do Avere) e [Visão geral da implantação](avere-vfxt-deploy-overview.md). 

Após seguir as instruções neste documento, você terá uma rede virtual, uma sub-rede, um controlador e um cluster vFXT, conforme mostrado no diagrama a seguir:

![diagrama mostrando a vnet que contém o Armazenamento de Blobs opcional e uma sub-rede contendo três nós vFXT/cluster vFXT rotulados por VMs agrupadas e um controlador de cluster rotulado por VM](media/avere-vfxt-deployment.png)

Antes de começar, certifique-se de ter resolvido estes pré-requisitos:  

1. [Nova assinatura](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Permissões do proprietário da assinatura](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

Opcionalmente, é possível criar a função do nó de cluster [antes](avere-vfxt-pre-role.md) de criar o controlador de cluster, mas é mais simples fazer isso depois.

## <a name="create-the-cluster-controller-vm"></a>Crie a VM do cluster de controlador

A primeira etapa é criar a máquina virtual que criará e configurará os nós do cluster vFXT. 

O controlador de cluster é uma VM Linux com o software de criação do cluster do Avere vFXT e os scripts pré-instalados. Ele não precisa de potência de processamento significativa nem de espaço de armazenamento, então você pode escolher opções de baixo custo. Essa VM será usada ocasionalmente durante o tempo de vida do cluster vFXT.

Há dois métodos para criar a VM do controlador de cluster. Um [modelo do Azure Resource Manager](#create-controller---arm-template) é fornecido [abaixo](#create-controller---arm-template) para simplificar o processo, mas também é possível criar o controlador com base na [imagem do Azure Marketplace](#create-controller---azure-marketplace-image). 

É possível criar um novo grupo de recursos como parte da criação do controlador.

> [!TIP]
>
> Decida se você deseja ou não usar um endereço IP público no controlador de cluster. Um endereço IP público fornece acesso mais fácil ao cluster vFXT, mas cria um pequeno risco de segurança.
>
>  * Um endereço IP público no controlador de cluster permite que você o use como um host de atalho para se conectar ao cluster do Avere vFXT de fora da sub-rede privada.
>  * Se você não configurar um endereço IP público no controlador, deverá usar outro host de atalho, uma conexão VPN ou o ExpressRoute para acessar o cluster. Por exemplo, crie o controlador dentro da rede virtual que tem uma conexão VPN configurada.
>  * Se você criar um controlador com um endereço IP público, você deverá proteger a VM do controlador com um grupo de segurança de rede. Permita o acesso somente pela porta 22 para fornecer acesso à Internet.

### <a name="create-controller---resource-manager-template"></a>Criar controlador – modelo do Resource Manager

Para criar o nó do controlador de cluster no portal, clique no botão "Implantar no Azure" abaixo. Esse modelo de implantação cria a VM que criará e gerenciará o cluster do Avere vFXT.

[![botão para criar o controlador](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fazuredeploy.json)

Forneça as seguintes informações.

Na seção **NOÇÕES BÁSICAS**:  

* **Assinatura** para o cluster
* **Grupo de recursos** do cluster 
* **Localidade** 

Na seção **CONFIGURAÇÕES**:

* Criar ou não uma rede virtual

  * Se você criar uma vnet, o controlador de cluster receberá um endereço IP público para você poder acessá-la. Um grupo de segurança de rede também é criado para essa vnet que restringe o tráfego de entrada somente à porta 22.
  * Se desejar usar o ExpressRoute ou uma VPN para se conectar ao controlador de cluster, defina esse valor como `false` e especifique uma vnet existente nos campos restantes. O controlador de cluster usará essa vnet para a comunicação de rede. 

* Grupo de recursos de rede virtual, nome e nome da sub-rede – digite os nomes dos recursos existentes (se estiver usando uma vnet existente) ou digite novos nomes se estiver criando uma rede virtual
* **Nome do controlador** – defina um nome para a VM do controlador
* Nome de usuário do administrador do controlador – o padrão é `azureuser`
* Chave SSH – cole a chave pública a ser associada ao nome de usuário do administrador. Leia [Como criar e usar chaves SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) se precisar de Ajuda.

Em **TERMOS E CONDIÇÕES**: 

* Leia os termos de serviço e clique na caixa de seleção para aceitá-los. 

  > [!NOTE] 
  > Se você não for um proprietário de assinatura, faça um proprietário aceitar os termos para você seguindo as etapas de pré-requisitos em [Aceitar termos de software com antecedência](avere-vfxt-prereqs.md#accept-software-terms-in-advance). 


Clique em **Comprar** quando terminar. Depois de cinco ou seis minutos, o nó do controlador estará em funcionamento.

Visite a página de saídas para reunir as informações do controlador que você precisa para criar o cluster. Leia [informações necessárias para criar o cluster](#information-needed-to-create-the-cluster) para saber mais.

### <a name="create-controller---azure-marketplace-image"></a>Criar controlador – Imagem do Azure Marketplace

Localizar o modelo do controlador pesquisando o nome ``Avere`` no Azure Marketplace. Selecione o modelo **Avere vFXT para o controlador do Azure**.

Se você ainda não tiver feito isso, aceite os termos e habilite o acesso programático para a imagem do Marketplace clicando no link "Deseja implantar programaticamente?" embaixo do botão **Criar**.

![Captura de tela de um link para acesso programático, que está embaixo do botão Criar](media/avere-vfxt-deploy-programmatically.png)

Clique no botão **Habilitar** e salve a configuração.

![Captura de tela mostrando o clique do mouse para habilitar o acesso programático](media/avere-vfxt-enable-program.png)

Retorne à página principal do modelo **Avere vFXT para o Controlador do Azure** e clique em **Criar**. 

No primeiro painel, preencha ou confirme estas opções básicas:

* **Assinatura**
* **Grupo de recursos** (Insira um novo nome se desejar criar um grupo).
* **Nome da máquina virtual** – o nome do controlador
* **Região**
* **Opções de disponibilidade** – a redundância não é necessária
* **Imagem** – a imagem do nó do controlador do Avere vFXT
* **Tamanho** – deixe o padrão ou escolha outro tipo de baixo custo
* **Conta de administrador** – defina como entrar no controlador de cluster: 
  * Selecione o nome de usuário/senha ou a chave pública do SSH (recomendado).
  
    > [!TIP] 
    > Uma chave SSH é mais segura. Leia [Como criar e usar chaves SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) se precisar de Ajuda. 
  * Especificar o nome de usuário 
  * Colar a chave SSH ou inserir e confirme a senha
* **Regras de porta de entrada** – se estiver usando um endereço IP público, abra a porta 22 (SSH)

Clique em **Avançar** para definir opções de disco:

* **Tipo de disco do sistema operacional** – o valor padrão de HD é suficiente
* **Usar discos não gerenciados** – não necessário
* **Discos de dados** – não use

Clique em **Avançar** para as opções de rede:

* **Rede virtual** – selecione a vnet para o controlador ou insira um nome para criar uma nova rede vnet. Se não desejar usar um endereço IP público no controlador, considere localizá-lo dentro de uma vnet que tem o ExpressRoute ou outro método de acesso já configurado.
* **Sub-rede** – Selecione uma sub-rede dentro da vnet (opcional). Se desejar criar uma vnet, é possível criar uma nova sub-rede ao mesmo tempo.
* **IP público** – se desejar usar um endereço IP público, será possível especificá-lo aqui. 
* **Grupo de segurança de rede** – mantenha a configuração padrão (**Básica**) 
* **Portas de entrada públicas** – se estiver usando um endereço IP público, use este controle para permitir o acesso do tráfego SSH. 
* A **rede acelerada** não está disponível para essa VM.

Clique em **Avançar** para definir opções de gerenciamento:

* **Diagnóstico de inicialização** – altere para **Desativado**
* **Diagnóstico do sistema operacional convidado** – deixe desabilitado
* **Conta de armazenamento de diagnóstico** – opcionalmente, selecione ou especifique uma nova conta para manter informações de diagnóstico.
* **Identidade de serviço gerenciado** – altere essa opção para **Ativa**, que cria uma entidade de serviço do Azure AD para o controlador de cluster.
* **Desligamento automático** – mantenha desativado 

Nesse momento, será possível clicar em **Revisar + criar** se não desejar usar marcas de instância. Caso contrário, clique em **Avançar** duas vezes para ignorar a página **Configuração do convidado** e acessar a página de marcas. Quando terminar, clique em **Revisar + criar**. 

Depois que suas seleções forem validadas, clique no botão **Criar**.  

A criação leva cinco ou seis minutos.

## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Criar um ponto de extremidade de armazenamento (se estiver usando o Blob do Azure)

Se você estiver usando o Armazenamento de Blobs do Azure para seu armazenamento de dados de back-end, deverá criar um ponto de extremidade de serviço de armazenamento em sua rede virtual. Esse [ponto de extremidade de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) mantém o local do tráfego do Blob do Azure, em vez de roteá-lo por meio da Internet.

1. No portal, clique em **Redes virtuais** à esquerda.
1. Selecione a rede virtual do seu controlador. 
1. Clique em **Pontos de extremidade de serviço** à esquerda.
1. Clique em **Adicionar** na parte superior.
1. Mantenha o serviço como ``Microsoft.Storage`` e escolha a sub-rede do controlador.
1. Na parte inferior, clique em **Adicionar**.

  ![Captura de tela do portal do Azure com anotações para as etapas de criação de ponto de extremidade de serviço](media/avere-vfxt-service-endpoint.png)

## <a name="information-needed-to-create-the-cluster"></a>Informações necessárias para criar o cluster

Depois de criar o controlador de cluster, verifique se que você tem as informações que necessárias para as próximas etapas. 

Informações necessárias para se conectar ao controlador: 

* Nome de usuário do controlador e chave SSH (ou senha)
* Endereço IP do controlador ou outro método para se conectar à VM do controlador

Informações necessárias para o cluster: 

* Nome do grupo de recursos
* Localização do Azure 
* Nome da rede virtual
* Nome da sub-rede
* Nome de função do nó de cluster - esse nome é definido quando você cria a função, descrita [abaixo](#create-the-cluster-node-access-role)
* Nome da conta de armazenamento, se estiver criando um contêiner de blob

Se você criou o nó do controlador usando o modelo do Resource Manager, poderá obter as informações da [saída do modelo](#find-template-output). 

Se você usou a imagem do Azure Marketplace para criar o controlador, forneceu a maioria desses itens diretamente. 

Encontre qualquer item ausente, navegando até a página de informações da VM do controlador. Por exemplo, clique em **Todos os recursos** e procure o nome do controlador e, em seguida, clique no nome do controlador para ver seus detalhes.

### <a name="find-template-output"></a>Encontrar a saída do modelo

Para localizar essas informações na saída do modelo do Resource Manager, siga este procedimento:

1. Vá para o grupo de recursos do seu controlador de cluster.

1. No lado esquerdo, clique em **Implantações** e, em seguida, em **Microsoft.Template**.

   ![Página do portal do grupo de recursos com implantações selecionadas à esquerda e Microsoft.Template sendo exibido em uma tabela embaixo do nome da implantação](media/avere-vfxt-deployment-template.png)

1. No lado esquerdo, clique em **Saídas**. Copie os valores em cada um dos campos. 

   ![página de saídas com SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK, SUBNET e SUBNET_ID mostrado nos campos à direita dos rótulos](media/avere-vfxt-template-outputs.png)

## <a name="access-the-controller"></a>Acessar o controlador

Para fazer o restante das etapas de implantação, é necessário se conectar ao controlador de cluster.

1. O método para se conectar ao seu controlador de cluster depende de sua configuração.

   * Se o controlador tiver um endereço IP público, faça SSH no IP do controlador como o nome de usuário de administrador que você definiu (por exemplo, ``ssh azureuser@40.117.136.91``).
   * Se o controlador não tiver um IP público, use uma VPN ou uma conexão [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) para sua vnet.

1. Depois de fazer logon em seu controlador, faça a autenticação executando `az login`. Copie o código de autenticação fornecido no shell e, em seguida, usar um navegador da Web para carregar [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) e autenticar-se com o sistema Microsoft. Retorne ao shell para a confirmação.

   ![Saída da linha de comando do comando 'AZ login' exibindo o link do navegador e o código de autenticação](media/avere-vfxt-azlogin.png)

1. Adicione sua assinatura executando esse comando usando sua ID da assinatura: ```az account set --subscription YOUR_SUBSCRIPTION_ID```

## <a name="create-the-cluster-node-access-role"></a>Criar a função de acesso do nó de cluster

> [!NOTE] 
> * Se você não for um proprietário de assinatura e a função ainda não tiver sido criada, faça um proprietário de assinatura seguir estas etapas ou use o procedimento em [Criar a função de acesso de tempo de execução do cluster do Avere vFXT sem um controlador](avere-vfxt-pre-role.md).
> 
> * Os usuários internos da Microsoft devem usar a função existente chamada "Avere Cluster Runtime Operator" em vez de tentar criar uma. 

O [RBAC](https://docs.microsoft.com/azure/role-based-access-control/) (Controle de acesso baseado em função) fornece autorização para que os nós do cluster vFXT executem as tarefas necessárias.  

Como parte da operação normal do cluster vFXT, os nós do vFXT individuais precisam fazer coisas como ler propriedades de recurso do Azure, gerenciar o armazenamento e controlar as configurações do adaptador de rede de outros nós. 

1. No controlador, abra o arquivo ``/avere-cluster.json`` em um editor.

   ![console mostrando um comando de lista e, em seguida, "vi /avere-cluster.json"](media/avere-vfxt-open-role.png)

1. Edite o arquivo para incluir sua ID da assinatura e exclua a linha em cima dela. Salve o arquivo como ``avere-cluster.json``.

   ![Editor de texto do console mostrando a ID da assinatura e "remover esta linha" selecionado para exclusão](media/avere-vfxt-edit-role.png)

1. Use este comando para criar a função:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

Você passará o nome da função para o script de criação de cluster na próxima etapa. 

## <a name="create-nodes-and-configure-the-cluster"></a>Criar nós e configurar o cluster

Para criar o cluster do Avere vFXT, edite um dos scripts de exemplo incluídos no controlador e execute-o lá. Os scripts de exemplo estão localizados no diretório raiz (`/`) no controlador de cluster.

* Se desejar criar um contêiner de blobs para usar como o sistema de armazenamento de back-end do Avere vFXT, use o script ``create-cloudbacked-cluster``.

* Se você adicionar o armazenamento posteriormente, use o script ``create-minimal-cluster``.

> [!TIP]
> Scripts de protótipo para adicionar nós e a destruição do cluster vFXT também estão incluídos no diretório `/` da VM de controlador de cluster.

### <a name="edit-the-deployment-script"></a>Editar o script de implantação

Abra o script de exemplo em um editor. Convém salvar o script personalizado com um nome diferente para evitar substituir o exemplo original.

Insira valores para essas variáveis de script.

* Nome do grupo de recursos

  * Se você usar os componentes de armazenamento ou de rede que estão em grupos de recursos diferentes, remova a marca de comentário das variáveis e forneça esses nomes também. 

```python
# Resource groups
# At a minimum specify the resource group.  If the network resources live in a
# different group, specify the network resource group.  Likewise for the storage
# account resource group.
RESOURCE_GROUP=
#NETWORK_RESOURCE_GROUP=
#STORAGE_RESOURCE_GROUP=
```

* Nome da localização
* Nome da rede virtual
* Nome da sub-rede
* Nome de função do tempo de execução do Azure AD – se você tiver seguido o exemplo em [Criar a função de acesso do nó de cluster](#create-the-cluster-node-access-role), use ``avere-cluster``. 
* Nome da conta de armazenamento (se estiver criando um novo contêiner de blobs)
* Nome do cluster – não é possível ter dois clusters vFXT com o mesmo nome no mesmo grupo de recursos. 
* Senha administrativa – escolha uma senha segura para monitorar e administrar o cluster. Essa senha é atribuída ao usuário ``admin``. 
* Tipo de instância de nó – consulte os [tamanhos de nó do vFXT](avere-vfxt-deploy-plan.md#vfxt-node-sizes) para obter informações
* Tipo de cache de nó – consulte os [tamanhos de nó do vFXT](avere-vfxt-deploy-plan.md#vfxt-node-sizes) para obter informações

Salve o arquivo e saia.

### <a name="run-the-script"></a>Execute o script

Execute o script digitando o nome do arquivo criado. (Exemplo: `./create-cloudbacked-cluster-west1`)  

> [!TIP]
> Considere executar este comando dentro de um [multiplexador de terminal](http://linuxcommand.org/lc3_adv_termmux.php) como `screen` ou `tmux` caso você perca sua conexão.  

A saída também é registrada em `~/vfxt.log`.

Quando o script for concluído, copie o endereço IP de gerenciamento necessário para a administração do cluster.

![Saída de linha de comando do script exibindo o endereço IP de gerenciamento perto da extremidade](media/avere-vfxt-mgmt-ip.png)

## <a name="next-step"></a>Próxima etapa

Agora que o cluster está em execução e você conhece seu endereço IP de gerenciamento, é possível [conectar-se à ferramenta de configuração de cluster](avere-vfxt-cluster-gui.md) para habilitar o suporte e adicionar o armazenamento, se necessário.
