---
title: "Descobrir e avaliar as VMs do VMware locais para a migração para o Azure com o Migrações para Azure | Microsoft Docs"
description: "Descreve como descobrir e avaliar as VMs do VMware locais para a migração para o Azure, usando o serviço de Migrações para Azure."
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a2521630-730f-4d8b-b298-e459abdced46
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: b0818fbc1d227093fcc1b9b925d0859b8580f9c1
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Descobrir e avaliar as VMs do VMware locais para migração para o Azure

Os serviços de [Migrações para Azure](migrate-overview.md) avaliam as cargas de trabalho locais para migração para o Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto das Migrações para Azure.
> * Configurar uma máquina virtual (VM) de coletor local, para descobrir VMs do VMware locais para avaliação.
> * Agrupar VMs e criar uma avaliação.


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- **VMware**: as VMs que você planeja migrar devem ser gerenciadas por um vCenter Server executando a versão 5.5, 6.0 ou 6.5. Além disso, é necessário um host ESXi executando a versão 5.0 ou posteriores para implantar a VM de coletor. 
 
> [!NOTE]
> O suporte para Hyper-V está em nossos planos e será habilitado em breve. 

- **Conta do vCenter Server**: você precisa de uma conta de somente leitura para acessar o vCenter Server. O Migrações para Azure usa essa conta para descobrir as VMs locais.
- **Permissões**: no vCenter Server, você precisa de permissões para criar uma VM importando um arquivo no formato .OVA. 
- **Configurações de estatísticas**: as configurações de estatísticas para o vCenter Server devem ser definidas para o nível 3 antes de se iniciar a implantação. Se for inferior ao nível 3, a avaliação funcionará mas os dados de desempenho para armazenamento e rede não são coletados. As recomendações de tamanho nesse caso serão feitas com base nos dados de desempenho para CPU e memória e nos dados de configuração para os adaptadores de rede e de disco. 

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure
Faça logon no [Portal do Azure](https://portal.azure.com).

## <a name="create-a-project"></a>Criar um projeto

1. No portal do Azure, clique em **Criar um recurso**.
2. Procure **Migrações do Azure** e selecione o serviço (**Migrações do Azure (versão prévia)** nos resultados da pesquisa. Em seguida, clique em **Criar**.
3. Especifique um nome de projeto e a assinatura do Azure para o projeto.
4. Crie um novo grupo de recursos.
5. Especifique a região na qual deseja criar o projeto e clique em **Criar**. Os metadados coletados de VMs locais serão armazenados nesta região. Você só pode criar um projeto de Migrações para Azure na região Centro-oeste dos EUA nesta versão prévia. No entanto, você ainda pode planejar a migração de qualquer local de destino do Azure. 

    ![Migrações para Azure](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>Baixe o dispositivo coletor

O Migrações para Azure cria uma VM local conhecida como o dispositivo coletor. Essa VM descobre as VMs do VMware locais e envia os metadados sobre elas para o serviço de Migrações para Azure. Para configurar o dispositivo coletor, você baixa um arquivo .OVA e o importa para o servidor do vCenter local para criar a VM.

1. No projeto do Migrações para Azure, clique em **Introdução** > **Descobrir e avaliar** > **Descobrir máquinas**.
2. Em **Descobrir máquinas**, clique em **Baixar**, para baixar o arquivo .OVA.
3. Em **Copiar credenciais do projeto**, copie a ID e a chave do projeto. Você precisará delas quando configurar o coletor.

    ![Baixe o arquivo .ova](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verifique o dispositivo coletor

Verifique se o arquivo .OVA é seguro, antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para a OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. O hash gerado deve corresponder a essas configurações.
    
    Para obter a versão 1.0.8.38 de OVA
    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | dd27dd6ace28f9195a2b5d52a4003067 
    SHA1 | d2349e06a5d4693fc2a1c0619591b9e45c36d695
    SHA256 | 1492a0c6d6ef76e79269d5cd6f6a22f336341e1accbc9e3dfa5dad3049be6798

    Para obter a versão 1.0.8.40 de OVA
    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>Criar a VM do coletor

Importe o arquivo baixado para o vCenter Server.

1. No console do cliente do vSphere, clique em **Arquivo** > **Implantar o modelo de OVF**.

    ![Implantar o OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo .ova.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM do coletor e o objeto do inventário no qual a VM será hospedada.
5. Em **Host/Cluster**, especifique o host ou cluster no qual a VM do coletor será executada.
7. No armazenamento, especifique o destino de armazenamento para a VM do coletor.
8. Em **Formato de disco**, especifique o tipo e o tamanho do disco.
9. Em **Mapeamento de rede**, especifique a rede à qual a VM do coletor se conectará. A rede precisa de conectividade com a Internet, para enviar metadados para o Azure. 
10. Revise e confirme as configurações e clique em **Concluir**.

## <a name="run-the-collector-to-discover-vms"></a>Execute o coletor para descobrir as VMs

1. No console do cliente do vSphere, clique com botão direito do mouse na VM > **Abrir console**.
2. Forneça o idioma, fuso horário e preferências de senha para o dispositivo.
3. Na área de trabalho, clique no atalho **Executar coletor**.
4. No Coletor de Migrações para Azure, abra **Congifurar pré-requisitos**.
    - Aceite os termos de licença e leia as informações de terceiros.
    - O coletor verifica se a VM tem acesso à Internet.
    - Se a VM acessa a internet através de um proxy, clique em **Configurações de proxy** e especifique o endereço de proxy e a porta de escuta. Especifique as credenciais caso o proxy exija autenticação.
    - O coletor verifica se o serviço de criador de perfil do Windows está em execução. O serviço é instalado por padrão na VM do coletor.
    - Baixe e instale o VMware PowerCLI.
. Em **Descobrir máquinas**, faça o seguinte:
    - Especifique o nome (FQDN) ou endereço IP do servidor do vCenter.
    - Em **Nome de usuário** e **Senha**, especifique as credenciais de conta de somente leitura que o coletor usará para descobrir VMs no servidor do vCenter.
    - Em **Escopo de coleção**, selecione um escopo de descoberta de VM. O coletor só pode descobrir VMs dentro do escopo especificado. O escopo pode ser definido para uma pasta, datacenter ou cluster específicos. Ele não deve conter mais de 1000 VMs. 
    - Em **Marcar categoria para agrupamento**, selecione **Nenhuma**.
1. Em **Selecionar projeto**, especifique a ID do projeto de Migrações para Azure e da chave que você copiou do portal. Se não copiá-las, abra o portal do Azure da VM do coletor. Na página de **Visão geral** do projeto, clique em **Descobrir máquinas** e copie os valores.  
2. Em **Descoberta completa**, monitore a descoberta e verifique se os metadados coletados das VMs estão no escopo. O coletor fornece um tempo aproximado de descoberta.

> [!NOTE]
> O coletor só oferece suporte para "Inglês (Estados Unidos)" como o idioma do sistema operacional e o idioma da interface do coletor. O suporte para outros idiomas estará disponível em breve.


### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

O tempo de descoberta depende de quantos VMs estão sendo descobertas. Geralmente, para 100 VMs, após o coletor terminar a execução leva em torno de uma hora para a descoberta ser concluída. 

1. No projeto do Migration Planner, clique em **Gerenciar** > **Máquinas**.
2. Verifique se as VMs que você deseja descobrir aparecem no portal.


## <a name="create-and-view-an-assessment"></a>Criar e exibir uma avaliação

Após as VMs terem sido descobertas, você pode agrupá-las e criar uma avaliação. 

1. Na página de **Visão geral** do projeto, clique em **Criar avaliação**.
2. Clique em **Exibir tudo** para examinar as propriedades da avaliação.
3. Crie o grupo e especifique um nome de grupo.
4. Selecione as máquinas que deseja adicionar ao grupo.
5. Clique em **Criar avaliação**, para criar o grupo e a avaliação.
6. Depois que a avaliação é criada, é possível exibi-la em **Visão geral** > **Painel**.
7. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.

### <a name="sample-assessment"></a>Avaliação de exemplo

Aqui está um exemplo de relatório de avaliação. Ele inclui informações sobre se as VMs são compatíveis com o Azure e os custos mensais estimados. 

![Relatório de avaliação](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Preparação do Azure

Essa exibição mostra o status de preparação para cada máquina.

- Para VMs que estão prontas, o Migrações para Azure recomenda um tamanho de VM no Azure.
- Para VMs que não estão prontas, o Migrações para Azure explica o motivo e fornece as etapas para correção.
- O Migrações para Azure sugere ferramentas que você pode usar para a migração. Se a máquina é adequada para a migração lift-and-shift, é recomendável o serviço do Azure Site Recovery. Se for uma máquina de banco de dados, é recomendável o Serviço de Migração de Banco de Dados do Azure.

  ![Preparação para avaliação](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Estimativa de custo mensal

Essa exibição mostra o custo total de computação e armazenamento da execução das máquinas virtuais no Azure junto com os detalhes de cada máquina. As estimativas de custo são calculadas usando as recomendações de tamanho com base em desempenho para um computador e seus discos e as propriedades de avaliação. 

> [!NOTE]
> A estimativa de custo fornecida pelo Migrações para Azure serve para executar as VMs locais como VMs de	Infraestrutura como Serviço (IaaS) do Azure. Ela não considera nenhum custo de Plataforma como Serviço (PaaS) ou de Software como Serviço (SaaS). 

Os custos mensais estimados de computação e armazenamento são agregados para todas as VMs no grupo. 

![Avaliação de custo de VM](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

Você pode fazer uma busca detalhada para ver os detalhes para um computador específico.

![Avaliação de custo de VM](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>Próximas etapas

- [Saiba](how-to-scale-assessment.md) como configurar uma avaliação para um grande número de computadores locais.
- Saiba como criar grupos de avaliação mais detalhados usando [mapeamento de dependência do computador](how-to-create-group-machine-dependencies.md)
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
