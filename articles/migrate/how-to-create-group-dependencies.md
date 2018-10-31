---
title: Refinar um grupo de avaliação com o mapeamento do grupo de dependência nas Migrações para Azure | Microsoft Docs
description: Descreve como refinar uma avaliação usando o mapeamento de dependência de grupo no serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/25/2018
ms.author: raynew
ms.openlocfilehash: 2d2688799b1a0b4518b9c91bbc530936c834c5e3
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407429"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Refinar um grupo usando o mapeamento de dependências de grupo

Este artigo descreve como refinar a um grupo visualizando as dependências de todas as máquinas no grupo. Normalmente, você usa esse método quando quer refinar as associações de um grupo existente, verificando as dependências do grupo, antes de executar uma avaliação. Refinar um grupo usando a visualização de dependências pode ajudá-lo a planejar de forma eficaz sua migração para o Azure. Você pode descobrir todos os sistemas interdependentes que precisam migrar juntos. Isso ajuda a garantir que nada seja deixado para trás e que interrupções surpresa não ocorram quando você estiver migrando para o Azure.


> [!NOTE]
> Os grupos para os quais você deseja visualizar dependências de grupo não devem conter mais de 10 máquinas. Caso você tenha mais de 10 máquinas no grupo, recomendamos que o divida em grupos menores para aproveitar a funcionalidade de visualização de dependências.


## <a name="prepare-for-dependency-visualization"></a>Preparar para visualização de dependências
As Migrações para Azure utilizam a solução Mapa do Serviço no Log Analytics para habilitar a visualização de dependências dos computadores.

### <a name="associate-a-log-analytics-workspace"></a>Associar um workspace do Log Analytics
Para aproveitar a visualização de dependência, você precisa associar um workspace novo ou existente do Log Analytics a um projeto das Migrações para Azure. Você só pode criar ou anexar um workspace na mesma assinatura em que o projeto de migração é criado.

- Para anexar um workspace do Log Analytics a um projeto, em **Visão geral**, acesse a seção **Essenciais** do projeto e clique em **Requer configuração**

    ![Associar o workspace do Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Ao criar um workspace, você precisará especificar um nome para ele. O workspace será criado na mesma assinatura que o projeto de migração e em uma região na mesma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que o projeto de migração.
- A opção **Usar existente** lista apenas os workspaces criados em regiões em que o Mapa do Serviço está disponível. Se você tiver um workspace em uma região em que o Mapa do Serviço não está disponível, ele não será listado na lista suspensa.

> [!NOTE]
> Não é possível alterar o workspace associado a um projeto de migração.

### <a name="download-and-install-the-vm-agents"></a>Fazer o download e instalar os agente de VM
Para ver as dependências de um grupo, você precisa baixar e instalar agentes em cada computador local que fizer parte do grupo. Além disso, se você tiver máquinas sem conectividade com a Internet, será necessário fazer o download e instalar o [gateway do Log Analytics](../log-analytics/log-analytics-oms-gateway.md) nelas.

1. Em **Visão Geral**, clique em **Gerenciar** > **Grupos** e vá para o grupo necessário.
2. Na lista de computadores, na coluna **Agente de dependência**, clique em **Requer instalação** para ver instruções sobre como baixar e instalar os agentes.
3. Na página **Dependências**, faça baixe e instale o MMA (Microsoft Monitoring Agent) e o Agente de dependência em cada VM que faz parte do grupo.
4. Copie a ID e a chave do workspace. Você precisará delas quando instalar o MMA nas máquinas locais.

### <a name="install-the-mma"></a>Instalar o MMA

Para instalar o agente em uma máquina com Windows:

1. Clique duas vezes no agente baixado.
2. Na página de **Boas-vindas**, clique em **Avançar**. Na página **Termos de Licença**, clique em **Concordo** para aceitar a licença.
3. Em **Pasta de Destino**, mantenha ou modifique a pasta de instalação padrão > **Avançar**.
4. Em **Opções de Configuração do Agente**, selecione **Azure Log Analytics** > **Avançar**.
5. Clique em **Adicionar** para adicionar um workspace do Log Analytics. Cole a ID do workspace e a chave que você copiou do portal. Clique em **Próximo**.


Para instalar o agente em uma máquina com Linux:

1. Transfira o pacote apropriado (x86 ou x64) para seu computador Linux usando scp/sftp.
2. Instale o pacote usando o argumento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Instalar o Agente de dependência
1. Para instalar o Agente de dependência em uma máquina com Windows, clique duas vezes no arquivo de instalação e siga o assistente.
2. Para instalar o Agente de dependência em uma máquina com Linux, instale como raiz usando o comando a seguir:

    ```sh InstallDependencyAgent-Linux64.bin```

Saiba mais sobre o suporte do agente de Dependência para os sistemas operacionais [Windows](../monitoring/monitoring-service-map-configure.md#supported-windows-operating-systems) e [Linux](../monitoring/monitoring-service-map-configure.md#supported-linux-operating-systems).

## <a name="refine-the-group-based-on-dependency-visualization"></a>Refinar o grupo com base na visualização de dependências
Depois de instalar os agentes em todas as máquinas do grupo, você pode visualizar as dependências do grupo e refiná-lo seguindo as etapas abaixo.

1. No projeto das Migrações para Azure, em **Gerenciar**, clique em  **Grupos** e selecione o grupo.
2. Na página do grupo, clique em  **Exibir Dependências**, para abrir o mapa de dependências de grupo.
3. O mapa de dependências do grupo mostra os seguintes detalhes:
    - Conexões TCP de entrada (clientes) e saída (servidores) para/de todos os computadores que fazem parte do grupo
        - As máquinas dependentes que não têm o agente de dependência e o MMA instalado são agrupadas por números de porta
        - As máquinas dependentes que têm o agente de dependência e o MMA instalados aparecem como caixas separadas
    - Quanto aos processos em execução na máquina, você pode expandir cada caixa para exibir os processos
    - Quanto às propriedades como o nome de domínio totalmente qualificado, o sistema operacional, o endereço MAC de cada máquina, você pode clicar na caixa de cada máquina para exibir esses detalhes

     ![Exibir dependências de grupo](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Para exibir dependências mais granulares, clique no intervalo de tempo para modificá-las. Por padrão, o intervalo é de uma hora. Você pode modificar o intervalo, ou especificar as datas de início e de término e a duração.
4. Verifique as máquinas dependentes, o processo em execução dentro de cada máquina e identifique as máquinas que devem ser adicionadas ou removidas do grupo.
5. Use Ctrl+Clique para selecionar máquinas no mapa para adicionar ou remover do grupo.
    - Você só pode adicionar máquinas que foram descobertas.
    - Adicionar e remover máquinas de um grupo invalida as últimas avaliações dele.
    - Opcionalmente, você pode criar uma nova avaliação ao modificar o grupo.
5. Clique em **OK** para salvar o grupo.

    ![Adicionar ou remover máquinas](./media/how-to-create-group-dependencies/add-remove.png)

Se você quiser verificar as dependências de uma máquina específica que é exibida no mapa de dependência de grupo, [configure o mapeamento de dependência da máquina](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Próximas etapas
- [Saiba mais](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) sobre as perguntas frequentes na visualização de dependência.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
