---
title: Descobrir e avaliar as VMs do VMware locais para a migração para o Azure com o Migrações para Azure | Microsoft Docs
description: Descreve como descobrir e avaliar as VMs do VMware locais para a migração para o Azure, usando o serviço de Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 695298be6cb9f56de26b8682c556285aba22d4a6
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34272056"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Descobrir e avaliar as VMs do VMware locais para migração para o Azure

Os serviços de [Migrações para Azure](migrate-overview.md) avaliam as cargas de trabalho locais para migração para o Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta que as Migrações para Azure usem para descobrir VMs locais
> * Criar um projeto do Migrações para Azure.
> * Configurar uma máquina virtual (VM) de coletor local, para descobrir VMs do VMware locais para avaliação.
> * Agrupar VMs e criar uma avaliação.


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>pré-requisitos

- **VMware**: as VMs que você planeja migrar devem ser gerenciadas por um vCenter Server executando a versão 5.5, 6.0 ou 6.5. Além disso, é necessário um host ESXi executando a versão 5.0 ou posteriores para implantar a VM de coletor.

> [!NOTE]
> O suporte para Hyper-V está em nossos planos e será habilitado em breve.

- **Conta do vCenter Server**: você precisa de uma conta de somente leitura para acessar o vCenter Server. O Migrações para Azure usa essa conta para descobrir as VMs locais.
- **Permissões**: no vCenter Server, você precisa de permissões para criar uma VM importando um arquivo no formato .OVA.
- **Configurações de estatísticas**: as configurações de estatísticas para o vCenter Server devem ser definidas para o nível 3 antes de se iniciar a implantação. Se for inferior ao nível 3, a avaliação funcionará mas os dados de desempenho para armazenamento e rede não são coletados. As recomendações de tamanho nesse caso serão feitas com base nos dados de desempenho para CPU e memória e nos dados de configuração para os adaptadores de rede e de disco.

## <a name="create-an-account-for-vm-discovery"></a>Criar uma conta para a descoberta de VM

As Migrações para Azure precisam de acesso aos servidores VMware para descobrir automaticamente as VMs para avaliação. Crie uma conta do VMware com as seguintes propriedades. Especifique essa conta durante a instalação das Migrações para Azure.

- Tipo de usuário: pelo menos um usuário somente leitura
- Permissões: Objeto de Data Center –> Propagar para o Objeto Filho, role=Read-only
- Detalhes: usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.
- Para restringir o acesso, atribua a função Nenhum acesso com Propagar para objeto filho aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com).

## <a name="create-a-project"></a>Criar um projeto

1. No portal do Azure, clique em **Criar um recurso**.
2. Procure **Migrações para Azure** e selecione o serviço de **Migrações para Azure** nos resultados da pesquisa. Em seguida, clique em **Criar**.
3. Especifique um nome de projeto e a assinatura do Azure para o projeto.
4. Crie um novo grupo de recursos.
5. Especifique o local onde deseja criar o projeto e clique em **Criar**. Só é possível criar um projeto de Migrações para Azure nas regiões Centro-Oeste ou Leste dos EUA. No entanto, você ainda pode planejar a migração de qualquer local de destino do Azure. O local especificado para o projeto só é usado para armazenar os metadados coletados a partir das VMs locais.

    ![Migrações para Azure](./media/tutorial-assessment-vmware/project-1.png)



## <a name="download-the-collector-appliance"></a>Baixe o dispositivo coletor

O Migrações para Azure cria uma VM local conhecida como o dispositivo coletor. Essa VM descobre as VMs do VMware locais e envia os metadados sobre elas para o serviço Migrações para Azure. Para configurar o dispositivo coletor, baixe um arquivo .OVA e importe-o para o servidor do vCenter local para criar a VM.

1. No projeto do Migrações para Azure, clique em **Introdução** > **Descobrir e Avaliar** > **Descobrir Máquinas**.
2. Em **Descobrir máquinas**, clique em **Baixar**, para baixar o arquivo .OVA.
3. Em **Copiar credenciais do projeto**, copie a ID e a chave do projeto. Você precisará delas quando configurar o coletor.

    ![Baixe o arquivo .ova](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verifique o dispositivo coletor

Verifique se o arquivo .OVA é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. O hash gerado deve corresponder a estas configurações.

  Para a versão OVA 1.0.9.8

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

    Para a versão OVA 1.0.9.7

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

    Para a versão OVA 1.0.9.5

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  

    Para a versão OVA 1.0.9.2

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

    Para a versão OVA 1.0.8.59

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 71139e24a532ca67669260b3062c3dad
    SHA1 | 1bdf0666b3c9c9a97a07255743d7c4a2f06d665e
    SHA256 | 6b886d23b24c543f8fc92ff8426cd782a77efb37750afac397591bda1eab8656  

    Para a versão OVA 1.0.8.49
    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | cefd96394198b92870d650c975dbf3b8
    SHA1 | 4367a1801cf79104b8cd801e4d17b70596481d6f
    SHA256 | fda59f076f1d7bd3ebf53c53d1691cc140c7ed54261d0dc4ed0b14d7efef0ed9

    Para a versão OVA 1.0.8.40:

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>Criar a VM do coletor

Importe o arquivo baixado para o vCenter Server.

1. No console do cliente do vSphere, clique em **Arquivo** > **Implantar o Modelo de OVF**.

    ![Implantar o OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo .ova.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM do coletor e o objeto do inventário no qual a VM será hospedada.
5. Em **Host/Cluster**, especifique o host ou cluster no qual a VM do coletor será executada.
7. No armazenamento, especifique o destino de armazenamento para a VM do coletor.
8. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
9. Em **Mapeamento de rede**, especifique a rede à qual a VM do coletor se conectará. A rede precisa de conectividade com a Internet para enviar metadados para o Azure.
10. Revise e confirme as configurações e clique em **Concluir**.

## <a name="run-the-collector-to-discover-vms"></a>Execute o coletor para descobrir as VMs

1. No console do cliente do vSphere, clique com botão direito do mouse na VM > **Abrir console**.
2. Forneça o idioma, fuso horário e preferências de senha para o dispositivo.
3. Na área de trabalho, clique no atalho **Executar coletor**.
4. No Coletor de Migrações para Azure, abra **Configurar Pré-Requisitos**.
    - Aceite os termos de licença e leia as informações de terceiros.
    - O coletor verifica se a VM tem acesso à Internet.
    - Se a VM acessa a internet através de um proxy, clique em **Configurações de proxy** e especifique o endereço de proxy e a porta de escuta. Especifique as credenciais caso o proxy exija autenticação. [Saiba mais](https://docs.microsoft.com/en-us/azure/migrate/concepts-collector#internet-connectivity) sobre os requisitos de conectividade com a Internet e a lista de URLs acessada pelo coletor.

    > [!NOTE]
    > O endereço de proxy deve ser inserido no formato http://ProxyIPAddress ou http://ProxyFQDN. Há suporte apenas para o proxy HTTP.

    - O coletor verifica se o serviço coletor está em execução. O serviço é instalado por padrão na VM do coletor.
    - Baixe e instale o VMware PowerCLI.

5. Em **Especificar detalhes do vCenter Server**, faça o seguinte:
    - Especifique o nome (FQDN) ou endereço IP do servidor do vCenter.
    - Em **Nome de usuário** e **Senha**, especifique as credenciais de conta de somente leitura que o coletor usará para descobrir VMs no servidor do vCenter.
    - Em **Escopo de coleção**, selecione um escopo de descoberta de VM. O coletor só pode descobrir VMs dentro do escopo especificado. O escopo pode ser definido para uma pasta, datacenter ou cluster específicos. Ele não deve conter mais de 1500 VMs. [Saiba mais](how-to-scale-assessment.md) sobre como você pode descobrir um ambiente maior.

6. Em **Especificar projeto de migração**, especifique a ID do projeto de Migrações para Azure e da chave que você copiou do portal. Se não as copiar, abra o portal do Azure da VM do coletor. Na página de **Visão geral** do projeto, clique em **Descobrir Máquinas** e copie os valores.  
7. Em **Visualizar progresso de coleção**, monitore a descoberta e verifique se os metadados coletados das VMs estão no escopo. O coletor fornece um tempo aproximado de descoberta. [Saiba mais](https://docs.microsoft.com/en-us/azure/migrate/concepts-collector#what-data-is-collected) sobre quais dados são coletados pelo Coletor de Migrações para Azure.

> [!NOTE]
> O coletor só oferece suporte para "Inglês (Estados Unidos)" como o idioma do sistema operacional e o idioma da interface do coletor. O suporte para outros idiomas estará disponível em breve.


### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

O tempo de descoberta depende de quantas VMs estão sendo descobertas. Geralmente, para 100 VMs, após o coletor terminar a execução, leva em torno de uma hora para a descoberta ser concluída.

1. No projeto do Planejador de Migrações, clique em **Gerenciar** > **Máquinas**.
2. Verifique se as VMs que você deseja descobrir aparecem no portal.


## <a name="create-and-view-an-assessment"></a>Criar e exibir uma avaliação

Após as VMs terem sido descobertas, você pode agrupá-las e criar uma avaliação.

1. Na página **Visão geral** do projeto, clique em **Criar avaliação**.
2. Clique em **Exibir tudo** para examinar as propriedades da avaliação.
3. Crie o grupo e especifique um nome de grupo.
4. Selecione as máquinas que deseja adicionar ao grupo.
5. Clique em **Criar avaliação**, para criar o grupo e a avaliação.
6. Depois que a avaliação é criada, é possível exibi-la em **Visão geral** > **Painel**.
7. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.

### <a name="assessment-details"></a>Detalhes da avaliação

Uma avaliação inclui informações sobre se as VMs locais são compatíveis para o Azure, qual seria o tamanho correto de VM para executar a VM no Azure e os custos estimados de mensais do Azure.

![Relatório de avaliação](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Preparação do Azure

O modo de exibição da preparação para o Azure na avaliação mostra o status de preparação de cada VM. Dependendo das propriedades da VM, cada VM pode ser marcada como:
- Pronta para o Azure
- Condicionalmente pronta para o Azure
- Não pronta para o Azure
- Preparação desconhecida

Para VMs que estão prontas, o Migrações para Azure recomenda um tamanho de VM no Azure. A recomendação de tamanho feita pelas Migrações para Azure depende do critério de tamanho especificado nas propriedades de avaliação. Se o critério de dimensionamento for baseado no desempenho do dimensionamento, a recomendação de tamanho é feita levando em consideração o histórico de desempenho das VMs. Se o critério de dimensionamento é 'como local', a recomendação para o tamanho da VM no Azure é feita com a observação do tamanho da VM local (dimensionamento no estado em que se encontra). Os dados de utilização de CPU e memória da VM não são considerados para o dimensionamento da VM. No entanto, os discos, no caso do dimensionamento local, são dimensionados com a análise dos dados de desempenho.  [Saiba mais](concepts-assessment-calculation.md) sobre como o dimensionamento é feito nas Migrações para Azure.

Para VMs que não estão prontas ou condicionalmente prontas para o Azure, as Migrações para Azure explicam os problemas de preparação e fornecem etapas de correção.

As VMs para as quais as Migrações para Azure não conseguem identificar a Preparação para o Azure (devido à indisponibilidade de dados) são marcadas como preparação desconhecida.

Além do dimensionamento e da Preparação para o Azure, as Migrações para Azure também sugerem ferramentas que você pode usar para a migração da VM. Isso requer uma descoberta mais profunda no ambiente local. [Saiba mais](how-to-get-migration-tool.md) sobre como fazer uma descoberta mais profunda com a instalação de agentes nos computadores locais. Se os agentes não estiverem instalados nos computadores locais, sugere-se a migração fácil usando do [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Se os agentes estiverem instalados no computador local, as Migrações para Azure analisam os processos em execução no computador e identificam se ele é um computador de banco de dados ou não. Se o computador for um computador de banco de dados, sugere-se o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview), caso contrário, sugere-se o Azure Site Recovery como a ferramenta de migração.

  ![Preparação para avaliação](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Estimativa de custo mensal

Essa exibição mostra o custo total de computação e armazenamento da execução das máquinas virtuais no Azure junto com os detalhes de cada máquina. As estimativas de custo são calculadas com base nas recomendações de tamanho feitas pelas Migrações para Azure para um computador, seus discos e as propriedades de avaliação.

> [!NOTE]
> A estimativa de custo fornecida pelo Migrações para Azure serve para executar as VMs locais como VMs de	Infraestrutura como Serviço (IaaS) do Azure. As Migrações para Azure não consideram nenhum custo de Plataforma como Serviço (PaaS) ou Software como Serviço (SaaS).

Os custos mensais estimados de computação e armazenamento são agregados para todas as VMs no grupo.

![Avaliação de custo de VM](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>Classificação de confiança

Cada avaliação das Migrações para Azure está associada a uma classificação de confiança que varia de 1 a 5 estrelas (1 estrela sendo a mais baixa e 5 estrelas sendo a mais alta). A classificação de confiança é atribuída a uma avaliação com base na disponibilidade de pontos de dados necessários para calcular a avaliação. A classificação de confiança de uma avaliação ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pelas Migrações para Azure.

Para o dimensionamento baseado em desempenho da VM, as Migrações para Azure precisam dos dados de utilização da CPU e da memória. Além disso, para o dimensionamento de cada disco anexado à VM, é necessário ter a IOPS de leitura/gravação e a taxa de transferência. Da mesma forma, para cada adaptador de rede conectado à VM, as Migrações para Azure precisam da rede de entrada/saída para fazer o dimensionamento com base no desempenho. Se qualquer um dos números de utilização acima não estiver disponível no vCenter Server, a recomendação de tamanho feita pelas Migrações para Azure pode não ser confiável. Dependendo do percentual dos pontos de dados disponíveis, a classificação de confiança para a avaliação é fornecida conforme mostrado abaixo:

   **Disponibilidade dos pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 estrela
   21%-40% | 2 estrelas
   41%-60% | 3 estrelas
   61%-80% | 4 estrelas
   81%-100% | 5 estrelas

Uma avaliação pode não ter todos os pontos de dados disponíveis devido a um dos seguintes motivos:
- A configuração de estatísticas no vCenter Server não está definida para o nível 3. Se a configuração de estatísticas no vCenter Server for menor que o nível 3, os dados de desempenho de disco e de rede não são coletados do vCenter Server. Nesse caso, a recomendação fornecida pelas Migrações para Azure para o disco e a rede não é baseada na utilização. Sem considerar a IOPS/taxa de transferência do disco, as Migrações para Azure não podem identificar se o disco precisará de um disco premium no Azure e, portanto, as Migrações para Azure recomendam discos Standard para todos os discos.
- A configuração de estatísticas no vCenter Server foi definida para o nível 3 para uma duração curta, antes de iniciar a descoberta. Por exemplo, vamos pensar em um cenário em que você altera o nível de configuração de estatísticas para 3 hoje e inicia a descoberta usando o dispositivo de coletor amanhã (após 24 horas). Se estiver criando uma avaliação de um dia, você terá todos os pontos de dados, e a classificação de confiança da avaliação seria de 5 estrelas. Mas se estiver alterando a duração de desempenho nas propriedades de avaliação para um mês, a classificação de confiança diminuirá, visto que os dados de desempenho do disco e da rede do último mês não estariam disponíveis. Se você desejar considerar os dados de desempenho do último mês, é recomendável manter a configuração de estatísticas do vCenter Server no nível 3 por um mês antes de iniciar a descoberta.
- Algumas VMs foram desativadas durante o período para o qual a avaliação é calculada. Se todas as VMs foram desligadas por algum tempo, o vCenter Server não terá os dados de desempenho para aquele período.
- Algumas VMs foram criadas durante o período para o qual a avaliação é calculada. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente somente há uma semana. Nesses casos, o histórico de desempenho das novas VMs não estará lá durante todo o período.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação estiver abaixo de 4 estrelas, é recomendável alterar o nível de configurações de estatísticas do vCenter Server para 3, aguardar o tempo da duração que deseja avaliar (1 dia/1 semana/1 mês) e, em seguida, realizar a descoberta e a avaliação. Se não for possível fazer isso, o dimensionamento com base no desempenho pode não ser confiável, e é recomendável alternar para *dimensionamento como local* alterando as propriedades de avaliação.

## <a name="next-steps"></a>Próximas etapas

- [Saiba](how-to-modify-assessment.md) como personalizar uma avaliação de acordo com suas necessidades.
- Saiba como criar grupos de avaliação de alta confiança usando o [mapeamento de dependência do computador](how-to-create-group-machine-dependencies.md)
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
- [Saiba](how-to-scale-assessment.md) como descobrir e avaliar um grande ambiente VMware.
