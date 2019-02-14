---
title: Implantar o Avere vFXT para Azure
description: Etapas para implantar o cluster do Avere vFXT no Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 972ba937ad15fa9a6d2eb74e3e4c9e6e8f3923a4
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745428"
---
# <a name="deploy-the-vfxt-cluster"></a>Implantar cluster vFXT

Este procedimento explica como usar o assistente de implantação disponível no Azure Marketplace. O assistente implanta automaticamente o cluster usando um modelo do Azure Resource Manager. Depois que você insere os parâmetros no formulário e clica em **Criar**, o Azure automaticamente conclui estas etapas: 

* Criar o controlador de cluster, que é uma VM básica que contém o software necessário para implantar e gerenciar o cluster.
* Configurar o grupo de recursos e a infraestrutura de rede virtual, incluindo a criação de novos elementos se necessário.
* Criar VMs do nó de cluster e configurá-los como cluster do Avere.
* Se solicitado, crie um novo contêiner de Blobs do Azure e configure-o como um servidor de dados de núcleo de cluster.

Após seguir as instruções neste documento, você terá uma rede virtual, uma sub-rede, um controlador e um cluster vFXT, conforme mostrado no diagrama a seguir:

![diagrama mostrando a vnet que contém o Armazenamento de Blobs opcional e uma sub-rede contendo três nós vFXT/cluster vFXT rotulados por VMs agrupadas e um controlador de cluster rotulado por VM](media/avere-vfxt-deployment.png)

Depois de criar o cluster, você deverá [criar um ponto de extremidade de armazenamento](#create-a-storage-endpoint-if-using-azure-blob) em sua rede virtual se usar o Armazenamento de Blobs. 

Antes de usar o modelo de criação, verifique se você resolveu estes pré-requisitos:  

1. [Nova assinatura](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Permissões do proprietário da assinatura](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Funções de acesso personalizado](avere-vfxt-prereqs.md#create-access-roles) – você deve criar uma função de controle de acesso baseado em função para atribuir a nós do cluster. Você tem a opção de criar também uma função de acesso personalizado para o controlador de cluster, mas a maioria dos usuários terá a função de proprietário padrão, que fornece os privilégios de controlador correspondentes a um proprietário do grupo de recursos. Leia [Funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md#owner) para obter mais detalhes.

Para obter mais informações sobre as etapas e o planejamento de implantação de cluster, leia [Plan your Avere vFXT system](avere-vfxt-deploy-plan.md) (Planejar seu sistema vFXT do Avere) e [Visão geral da implantação](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Criar o Avere vFXT para Azure

Para acessar o modelo de criação no portal do Azure, procure Avere e escolha "Implantação do ARM Avere vFXT". 

![Janela do navegador mostrando o portal do Azure com a navegação estrutural "Novo > Marketplace > Tudo". Na página Tudo, o campo de pesquisa tem o termo "avere" e o segundo resultado, "Implantação do ARM Avere vFXT", está contornada em vermelho para realçá-lo.](media/avere-vfxt-template-choose.png)

Depois de ler os detalhes na página Implantação do ARM Avere vFXT, clique em **Criar** para começar. 

![O Azure marketplace com a primeira página do modelo de implantação exibida](media/avere-vfxt-deploy-first.png)

O modelo é dividido em quatro etapas – duas páginas de coleta de informações mais validação e confirmação das etapas. 

* A página um concentra-se nas configurações para a VM do controlador de cluster. 
* A página dois coleta parâmetros para criar o cluster e os recursos associados, como sub-redes e armazenamento. 
* A página três resume as configurações e valida a configuração. 
* A página quatro explica os termos e condições de software e permite que você inicie o processo de criação do cluster. 

## <a name="page-one-parameters---cluster-controller-information"></a>Parâmetros da página um – informações do controlador de cluster

A primeira página do modelo de implantação coleta informações sobre o controlador de cluster. 

![Primeira página do modelo de implantação](media/avere-vfxt-deploy-1.png)

Preencha as seguintes informações:

* **Nome do controlador de cluster** – defina o nome para a VM do controlador de cluster.

* **Nome de usuário do controlador** – preencha o nome de usuário raiz para a VM do controlador de cluster. 

* **Tipo de autenticação** – escolha autenticação por senha ou chave pública SSH para se conectar ao controlador. O método de chave pública SSH é recomendado. Leia [Como criar e usar chaves SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) se precisar de ajuda.

* **Senha** ou **Chave pública SSH** – dependendo do tipo de autenticação selecionado, você deve fornecer uma chave pública RSA ou uma senha nos próximos campos. Essa credencial é usada com o nome de usuário fornecido anteriormente.

* **ID da função de criação de cluster do Avere** – use esse campo para especificar a função de controle de acesso para o controlador de cluster. O valor padrão é a função interna [Proprietário](../role-based-access-control/built-in-roles.md#owner). Privilégios de proprietário para o controlador de cluster são restritos ao grupo de recursos do cluster. 

  Você deve usar o identificador global exclusivo que corresponde à função. Para o valor padrão (Proprietário), o GUID é 8e3af657-a8ff-443c-a75c-2fe8c4bcb635. Para localizar o GUID para uma função personalizada, use este comando: 

  ```azurecli
  az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
  ```

* **Assinatura** – selecione a assinatura para o Avere vFXT. 

* **Grupo de recursos** - escolha um grupo de recursos vazio já criado para o cluster do Avere vFXT ou clique em "Criar novo" e insira um novo nome de grupo de recursos. 

* **Localização** – selecione a localização do Azure para seu cluster e recursos.

Clique em **OK** quando terminar. 

> [!NOTE]
> Se você quiser que o controlador de cluster tenha um endereço IP público, crie uma nova rede virtual para o cluster, em vez de selecionar uma rede existente. Essa configuração está na página dois.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Parâmetros da página dois – informações do cluster do vFXT

A segunda página do modelo de implantação permite que você defina o tamanho do cluster, o tipo de nó, o tamanho do cache e os parâmetros de armazenamento, entre outras configurações. 

![Segunda página do modelo de implantação](media/avere-vfxt-deploy-2.png)

* **Contagem de nó de cluster do Avere vFXT** – escolha o número de nós a serem usados no cluster. O mínimo é de três nós e o máximo é de doze. 

* **Senha de administração de cluster** – crie a senha de administração de cluster. Essa senha será usada com o nome de usuário ```admin``` para entrar no painel de controle do cluster para monitorar o cluster e definir as configurações.

* **Função de operações de cluster do Avere** – especifique o nome da função de controle de acesso para os nós de cluster. Essa é uma função personalizada criada como uma etapa de pré-requisito. 

  O exemplo descrito em [Criar a função de acesso do nó de cluster](avere-vfxt-prereqs.md#create-the-cluster-node-access-role) salva o arquivo como ```avere-operator.json``` e o nome da função correspondente é ```avere-operator```.

* **Nome do cluster do Avere vFXT** – dê ao cluster um nome exclusivo. 

* **Tamanho** – especifique o tipo VM a ser usado ao criar os nós do cluster. 

* **Tamanho do cache por nó** – o cache do cluster é disseminado entre os nós de cluster, portanto, o tamanho do cache total no cluster do Avere vFXT será o tamanho do cache por nó multiplicado pelo número de nós. 

  A configuração recomendada é usar 1 TB por nó se você estiver usando nós de cluster Standard_D16s_v3 e usar 4 TB por nó se você estiver usando nós Standard_E32s_v3.

* **Rede virtual** – selecione uma VNET existente para hospedar o cluster ou defina uma nova VNET para criar. 

  > [!NOTE]
  > Se você criar uma nova VNET, o controlador de cluster terá um endereço IP público para que você possa acessar a nova rede privada. Se você escolher uma rede virtual existente, o controlador de cluster será configurado sem um endereço IP público. 
  > 
  > Um endereço IP publicamente visível no controlador de cluster fornece acesso mais fácil para o cluster vFXT, mas gera um pequeno risco de segurança. 
  >  * Um endereço IP público no controlador de cluster permite que você o use como um host de atalho para se conectar ao cluster do Avere vFXT de fora da sub-rede privada.
  >  * Se você não configurar um endereço IP público no controlador, deverá usar outro host de atalho, uma conexão VPN ou o ExpressRoute para acessar o cluster. Por exemplo, crie o controlador dentro da rede virtual que já tem uma conexão VPN configurada.
  >  * Se você criar um controlador com um endereço IP público, você deverá proteger a VM do controlador com um grupo de segurança de rede. Por padrão, a implantação do Avere vFXT para Azure cria um grupo de segurança de rede e restringe o acesso de entrada somente à porta 22 para os controladores com endereços IP públicos. Você pode proteger ainda mais o sistema bloqueando o acesso a seu intervalo de endereços IP de origem, ou seja, permitir somente conexões de computadores que você pretende usar para acesso ao cluster.

* **Sub-rede** – escolha uma sub-rede de sua rede virtual existente ou crie uma nova. 

* **Usar o Armazenamento de Blobs** - escolha **true** para criar um novo contêiner de Blobs do Azure e configurá-lo como um armazenamento de back-end para o novo cluster do Avere vFXT. Essa opção também cria uma nova conta de armazenamento dentro do mesmo grupo de recursos do cluster. 

  Defina este campo como **false** se não quiser criar um novo contêiner. Nesse caso, você deve anexar e configurar o armazenamento depois de criar o cluster. Leia [Configurar armazenamento](avere-vfxt-add-storage.md) para obter instruções. 

* **Conta de armazenamento** - se você estiver criando um novo contêiner de Blobs do Azure, insira um nome para a nova conta de armazenamento. 

## <a name="validation-and-purchase"></a>Validação e compra

A página três apresenta um resumo da configuração e valida os parâmetros. Após a validação ser bem-sucedida, clique no botão **OK** para continuar. 

![Terceira página do modelo de implantação – validação](media/avere-vfxt-deploy-3.png)

Na quarta página, clique no botão **Criar** para aceitar os termos e criar o cluster do Avere vFXT para Azure. 

![Quarta página do modelo de implantação – termos e condições, botão criar](media/avere-vfxt-deploy-4.png)

A implantação do cluster leva de 15 a 20 minutos.

## <a name="gather-template-output"></a>Coletar a saída de modelo

Quando o modelo do Avere vFXT termina de criar o cluster, ele produz algumas informações importantes sobre o novo cluster. 

> [!TIP]
> Copie o endereço IP de gerenciamento da saída do modelo. Você precisa desse endereço para administrar o cluster.

Para localizar essas informações, siga este procedimento:

1. Vá para o grupo de recursos do seu controlador de cluster.

1. No lado esquerdo, clique em **Implantações** e, em seguida, em **microsoft-avere.vfxt-template**.

   ![Página do portal do grupo de recursos com implantações selecionadas à esquerda e microsoft-avere.vfxt-template sendo exibido em uma tabela embaixo do nome da implantação](media/avere-vfxt-outputs-deployments.png)

1. No lado esquerdo, clique em **Saídas**. Copie os valores em cada um dos campos. 

   ![produz a página mostrando os valores SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs e MGMT_IP em campos à direita dos rótulos](media/avere-vfxt-outputs-values.png)


## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Criar um ponto de extremidade de armazenamento (se estiver usando o Blob do Azure)

Se você estiver usando o Armazenamento de Blobs do Azure para seu armazenamento de dados de back-end, deverá criar um ponto de extremidade de serviço de armazenamento em sua rede virtual. Esse [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) mantém o tráfego de Blobs do Azure localmente em vez de roteá-lo para fora da rede virtual.

1. No portal, clique em **Redes virtuais** à esquerda.
1. Selecione a rede virtual do seu controlador. 
1. Clique em **Pontos de extremidade de serviço** à esquerda.
1. Clique em **Adicionar** na parte superior.
1. Mantenha o serviço como ``Microsoft.Storage`` e escolha a sub-rede do controlador.
1. Na parte inferior, clique em **Adicionar**.

  ![Captura de tela do portal do Azure com anotações para as etapas de criação de ponto de extremidade de serviço](media/avere-vfxt-service-endpoint.png)

## <a name="next-step"></a>Próxima etapa

Agora que o cluster está em execução e você conhece seu endereço IP de gerenciamento, é possível [conectar-se à ferramenta de configuração de cluster](avere-vfxt-cluster-gui.md) para habilitar o suporte, adicionar o armazenamento se necessário e personalizar outras configurações do cluster.
