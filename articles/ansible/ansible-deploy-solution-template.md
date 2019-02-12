---
title: Implantar o modelo de solução Ansible para Azure para o CentOS
description: Saiba como implantar o modelo de solução do Ansible em uma máquina virtual do CentOS hospedado no Azure, juntamente com ferramentas configuradas para funcionar com o Azure.
ms.service: ansible
keywords: ansible, azure, devops, solution template, virtual machine, managed identities for azure resources, centos, red hat
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 01/28/2019
ms.openlocfilehash: 9fa711c86e3ef7f05e87504905ec642808d8f601
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770275"
---
# <a name="deploy-the-ansible-solution-template-for-azure-to-centos"></a>Implantar o modelo de solução Ansible para Azure para o CentOS
O modelo de solução do Ansible para Azure destina-se a configurar uma instância do Ansible em uma máquina virtual do CentOS, juntamente com o Ansible e um conjunto de ferramentas configurado para funcionar com o Azure. As ferramentas incluem:

- **Módulos do Ansible do Azure** – os [Módulos do Ansible para Azure](./ansible-matrix.md) são um conjunto de módulos que permitem a você criar e gerenciar sua infraestrutura no Azure. A versão mais recente desses módulos é implantada por padrão. No entanto, durante o processo de implantação de modelo de solução, você pode especificar um número de versão apropriado para seu ambiente.
- **CLI (interface de linha de comando) do Azure 2.0** – a [CLI do Azure 2.0](/cli/azure/?view=azure-cli-latest) é a experiência da linha de comando multiplataforma da Microsoft para gerenciar os recursos do Azure. 
- **Identidades gerenciadas para recursos do Azure** – o recurso [Identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) aborda a questão de manter as credenciais de aplicativo de nuvem seguras.

## <a name="prerequisites"></a>Pré-requisitos
- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="deploy-the-ansible-solution-template-from-the-azure-marketplace"></a>Implantar o modelo de solução do Ansible do Azure Marketplace

1. Navegue até [Modelo de solução do Ansible no Azure Marketplace](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Selecione **OBTENHA AGORA**.

1. É exibida uma janela detalhando Termos de uso, Política de Privacidade e Uso dos Termos do Marketplace do Azure. Selecione **Continuar**.

1. O portal do Azure aparece e exibe a página do Ansible que descreve o modelo de solução. Selecione **Criar**.

1. Na página **Criar Ansible**, você verá várias guias. Na guia **Noções básicas**, insira as informações necessárias:

    - **Nome** – especifique o nome de sua instância do Ansible. Para fins de demonstração, o nome `ansiblehost` é usado.
    - **Nome de usuário:** – especifique o nome de usuário que terá acesso à instância do Ansible. Para fins de demonstração, o nome `ansibleuser` é usado.
    - **Tipo de autenticação:** – selecione a **senha** ou a **chave pública SSH**. Para fins de demonstração, **chave pública SSH** é selecionada.
    - **Senha** e **Confirmar senha** – se você selecionar **Senha** para **Tipo de autenticação**, insira sua senha para esses valores.
    - **Chave pública SSH** – se você selecionar **Chave pública SSH** para **Tipo de autenticação**, insira a chave pública RSA no formato linha única – começando com `ssh-rsa`.
    - **Assinatura** – selecione sua assinatura do Azure na lista suspensa.
    - **Grupo de recursos** – selecione um grupo de recursos existente na lista suspensa ou selecione **Criar novo** e especifique um nome para um novo grupo de recursos. Para fins de demonstração, um novo grupo de recursos denominado `ansiblerg` é usado.
    - **Localização** – selecione a localização na lista suspensa apropriada ao seu cenário.

    ![Guia Portal do Azure para configurações básicas do Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Selecione **OK**.

1. Na guia **Configurações Adicionais**, insira as informações necessárias:

    - **Tamanho** – o portal do Azure assumirá como padrão um tamanho padrão. Para especificar um tamanho diferente que acomode o seu cenário específico, selecione a seta para exibir uma lista de tamanhos diferentes.
    - **Tamanho do disco da VM** – selecione **SSD** (unidade de estado sólido Premium) ou **HD** (unidade de disco rígido). Para fins de demonstração, **SSD** é selecionada por seus benefícios de desempenho. Para obter mais informações sobre cada um desses tipos de armazenamento em disco, leia os seguintes artigos:
        - [Armazenamento Premium de alto desempenho e discos gerenciados para VMs](/azure/virtual-machines/windows/premium-storage)
        - [Managed Disks SSD Standard para carga de trabalho da Máquina virtual do Azure](/azure/virtual-machines/windows/disks-standard-ssd)
    - **Endereço IP público** – especifique essa configuração se você quiser se comunicar com a máquina virtual de fora da máquina virtual. O padrão é um novo endereço IP público que tem o nome `ansible-pip`. Para especificar um endereço IP diferente, selecione a seta para especificar os atributos – como nome, SKU e atribuição de endereço IP. 
    - **Rótulo de nome de domínio** – insira o nome de domínio público da máquina virtual. O nome deve ser exclusivos e atender aos requisitos de nomenclatura. Para obter mais informações sobre como especificar um nome para a máquina virtual, confira [Convenções de nomenclatura para recursos do Azure](/azure/architecture/best-practices/naming-conventions).
    - **Versão do Ansible** – especifique um número de versão ou o valor `latest` para implantar a versão mais recente. Selecione o ícone de informações ao lado de **versão do Ansible** para obter mais informações sobre as versões disponíveis.

    ![Guia do portal do Azure para configurações adicionais do Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Selecione **OK**.

1. Na guia **Configurações de Integração do Ansible**, especifique o tipo de autenticação. Para obter mais informações sobre como proteger recursos do Azure, confira [O que são identidades gerenciadas para recursos do Azure?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Guia Portal do Azure para configurações de integração do Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Selecione **OK**.

1. A página **Resumo** é exibida mostrando o processo de validação e listando os critérios especificados para a implantação do Ansible. Um link na parte inferior da guia permite a você **Baixar o modelo e os parâmetros** para uso com plataformas e idiomas do Azure compatíveis. 

    ![Guia Portal do Azure para a guia Resumo do Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Selecione **OK**.

1. Quando a guia **Criar** é exibida, selecione **OK** para implantar o Ansible.

1. Selecione o ícone **Notificações** na parte superior da página do portal para acompanhar a implantação do Ansible. Depois que a implantação estiver concluída, selecione **Ir para o grupo de recursos**. 

    ![Guia Portal do Azure para a guia Resumo do Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-complete.png)

1. Na página do grupo de recursos, obtenha o endereço IP do host do Ansible e entre para gerenciar seus recursos do Azure usando o Ansible.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Usar o Ansible para criar uma máquina virtual do Linux no Azure](/azure/virtual-machines/linux/ansible-create-vm)
