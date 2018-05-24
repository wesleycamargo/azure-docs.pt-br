---
title: Firewall local de Sincronização de Arquivos do Azure e configurações de proxy | Microsoft Docs
description: Configuração da rede local de Sincronização de Arquivos do Azure
services: storage
documentationcenter: ''
author: fauhse
manager: klaasl
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 979897e3cb703b36a46e96848a9176d6d4c6cc6a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072259"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Configurações de proxy e firewall da Sincronização de arquivos do Azure
A Sincronização de arquivos do Azure se conecta seus servidores locais para arquivos do Azure, permitindo camadas de recursos de nuvem e sincronização de vários locais. Como tal, um servidor local deve estar conectado à internet. Um administrador de TI precisa decidir o melhor caminho para o servidor acessar os serviços de nuvem do Azure.

Este artigo fornecerá informações sobre requisitos específicos e as opções disponíveis para conectar com êxito e segurança o servidor de Sincronização de Arquivos do Azure.

> [!Important]
> A Sincronização de Arquivos do Azure ainda não suporta firewalls e redes virtuais para uma conta de armazenamento. 

## <a name="overview"></a>Visão geral
A Sincronização de Arquivos do Azure atua como um serviço de coordenação entre o Windows Server, seu compartilhamento de arquivos do Azure e vários outros serviços do Azure para sincronizar os dados conforme descrito em seu grupo de sincronização. Para que a Sincronização de Arquivos do Azure funcione corretamente, você precisará configurar seus servidores para se comunicar com os seguintes serviços do Azure:

- Armazenamento do Azure
- Sincronização de Arquivos do Azure
- Azure Resource Manager
- Serviços de Autenticação

> [!Note]  
> O agente de Sincronização de Arquivos do Azure no Windows Server inicia todas as solicitações para serviços que resultam em apenas ter que considerar o tráfego de saída de uma perspectiva de firewall de nuvem. <br /> Nenhum serviço do Azure inicia uma conexão com o agente de sincronização de arquivos do Azure.


## <a name="ports"></a>Portas
A Sincronização de Arquivos do Azure move metadados e dados de arquivos exclusivamente via HTTPS e exige que a porta 443 tenha a saída aberta.
Como resultado, todo o tráfego é criptografado.

## <a name="networks-and-special-connections-to-azure"></a>Redes e conexões especiais para o Azure
O agente de Sincronização de Arquivos do Azure não tem requisitos sobre canais especiais como [ExpressRoute](../../expressroute/expressroute-introduction.md), etc. para o Azure.

A Sincronização de Arquivos do Azure funcionará por meios disponíveis que permitem o alcance no Azure, automaticamente se adaptando a várias características de rede com largura de banda, latência, bem como oferecendo controle de administração para ajustar. Alguns recursos não estão disponíveis neste momento. Se você quiser configurar o comportamento específico, fale por meio do [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
A Sincronização de Arquivos do Azure atualmente oferece suporte a configurações de proxy de todo o computador. Essa configuração de proxy é transparente para o agente de Sincronização de Arquivos do Azure como o tráfego inteiro do servidor é roteado através desse proxy.

As configurações de proxy de aplicativo específicas estão em desenvolvimento e terão suporte em uma futura versão do agente de Sincronização de Arquivos do Azure. Isso permitirá a configuração de um proxy especificamente para o tráfego de sincronização de arquivos do Azure.

## <a name="firewall"></a>Firewall
Conforme mencionado em uma seção anterior, a porta 443 precisa estar com a saída aberta. Com base em políticas em seu banco de dados, a ramificação ou a região, restringir ainda mais o tráfego por essa porta a domínios específicos pode ser desejado ou necessário.

A tabela a seguir descreve os domínios necessários para a comunicação:

| Serviço | Domínio | Uso |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Qualquer chamada de usuário (como o PowerShell) passa por essa URL, incluindo a chamada de registro inicial do servidor. |
| **Azure Active Directory** | https://login.windows.net | As chamadas do Azure Resource Manager devem ser feitas por um usuário autenticado. Para ter êxito, essa URL é usada para autenticação do usuário. |
| **Azure Active Directory** | https://graph.windows.net/ | Como parte da implantação de Sincronização de Arquivos do Azure, será criado um objeto de serviço do Azure Active Directory da assinatura. Essa URL é usada para fazer isso. Essa entidade de segurança é usada para a delegação de um conjunto mínimo de direitos para o Serviço de Sincronização de Arquivos do Azure. O usuário que estiver executando a configuração inicial de Sincronização de Arquivos do Azure deve ser um usuário autenticado com privilégios de proprietário da assinatura. |
| **Armazenamento do Azure** | &ast;.core.windows.net | Quando o servidor baixa um arquivo, o servidor executa essa movimentação de dados de forma mais eficiente quando se comunica diretamente com o Compartilhamento de Arquivos do Azure na Conta de Armazenamento. O servidor tem uma chave SAS que só permite o acesso de compartilhamento do arquivo de destino. |
| **Sincronização de Arquivos do Azure** | &ast;. one.microsoft.com | Após o registro do servidor inicial, o servidor recebe uma URL regional para a instância do serviço de Sincronização de Arquivos do Azure nessa região. O servidor pode usar a URL para se comunicar de forma direta e eficiente com a instância de tratando sua sincronização. |

> [!Important]
> Ao permitir o tráfego para o &ast;. one.microsoft.com, o tráfego para mais do que apenas o serviço de sincronização é possível a partir do servidor. Há muitos mais serviços da Microsoft nos subdomínios.

Se o &ast;. one.microsoft.com for muito grande, você pode limitar a comunicação do servidor, permitindo  apenas instâncias explícitas regionais do serviço de Sincronização de Arquivos do Azure. Quais instâncias de escolha dependem da região do Serviço de Sincronização de Armazenamento ao qual você tenha implantado e registrado o servidor. Essa é a que você precisa permitir para o servidor. Assim haverá mais URLs para habilitar novos recursos de continuidade de negócios. 

| Região | URL de ponto de extremidade regional da Sincronização de Arquivos do Azure |
|--------|---------------------------------------|
| Leste da Austrália | https://kailani-aue.one.microsoft.com |
| Canadá Central | https://kailani-cac.one.microsoft.com |
| Leste dos EUA | https://kailani1.one.microsoft.com |
| Sudeste Asiático | https://kailani10.one.microsoft.com |
| Sul do Reino Unido | https://kailani-uks.one.microsoft.com |
| Europa Ocidental | https://kailani6.one.microsoft.com |
| Oeste dos EUA | https://kailani.one.microsoft.com |

> [!Important]
> Se você definir essas regras de firewall detalhadas, consulte este documento com frequência e atualize suas regras de firewall para evitar interrupções de serviço devido a  listagens de URL incompletas ou desatualizadas em suas configurações de firewall.

## <a name="summary-and-risk-limitation"></a>Resumo e limitação de risco
As listas no início deste documento contém as URLs de Sincronização de Arquivos do Azure com que está se comunicando. Os firewalls devem poder permitir o tráfego de saída para esses domínios, bem como as respostas deles. A Microsoft se esforça para manter essa lista atualizada.

A configuração das regras de firewall de restrição de domínio pode ser uma medida para melhorar a segurança. Se essas configurações de firewall são usadas, é necessário ter em mente que as URLs serão adicionadas e alteradas ao longo do tempo. Portanto, é uma medida prudente verificar as tabelas neste documento como parte de um processo de gerenciamento de alterações de uma versão do agente de Sincronização de Arquivos do Azure para outro em uma implantação de teste do agente mais recente. Dessa forma, você pode garantir que seu firewall seja configurado para permitir o tráfego para os domínios que o agente mais recente requer.

## <a name="next-steps"></a>Próximas etapas
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Implantar a Sincronização de arquivos do Azure (versão prévia)](storage-sync-files-deployment-guide.md)
