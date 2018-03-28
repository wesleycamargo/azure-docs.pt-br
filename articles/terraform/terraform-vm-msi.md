---
title: Utilizar uma imagem do Azure Marketplace para criar uma Máquina Virtual do Terraform do Linux com Identidade de Serviço Gerenciada
description: Utilizar imagem do Marketplace para criar Máquina Virtual do Terraform do Linux com Identidade de Serviço Gerenciada e Estado Remoto para facilmente implantar recursos no Azure.
keywords: terraform, devops, MSI, máquina virtual, estado remoto, azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: ce8a3e8b813bf4224a1fd77d60ec30f2f8e080a7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Utilizar uma imagem do Azure Marketplace para criar uma Máquina Virtual do Terraform do Linux com Identidade de Serviço Gerenciada

Este artigo mostra como utilizar uma [imagem do Marketplace do Terraform](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) para criar uma `Ubuntu Linux VM (16.04 LTS)` com a última versão do [Terraform](https://www.terraform.io/intro/index.html) instalada e configurada usando [MSI (Identidade de Serviço Gerenciada)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Essa imagem também configura um back-end remoto para habilitar o gerenciamento de [Estado Remoto](https://www.terraform.io/docs/state/remote.html) utilizando o Terraform. A imagem do Marketplace do Terraform facilita o uso do Terraform no Azure em minutos, sem a necessidade de instalar o Terraform e configurar a autenticação manualmente. 

Não há encargos de software para essa imagem de VM do Terraform. Você paga apenas os valores de uso de hardware do Azure que são avaliadas com base no tamanho da máquina virtual provisionada. Para obter mais detalhes sobre os valores de computação, é possível localizá-los na [Página de preços de Máquinas Virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>pré-requisitos
Antes de poder criar uma Máquina Virtual do Terraform do Linux, você deverá ter uma assinatura do Azure. Se você não tiver uma, consulte [Criar sua conta gratuita do Azure hoje](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Criar a Máquina Virtual do Terraform 

A seguir são apresentadas as etapas para criar uma instância da Maquina Virtual do Terraform do Linux. 

1. Navegue até a lista [Criar um Recurso](https://ms.portal.azure.com/#create/hub) no Portal do Azure.
2. Pesquise `Terraform` na barra de pesquisa `Search the Marketplace`. Selecione o modelo `Terraform`. Selecione o botão **Criar** na guia de detalhes do Terraform no lado inferior direito.
![alt text](media\terraformmsi.png)
3. As seções a seguir fornecem entradas para cada uma das etapas no assistente (**enumeradas à direita**) para criar a Máquina Virtual do Terraform Linux.  A seguir são apresentadas as entradas necessárias para configurar cada uma dessas etapas

## <a name="details-in-create-terraform-tab"></a>Detalhes na guia Criar Terraform

A seguir são apresentados os detalhes que precisam ser inseridos na guia Criar Terraform.

a. **Noções básicas**
    
* **Nome**: Nome da Máquina Virtual do Terraform.
* **Nome de Usuário**: primeira ID de entrada da conta.
* **Senha**: primeira senha da conta (você pode usar a chave pública SSH, em vez de senha).
* **Assinatura**: se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado. Você deve ter privilégios de criação de recurso nessa assinatura.
* **Grupo de recursos**: é possível criar um novo grupo ou usar um existente.
* **Local**: selecione o datacenter mais apropriado. Normalmente, é o datacenter que contém a maioria dos seus dados ou que está mais próximo de sua localização física para o acesso mais rápido à rede.

b. **Configurações adicionais**

* Tamanho: o tamanho da Máquina Virtual.
* Tipo de disco da VM: escolha entre SSD ou HD

c. **Resumo do Terraform**

* Verifique se todas as informações inseridas estão corretas. 

d. **Comprar**

* Para iniciar o provisionamento, clique em Comprar. Um link para os termos da transação é fornecido. A VM não tem encargos adicionais além dos de computação para o tamanho do servidor que você escolheu na etapa Tamanho.

A imagem de VM do Terraform executa as etapas a seguir

* Cria uma VM com identidade atribuída pelo sistema com base na imagem LTS do Ubuntu 16.04
* Instala a extensão MSI na VM para permitir que os tokens OAuth sejam emitidos para recursos do Azure
* Atribui permissões de RBAC para Identidade Gerenciada, concedendo direitos do proprietário para o grupo de recursos
* Cria uma pasta de modelos do Terraform (tfTemplate)
* Pré-configura o estado remoto do Terraform com o back-end do Azure

## <a name="how-to-access-and-configure-linux-terraform-virtual-machine"></a>Como acessar e configurar a Máquina Virtual do Terraform do Linux

Após a criação da VM, você poderá entrar nela usando SSH. Use as credenciais da conta criada na seção Noções Básicas da etapa 3 para a interface do shell de texto. No Windows, é possível baixar uma ferramenta do cliente SSH como [Putty](http://www.putty.org/)

Após utilizar `SSH` para conectar a Máquina Virtual, será necessário conceder permissões de colaborador para toda a assinatura da Identidade de Serviço Gerenciada na Máquina Virtual. A permissão de colaborador ajuda a MSI na VM a usar o Terraform para criar recursos fora do grupo de recursos da VM. Você pode facilmente realizar essa ação executando um script uma vez. A seguir é apresentado o comando para fazer isso.

`. ~/tfEnv.sh`

O script anterior usa o mecanismo [Logon interativo AZ CLI v 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) para autenticar com o Azure e atribuir a permissão de colaborador da Identidade de Serviço Gerenciada da Máquina Virtual em toda a assinatura. 

 A VM tem o back-end de Estado Remoto do Terraform criado e para habilitá-lo na implantação do Terraform será necessário copiar o arquivo remoteState.tf do diretório tfTemplate para a raiz dos scripts do Terraform.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Leia mais sobre o Gerenciamento de Estado Remoto [aqui](https://www.terraform.io/docs/state/remote.html). A chave de acesso de armazenamento é exposta nesse arquivo e precisa ser cuidadosamente verificada no controle do código-fonte.  

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como configurar uma Máquina Virtual do Terraform do Linux no Azure. A seguir são apresentados alguns recursos adicionais para saber mais sobre o Terraform no Azure. 

 [Hub do Terraform no Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentação do provedor do Terraform do Azure](http://aka.ms/terraform)  
 [Origem do provedor do Terraform do Azure](http://aka.ms/tfgit)  
 [Módulos do Terraform do Azure](http://aka.ms/tfmodules)
 

















