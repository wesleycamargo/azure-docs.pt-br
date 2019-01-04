---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: b099f5ff7e43f2deeb3b8c41adcb802cd431a65a
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635996"
---
O agente de Sincronização de arquivos do Azure é atualizado regularmente para adicionar novos recursos e resolver problemas. Recomendamos que você configure o Microsoft Update para obter atualizações para o agente de Sincronização de arquivos do Azure à medida que elas ficarem disponíveis.

#### <a name="major-vs-minor-agent-versions"></a>Versões do agente principal versus secundário
* As versões do agente principal geralmente contêm novos recursos e têm um número crescente como a primeira parte do número da versão. Por exemplo:  *2.\*.\**
* As versões do agente secundário também são chamadas de "patches" e são lançadas com mais frequência do que as versões do principal. Geralmente contêm correções de bug e pequenas melhorias, mas sem novos recursos. Por exemplo: *\*.3.\**

#### <a name="upgrade-paths"></a>Caminhos de atualização
Há quatro maneiras aprovadas e testadas para instalar as atualizações do agente de Sincronização de Arquivos do Azure. 
1. **(Preferencial) Configure o Microsoft Update para baixar e instalar automaticamente as atualizações do agente.**  
    É sempre recomendável executar todas as atualizações de Sincronização de arquivos do Azure para garantir que você tenha acesso às últimas correções para o Server Agent. O Microsoft Update simplifica esse processo, baixando e instalando atualizações automaticamente para você.
2. **Use AfsUpdater.exe para baixar e instalar atualizações de agente.**  
    O AfsUpdater.exe está localizado no diretório de instalação do agente. Clique duas vezes no executável para baixar e instalar atualizações de agente. 
3. **Corrigir um agente existente de Sincronização de arquivos do Azure utilizando um arquivo de patch do Microsoft Update ou um executável .msp. O último pacote de atualização de Sincronização de arquivos do Azure pode ser baixado no [Catálogo do Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Executar um executável .msp atualizará a instalação de Sincronização de arquivos do Azure com o mesmo método usado automaticamente pelo Microsoft Update no caminho de atualização anterior. A aplicação de um patch do Microsoft Update realizará uma atualização no local de uma instalação e Sincronização de arquivos do Azure.
4. **Baixe o instalador do agente mais recente de Sincronização de arquivos do Azure no [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). O download do instalador é um pacote do Microsoft Windows Installer ou um executável .msi.**   
    Para fazer upgrade de uma instalação existente do agente de Sincronização de Arquivos do Azure, desinstale a versão mais antiga e então instale a última versão do instalador baixado. O registro do servidor, os grupos de sincronização e outras configurações são mantidos pelo instalador de Sincronização de arquivos do Azure.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garantia de gerenciamento de alterações e ciclo de vida do agente
A Sincronização de arquivos do Azure é um serviço de nuvem que permite a introdução contínua de novos recursos e funcionalidades. Isso significa que uma versão específica do agente de Sincronização de arquivos do Azure somente poderá ter suporte por um tempo limitado. Para facilitar a implantação, as regras a seguir garantem que você tenha tempo e notificações suficientes para acomodar upgrades/atualizações do agente no processo de gerenciamento de alterações:

- As versões do agente principal terão suporte por pelo menos seis meses, a partir da data da versão inicial.
- Garantimos que há uma sobreposição de pelo menos três meses entre o suporte das versões do agente principal. 
- Avisos para servidores registrados que utilizam um agente que expirará em breve serão emitidos pelo menos três meses antes da expiração. É possível verificar se um servidor registrado está usando uma versão mais antiga do agente na seção de servidores registrados de um Serviço de Sincronização de Armazenamento.
- O tempo de vida de uma versão do agente secundário está associado à versão principal associada. Por exemplo, quando a versão do agente 3.0 for lançada, as versões do agente 2.\* serão todas definidas para expirar conjuntamente.

> [!Note]
> A instalação de uma versão do agente com um aviso de expiração exibirá um aviso, porém com êxito. A tentativa de instalar ou conectar uma versão do agente expirada não tem suporte e será bloqueada.
