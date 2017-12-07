---
title: "Agrupar máquinas usando dependências de máquina com as Migrações para Azure | Microsoft Docs"
description: "Descreve como criar uma avaliação usando dependências de máquina com o serviço Migrações para Azure."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 5ed49f3dc71af6a8c1c7b6c9e38fd84452505aec
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Agrupar máquinas usando o mapeamento de dependências da máquina

Este artigo descreve como criar um grupo de máquinas para avaliação das [Migrações para Azure](migrate-overview.md) usando o mapeamento de dependência de máquina. Normalmente, você usa esse método quando quer avaliar grupos de VMs com níveis mais altos de confiança por meio da verificação das dependências de máquina, antes de executar uma avaliação.



## <a name="prepare-machines-for-dependency-mapping"></a>Preparar as máquinas para mapeamento de dependência
Para incluir máquinas no mapeamento de dependência, faça o download e instale agentes em cada máquina local que você deseja avaliar. Além disso, se houver máquinas sem conectividade com a internet, você precisará fazer o download e instalar um [gateway OMS](../log-analytics/log-analytics-oms-gateway.md) nelas.

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
5. Clique em **Adicionar** para adicionar um novo espaço de trabalho OMS. Cole a ID do espaço de trabalho e a chave que você copiou do portal. Clique em **Avançar**.


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
2. Agora, a coluna **Dependências** deve aparecer como **Exibir Dependências**. Clique na coluna para exibir as dependências.
3. Para cada máquina, você pode verificar:
    - Se o MMA e o agente de dependência estão instalados, e se a máquina foi descoberta.
    - O sistema operacional convidado em execução na máquina.
    - Portas e conexões IP de entrada e saída.
    - Processos em execução nas máquinas.
    - Dependências entre máquinas.

4. Para dependências mais granulares, clique no intervalo de tempo para modificá-las. Por padrão, o intervalo é de uma hora. Você pode modificar o intervalo, ou especificar as datas de início e de término e a duração.
5. Depois de identificar máquinas dependentes que você deseja agrupar, selecione as máquinas no mapa e clique em **Agrupar máquinas**.
6. Especifique um nome de grupo. Verifique se a máquina foi descoberta pelas Migrações para Azure. Se não tiver sido, execute novamente o processo de descoberta no local. Se quiser, você pode executar uma avaliação imediatamente.
7. Clique em **OK** para salvar o grupo.

    ![Crie um grupo com dependências de máquina](./media/how-to-create-group-machine-dependencies/create-group.png)

## <a name="next-steps"></a>Próximas etapas

- [Saiba como](how-to-create-group-dependencies.md) refinar o grupo verificando as dependências do grupo
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
