---
title: Configurar nós de transação de serviço do Azure Blockchain
description: Como configurar nós de transação de serviço do Azure Blockchain
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dffeb81ae1eb244c38639a1241c0581e6fcdf94a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027954"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configurar nós de transação de serviço do Azure Blockchain

Para interagir com o serviço do Azure Blockchain, fazer isso por meio de se conectar a um ou mais nós de transação no seu membro de blockchain.  Para interagir com nós de transação, você precisará configurar seus nós para o acesso.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um membro de Blockchain do Azure](create-member.md)

## <a name="transaction-node-overview"></a>Visão geral de nó de transação

Nós de transação são usados para enviar as transações de blockchain no serviço de Blockchain do Azure por meio de um ponto de extremidade público. O nó de transação padrão contém a chave privada da conta Ethereum registrada do blockchain e como tal, não pode ser excluído.

Para exibir os detalhes do nó de transação padrão:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Navegue até seu membro de serviço de Blockchain do Azure. Selecione **nós de transação**.

    ![Selecione o nó da transação padrão](./media/configure-transaction-nodes/nodes.png)

    Detalhes de visão geral incluem endereços de ponto de extremidade público e a chave pública.

## <a name="create-transaction-node"></a>Criar nó de transação

Você pode adicionar até nove nós de transações adicionais ao seu membro de blockchain, para um total de dez nós de transação. Adicionando nós de transação, você pode aumentar a escalabilidade ou distribuir a carga. Por exemplo, você poderia ter um ponto de extremidade do nó de transação para aplicativos cliente diferentes.

Para adicionar um nó de transação:

1. No portal do Azure, navegue até seu membro de serviço de Blockchain do Azure e selecione **nós de transação > Adicionar**.
1. Conclua as configurações para o novo nó de transação.

    ![Adicionar nó de transação](./media/configure-transaction-nodes/add-node.png)

    | Configuração | DESCRIÇÃO |
    |---------|-------------|
    | NOME | Nome do nó de transação. O nome é usado para criar o endereço DNS para o ponto de extremidade do nó de transação. Por exemplo, `newnode-myblockchainmember.blockchain.azure.com`. O nome do nó não pode ser alterado depois que ele é criado. |
    | Senha | Defina uma senha forte. Use a senha para acessar o ponto de extremidade do nó de transação com a autenticação básica.

1. Selecione **Criar**.

    Um novo nó de transação de provisionamento leva cerca de 10 minutos. Nós de transações adicionais incorrer em custos. Para obter mais informações sobre os custos, consulte [os preços do Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Pontos de extremidade

Nós de transação têm um nome DNS exclusivo e pontos de extremidade públicos.

Para exibir detalhes de ponto de extremidade do nó da transação:

1. No portal do Azure, navegue até um dos seus nós de transação de membro de serviço de Blockchain do Azure e selecione **visão geral**.

    ![Pontos de extremidade](./media/configure-transaction-nodes/endpoints.png)

Pontos de extremidade de nó de transação são seguros e exigem autenticação. Você pode se conectar ao ponto de extremidade de um transação usando a autenticação do Azure AD, autenticação básica de HTTPS e usando uma chave de acesso via HTTPS ou Websocket por SSL.

### <a name="azure-active-directory-access-control"></a>Controle de acesso do Azure Active Directory

Pontos finais de nó da transação Blockchain Service do Azure dão suporte a autenticação do Active Directory do Azure (AD Azure). Você pode conceder acesso à entidade de serviço, grupo e usuário do Azure AD para seu ponto de extremidade.

Para conceder o Azure de controle de acesso do AD para seu ponto de extremidade:

1. No portal do Azure, navegue até seu membro de serviço de Blockchain do Azure e selecione **nós de transação > controle de acesso (IAM) > Adicionar > Adicionar atribuição de função**.
1. Crie uma nova atribuição de função para um usuário, grupo ou entidade de serviço (funções de aplicativo).

    ![Adicionar função IAM](./media/configure-transaction-nodes/add-role.png)

    | Configuração | Ação |
    |---------|-------------|
    | Função | Selecione **proprietário**, **Colaborador**, ou **leitor**.
    | Atribuir acesso a | Selecione **usuário, grupo ou entidade de serviço do Azure AD**.
    | Selecionar | Pesquise o usuário, grupo ou entidade de serviço que você deseja adicionar.

1. Selecione **salvar** para adicionar a atribuição de função.

Para obter mais informações sobre o controle de acesso do AD do Azure, consulte [gerenciar o acesso aos recursos do Azure usando o portal do Azure e o RBAC](../../role-based-access-control/role-assignments-portal.md)

Para obter detalhes sobre como conectar-se usando a autenticação do AD do Azure, consulte [conectar-se ao nó usando a autenticação do AAD a](configure-aad.md).

### <a name="basic-authentication"></a>Autenticação básica

Para a autenticação básica de HTTPS, as credenciais de nome e a senha do usuário são passadas no cabeçalho da solicitação HTTPS para o ponto de extremidade.

Você pode exibir detalhes de ponto de extremidade de autenticação básica do nó da transação no portal do Azure. Navegue até um dos seus nós de transação de membro de serviço de Blockchain do Azure e selecione **autenticação básica** nas configurações.

![Autenticação básica](./media/configure-transaction-nodes/basic.png)

O nome de usuário é o nome do seu nó e não pode ser alterado.

Para usar a URL, substitua \<senha\> com a senha definida quando o nó foi provisionado. Você pode atualizar a senha, selecionando **redefinição de senha**.

### <a name="access-keys"></a>Chaves de acesso

Para autenticação de chave de acesso, a chave de acesso é incluída na URL do ponto de extremidade. Quando o nó de transação é provisionado, duas chaves de acesso são geradas. A chave de acesso pode ser usada para autenticação. Duas chaves permitem que você altere e rotação de chaves.

Você pode exibir detalhes de chave de acesso do nó da transação e copiar os endereços de ponto de extremidade que incluem as chaves de acesso. Navegue até um dos seus nós de transação de membro de serviço de Blockchain do Azure e selecione **chaves de acesso** nas configurações.

### <a name="firewall-rules"></a>Regras de firewall

Regras de firewall permitem que você limitar os endereços IP que podem tentar se autenticar com o nó de transação.  Se nenhuma regra de firewall é configurada para o nó de transação, ele não pode ser acessado por qualquer pessoa.  

Para exibir as regras de firewall do nó da transação, navegue até um dos seus nós de transação de membro de serviço de Blockchain do Azure e selecione **regras de Firewall** nas configurações.

Você pode adicionar regras de firewall, inserindo um nome de regra, iniciando o IP endereço e um final de endereços IP na **regras de Firewall** grade.

![Regras de firewall](./media/configure-transaction-nodes/firewall-rules.png)

Para habilitar:

* **Único endereço IP:** Configure o mesmo endereço IP inicial e final de endereços IP.
* **Intervalo de endereços IP:** Configure o início e final do intervalo de endereços IP. Por exemplo, um intervalo começando em 10.221.34.0 e terminando às 10.221.34.255 permitiria que a sub-rede 10.221.34.xxx inteira.
* **Permita todos os endereços IP:** Configure o endereço IP inicial para 0.0.0.0 e o endereço IP final para 255.255.255.255.

## <a name="connection-strings"></a>Cadeias de conexão

Sintaxe de cadeia de caracteres de Conexão para o nó de transação é fornecida para basic autenticação ou usando chaves de acesso. Cadeias de Conexão, incluindo as chaves de acesso via HTTPS e WebSockets são fornecidas.

Você pode exibir cadeias de caracteres de conexão do nó da transação e copiar os endereços de ponto de extremidade. Navegue até um dos seus nós de transação de membro de serviço de Blockchain do Azure e selecione **cadeias de caracteres de Conexão** nas configurações.

![Cadeias de conexão](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Exemplo de código

Código de exemplo é fornecido para habilitar rapidamente a se conectar ao seu nó de transação por meio de Web3, Nethereum, Web3js e Truffle.

Você pode exibir o código de conexão de exemplo do nó da transação e copie-o para usar ferramentas de desenvolvedor populares. Navegue até um dos seus nós de transação de membro de serviço de Blockchain do Azure e selecione **código de exemplo** nas configurações.

Escolha a guia Web3 ou Nethereum para exibir o código de exemplo que você deseja usar.

![Exemplo de código](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar nós de transações usando a CLI do Azure](manage-cli.md)
