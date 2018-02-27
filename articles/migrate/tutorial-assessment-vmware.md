---
title: "Descobrir e avaliar as VMs do VMware locais para a migração para o Azure com o Migrações para Azure | Microsoft Docs"
description: "Descreve como descobrir e avaliar as VMs do VMware locais para a migração para o Azure, usando o serviço de Migrações para Azure."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 06/02/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 0c82eeaeb17fb670b6d277d1b703b44b84343877
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Descobrir e avaliar as VMs do VMware locais para migração para o Azure

Os serviços de [Migrações para Azure](migrate-overview.md) avaliam as cargas de trabalho locais para migração para o Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
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

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure
Faça logon no [Portal do Azure](https://portal.azure.com).

## <a name="create-a-project"></a>Criar um projeto

1. No portal do Azure, clique em **Criar um recurso**.
2. Procure **Migrações para Azure** e selecione o serviço **Migrações para Azure (versão prévia)** nos resultados da pesquisa. Em seguida, clique em **Criar**.
3. Especifique um nome de projeto e a assinatura do Azure para o projeto.
4. Crie um novo grupo de recursos.
5. Especifique o local onde deseja criar o projeto e clique em **Criar**. Você só pode criar um projeto de Migrações para Azure na região Centro-oeste dos EUA nesta versão prévia. No entanto, você ainda pode planejar a migração de qualquer local de destino do Azure. O local especificado para o projeto só é usado para armazenar os metadados coletados a partir das VMs locais. 

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
    - Se a VM acessa a internet através de um proxy, clique em **Configurações de proxy** e especifique o endereço de proxy e a porta de escuta. Especifique as credenciais caso o proxy exija autenticação.

    > [!NOTE]
    > O endereço de proxy deve ser inserido no formulário http://ProxyIPAddress ou http://ProxyFQDN. Há suporte apenas para o proxy HTTP.

    - O coletor verifica se o serviço coletor está em execução. O serviço é instalado por padrão na VM do coletor.
    - Baixe e instale o VMware PowerCLI.

5. Em **Especificar detalhes do vCenter Server**, faça o seguinte:
    - Especifique o nome (FQDN) ou endereço IP do servidor do vCenter.
    - Em **Nome de usuário** e **Senha**, especifique as credenciais de conta de somente leitura que o coletor usará para descobrir VMs no servidor do vCenter.
    - Em **Escopo de coleção**, selecione um escopo de descoberta de VM. O coletor só pode descobrir VMs dentro do escopo especificado. O escopo pode ser definido para uma pasta, datacenter ou cluster específicos. Ele não deve conter mais de 1000 VMs. 

6. Em **Especificar projeto de migração**, especifique a ID do projeto de Migrações para Azure e da chave que você copiou do portal. Se não as copiar, abra o portal do Azure da VM do coletor. Na página de **Visão geral** do projeto, clique em **Descobrir Máquinas** e copie os valores.  
7. Em **Visualizar progresso de coleção**, monitore a descoberta e verifique se os metadados coletados das VMs estão no escopo. O coletor fornece um tempo aproximado de descoberta.

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

Para VMs que estão prontas, o Migrações para Azure recomenda um tamanho de VM no Azure. A recomendação de tamanho feita pelas Migrações para Azure depende do critério de tamanho especificado nas propriedades de avaliação. Se o critério de dimensionamento for baseado no desempenho do dimensionamento, a recomendação de tamanho é feita levando em consideração o histórico de desempenho das VMs. Se o critério de dimensionamento for 'como local', a recomendação é feita observando o tamanho da VM local (tamanho no estado em que se encontra). Dados de utilização não são considerados neste caso. [Saiba mais](concepts-assessment-calculation.md) sobre como o dimensionamento é feito nas Migrações para Azure. 

Para VMs que não estão prontas ou condicionalmente prontas para o Azure, as Migrações para Azure explicam os problemas de preparação e fornecem etapas de correção. 

As VMs para as quais as Migrações para Azure não conseguem identificar a Preparação para o Azure (devido à indisponibilidade de dados) são marcadas como preparação desconhecida.

Além do dimensionamento e da Preparação para o Azure, as Migrações para Azure também sugerem ferramentas que você pode usar para a migração da VM. Se o computador for adequado para a migração fácil, é recomendável o serviço do [Azure Site Recovery]. Se for uma máquina de banco de dados, é recomendável o Serviço de Migração de Banco de Dados do Azure.

  ![Preparação para avaliação](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Estimativa de custo mensal

Essa exibição mostra o custo total de computação e armazenamento da execução das máquinas virtuais no Azure junto com os detalhes de cada máquina. As estimativas de custo são calculadas usando as recomendações de tamanho com base em desempenho para um computador e seus discos e as propriedades de avaliação. 

> [!NOTE]
> A estimativa de custo fornecida pelo Migrações para Azure serve para executar as VMs locais como VMs de	Infraestrutura como Serviço (IaaS) do Azure. As Migrações para Azure não consideram nenhum custo de Plataforma como Serviço (PaaS) ou Software como Serviço (SaaS). 

Os custos mensais estimados de computação e armazenamento são agregados para todas as VMs no grupo. 

![Avaliação de custo de VM](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

#### <a name="confidence-rating"></a>Classificação de confiança

Cada avaliação das Migrações para Azure está associada a uma classificação de confiança que varia de uma 1 a 5 estrelas (1 estrela sendo a menor e 5 estrelas sendo a mais alta). A classificação de confiança é atribuída a uma avaliação baseada na disponibilidade de pontos de dados necessários para calcular a avaliação. Isso ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pelas Migrações para Azure. 

A classificação de confiança é útil quando você estiver fazendo *dimensionamento baseado em desempenho*, uma vez que nem todos os pontos de dados podem estar disponíveis. Para *dimensionamento como local*, a classificação de confiança é sempre 5 estrelas, uma vez que as Migrações para Azure têm todos os dados necessários para dimensionar a VM. 

Para o dimensionamento baseado em desempenho, as Migrações para Azure precisam de dados de utilização da CPU e memória. Para cada disco anexado à VM, é necessário ter a IOPS de leitura/gravação e a taxa de transferência para fazer o dimensionamento baseado em desempenho. Da mesma forma, para cada adaptador de rede conectado à VM, as Migrações para Azure precisam da rede de entrada/saída para fazer o dimensionamento baseado em desempenho. Se qualquer um dos números de utilização acima não estiver disponível no vCenter Server, a recomendação de tamanho feita pelas Migrações para Azure pode não ser confiável. Dependendo do percentual dos pontos de dados disponíveis, é fornecida a classificação de confiança para a avaliação:

   **Disponibilidade dos pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 estrela
   21%-40% | 2 estrelas
   41%-60% | 3 estrelas
   61%-80% | 4 estrelas
   81%-100% | 5 estrelas

Uma avaliação pode não ter todos os pontos de dados disponíveis devido a um dos seguintes motivos:
- A configuração de estatísticas no vCenter Server não está definida para o nível 3, e a avaliação tem o dimensionamento baseado em desempenho como o critério de dimensionamento. Se a configuração de estatísticas no vCenter Server for menor que o nível 3, os dados de desempenho de disco e de rede não são coletados do vCenter Server. Nesse caso, a recomendação fornecida pelas Migrações para Azure para o disco e a rede é baseada somente no que foi alocado no local. Para o armazenamento, as Migrações para Azure recomendam discos padrão, pois não há como identificar se o disco tem IOPS/taxa de transferência altas e precisa de discos premium.
- A configuração de estatísticas no vCenter Server foi definida para o nível 3 para uma duração curta, antes de iniciar a descoberta. Por exemplo, se você alterar o nível de configuração de estatísticas para 3 hoje e iniciar a descoberta usando o dispositivo de coletor amanhã (após 24 horas), se estiver criando uma avaliação de um dia, você tem todos os pontos de dados. Mas se você estiver alterando a duração de desempenho nas propriedades de avaliação para um mês, a classificação de confiança diminui uma vez que o disco e os dados de desempenho de rede do último mês não estão disponíveis. Se você desejar considerar os dados de desempenho do último mês, é recomendável manter a configuração de estatísticas do vCenter Server no nível 3 por um mês antes de iniciar a descoberta. 
- Algumas VMs desativadas durante o período para o qual a avaliação é calculada. Se todas as VMs foram desligadas por um tempo, o vCenter Server não terá os dados de desempenho para aquele período. 
- Algumas VMs foram criadas durante o período para o qual a avaliação é calculada. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente somente há uma semana. Nesses casos, o histórico de desempenho das novas VMs não estará lá por toda a duração.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação estiver abaixo de 3 estrelas, é recomendável alterar o nível de configurações de estatísticas do vCenter Server para 3, aguardar o tempo da duração que deseja avaliar (1 dia/1 semana/1 mês) e, em seguida, realizar a descoberta e a avaliação. Se não for possível fazer isso, o dimensionamento baseado em desempenho pode não ser confiável, e é recomendável alternar para *dimensionamento como local* alterando as propriedades de avaliação.
 
## <a name="next-steps"></a>Próximas etapas

- [Saiba](how-to-scale-assessment.md) como descobrir e avaliar um grande ambiente VMware.
- Saiba como criar grupos de avaliação de alta confiança usando o [mapeamento de dependência do computador](how-to-create-group-machine-dependencies.md)
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
