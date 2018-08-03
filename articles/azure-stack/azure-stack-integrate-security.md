---
title: Integração do datacenter do Azure Stack - segurança
description: Saiba como integrar a segurança do Azure Stack com segurança seu datacenter
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 9f356b814ac1ac6ca8b6d6efe7cb9f5d9ed66270
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442468"
---
# <a name="azure-stack-datacenter-integration---security"></a>Integração do datacenter do Azure Stack - segurança
O Azure Stack foi projetado e criado tendo em mente a segurança. O Azure Stack é um sistema fechado, portanto, não há suporte para a instalação do agente de segurança de software.

Este artigo ajuda você a integrar os recursos de segurança do Azure Stack com as soluções de segurança já implantadas em seu datacenter.

## <a name="security-logs"></a>Logs de segurança

O Azure Stack coleta eventos de segurança para funções de infraestrutura e os nós de unidade de escala e de sistema operacional a cada dois minutos. Os logs são armazenados em contêineres de blob da conta de armazenamento.

Há uma conta de armazenamento por função de infraestrutura e uma conta de armazenamento geral para todos os eventos de sistema de operacional típico.

O provedor de recursos de integridade pode ser chamado por meio do protocolo REST para recuperar a URL para o contêiner de blob. Soluções de segurança de terceiros podem usar as contas de armazenamento e de API para recuperar eventos para processamento.

### <a name="use-azure-storage-explorer-to-view-events"></a>Use o Gerenciador de armazenamento do Azure para exibir eventos

Você pode recuperar os eventos coletados pela pilha do Azure usando uma ferramenta chamada Gerenciador de armazenamento do Azure. Você pode baixar o Gerenciador de armazenamento do Azure do [ http://storageexplorer.com ](http://storageexplorer.com).

O procedimento a seguir está um exemplo que você pode usar para configurar o Gerenciador de armazenamento do Azure para o Azure Stack:

1. Entre no portal do administrador do Azure Stack como um operador.
1. Navegue **contas de armazenamento** e procure **frphealthaccount**. O **frphealthaccount** é a conta de armazenamento geral usada para armazenar todos os eventos do sistema operacional.

   ![Contas de armazenamento](media/azure-stack-integrate-security/storage-accounts.png)

1. Selecione **frphealthaccount**, em seguida, clique em **chaves de acesso**.

   ![Chaves de acesso](media/azure-stack-integrate-security/access-keys.png)

1. Copie a chave de acesso para a área de transferência.
1. Abra o Gerenciador de Armazenamento do Azure.
1. Sobre o **edite** menu, selecione **destino Azure Stack**.
1. Selecione **adicionar conta**e, em seguida, selecione **usar um nome de conta de armazenamento e a chave**.

   ![Conectar ao armazenamento](media/azure-stack-integrate-security/connect-storage.png)

1. Clique em **Próximo**.
1. Sobre o **anexar armazenamento externo** página:

   a. Digite o nome da conta **frphealthaccount**.

   b. Cole a chave de acesso da conta de armazenamento.

   c. Sob **domínio de pontos de extremidade de armazenamento**, selecione **outras**e especifique o ponto de extremidade de armazenamento **[Região]. [ DomainName]**.

   d. Selecione o **usar HTTP** caixa de seleção.

   ![Anexar armazenamento externo](media/azure-stack-integrate-security/attach-storage.png)

1. Clique em **próxima**, examine o resumo e **concluir** o assistente.
1. Agora você pode procurar os contêineres de blob individuais e baixar os eventos.

   ![Procurar blobs](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Usar linguagens de programação para eventos de acesso

Você pode usar várias linguagens de programação para acessar uma conta de armazenamento. Use a documentação a seguir para escolher um exemplo que corresponde ao seu idioma:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Auditoria de acesso do dispositivo

Todos os dispositivos físicos no Azure Stack suportam o uso de TACACS ou RADIUS. Isso inclui acesso ao controlador de gerenciamento de placa-base (BMC) e comutadores de rede.

Soluções de pilha do Azure não são fornecidos com o RADIUS ou TACACS interno. No entanto, as soluções foram validadas para suporte ao uso do RADIUS ou TACACS soluções existentes disponíveis no mercado.

Para RADIUS, MSCHAPv2 foi validada. Isso representa a implementação mais segura usando RADIUS.
Consulte seu fornecedor de hardware de OEM para habilitar o TACAS ou RADIUS os dispositivos incluídos com sua solução do Azure Stack.

## <a name="syslog"></a>syslog

Todos os dispositivos físicos no Azure Stack podem enviar mensagens do Syslog. Soluções de pilha do Azure não são fornecidos com um servidor Syslog. No entanto, as soluções foram validadas para dar suporte a envio de mensagens a soluções existentes do Syslog disponíveis no mercado.

O endereço de destino de Syslog é um parâmetro opcional coletado para a implantação, mas ele também pode ser adicionado após a implantação.

## <a name="next-steps"></a>Próximas etapas

[Política de manutenção](azure-stack-servicing-policy.md)
