---
title: "Integração do data center do Azure pilha - segurança"
description: "Saiba como integrar a segurança da pilha do Azure com a segurança de seu datacenter"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/17/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: eb7c651362838d44d6558e080e6130b4a8041d1e
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---security"></a>Integração do data center do Azure pilha - segurança

*Aplica-se a: sistemas integrados de pilha do Azure*

A pilha do Azure foi projetada e criada com base na segurança. Pilha do Azure é um sistema de bloqueio, portanto não há suporte para a instalação do agente de segurança de software.

Este artigo ajuda você a integrar os recursos de segurança da pilha do Azure com as soluções de segurança já implantadas em seu data center.

## <a name="security-logs"></a>Logs de segurança

Pilha do Azure coleta eventos de segurança para funções de infraestrutura e nós de unidade de escala e de sistema operacional a cada dois minutos. Os logs são armazenados em contêineres de blob da conta de armazenamento.

Há uma conta de armazenamento por função de infraestrutura e uma conta de armazenamento geral para todos os eventos de sistema operacional típico.

O provedor de recursos de integridade pode ser chamado por meio do protocolo REST para recuperar a URL para o contêiner de blob. Soluções de segurança de terceiros podem usar as contas de armazenamento e de API para recuperar eventos para processamento.

### <a name="use-azure-storage-explorer-to-view-events"></a>Usar o Azure Storage Explorer para exibir eventos

Você pode recuperar eventos coletados pela pilha do Azure usando uma ferramenta chamada Gerenciador de armazenamento do Azure. Você pode baixar o Azure Storage Explorer do [http://storageexplorer.com](http://storageexplorer.com).

O procedimento a seguir é um exemplo que você pode usar para configurar o Azure Storage Explorer para a pilha do Azure:

1. Entre no portal de administrador a pilha do Azure como um operador.
2. Procurar **contas de armazenamento** e procure **frphealthaccount**. O **frphealthaccount** é a conta de armazenamento geral usada para armazenar todos os eventos do sistema operacional.

   ![Contas de armazenamento](media/azure-stack-integrate-security/storage-accounts.png)

3. Selecione **frphealthaccount**, em seguida, clique em **chaves de acesso**.

   ![Chaves de acesso](media/azure-stack-integrate-security/access-keys.png)

4. Copie a chave de acesso para sua área de transferência.
5. Abra o Gerenciador de Armazenamento do Azure.
6. Sobre o **editar** menu, selecione **pilha do Azure de destino**.
7. Selecione **adicionar conta**e, em seguida, selecione **usar um nome de conta de armazenamento e chave**.

   ![Conectar o armazenamento](media/azure-stack-integrate-security/connect-storage.png)

8. Clique em **Avançar**.
9. Sobre o **anexar armazenamento externo** página:

   a. Digite o nome da conta **frphealthaccount**.

   b. Cole a chave de acesso da conta de armazenamento.

   c. Em **domínio de pontos de extremidade de armazenamento**, selecione **outros**e especifique o ponto de extremidade de armazenamento **[Região]. [ DomainName]**.

   d. Selecione o **usar HTTP** caixa de seleção.

   ![Anexar armazenamento externo](media/azure-stack-integrate-security/attach-storage.png)

10. Clique em **próximo**, revise o resumo e **concluir** o assistente.
11. Agora você pode procurar os contêineres de blob individuais e baixar os eventos.

   ![Procure blobs](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Usar linguagens de programação para eventos de acesso

Você pode usar várias linguagens de programação para acessar uma conta de armazenamento. Use a seguinte documentação para obter um exemplo que corresponde a seu idioma:

[https://Azure.microsoft.com/resources/Samples/?Term=Storage+Account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Auditoria de acesso do dispositivo

Todos os dispositivos físicos na pilha do Azure suportam o uso de TACACS ou RADIUS. Isso inclui o acesso ao controlador de gerenciamento da placa-base (BMC) e comutadores de rede.

Soluções de pilha do Azure não são fornecidos com RADIUS ou TACACS interno. No entanto, as soluções foram validadas para suporte ao uso de RADIUS ou TACACS soluções existentes disponíveis no mercado.

Para RADIUS, MSCHAPv2 foi validado. Isso representa a implementação mais segura usando o RADIUS.
Consulte seu fornecedor de hardware do OEM para habilitar TACAS ou RADIUS em dispositivos incluídos com sua solução de pilha do Azure.

## <a name="syslog"></a>syslog

Todos os dispositivos físicos na pilha do Azure podem enviar mensagens de Syslog. Soluções de pilha do Azure não são fornecidos com um servidor Syslog. No entanto, as soluções foram validadas para dar suporte a envio de mensagens a soluções existentes do Syslog disponíveis no mercado.

O endereço de destino do Syslog é um parâmetro opcional coletado para implantação, mas ele também pode ser adicionado após a implantação.

## <a name="next-steps"></a>Próximas etapas

[Azure pilha datacenter integração - publicar pontos de extremidade](azure-stack-integrate-endpoints.md)
