---
title: "Refinar um grupo de avaliação com o mapeamento do grupo de dependência nas Migrações para Azure | Microsoft Docs"
description: "Descreve como refinar uma avaliação usando o mapeamento de dependência de grupo no serviço Migrações para Azure."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/22/2017
ms.author: raynew
ms.openlocfilehash: 3b10765894501791004e3a9221363f196cc0c91d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Refinar um grupo usando o mapeamento de dependências de grupo

Este artigo descreve como refinar a um grupo visualizando as dependências de todas as máquinas no grupo. Normalmente, você usa esse método quando quer refinar as associações de um grupo existente, verificando as dependências do grupo, antes de executar uma avaliação. Refinar um grupo usando a visualização de dependências pode ajudar a planejar com eficácia a migração para o Azure. Você pode descobrir todos os sistemas interdependentes que precisam migrar juntos. Isso ajuda a garantir que nada seja deixado para trás e que interrupções surpresa não ocorram quando você estiver migrando para o Azure. 


> [!NOTE]
> Os grupos para os quais você deseja visualizar dependências de grupo não devem conter mais de 10 máquinas. Caso você tenha mais de 10 máquinas no grupo, recomendamos que o divida em grupos menores para aproveitar a funcionalidade de visualização de dependências.


# <a name="prepare-the-group-for-dependency-visualization"></a>Preparar o grupo para visualização de dependências
Para ver as dependências de um grupo, você precisa baixar e instalar agentes em cada computador local que fizer parte do grupo. Além disso, se houver máquinas sem conectividade com a internet, você precisará fazer o download e instalar um [gateway OMS](../log-analytics/log-analytics-oms-gateway.md) nelas.

### <a name="download-and-install-the-vm-agents"></a>Fazer o download e instalar os agente de VM
1. Em **Visão Geral**, clique em **Gerenciar** > **Grupos** e vá para o grupo necessário.
2. Na lista de computadores, na coluna **Agente de dependência**, clique em **Requer instalação** para ver instruções sobre como baixar e instalar os agentes.
3. Na página **Dependências**, faça baixe e instale o MMA (Microsoft Monitoring Agent) e o Agente de dependência em cada VM que faz parte do grupo.
4. Copie a ID e a chave do espaço de trabalho. Você precisará delas quando instalar o MMA nas máquinas locais.

### <a name="install-the-mma"></a>Instalar o MMA

Para instalar o agente em uma máquina com Windows:

1. Clique duas vezes no agente baixado.
2. Na página de **Boas-vindas**, clique em **Avançar**. Na página **Termos de Licença**, clique em **Concordo** para aceitar a licença.
3. Em **Pasta de Destino**, mantenha ou modifique a pasta de instalação padrão > **Avançar**. 
4. Em **Opções de Instalação do Agente**, selecione **Azure Log Analytics (OMS)** > **Avançar**. 
5. Clique em **Adicionar** para adicionar um novo espaço de trabalho OMS. Cole a ID do espaço de trabalho e a chave que você copiou do portal. Clique em **Próximo**.


Para instalar o agente em uma máquina com Linux:

1. Transfira o pacote apropriado (x86 ou x64) para seu computador Linux usando scp/sftp.
2. Instale o pacote usando o argumento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Instalar o Agente de dependência
1. Para instalar o Agente de dependência em uma máquina com Windows, clique duas vezes no arquivo de instalação e siga o assistente.
2. Para instalar o Agente de dependência em uma máquina com Linux, instale como raiz usando o comando a seguir:

    ```sh InstallDependencyAgent-Linux64.bin```

[Saiba mais](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) sobre sistemas operacionais com suporte do Agente de dependência. 

## <a name="refine-the-group-based-on-dependency-visualization"></a>Refinar o grupo com base na visualização de dependências
Depois de instalar os agentes em todas as máquinas do grupo, você pode visualizar as dependências do grupo e refiná-lo seguindo as etapas abaixo.

1. No projeto das Migrações para Azure, em **Gerenciar**, clique em  **Grupos** e selecione o grupo.
2. Na página do grupo, clique em  **Exibir Dependências**, para abrir o mapa de dependências de grupo.
3. O mapa de dependências do grupo mostra os seguintes detalhes:
    - Conexões TCP de entrada (clientes) e saída (servidores) para/de todos os computadores que fazem parte do grupo
        - As máquinas dependentes que não têm o agente de dependência e o MMA instalado são agrupadas por números de porta
        - As máquinas dependentes que têm o agente de dependência e o MMA instalado são mostradas como caixas separadas 
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

[Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
