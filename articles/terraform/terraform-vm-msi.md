---
title: Utilizar uma imagem do Azure Marketplace para criar uma máquina virtual do Terraform do Linux com uma identidade gerenciada
description: Utilizar imagem do Marketplace para criar máquina virtual do Terraform do Linux com uma identidade gerenciada e Gerenciamento de Estado Remoto para facilmente implantar recursos no Azure.
services: terraform
ms.service: terraform
keywords: terraform, devops, MSI, máquina virtual, estado remoto, azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 3/12/2018
ms.openlocfilehash: 3f10e5dfd0a4aac859c1a946b5f0a680899e0888
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852534"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-identities-for-azure-resources"></a>Utilizar uma imagem do Azure Marketplace para criar uma máquina virtual do Terraform do Linux com identidades gerenciadas para recursos do Azure

Este artigo mostra como utilizar uma [imagem do Marketplace do Terraform](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) para criar uma VM de Linux Ubuntu (16.04 LTS) com a última versão do [Terraform](https://www.terraform.io/intro/index.html) instalada e configurada usando [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Essa imagem também configura um back-end remoto para habilitar o gerenciamento de [estado remoto](https://www.terraform.io/docs/state/remote.html) utilizando o Terraform. 

A imagem do Marketplace do Terraform facilita o uso do Terraform no Azure, sem a necessidade de instalar e configurar o Terraform manualmente. 

Não há encargos de software para essa imagem de VM do Terraform. Você paga apenas pelo valor de uso de hardware do Azure, que são avaliadas com base no tamanho da máquina virtual provisionada. Para obter mais informações sobre os valores de computação, consulte a [Página de preços de máquinas virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder criar uma máquina virtual do Terraform do Linux, você deverá ter uma assinatura do Azure. Se você não tiver uma, consulte [Criar sua conta gratuita do Azure hoje](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Criar a máquina virtual do Terraform 

A seguir são apresentadas as etapas para criar uma instância de uma maquina virtual do Terraform do Linux: 

1. No Portal do Azure, vá até a lista [Criar um Recurso](https://ms.portal.azure.com/#create/hub).

2. Na barra de pesquisa **Pesquisar no Marketplace**, procure **Terraform**. Selecione o modelo do **Terraform**. 

3. Na guia de detalhes do Terraform na parte inferior direita, selecione o botão **Criar**.

    ![Criar uma máquina virtual do Terraform](media/terraformmsi.png)

4. As seções a seguir fornecem entradas para cada uma das etapas no assistente para criar a máquina virtual do Terraform Linux. A seção a seguir lista as entradas que são necessárias para configurar cada uma dessas etapas.

## <a name="details-on-the-create-terraform-tab"></a>Detalhes na guia Criar Terraform

Digite os seguintes detalhes na guia **Criar Terraform**:

1. **Noções básicas**
    
   * **Nome**: O nome da sua máquina virtual Terraform.
   * **Nome de usuário**: A primeira ID de entrada da conta.
   * **Senha**: A primeira senha da conta. (Você pode usar uma chave pública SSH, em vez de senha).
   * **Assinatura**: A assinatura na qual o computador será criado e cobrado. Você deve ter privilégios de criação de recurso nessa assinatura.
   * **Grupo de recursos**: Um grupo de recursos novo ou existente.
   * **Localização**: O datacenter que é mais apropriado. Normalmente, é o datacenter que contém a maioria dos seus dados ou que está mais próximo de sua localização física para oferecer acesso mais rápido à rede.

2. **Configurações adicionais**

   * **Tamanho**: Tamanho da máquina virtual. 
   * **Tipo de disco da VM**: SSD ou HD.

3. **Resumo do Terraform**

   * Verifique se todas as informações inseridas estão corretas. 

4. **Comprar**

   * Para iniciar o processo de provisionamento, selecione **Comprar**. Um link para os termos da transação é fornecido. A VM não tem encargos adicionais além dos de computação para o tamanho do servidor que você escolheu na etapa de tamanho.

A imagem de VM do Terraform executa as etapas a seguir:

* Cria uma VM com identidade atribuída pelo sistema baseado na imagem LTS do Ubuntu 16.04.
* Instala a extensão MSI na VM para permitir que os tokens OAuth sejam emitidos para recursos do Azure.
* Atribui permissões de RBAC para identidade gerenciada, concedendo direitos do proprietário para o grupo de recursos.
* Cria uma pasta de modelos do Terraform (tfTemplate).
* Pré-configura um estado remoto do Terraform com o back-end do Azure.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Acessar e configurar a máquina virtual do Terraform para Linux

Após criar a VM, você poderá entrar nela usando SSH. Use as credenciais da conta criada na seção “Noções Básicas” da etapa 3 para a interface do shell de texto. No Windows, você pode baixar uma ferramenta de cliente SSH como o [Putty](http://www.putty.org/).

Após utilizar o SSH para conectar a máquina virtual, será necessário conceder permissões de colaborador para toda a assinatura para identidades gerenciadas para recursos do Azure na máquina virtual. 

A permissão de colaborador ajuda a MSI na VM a usar o Terraform para criar recursos fora do grupo de recursos da VM. Você pode facilmente realizar essa ação executando um script uma vez. Use o seguinte comando:

`. ~/tfEnv.sh`

O script anterior usa o mecanismo [Logon interativo AZ CLI v 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) para autenticar com o Azure e atribuir a permissão de colaborador da Identidade Gerenciada da máquina virtual em toda a assinatura. 

 A VM tem um back-end de estado remoto do Terraform. Para habilitá-lo na implantação do Terraform, copie o arquivo remoteState.tf do diretório tfTemplate para a raiz dos scripts do Terraform.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Para obter mais informações sobre o Gerenciamento de Estado Remoto, consulte [esta página sobre o estado remoto do Terraform](https://www.terraform.io/docs/state/remote.html). A chave de acesso de armazenamento é exposta neste arquivo e precisa ser excluída antes de confirmar os arquivos de configuração do Terraform no controle do código-fonte.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como configurar uma máquina virtual do Terraform do Linux no Azure. A seguir são apresentados alguns recursos adicionais para ajudá-lo a saber mais sobre o Terraform no Azure: 

 [Hub do Terraform no Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentação do provedor do Terraform do Azure](https://aka.ms/terraform)  
 [Origem do provedor do Terraform do Azure](https://aka.ms/tfgit)  
 [Módulos do Terraform do Azure](https://aka.ms/tfmodules)
 

















