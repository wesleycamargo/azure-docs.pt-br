---
title: Modelo de solução de blockchain Ethereum de pilha do Azure
description: Usar modelos de solução personalizada para implantar e configurar uma rede de blockchain do consórcio Ethereum no Azure Stack
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/12/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: b4c8ff113ff76586cc4a91adfe568b07327a2d94
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721969"
---
# <a name="azure-stack-ethereum-blockchain-solution-templates"></a>Modelos de solução de blockchain Ethereum de pilha do Azure

O modelo de solução de Ethereum foi projetado para tornar mais fácil e rápido para implantar e configurar uma rede de blockchain Ethereum consortium vários membros com o mínimo de conhecimento do Azure e Ethereum.

Com um punhado de entradas do usuário e uma implantação de clique único por meio do portal de locatário do Azure Stack, cada membro pode provisionar sua superfície de rede. Superfície de rede de cada membro consiste em um conjunto de nós de transação com balanceamento de carga com o qual um aplicativo ou usuário pode interagir para enviar um dispositivo Virtual de rede (NVA), um conjunto de nós de mineração para registrar transações e transações. Uma etapa de conexão subsequentes se conecta as NVAs para criar uma rede de blockchain de vários membros totalmente configurado.

## <a name="prerequisites"></a>Pré-requisitos

Baixe os itens mais recentes [do Marketplace](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS
* Windows Server 2016
* Script personalizado para Linux 2.0
* Extensão de script personalizado para o Windows

Para obter mais informações sobre os cenários de blockchain, consulte [modelo de solução de prova de trabalho consortium Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).

## <a name="deployment-architecture"></a>Arquitetura de implantação

Esse modelo de solução pode implantar a rede de consórcio de Ethereum membro única ou multicamada. A rede virtual está conectada em uma topologia de cadeia usando recursos de solução de virtualização de rede e conexão. 

## <a name="deployment-use-cases"></a>Casos de uso de implantação

O modelo pode implantar Ethereum consortium para o líder e associação de membro em uma variedade de formas, aqui são aqueles que testamos:

- Em uma pilha do Azure com vários nós, com o Azure AD ou AD FS, implantar o cliente potencial e membro usando a mesma assinatura ou com assinaturas diferentes.
- Em um único nó do Azure Stack (com o Azure AD) implante clientes potenciais e membro usando a mesma assinatura.

### <a name="standalone-and-consortium-leader-deployment"></a>Implantação de líder consortium e independente

O modelo de líder de consortium configura a superfície do primeiro membro na rede. 

1. Baixe o [modelo líder do GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json)
2. No portal de administração do Azure Stack, selecione **+ criar um recurso > implantação de modelo** para implantar a partir de um modelo personalizado.
3. Selecione **Editar modelo** para editar o novo modelo personalizado.
4. No painel de edição à direita, copie e cole o modelo líder JSON baixados anteriormente.
    
    ![Editar modelo de líder](media/azure-stack-ethereum/edit-leader-template.png)

5. Clique em **Salvar**.
6. Selecione **Editar parâmetros** e preencha os parâmetros de modelo para sua implantação.
    
    ![Editar parâmetros de modelo de líder](media/azure-stack-ethereum/edit-leader-parameters.png)

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | A cadeia de caracteres usada como base para nomear os recursos implantados. | Caracteres alfanuméricos com comprimento de 1 a 6 | Eth
    TIPO DE AUTENTICAÇÃO | O método para autenticar para a máquina virtual. | Senha ou chave pública SSH | Senha
    ADMINUSERNAME | Nome de usuário do administrador de cada VM implantada | 1 a 64 caracteres | gethadmin
    ADMINPASSWORD (tipo de autenticação = senha)| A senha para a conta de administrador para cada uma das máquinas virtuais implantadas. A senha deve ter três dos seguintes requisitos: um caractere maiúsculo, um caractere minúsculo, um número e um caractere especial. <br />Embora todas as máquinas virtuais tenham inicialmente a mesma senha, você pode alterar a senha após o provisionamento.|12 a 72 caracteres|
    ADMINSSHKEY (tipo de autenticação = sshPublicKey) | A chave de shell segura usada para login remoto. | |
    GENESISBLOCK | Cadeia de caracteres JSON que representa o bloco gênese personalizado.  Especificando um valor para esse parâmetro é opcional. | |
    ETHEREUMACCOUNTPSSWD | A senha de administrador usada para proteger a conta de Ethereum. | |
    ETHEREUMACCOUNTPASSPHRASE | A frase secreta usada para gerar a chave privada associada à conta Ethereum. | |
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
8. Na **implantação personalizada**, especifique **assinatura**, **grupo de recursos**, e **local do grupo de recursos**.
    
    ![Parâmetros de implantação de líder](media/azure-stack-ethereum/leader-deployment-parameters.png)

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    Assinatura | A assinatura para a qual deseja implantar a rede consortium | | Assinatura de consumo
    Grupo de recursos | O grupo de recursos para o qual implantar a rede do consórcio. | | EthereumResources
    Local padrão | A região do Azure para o grupo de recursos. | | local

8. Selecione **Criar**.

Implantação pode levar 20 minutos ou mais para ser concluída.

Após a implantação for concluída, você pode examinar o resumo de implantação **Microsoft. Modelo** na seção de implantação do grupo de recursos. O resumo contém valores de saída que podem ser usados para associar os membros consortium.

Para verificar a implantação do líder, procure o site de administração do líder. Você pode encontrar o endereço do site de administração na seção de saída **Template** implantação.  

![Resumo da implantação de líder](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Ingressar na implantação de membro consortium

1. Baixe o [modelo de membro consortium do GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json)
2. No portal de administração do Azure Stack, selecione **+ criar um recurso > implantação de modelo** para implantar a partir de um modelo personalizado.
3. Selecione **Editar modelo** para editar o novo modelo personalizado.
4. No painel de edição à direita, copie e cole o modelo líder JSON que você baixou anteriormente.
5. Clique em **Salvar**.
6. Selecione **Editar parâmetros** e preencha os parâmetros de modelo para sua implantação.

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | A cadeia de caracteres usada como base para nomear os recursos implantados. | Caracteres alfanuméricos com comprimento de 1 a 6 | Eth
    TIPO DE AUTENTICAÇÃO | O método para autenticar para a máquina virtual. | Senha ou chave pública SSH | Senha
    ADMINUSERNAME | Nome de usuário do administrador de cada VM implantada | 1 a 64 caracteres | gethadmin
    ADMINPASSWORD (tipo de autenticação = senha)| A senha para a conta de administrador para cada uma das máquinas virtuais implantadas. A senha deve ter três dos seguintes requisitos: um caractere maiúsculo, um caractere minúsculo, um número e um caractere especial. <br />Embora todas as máquinas virtuais tenham inicialmente a mesma senha, você pode alterar a senha após o provisionamento.|12 a 72 caracteres|
    ADMINSSHKEY (tipo de autenticação = sshPublicKey) | A chave de shell segura usada para login remoto. | |
    CONSORTIUMMEMBERID | A ID associada a cada membro da rede consortium.   | Essa ID deve ser exclusiva na rede. | 0
    NUMMININGNODES | Número de nós de mineração. | Entre 2 e 15. | 2
    MNNODEVMSIZE | Tamanho da VM de nós de mineração. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Desempenho de armazenamento de nós de mineração. | | Standard_LRS
    NUMTXNODES | Número de nós de transação. | Entre 1 e 5. | 1
    TXNODEVMSIZE | Tamanho da VM de nós de transação. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Desempenho de armazenamento de nós de transação. | | Standard_LRS
    CONSORTIUMDATA | A URL que aponta para os dados de configuração relevantes consortium fornecidos pela implantação de outro membro. Esse valor pode ser encontrado na saída de implantação do líder. | |
    REMOTEMEMBERVNETADDRESSSPACE | O endereço IP NVA do líder. Esse valor pode ser encontrado na saída de implantação do líder. | | 
    REMOTEMEMBERNVAPUBLICIP | O endereço IP NVA do líder. Esse valor pode ser encontrado na saída de implantação do líder. | | 
    CONNECTIONSHAREDKEY | Um segredo pré-estabelecidas entre os membros da rede consortium que está estabelecendo uma conexão. | |
    BASEURL | URL base para o modelo. | Use o valor padrão, a menos que você deseja personalizar os modelos de implantação. | 

7. Selecione **OK**.
8. Na **implantação personalizada**, especifique **assinatura**, **grupo de recursos**, e **local do grupo de recursos**.

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    Assinatura | A assinatura para a qual deseja implantar a rede consortium | | Assinatura de consumo
    Grupo de recursos | O grupo de recursos para o qual implantar a rede do consórcio. | | MemberResources
    Local padrão | A região do Azure para o grupo de recursos. | | local

8. Selecione **Criar**.

Implantação pode levar 20 minutos ou mais para ser concluída.

Após a implantação for concluída, você pode examinar o resumo de implantação **Template** na seção de implantação do grupo de recursos. O resumo contém valores de saída que podem ser usados para conectar-se membros consortium.

Para verificar a implantação do membro, procure o site de administração do membro. Você pode encontrar o endereço do site de administração na seção de saída da implantação de Template.

![Resumo da implantação de membro](media/azure-stack-ethereum/ethereum-node-status-2.png)

Conforme mostrado na figura, o status de nós do membro está **não está em execução**. Isso ocorre porque a conexão entre o líder e de membro não for estabelecida. A conexão entre o membro e o líder é uma conexão bidirecional. Quando você implanta um membro, o modelo automaticamente cria a conexão de membro para o líder. Para criar a conexão de líder ao membro, vá para a próxima etapa.

### <a name="connect-member-and-leader"></a>Conectar-se o membro e líder

Este modelo cria uma conexão do líder a um membro remoto. 

1. Baixe o [conectar-se o modelo de membro e líder do GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json)
2. No portal de administração do Azure Stack, selecione **+ criar um recurso > implantação de modelo** para implantar a partir de um modelo personalizado.
3. Selecione **Editar modelo** para editar o novo modelo personalizado.
4. No painel de edição à direita, copie e cole o modelo líder JSON que você baixou anteriormente.
    
    ![Editar conexão de modelo](media/azure-stack-ethereum/edit-connect-template.png)

5. Clique em **Salvar**.
6. Selecione **Editar parâmetros** e preencha os parâmetros de modelo para sua implantação.
    
    ![Editar conectar-se parâmetros de modelo](media/azure-stack-ethereum/edit-connect-parameters.png)

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Prefixo do nome do líder. Esse valor pode ser encontrado na saída de implantação do líder.  | Caracteres alfanuméricos com comprimento de 1 a 6 | |
    MEMBERROUTETABLENAME | Nome do líder tabela de rotas. Esse valor pode ser encontrado na saída de implantação do líder. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Espaço do membro de endereço. Esse valor pode ser encontrado na saída de implantação do membro. | |
    CONNECTIONSHAREDKEY | Um segredo pré-estabelecidas entre os membros da rede consortium que está estabelecendo uma conexão.  | |
    REMOTEMEMBERNVAPUBLICIP | O endereço IP NVA do membro. Esse valor pode ser encontrado na saída de implantação do membro. | |
    MEMBERNVAPRIVATEIP | NVA endereço IP do líder. Esse valor pode ser encontrado na saída de implantação do líder. | |
    LOCAL | Local do seu ambiente do Azure Stack. | | local
    BASEURL | URL base para o modelo. | Use o valor padrão, a menos que você deseja personalizar os modelos de implantação. | 

7. Selecione **OK**.
8. Na **implantação personalizada**, especifique **assinatura**, **grupo de recursos**, e **local do grupo de recursos**.
    
    ![Conectar-se parâmetros de implantação](media/azure-stack-ethereum/connect-deployment-parameters.png)

    Nome do Parâmetro | DESCRIÇÃO | Valores Permitidos | Valor de exemplo
    ---------------|-------------|----------------|-------------
    Assinatura | Assinatura do líder. | | Assinatura de consumo
    Grupo de recursos | Grupo de recursos do líder. | | EthereumResources
    Local padrão | A região do Azure para o grupo de recursos. | | local

8. Selecione **Criar**.

Após a implantação for concluída, demora alguns minutos para o líder e membro iniciar a comunicação. Para verificar a implantação, atualize o site de administração do membro. Status de nós do membro deve estar em execução. 

![Verificar a implantação](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre Ethereum e o Azure, consulte [tecnologia e aplicativos Blockchain | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/).
- Para obter mais informações sobre os cenários de blockchain no Azure, consulte [modelo de solução de prova de trabalho consortium Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).