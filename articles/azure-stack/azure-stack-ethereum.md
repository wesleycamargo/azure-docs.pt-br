---
title: Modelo de solução de Ethereum de pilha do Azure
description: Usar modelos de solução personalizada para implantar e configurar uma rede de Ethereum consortium na pilha do Azure
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 6/28/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 4c2b0cda2d4144cde733f7f57ac6311e1a69f547
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114721"
---
# <a name="azure-stack-ethereum-solution-templates"></a>Modelos de solução de Ethereum de pilha do Azure

O modelo de solução de Ethereum foi projetado para tornar mais fácil e rápido implantar e configurar uma rede de Ethereum consortium vários membros com o mínimo de conhecimento do Azure e Ethereum.

Com algumas entradas de usuário e uma implantação de clique simples por meio do portal do administrador de pilha do Azure, cada membro pode provisionar sua superfície de rede. Superfície de rede de cada membro consiste em um conjunto de nós de transação com balanceamento de carga com o qual um aplicativo ou o usuário pode interagir para enviar uma rede Virtual Appliance NVA (), um conjunto de nós de mineração para transações de registro e transações. Uma etapa de conexão subsequentes se conecta as NVAs para criar uma rede totalmente configurado blockchain de vários membros.

## <a name="prerequisites"></a>Pré-requisitos

Baixe os seguintes [do Marketplace](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS versão 16.04.201802220
* Windows Server 2016 
* Script personalizado para Linux 2.0 
* Extensão de script personalizado 

Para obter mais informações sobre cenários de blockchain no Azure, consulte [modelo de solução de prova de trabalho consortium Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).

É necessária uma assinatura do Azure que pode dar suporte a implantação de várias máquinas virtuais. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="deployment-architecture"></a>Arquitetura de implantação

Este modelo de solução pode implantar um único ou vários membros Ethereum consortium de rede. A rede virtual está conectada em uma topologia de cadeia usando recursos de solução de virtualização de rede e conexão. 

## <a name="deployment-use-cases"></a>Casos de uso de implantação

O modelo pode implantar Ethereum consortium para preenchimento e associação de membro em uma variedade de formas, aqui estão os que nós testamos:
- Em uma pilha de vários nós do Azure, com o Azure AD ou AD FS, implantar o cliente potencial e membro usando a mesma assinatura ou com assinaturas diferentes.
- Em uma pilha do Azure de nó único (com o Azure AD) implante o cliente potencial e membro usando a mesma assinatura.

### <a name="standalone-and-consortium-leader-deployment"></a>Implantação de preenchimento autônomo e consortium

O modelo de preenchimento de consortium configura o volume do primeiro membro na rede. 

1. Baixe o [modelo líder do GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/ConsortiumLeader/mainTemplate.json)
2. No portal de administração de pilha do Azure, selecione **Novo > implantação de modelo** para implantar um modelo personalizado.
3. Selecione **Editar modelo** para editar o novo modelo personalizado.
4. No painel de edição à direita, copie e cole o modelo líder JSON que você baixou anteriormente.
    
    ![Editar modelo de preenchimento](media/azure-stack-ethereum/edit-leader-template.png)

5. Clique em **Salvar**.
6. Selecione **Editar parâmetros** e complete os parâmetros de modelo para sua implantação.
    
    ![Editar parâmetros de modelo de preenchimento](media/azure-stack-ethereum/edit-leader-parameters.png)

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Cadeia de caracteres usada como base para nomear os recursos implantados. | Caracteres alfanuméricos com comprimento de 1 a 6 | Eth
    TIPO DE AUTENTICAÇÃO | O método para autenticar para a máquina virtual. | Senha ou chave pública SSH | Senha
    ADMINUSERNAME | Nome de usuário do administrador de cada VM implantada | 1 a 64 caracteres | gethadmin
    ADMINPASSWORD (tipo de autenticação = senha)| A senha para a conta de administrador para cada uma das máquinas virtuais implantadas. A senha deve ter três dos seguintes requisitos: um caractere maiúsculo, um caractere minúsculo, um número e um caractere especial. <br />Embora todas as máquinas virtuais tenham inicialmente a mesma senha, você pode alterar a senha após o provisionamento.|12 a 72 caracteres|
    ADMINSSHKEY (tipo de autenticação = parâmetros sshPublicKey) | A chave de shell segura usada para login remoto. | |
    GENESISBLOCK | Cadeia de caracteres JSON que representa o bloco gênese personalizado. | |
    ETHEREUMACCOUNTPSSWD | A senha de administrador usada para proteger a conta de Ethereum. | |
    ETHEREUMACCOUNTPASSPHRASE | A senha usada para gerar a chave privada associada à conta de Ethereum. | |
    ETHEREUMNETWORKID | A ID de rede do consórcio. | Usar qualquer valor entre 5 e 999.999.999 | 72
    CONSORTIUMMEMBERID | A ID associada a cada membro da rede consortium.   | Essa ID deve ser exclusiva na rede. | 0
    NUMMININGNODES | Número de nós de mineração. | Entre 2 e 15. | 2
    MNNODEVMSIZE | Tamanho da VM de nós de mineração. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Desempenho de armazenamento de nós de mineração. | | Standard_LRS
    NUMTXNODES | Número de nós de transação. | Entre 1 e 5. | 1
    TXNODEVMSIZE | Tamanho da VM de nós de transação. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Desempenho de armazenamento de nós de transação. | | Standard_LRS
    BASEURL | URL para obter os modelos dependendo da base. | Use o valor padrão, a menos que você deseja personalizar os modelos de implantação. | 

7. Selecione **OK**.
8. Em **implantação personalizada**, especifique **assinatura**, **grupo de recursos**, e **local do grupo de recursos**.
    
    ![Parâmetros de implantação de preenchimento](media/azure-stack-ethereum/leader-deployment-parameters.png)

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    Assinatura | A assinatura para a qual deseja implantar a rede consortium | | Assinatura de consumo
    Grupo de recursos | O grupo de recursos para o qual implantar a rede do consórcio. | | EthereumResources
    Local padrão | A região do Azure para o grupo de recursos. | | local

8. Selecione **Criar**.

Implantação pode levar 20 minutos ou mais para ser concluído.

Após a conclusão da implantação, você pode examinar o resumo de implantação **Microsoft. Modelo** na seção de implantação do grupo de recursos. O resumo contém valores de saída que podem ser usados para associar consortium membros.

Para verificar a implantação do preenchimento, procure o site de administração do preenchimento. Você pode encontrar o endereço do site de administração na seção de saída da **Microsoft.Template** implantação.  

![Resumo de implantação de preenchimento](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Implantação de membro consortium junção

1. Baixe o [modelo de membro consortium do GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/JoiningMember/mainTemplate.json)
2. No portal de administração de pilha do Azure, selecione **Novo > implantação de modelo** para implantar um modelo personalizado.
3. Selecione **Editar modelo** para editar o novo modelo personalizado.
4. No painel de edição à direita, copie e cole o modelo líder JSON que você baixou anteriormente.
5. Clique em **Salvar**.
6. Selecione **Editar parâmetros** e complete os parâmetros de modelo para sua implantação.

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Cadeia de caracteres usada como base para nomear os recursos implantados. | Caracteres alfanuméricos com comprimento de 1 a 6 | Eth
    TIPO DE AUTENTICAÇÃO | O método para autenticar para a máquina virtual. | Senha ou chave pública SSH | Senha
    ADMINUSERNAME | Nome de usuário do administrador de cada VM implantada | 1 a 64 caracteres | gethadmin
    ADMINPASSWORD (tipo de autenticação = senha)| A senha para a conta de administrador para cada uma das máquinas virtuais implantadas. A senha deve ter três dos seguintes requisitos: um caractere maiúsculo, um caractere minúsculo, um número e um caractere especial. <br />Embora todas as máquinas virtuais tenham inicialmente a mesma senha, você pode alterar a senha após o provisionamento.|12 a 72 caracteres|
    ADMINSSHKEY (tipo de autenticação = parâmetros sshPublicKey) | A chave de shell segura usada para login remoto. | |
    CONSORTIUMMEMBERID | A ID associada a cada membro da rede consortium.   | Essa ID deve ser exclusiva na rede. | 0
    NUMMININGNODES | Número de nós de mineração. | Entre 2 e 15. | 2
    MNNODEVMSIZE | Tamanho da VM de nós de mineração. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Desempenho de armazenamento de nós de mineração. | | Standard_LRS
    NUMTXNODES | Número de nós de transação. | Entre 1 e 5. | 1
    TXNODEVMSIZE | Tamanho da VM de nós de transação. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Desempenho de armazenamento de nós de transação. | | Standard_LRS
    CONSORTIUMDATA | URL apontando para os dados de configuração relevantes consortium fornecidos pela implantação de outro membro. Esse valor pode ser encontrado na saída de implantação do preenchimento. | |
    REMOTEMEMBERVNETADDRESSSPACE | O endereço de IP de NVA do líder. Esse valor pode ser encontrado na saída de implantação do preenchimento. | | 
    REMOTEMEMBERNVAPUBLICIP | O endereço de IP de NVA do líder. Esse valor pode ser encontrado na saída de implantação do preenchimento. | | 
    CONNECTIONSHAREDKEY | Um segredo pré-estabelecidas entre os membros da rede consortium que estabelece uma conexão. | |
    BASEURL | URL base para o modelo. | Use o valor padrão, a menos que você deseja personalizar os modelos de implantação. | 

7. Selecione **OK**.
8. Em **implantação personalizada**, especifique **assinatura**, **grupo de recursos**, e **local do grupo de recursos**.

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    Assinatura | A assinatura para a qual deseja implantar a rede consortium | | Assinatura de consumo
    Grupo de recursos | O grupo de recursos para o qual implantar a rede do consórcio. | | MemberResources
    Local padrão | A região do Azure para o grupo de recursos. | | local

8. Selecione **Criar**.

Implantação pode levar 20 minutos ou mais para ser concluído.

Após a conclusão da implantação, você pode examinar o resumo de implantação **Microsoft.Template** na seção de implantação do grupo de recursos. O resumo contém valores de saída que podem ser usados para conectar-se membros consortium.

Para verificar a implantação do membro, procure o site de administração do membro. Você pode encontrar o endereço do site de administração na seção de saída da implantação de Microsoft.Template.

![Resumo de implantação de membro](media/azure-stack-ethereum/ethereum-node-status-2.png)

Conforme mostrado na figura, o status de nós do membro é **não está em execução**. Isso ocorre porque a conexão entre o membro e líder não for estabelecida. A conexão entre o membro e o preenchimento é uma conexão bidirecional. Quando você implanta o membro, o modelo automaticamente cria a conexão do membro para o preenchimento. Para criar a conexão de preenchimento para o membro, vá para a próxima etapa.

### <a name="connect-member-and-leader"></a>Conecte-se o membro e preenchimento

Este modelo cria uma conexão do líder em um membro remoto. 

1. Baixe o [conectar-se o modelo de membro e líder do GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/Connection/mainTemplate.json)
2. No portal de administração de pilha do Azure, selecione **Novo > implantação de modelo** para implantar um modelo personalizado.
3. Selecione **Editar modelo** para editar o novo modelo personalizado.
4. No painel de edição à direita, copie e cole o modelo líder JSON que você baixou anteriormente.
    
    ![Editar conexão de modelo](media/azure-stack-ethereum/edit-connect-template.png)

5. Clique em **Salvar**.
6. Selecione **Editar parâmetros** e complete os parâmetros de modelo para sua implantação.
    
    ![Editar parâmetros de modelo de conexão](media/azure-stack-ethereum/edit-connect-parameters.png)

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Prefixo do nome do preenchimento. Esse valor pode ser encontrado na saída de implantação do preenchimento.  | Caracteres alfanuméricos com comprimento de 1 a 6 | |
    MEMBERROUTETABLENAME | Nome da tabela de rotas do preenchimento. Esse valor pode ser encontrado na saída de implantação do preenchimento. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Espaço do membro de endereço. Esse valor pode ser encontrado na saída de implantação do membro. | |
    CONNECTIONSHAREDKEY | Um segredo pré-estabelecidas entre os membros da rede consortium que estabelece uma conexão.  | |
    REMOTEMEMBERNVAPUBLICIP | O endereço de IP de NVA do membro. Esse valor pode ser encontrado na saída de implantação do membro. | |
    MEMBERNVAPRIVATEIP | NVA endereço IP do líder. Esse valor pode ser encontrado na saída de implantação do preenchimento. | |
    LOCAL | Local do seu ambiente de pilha do Azure. | | local
    BASEURL | URL base para o modelo. | Use o valor padrão, a menos que você deseja personalizar os modelos de implantação. | 

7. Selecione **OK**.
8. Em **implantação personalizada**, especifique **assinatura**, **grupo de recursos**, e **local do grupo de recursos**.
    
    ![Conecte-se parâmetros de implantação](media/azure-stack-ethereum/connect-deployment-parameters.png)

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    Assinatura | Assinatura do preenchimento. | | Assinatura de consumo
    Grupo de recursos | Grupo de recursos do preenchimento. | | EthereumResources
    Local padrão | A região do Azure para o grupo de recursos. | | local

8. Selecione **Criar**.

Após a conclusão da implantação, ele leva alguns minutos para preenchimento e membro para iniciar a comunicação. Para verificar a implantação, atualize o site de administração do membro. Status de nós do membro deve estar em execução. 

![Verificar a implantação](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre Ethereum e o Azure, consulte [Blockchain tecnologia e aplicativos | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/).
- Para obter mais informações sobre cenários de blockchain no Azure, consulte [modelo de solução de prova de trabalho consortium Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).