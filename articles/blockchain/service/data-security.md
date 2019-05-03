---
title: Segurança do Azure Blockchain Service
description: Conceitos do Azure Blockchain Service dados acesso e segurança
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028194"
---
# <a name="azure-blockchain-service-security"></a>Segurança do Azure Blockchain Service

Service de Blockchain do Azure usa vários recursos do Azure para manter seus dados seguros e disponíveis. Dados são protegidos usando o isolamento, criptografia e autenticação.

## <a name="isolation"></a>Isolamento

Recursos de Blockchain Service do Azure são isolados em uma rede virtual privada. Cada nó de transação e a validação é uma máquina virtual (VM). VMs em uma rede virtual não pode se comunicar diretamente com VMs em uma rede virtual diferente. Isolamento garante a comunicação permanece privada dentro da rede virtual. Para obter mais informações sobre o isolamento de rede virtual do Azure, consulte [isolamento na nuvem pública do Azure](../../security/azure-isolation.md#networking-isolation).

![Diagrama de rede virtual](./media/data-security/vnet.png)

## <a name="encryption"></a>Criptografia

Dados de usuário são armazenados no armazenamento do Azure. Dados de usuário são criptografados em movimento e em repouso para segurança e confidencialidade. Para obter mais informações, consulte: [Guia de segurança do armazenamento do Azure](../../storage/common/storage-security-guide.md).

## <a name="authentication"></a>Authentication

As transações podem ser enviadas a nós de blockchain por meio de um ponto de extremidade RPC. Os clientes se comunicam com um nó de transação usando um servidor proxy reverso que usam autenticação de identificadores e criptografa os dados sobre SSL.

![Diagrama de autenticação](./media/data-security/authentication.png)

Há três modos de autenticação para acesso RPC.

### <a name="basic-authentication"></a>Autenticação básica

Autenticação básica usa um cabeçalho de autenticação HTTP que contém o nome de usuário e senha. Nome de usuário é o nome do nó de blockchain. Senha é definida durante o provisionamento de um membro ou um nó. A senha pode ser alterada usando o portal do Azure ou CLI.

### <a name="access-keys"></a>Chaves de acesso

As chaves de acesso usam uma cadeia de caracteres gerada aleatoriamente incluída na URL do ponto de extremidade. Duas chaves de acesso ajudam a habilitar a rotação de chaves. As chaves podem ser regeneradas do portal do Azure e da CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) usa uma declaração com base no mecanismo de autenticação em que o usuário é autenticado pelo Azure AD usando as credenciais de usuário do Azure AD. Azure AD fornece gerenciamento de identidade baseado em nuvem e permite que os clientes usem uma única identidade entre toda aplicativos enterprise e acesso na nuvem. Azure Blockchain Service integra-se com o Azure AD, permitindo a federação de identificação, única logon e autenticação multifator. Você pode atribuir usuários, grupos e funções de aplicativo em sua organização para acesso de membro e o nó de blockchain.

O proxy de cliente do AD do Azure está disponível no [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). O proxy de cliente direciona o usuário para a página de entrada do AD do Azure e obtém um token de portador após a autenticação bem-sucedida. Posteriormente, o usuário se conecta a um aplicativo de cliente Ethereum como Geth ou Truffle ao ponto de extremidade do proxy do cliente. Por fim, quando uma transação for enviada, o proxy de cliente injeta o token de portador no cabeçalho http e o proxy reverso valida o token usando o protocolo OAuth.

## <a name="keys-and-ethereum-accounts"></a>Chaves e Ethereum contas

Durante o provisionamento de um membro de serviço de Blockchain do Azure, uma conta de Ethereum e um par de chaves público e privado é gerado. A chave privada é usada para enviar as transações ao blockchain. A conta de Ethereum é os últimos 20 bytes do hash da chave pública. A conta Ethereum também é chamada um wallet.

O par de chaves público e privado é armazenado como um arquivo de chave no formato JSON. A chave privada é criptografada usando a senha inserida quando o serviço de razão de blockchain é criado.

As chaves privadas são usadas para assinar digitalmente as transações. Em particulares blockchains, um contrato inteligente assinado por uma chave privada representa a identidade do signatário. Para verificar a validade da assinatura, o receptor pode comparar a chave pública do assinante com o endereço computado da assinatura.

Chaves da Constellation são usadas para identificar exclusivamente um nó de Quorum. Chaves Constellation são geradas no momento do provisionamento de nó e são especificadas no parâmetro privateFor de uma transação privada no Quorum.

## <a name="next-steps"></a>Próximas etapas

[Configurar nós de transação de serviço do Azure Blockchain](configure-transaction-nodes.md)
