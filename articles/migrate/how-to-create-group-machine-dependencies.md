---
title: "Agrupar máquinas usando dependências de máquina com as Migrações para Azure | Microsoft Docs"
description: "Descreve como criar uma avaliação usando dependências de máquina com o serviço Migrações para Azure."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/25/2017
ms.author: raynew
ms.openlocfilehash: 720380fd14d9eaf4856ad75269a80f2b63a4725f
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Agrupar máquinas usando o mapeamento de dependências da máquina

Este artigo descreve como criar um grupo de máquinas para avaliação das [Migrações para Azure](migrate-overview.md) visualizando as dependência das máquinas. Normalmente, você usa esse método quando quer avaliar grupos de VMs com níveis mais altos de confiança por meio da verificação das dependências de máquina, antes de executar uma avaliação. A visualização de dependências pode ajudá-lo a planejar de forma eficaz sua migração para o Azure. Isso ajuda a garantir que nada seja deixado para trás e que interrupções surpresa não ocorram quando você estiver migrando para o Azure. Você pode descobrir todos os sistemas interdependentes que precisam migrar juntos, bem como identificar se um sistema em execução ainda está atendendo aos usuários ou é candidato para encerramento em vez de migração. 


## <a name="prepare-machines-for-dependency-mapping"></a>Preparar as máquinas para mapeamento de dependência
Para ver as dependências das máquinas, faça o download e instale agentes em cada máquina local que você deseja avaliar. Além disso, se houver máquinas sem conectividade com a internet, você precisará fazer o download e instalar um [gateway OMS](../log-analytics/log-analytics-oms-gateway.md) nelas.

### <a name="download-and-install-the-vm-agents"></a>Fazer o download e instalar os agente de VM
1. Em **Visão geral**, clique em **Gerenciar** > **Máquinas**e selecione a máquina exigida.
2. Na coluna **Dependências**, clique em **Instalar agentes**. 
3. Na página **Dependências**, faça o download e instale o MMA (Microsoft Monitoring Agent) e o Agente de dependência em cada VM que você deseja avaliar.
4. Copie a ID e a chave do espaço de trabalho. Você precisará delas quando instalar o MMA na máquina local.

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

## <a name="create-a-group"></a>Criar um grupo

1. Depois de instalar os agentes, acesse o portal e clique em **Gerenciar** > **Máquinas**.
2. Pesquise pela máquina na qual você instalou os agentes.
3. Agora, a coluna **Dependências** da máquina deve aparecer como **Exibir Dependências**. Clique na coluna para exibir as dependências da máquina.
4. O mapa de dependências da máquina mostra os seguintes detalhes:
    - Conexões TCP de entrada (clientes) e saída (servidores) para/da máquina
        - As máquinas dependentes que não têm o agente de dependência e o MMA instalado são agrupadas por números de porta
        - As máquinas dependentes que têm o agente de dependência e o MMA instalado são mostradas como caixas separadas 
    - Quanto aos processos em execução na máquina, você pode expandir cada caixa para exibir os processos
    - Quanto às propriedades como o nome de domínio totalmente qualificado, o sistema operacional, o endereço MAC de cada máquina, você pode clicar na caixa de cada máquina para exibir esses detalhes

 ![Exibir dependências de máquina](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Você pode procurar por dependências em períodos diferentes clicando no período no rótulo de intervalo de tempo. Por padrão, o intervalo é de uma hora. Você pode modificar o intervalo, ou especificar as datas de início e de término e a duração.
5. Depois de identificar máquinas dependentes que você deseja agrupar, use Ctrl+Clique para selecionar várias máquinas no mapa e clique em **Agrupar máquinas**.
6. Especifique um nome de grupo. Verifique se as máquinas dependentes foram descobertas pelas Migrações para Azure. 

    > [!NOTE]
    > Se uma máquina dependente não for descoberta pelas Migrações para Azure, você não poderá adicioná-lo ao grupo. Para adicionar essas máquinas ao grupo, você precisa executar o processo de descoberta novamente com o escopo correto no vCenter Server e certificar-se de que a máquina seja descoberta pelas Migrações para Azure.  

7. Se quiser criar uma avaliação para esse grupo, marque a caixa de seleção para criar uma nova avaliação para o grupo.
8. Clique em **OK** para salvar o grupo.

Após o grupo ser criado, é recomendável instalar agentes em todas as máquinas do grupo e refinar o grupo visualizando a dependência de todo o grupo.

## <a name="next-steps"></a>Próximas etapas

- [Saiba como](how-to-create-group-dependencies.md) refinar o grupo visualizando as dependências do grupo
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
