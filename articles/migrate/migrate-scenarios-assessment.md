---
title: Avaliar cargas de trabalho locais que podem ser migradas para o Azure com o DMA e as Migrações para Azure | Microsoft Docs
description: Saiba como preparar o Azure para a migração de computadores locais usando o DMA (Assistente de Migração de Dados) e o serviço Migrações para Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 33e31c47a6125ac363410a9a78e9c9310c74d51e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>Cenário 1: avaliar cargas de trabalho locais que podem ser migradas para o Azure

Enquanto estão pensando em fazer a migração para o Azure, a empresa Contoso deseja executar uma avaliação técnica e financeira para descobrir se as cargas de trabalho local são adequadas para a migração para a nuvem. Mais especificamente, eles querem avaliar a compatibilidade de computadores e banco de dados para migração e estimar a capacidade e os custos de execução de seus recursos no Azure.

Para experimentarem e entenderem melhor as tecnologias envolvidas, eles vão avaliar e migrar um pequeno aplicativo de viagens local. É um aplicativo de duas camadas, com um aplicativo Web em execução em uma VM e um banco de dados do SQL Server em uma segunda VM. O aplicativo é implantado no VMware e o ambiente é gerenciado por um vCenter Server. Ele fará a avaliação usando o DMA (Assistente de Migração de Dados) e o serviço Migrações para Azure.

**Tecnologia** | **Descrição** | **Custo**
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | O DMA avalia e detecta problemas de compatibilidade que podem afetar a funcionalidade do banco de dados no Azure. Além disso, ele avalia a paridade de recursos entre os SQL Servers de origem e de destino e recomenda melhorias de desempenho e confiabilidade para seu ambiente de destino. | É uma ferramenta que pode ser baixada gratuitamente. 
[Migrações para Azure](https://docs.microsoft.com/azure/migrate/migrate-overview) | O serviço ajuda a avaliar os computadores locais que devem ser migrados para o Azure. Ele avalia a adequação da migração dos computadores e fornece estimativas de dimensionamento e custo para a execução no Azure. No momento, o serviço Migrações para Azure pode avaliar máquinas virtuais VMware locais que podem ser migradas o Azure. | No momento (abril de 2018), não há custo adicional para usar esse serviço.
[Mapa do Serviço](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | As Migrações para Azure usam o Mapa do Serviço para mostrar as dependências entre computadores que você deseja migrar. |  O Mapa do Serviço é parte do Azure Log Analytics. Ele pode ser usado gratuitamente por 180 dias. 

Neste cenário, vamos baixar e executar o DMA para avaliar o banco de dados do SQL Server local do nosso aplicativo de viagem. Vamos usar as Migrações para Azure com o mapeamento de dependências para avaliar as VMs do aplicativo antes de migramos essas VMs para o Azure.

> [!NOTE]
> Para este cenário, o nosso destino de avaliação do banco de dados será "SQL Server em uma VM do Azure". No entanto, no artigo do próximo cenário, vamos executar a migração para uma Instância Gerenciada SQL do Azure. Estamos usando essa abordagem porque o DMA atualmente não dá suporte à avaliação de um destino de Instância Gerenciada SQL do Azure.

## <a name="architecture"></a>Arquitetura

Neste cenário, vamos configurar 

 ![Arquitetura de avaliação da migração](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

Neste cenário:
- A Contoso tem um datacenter local (**contoso-datacenter**), com um controlador de domínio local ( **contosodc1**).
- O aplicativo de viagem interno tem camadas em duas VMs, **WEBVM** e **SQLVM**, e está localizado no host VMware ESXi **contosohost1.contoso.com**.
- O ambiente VMware é gerenciado pelo vCenter Server (**vcenter.contoso.com**) em execução em uma VM.




## <a name="prerequisites"></a>pré-requisitos

Veja o que é necessário para implantar este cenário:

- Um vCenter Server local executando a versão 5.5, 6.0 ou 6.5.
- Uma conta somente leitura no vCenter Server ou permissões para criar uma. 
- Permissões para criar uma VM no vCenter Server usando um modelo OVA.
- Pelo menos um host ESXi executando a versão 5.0 ou posterior.
- Pelo menos duas VMs VMware locais, uma delas executando um banco de dados do SQL Server.
- Você deve ter permissões para instalar agentes das Migrações para Azure em cada VM.
- As VMs devem ter conectividade direta com a Internet.
        - Você pode restringir o acesso à internet para as [URLs necessárias](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        - Se houver máquinas sem conectividade com a Internet, você precisará baixar e instalar um [gateway do OMS](../log-analytics/log-analytics-oms-gateway.md) nelas.
- O FQDN da VM que está executando a instância do SQL Server, para a avaliação do banco de dados.
- O Firewall do Windows em execução na VM do SQL Server deve permitir conexões externas na porta TCP 1433 (padrão), para que o DMA possa se conectar.


## <a name="scenario-overview"></a>Visão geral do cenário

Veja o que vamos fazer:


> [!div class="checklist"]
> * **Etapa 1: preparar o Azure**. Basta ter uma assinatura do Azure.
> * **Etapa 2: baixar e instalar o DMA**: preparar o DMA para avaliação do banco de dados do SQL Server local.
> * **Etapa 3: avaliar o banco de dados**: executar e analisar a avaliação do banco de dados.
> * **Etapa 4: preparar a avaliação de VMs com as Migrações para Azure**: configurar as contas locais e ajustar as configurações do VMware.
> * **Etapa 5: descobrir as VMs locais**: criar uma VM do coletor das Migrações para Azure. Em seguida, execute o coletor para descobrir VMs para avaliação.
> * **Etapa 6: preparar a análise de dependências**: instalar agentes das Migrações para Azure nas VMs, para que possamos ver o mapeamento de dependências entre as VMs.
> * **Etapa 7: avaliar as VMs**: verificar dependências, agrupar as VMs e executar a avaliação. Depois que a avaliação estiver pronta, analise-a na preparação para migração.


## <a name="step-1-prepare-azure"></a>Etapa 1: preparar o Azure

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.
- Se você usar uma assinatura existente e não for o administrador, precisará falar com o administrador para receber permissões de Proprietário ou Colaborador da assinatura ou para atribui-las ao grupo de recursos usado neste cenário.


## <a name="step-2-download-and-install-the-dma"></a>Etapa 2: baixar e instalar o DMA

1. Baixe o DMA do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=53595).
    - Você pode instalar o Assistente em qualquer computador que possa se conectar à instância do SQL. Não é necessário executá-lo no computador do SQL Server.
    - Você não deve executá-lo no computador host do SQL Server.
2. Clique duas vezes no arquivo de instalação baixado (DownloadMigrationAssistant.msi) para iniciar a instalação.
3. Na página **Concluir**, verifique se **Iniciar o Assistente de Migração de Dados da Microsoft** está selecionado e clique em **Concluir**.

## <a name="step-3-run-and-analyze-the-database-assessment"></a>Etapa 3: executar e analisar a avaliação do banco de dados

Execute uma avaliação para analisar a instância do SQL Server de origem em relação a um destino especificado.

1. Em **Novo**, selecione **Avaliação**e forneça um nome ao projeto de avaliação.
2. Em **Tipo de servidor de origem**, selecione **SQL Server**. Em **Tipo de servidor de destino**, selecione **SQL Server em Máquinas Virtuais do Azure**.

    ![Selecionar a origem](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      No momento, o DMA não dá suporte à avaliação de migração para uma Instância Gerenciada SQL. Como alternativa, estamos usando o SQL Server na VM do Azure como nosso suposto destino para a avaliação.

1.  Em **Selecionar a Versão de Destino**, especifique a versão de destino do SQL Server que você deseja executar no Azure e o que você deseja descobrir na avaliação:
    - Os **Problemas de Compatibilidade** informam sobre alterações que podem interromper a migração ou que exigem um ajuste secundário antes da migração. Eles também informam sobre recursos em uso no momento que foram preteridos. Os problemas são organizados por nível de compatibilidade. 
    - A **Recomendação de novos recursos** indicam novos recursos na plataforma do SQL Server de destino que podem ser usados pelo banco de dados após a migração. Eles são organizados por desempenho, segurança e armazenamento. 

    ![Selecionar o destino](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. Em **Conectar-se a um servidor**, especifique o nome do computador que está executando a instância do SQL Server, o tipo de autenticação e os detalhes de conexão. E clique em **Conectar**.

    ![Selecionar o destino](./media/migrate-scenarios-assessment/dma-assessment-3.png)
    
3. Em **Adicionar fonte**, selecione o banco de dados que você deseja avaliar e clique em **Adicionar**.
4. Uma avaliação com o nome especificado será criada.

    ![Criar avaliação](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  **Avançar** para iniciar a avaliação.
6. Em **Rever resultados**, você verá os resultados para os testes de avaliação habilitados.


### <a name="analyze-the-database-assessment"></a>Analisar a avaliação do banco de dados

Os resultados são exibidos no Assistente assim que ficam disponíveis. 

1. No relatório **Problemas de Compatibilidade**, verifique se seu banco de dados tem problemas em cada nível de compatibilidade e, se for o caso, como corrigi-los. Os níveis de compatibilidade mapeiam para as versões do SQL Server da seguinte forma:
    - 100: SQL Server 2008/Banco de Dados SQL do Azure
    - 110: SQL Server 2012/Banco de Dados SQL do Azure
    - 120: SQL Server 2014/Banco de Dados SQL do Azure
    - 130: SQL Server 2016/Banco de Dados SQL do Azure
    - 140: SQL Server 2017/Banco de Dados SQL do Azure

    ![Problemas de compatibilidade](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. No relatório **Recomendações de recursos**, exiba os recursos de desempenho, segurança e armazenamento que devem ser configurados, conforme recomendado, após a migração. Vários recursos são recomendados, incluindo OLTP na memória e Columnstore, Stretch Database, Always Encrypted, Máscara de Dados Dinâmicos e Transparent Data Encryption.

    ![Recomendações de recurso](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. Se corrigir algum problema, clique em **Reiniciar Avaliação** para executá-la novamente. 
4. Clique em **Exportar relatório** para obter o relatório de avaliação no formato JSON ou CSV.

Se você estiver executando uma avaliação em maior escala:

- Você pode executar várias avaliações simultaneamente e exibir o estado das avaliações abrindo a página **Todas as Avaliações**.
- Você pode [consolidar avaliações em um banco de dados do SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- Você pode [consolidar avaliações em um relatório do Power BI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>Etapa 4: preparar a avaliação de VMs com as Migrações para Azure

Crie uma conta do VMware que as Migrações para Azure usarão a fim de descobrir as VMs para avaliação automaticamente. Verifique se você tem permissões para criar uma máquina virtual, observe as portas que devem estar abertas e defina o nível de configuração das estatísticas.

### <a name="set-up-a-vmware-account"></a>Configurar uma conta do VMware

 Você precisa de uma conta somente leitura no vCenter. Se você não tem uma, crie uma conta do VMware com as seguintes propriedades:

- Tipo de usuário: pelo menos um usuário somente leitura.
- Permissões: objeto de Data Center –> Propagar para o Objeto Filho, role=Read-only.
- Detalhes: usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.
- Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).

### <a name="verify-permissions-to-create-a-vm"></a>Verificar permissões para criar uma VM

Verifique se você tem permissões para criar uma VM importando um arquivo no formato .OVA. [Saiba mais](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Verificar portas

Neste cenário, vamos configurar o mapeamento de dependências. Esse recurso requer um agente instalado nas VMs que você está avaliando. Esse agente deve poder se conectar ao Azure da porta TCP 443 em cada VM. [Saiba mais](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) sobre os requisitos de conexão.


### <a name="set-statistics-settings"></a>Definir configurações de estatísticas

Antes de iniciar a implantação, as configurações de estatísticas para o vCenter Server devem ser definidas para o nível 3. Observe que:
- Depois de definir o nível, você precisa aguardar no mínimo um dia antes de executar a avaliação. Caso contrário, ele pode não funcionar conforme o esperado.
- Se o nível for maior que 3, a avaliação funcionará, mas:
    - Não será possível coletar dados de desempenho de discos e rede.
    - Em relação ao armazenamento, as Migrações para Azure recomendam um disco padrão no Azure, com o mesmo tamanho do disco local.
    - Em relação à rede, para cada adaptador de rede local, um adaptador de rede será recomendado no Azure.
    - Em relação à computação, as Migrações para Azure examinarão o tamanho da memória e dos núcleos da VM e recomendarão uma VM do Azure com a mesma configuração. Se houver vários discos qualificados, será recomendado aquele com o menor custo.
   
    
[Saiba mais](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) sobre tamanhos com nível 3.

Defina o nível da seguinte maneira:

1. No cliente Web vSphere, abra a instância do vCenter Server.
2. Selecione a guia **Gerenciar** e, em **Configurações**, clique em **Geral**.
3. Clique em **Editar** e em **Estatísticas**; defina as configurações de nível de estatísticas como **Nível 3**.

    ![Nível de estatísticas do vCenter](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>Etapa 5: descobrir VMs

Crie um projeto das Migrações para Azure, baixe e configure a VM do coletor. Em seguida, execute o coletor para descobrir suas VMs.

### <a name="create-a-project"></a>Criar um projeto

1. Faça logon no [portal do Azure](https://portal.azure.com) e clique em **Criar um recurso**.
2. Procure **Migrações para Azure**. Selecione **Migrações para Azure** nos resultados da pesquisa e clique em **Criar**.
3. Especifique um nome de projeto e a assinatura do Azure.
4. Crie um novo grupo de recursos.
5. Especifique o local do projeto e clique em **Criar**.

    - Só é possível criar um projeto de Migrações para Azure nas regiões Centro-Oeste ou Leste dos EUA.
    - Você pode planejar uma migração para qualquer local de destino.
    - O local do projeto só é usado para armazenar os metadados coletados a partir das VMs locais.

    ![Migrações para Azure](./media/migrate-scenarios-assessment/project-1.png)


    

### <a name="download-the-collector-appliance"></a>Baixe o dispositivo coletor

O Migrações para Azure cria uma VM local conhecida como o dispositivo coletor. Essa VM descobre as VMs do VMware locais e envia os metadados sobre elas para o serviço Migrações para Azure. Para configurar o dispositivo coletor, baixe um arquivo .OVA e importe-o para o servidor do vCenter local para criar a VM.

1. No projeto do Migrações para Azure, clique em **Introdução** > **Descobrir e Avaliar** > **Descobrir Máquinas**.
2. Em **Descobrir máquinas**, clique em **Baixar**, para baixar o arquivo .OVA.
3. Em **Copiar credenciais do projeto**, copie a ID e a chave do projeto. Você precisará delas quando configurar o coletor.

    ![Baixe o arquivo .ova](./media/migrate-scenarios-assessment/download-ova.png) 

### <a name="verify-the-collector-appliance"></a>Verifique o dispositivo coletor

Verifique se o arquivo .OVA é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. O hash gerado deve corresponder a estas configurações (versão 1.0.9.7)
    
    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c
    

### <a name="create-the-collector-appliance"></a>Criar o dispositivo coletor

Importe o arquivo baixado para o vCenter Server.

1. No console do cliente do vSphere, clique em **Arquivo** > **Implantar o Modelo de OVF**.

    ![Implantar o OVF](./media/migrate-scenarios-assessment/vcenter-wizard.png) 

2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA e clique em **Avançar**.
3. Em **Detalhes do Modelo OVF**, clique em **Avançar**. Em **Contrato de Licença de Usuário Final**, clique em **Aceitar** para aceitar o contrato e clique em **Avançar**.
4. Em **Nome e Local**, especifique um nome amigável para a VM do coletor e o local do inventário no qual a VM será hospedada e clique em **Avançar**. Especifique o host ou o cluster no qual o dispositivo do coletor será executado.
5. Em **Armazenamento**, especifique onde você deseja armazenar os arquivos do dispositivo e clique em **Avançar**.
6. Em **Formato de Disco**, especifique como você deseja provisionar o armazenamento.
7. Em **Mapeamento de rede**, especifique a rede à qual a VM do coletor se conectará. A rede precisa de conectividade com a Internet para enviar metadados para o Azure. 
8. Em **Pronto para Concluir**, reveja as configurações, selecione **Ligar após a implantação**e clique em **Concluir**.

Uma mensagem confirmando a conclusão bem-sucedida é emitida depois que o dispositivo é criado.

### <a name="run-the-collector-to-discover-vms"></a>Executar o coletor para descobrir VMs

Antes de começar, observe que o coletor só dá suporte a "Inglês (Estados Unidos)" como os idiomas do sistema operacional e da interface do coletor.

1. No console do cliente do vSphere, clique com botão direito do mouse na VM > **Abrir console**.
2. Forneça o idioma, fuso horário e preferências de senha para o dispositivo.
3. Na área de trabalho, clique no atalho **Executar coletor**.

    ![Atalho do coletor](./media/migrate-scenarios-assessment/collector-shortcut.png) 
    
4. No Coletor de Migrações para Azure, abra **Configurar Pré-Requisitos**.
    - Aceite os termos de licença e leia as informações de terceiros.
    - O coletor verifica se a VM tem acesso à Internet, se a hora está sincronizada, se o serviço do coletor está em execução (ele vem instalado por padrão na VM). Ele também instala o VMWare PowerCLI. 
    
    > [!NOTE]
    > Estamos presumindo que a VM tem acesso direto à Internet, sem um proxy.

    ![Verificar pré-requisitos](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)
    

5. Em **Especificar detalhes do vCenter Server**, faça o seguinte:
    - Especifique o nome (FQDN) ou endereço IP do servidor do vCenter.
    - Em **Nome de usuário** e **Senha**, especifique as credenciais de conta somente leitura que o coletor usará para descobrir VMs no vCenter Server.
    - Em **Escopo de seleção**, selecione um escopo de descoberta de VM. O coletor só pode descobrir VMs dentro do escopo especificado. O escopo pode ser definido para uma pasta, datacenter ou cluster específicos. Ele não deve conter mais de 1500 VMs. 

    ![Conectar-se ao vCenter](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. Em **Especificar projeto de migração**, especifique a ID do projeto de Migrações para Azure e da chave que você copiou do portal. Se não as copiar, abra o portal do Azure da VM do coletor. Na página de **Visão geral** do projeto, clique em **Descobrir Máquinas** e copie os valores.  

    ![Conecte-se ao Azure](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. Em **Visualizar progresso de coleção**, monitore a descoberta e verifique se os metadados coletados das VMs estão no escopo. O coletor fornece um tempo aproximado de descoberta.

    ![Coleção em andamento](./media/migrate-scenarios-assessment/collector-collection-process.png)
   


### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Após a conclusão da coleta, verifique se as VMs aparecem no portal.

1. No projeto das Migrações para Azure, clique em **Gerenciar** > **Computadores**.
2. Verifique se as VMs que você deseja descobrir aparecem na página.

    ![Computadores descobertos](./media/migrate-scenarios-assessment/discovery-complete.png)

3. Observe que as máquinas atualmente não têm os agentes das Migrações para Azure instalados. Precisamos instalá-los para que as dependências possam ser exibidas.
    
    ![Computadores descobertos](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>Etapa 6: preparar a análise de dependências

Para exibir as dependências entre as VMs que desejamos avaliar, podemos baixar e instalar agentes nas VMs do aplicativo Web, WEBVM e SQLVM.

### <a name="take-a-snapshot"></a>Tirar um instantâneo

Se você quiser ter uma cópia da sua VM antes de modificá-la, crie um instantâneo antes de instalar os agentes.

![Instantâneo da máquina](./media/migrate-scenarios-assessment/snapshot-vm.png) 


### <a name="download-and-install-the-vm-agents"></a>Fazer o download e instalar os agente de VM

1.  No projeto das Migrações para Azure > página **Máquinas**, selecione a máquina e clique em **Requer instalação** na coluna **Dependências**.
2.  Na página **Descobrir Máquinas**, para cada VM, baixe e instale o MMA (Microsoft Monitoring Agent) e o agente de dependência.
3.  Copie a ID e a chave do espaço de trabalho. Você precisará delas quando instalar o MMA.

    ![Download do agente](./media/migrate-scenarios-assessment/download-agents.png) 



#### <a name="install-the-mma"></a>Instalar o MMA

1. Clique duas vezes no agente baixado.
2. Na página de **Boas-vindas**, clique em **Avançar**. Na página **Termos de Licença**, clique em **Concordo** para aceitar a licença.
3. Em **Pasta de Destino**, mantenha a pasta de instalação padrão > **Avançar**. 
4. Em **Opções de Instalação do Agente**, selecione **Conectar o agente ao Azure Log Analytics** > **Avançar**. 

    ![Instalação do MMA](./media/migrate-scenarios-assessment/mma-install.png) 
5. No **Azure Log Analytics**, cole a ID do espaço de trabalho e a chave que você copiou do portal. Clique em **Próximo**.
    ![Instalação do MMA](./media/migrate-scenarios-assessment/mma-install2.png) 

6. Em **Pronto para Instalar**, instale o MMA.



#### <a name="install-the-dependency-agent"></a>Instalar o Agente de dependência

1.  Clique duas vezes no agente de dependência baixado.
2.  Na página **Termos de Licença**, clique em **Concordo em aceitar a licença**.
3.  Em **Instalando**, aguarde a conclusão da instalação. Em seguida, clique em **Próximo**.

    ![Agente de dependência](./media/migrate-scenarios-assessment/dependency-agent.png) 


       
## <a name="step-7-run-and-analyze-the-vm-assessment"></a>Etapa 7: executar e analisar a avaliação de VMs

Verifique as dependências da máquina e crie um grupo. Em seguida, execute a avaliação.

### <a name="verify-dependencies-and-create-a-group"></a>Verifique as dependências e crie um grupo.

1.  Na página **Máquinas**, clique em **Exibir Dependências** para as VMs que você deseja analisar.

    ![Exibir dependências de máquina](./media/migrate-scenarios-assessment/view-machine-dependencies.png) 

2. Para a SQLVM, o mapa de dependências mostra os seguintes detalhes:

    - Processos e grupos de processo com conexões de rede ativas em execução na SQLVM durante o período de tempo especificado (uma hora, por padrão)
    - Conexões TCP de entrada (cliente) e de saída (servidor) de e para todas as máquinas dependentes.
    - Máquinas dependentes com agentes das Migrações para Azure instalados são exibidas como caixas separadas
    - Máquinas sem os agentes instalados mostram informações de endereço IP e porta.
    
 3. Para máquinas com o agente instalado (WEBVM), clique na caixa da máquina para exibir mais informações, incluindo o FQDN, o sistema operacional e o endereço MAC. 

    ![Exibir dependências de grupo](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. Agora, selecione as máquinas virtuais que você deseja adicionar ao grupo (SQLVM e WEBVM).  Use CTRL+clique para selecionar várias VMs.
5. Clique em **Criar Grupo** e especifique um nome (smarthotelapp).

> [!NOTE]
    > Para exibir dependências mais granulares, você pode expandir o intervalo de tempo. Você pode selecionar uma duração específica ou datas de início e término. 


### <a name="run-an-assessment"></a>Ler uma avaliação


1. Na página **Grupos** , abra o grupo (smarthotelapp)
2. Clique em **Criar avaliação**.

    ![Criar uma avaliação](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. A avaliação é exibida na página **Gerenciar** > **Avaliações**.


### <a name="modify-assessment-settings"></a>Modificar configurações de avaliação

Neste tutorial, usamos as configurações de avaliação padrão, mas você pode personalizar as configurações da seguinte maneira:

1. Na página **Avaliações** do projeto de migração, selecione a avaliação e clique em **Editar propriedades**.
2. Modifique as propriedades de acordo com a tabela a seguir:

    **Configuração** | **Detalhes** | **Padrão**
    --- | --- | ---
    **Local de destino** | O local do Azure para o qual você deseja migrar | Sem padrão.
    **Redundância de armazenamento** | O tipo de redundância de armazenamento que as VMs do Azure usarão após a migração. | O [armazenamento com redundância local (LRS)](../storage/common/storage-redundancy-lrs.md) é o valor padrão. As Migrações para Azure só dão suporte a avaliações com base em discos gerenciados e os discos gerenciados só dão suporte ao LRS, ou seja, para isso serve a opção LRS. 
    **Critério de dimensionamento** | O critério a ser usado pelas Migrações para Azure para redimensionar VMs para o Azure. Você pode fazer dimensionamento *com base no desempenho* ou dimensionar as VMs *como locais*, sem considerar o histórico de desempenho. | O dimensionamento com base no desempenho é a opção padrão.
    **Histórico de desempenho** | A duração a considerar para avaliar o desempenho das VMs. Essa propriedade só é aplicável quando o critério de dimensionamento é *dimensionamento com base no desempenho*. | O padrão é um dia.
    **Utilização de percentual** | O valor percentual da amostra de desempenho definido para ser considerado para o redimensionamento. Essa propriedade só é aplicável quando o critério de dimensionamento é *dimensionamento com base no desempenho*.  | O padrão é 95 por cento.
    **Tipo de preços** | É possível especificar o [tipo de preço (Básico/Standard)](../virtual-machines/windows/sizes-general.md) das VMs de destino do Azure. Por exemplo, se você estiver planejando migrar um ambiente de produção, deve considerar a camada Standard, que fornece VMs com baixa latência, mas pode custar mais. Por outro lado, caso tenha um ambiente de desenvolvimento de teste, talvez você deva considerar a camada Básica, que tem VMs com latência maior e custos mais baixos. | Por padrão, o tipo [Standard](../virtual-machines/windows/sizes-general.md) é usado.
    **Fator de conforto** | As Migrações para Azure consideram um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos. | A configuração padrão é 1.3 x.
    **Oferta** | A [Oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que você está inscrito. | [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) é o padrão.
    **Moeda** | Moeda de cobrança. | O padrão é dólares americanos.
    **Desconto (%)** | Qualquer desconto específico da assinatura  recebido por você sobre a oferta do Azure. | A configuração padrão é 0%.
    **Benefício Híbrido do Azure** | Especifique se você tem a garantia do software e é qualificado para o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, os preços do Windows Azure são considerados para VMs do Windows. | O padrão é Sim.

3. Clique em **Salvar** para atualizar as configurações de avaliação.


### <a name="analyze-the-vm-assessment"></a>Analisar a avaliação de VMs

Uma avaliação das Migrações para Azure inclui informações sobre a compatibilidade das VMs locais com o Azure, o dimensionamento correto sugerido para a VM do Azure e os custos estimados mensais do Azure. 

![Relatório de avaliação](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Revisar classificação de confiança

![Exibição de avaliação](./media/migrate-scenarios-assessment/assessment-display.png)

Sua avaliação obtém uma classificação de confiança de 1 a 5 estrelas (1 estrela é a mais baixa e 5 estrelas, a mais alta).
- A classificação de confiança é atribuída a uma avaliação com base na disponibilidade de pontos de dados necessários para calcular a avaliação.
- A classificação ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pelas Migrações para Azure.
- A classificação de confiança é útil ao fazer o *dimensionamento baseado em desempenho*, uma vez que as Migrações para Azure podem não ter pontos de dados suficientes para fazer o dimensionamento baseado na utilização. Para *como dimensionamento local*, a classificação de confiança é sempre de 5 estrelas, uma vez que as Migrações para Azure têm todos os pontos de dados necessários para dimensionar a VM.
- Dependendo do percentual dos pontos de dados disponíveis, é fornecida a classificação de confiança para a avaliação:

   **Disponibilidade dos pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 estrela
   21%-40% | 2 estrelas
   41%-60% | 3 estrelas
   61%-80% | 4 estrelas
   81%-100% | 5 estrelas

#### <a name="verify-readiness"></a>Verificar a preparação

![Preparação para avaliação](./media/migrate-scenarios-assessment/azure-readiness.png)  

O relatório de avaliação mostra as informações resumidas na tabela. Observe que, para mostrar o dimensionamento com base no desempenho, as Migrações para Azure precisam das informações a seguir. Se essas informações não puderem ser coletadas, a avaliação de dimensionamento poderá não ser exata.

- Dados de utilização de CPU e memória.
- IOPS de leitura/gravação e a taxa de transferência para cada disco anexado à VM.
- Informações de entrada/saída de rede para cada adaptador de rede conectado à VM.


**Configuração** | **Indicação** | **Detalhes**
--- | --- | ---
**Preparação da VM do Azure** | Indica se a VM está pronta para a migração | Possíveis estados:</br><br/>- Pronto para o Azure<br/><br/>- Pronto com condições <br/><br/>- Não pronto para o Azure<br/><br/>- Preparação desconhecida<br/><br/> Se uma VM não estiver pronta, vamos mostrar alguns passos de correção.
**Tamanho da VM do Azure** | Para VMs prontas, recomendamos um tamanho de VM do Azure. | As recomendações de dimensionamento dependem das propriedades da avaliação:<br/><br/>- Se você usou o dimensionamento com base no desempenho, o dimensionamento considerará o histórico de desempenho das VMs.<br/><br/>- Se você usou 'como a local', o dimensionamento será baseado no tamanho da VM local e os dados de utilização não serão usados.
**Ferramenta sugerida** | Como nossos computadores estão executando os agentes, as Migrações para Azure analisam os processos em execução no computador e identificam se ele é um computador de banco de dados ou não.
**Informações da VM** | O relatório mostra as configurações para a VM local, incluindo o sistema operacional, o tipo de inicialização e informações de disco e armazenamento.




#### <a name="review-monthly-cost-estimates"></a>Revisar estimativas de custo mensal

Essa exibição mostra o custo total de computação e armazenamento da execução das máquinas virtuais no Azure, junto com os detalhes de cada máquina. 

![Preparação para avaliação](./media/migrate-scenarios-assessment/azure-costs.png) 

- As estimativas de custo são calculadas usando as recomendações de tamanho da máquina.
- Os custos mensais estimados de computação e armazenamento são agregados para todas as VMs no grupo. 


## <a name="conclusion"></a>Conclusão

Neste cenário:

> [!div class="checklist"]
> * Avaliamos nosso banco de dados local com a ferramenta DMA.
> * Avaliamos nossas VMs locais com o serviço Migrações para Azure usando o mapeamento de dependências.
> * Revisamos as avaliações para fazer com que nossos recursos locais estejam prontos para a migração para o Azure.

## <a name="next-steps"></a>Próximas etapas

Vamos prosseguir para o próximo cenário a fim de fazer uma migração lift-and-shift das VMs locais para o Azure.



