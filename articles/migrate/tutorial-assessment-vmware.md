---
title: Descobrir e avaliar as VMs do VMware locais para a migração para o Azure com o Migrações para Azure | Microsoft Docs
description: Descreve como descobrir e avaliar as VMs do VMware locais para a migração para o Azure, usando o serviço de Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cdd852e56cf966371cda62f89cee62956551f5c0
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313100"
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

## <a name="prerequisites"></a>Pré-requisitos

- **VMware**: as VMs que você planeja migrar devem ser gerenciadas por um vCenter Server executando a versão 5.5, 6.0, 6.5 ou 6.7. Além disso, é necessário um host ESXi executando a versão 5.5 ou superior para implantar a VM de coletor.
- **Conta do vCenter Server**: você precisa de uma conta somente leitura para acessar o vCenter Server. O Migrações para Azure usa essa conta para descobrir as VMs locais.
- **Permissões**: no vCenter Server, você precisa de permissões para criar uma VM importando um arquivo no formato .OVA.

## <a name="create-an-account-for-vm-discovery"></a>Criar uma conta para a descoberta de VM

As Migrações para Azure precisam de acesso aos servidores VMware para descobrir automaticamente as VMs para avaliação. Crie uma conta do VMware com as seguintes propriedades. Especifique essa conta durante a instalação das Migrações para Azure.

- Tipo de usuário: Pelo menos um usuário somente leitura
- Permissões: Objeto de data center –> Propagar para o objeto filho, função = somente leitura
- Detalhes: Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.
- Para restringir o acesso, atribua aos objetos filho a função Nenhum acesso com o objeto filho Propagar para (hosts vSphere, datastores, VMs e redes).


## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com).

## <a name="create-a-project"></a>Criar um projeto

1. No portal do Azure, clique em **Criar um recurso**.
2. Procure **Migrações para Azure** e selecione o serviço de **Migrações para Azure** nos resultados da pesquisa. Em seguida, clique em **Criar**.
3. Especifique um nome de projeto e a assinatura do Azure para o projeto.
4. Crie um novo grupo de recursos.
5. Especifique a localização geográfica na qual você deseja criar o projeto e clique em **Criar**. Você só pode criar um projeto das Migrações para Azure nas seguintes regiões geográficas. No entanto, você ainda pode planejar a migração de qualquer local de destino do Azure. A localização geográfica especificada para o projeto só é usada para armazenar os metadados coletados das VMs locais.

**Geografia** | **Localização de armazenamento**
--- | ---
Azure Government | Gov. dos EUA – Virgínia
Ásia | Sudeste Asiático
Europa | Europa Setentrional ou Europa Ocidental
Estados Unidos | Leste dos EUA ou Centro-oeste dos EUA

![Migrações para Azure](./media/tutorial-assessment-vmware/project-1.png)


## <a name="download-the-collector-appliance"></a>Baixe o dispositivo coletor

O Migrações para Azure cria uma VM local conhecida como o dispositivo coletor. Essa VM descobre as VMs do VMware locais e envia os metadados sobre elas para o serviço Migrações para Azure. Para configurar o dispositivo coletor, baixe um arquivo .OVA e importe-o para o servidor do vCenter local para criar a VM.

1. No projeto do Migrações para Azure, clique em **Introdução** > **Descobrir e Avaliar** > **Descobrir Máquinas**.
2. Em **Descobrir máquinas**, clique em **Fazer o download** para fazer o download do dispositivo.

    O dispositivo do Migrações para Azure se comunica com o vCenter Server e cria o perfil do ambiente local continuamente para coletar dados de utilização em tempo real para cada VM. Ele coleta as contagens de pico para cada métrica (utilização da CPU, utilização de memória, etc.). O modelo não depende das configurações de estatísticas do vCenter Server para a coleta de dados de desempenho. Você pode interromper a criação de perfil contínua a qualquer momento no dispositivo.

    > [!NOTE]
    > O dispositivo de descoberta única já foi preterido, pois esse método se baseou nas configurações de estatísticas do vCenter Server para disponibilidade de pontos de dados de desempenho e contadores de desempenho médios coletados, o que resultou em subdimensionamento de VMs para migração para o Azure.

    **Avaliações rápidas:** com o dispositivo de descoberta contínua, após a conclusão da descoberta (leva algumas horas dependendo do número de VMs), você poderá criar avaliações imediatamente. Já que a coleta de dados de desempenho começa quando você inicia a descoberta, se você estiver procurando avaliações rápidas, deverá escolher o critério de dimensionamento na avaliação como *local*. Para avaliações baseadas no desempenho, é aconselhável esperar pelo menos um dia após o início da descoberta para obter recomendações de tamanhos confiáveis.

    O dispositivo coleta apenas dados de desempenho continuamente e não detecta nenhuma alteração de configuração no ambiente local (ou seja, adição de VM, exclusão, adição de disco, etc.). Se houver uma alteração de configuração no ambiente local, você poderá fazer o seguinte para refletir as alterações no portal:

    - Adição de itens (VMs, discos, núcleos, etc.): Para refletir essas alterações no portal do Azure, você pode interromper a descoberta do dispositivo e iniciá-la novamente. Isso garantirá que as alterações sejam atualizadas no projeto de Migrações para Azure.

    - Exclusão de VMs: Devido à maneira como o dispositivo é projetado, a exclusão de VMs não é refletida, mesmo se você parar e iniciar a descoberta. Isso ocorre porque os dados das descobertas subsequentes são anexados a descobertas antigas e não substituídos. Nesse caso, você pode simplesmente ignorar a VM no portal, removendo-a do grupo e recalculando a avaliação.


3. Em **Copiar credenciais do projeto**, copie a ID e a chave do projeto. Você precisará delas quando configurar o coletor.

    ![Baixe o arquivo .ova](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verifique o dispositivo coletor

Verifique se o arquivo .OVA é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. O hash gerado deve corresponder a estas configurações.

#### <a name="continuous-discovery"></a>Descoberta contínua

  Para a versão OVA 1.0.10.11

  **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 5f6b199d8272428ccfa23543b0b5f600
    SHA1 | daa530de6e8674a66a728885a7feb3b0a2e8ccb0
    SHA256 | 85da50a21a7a6ca684418a87ccc1dd4f8aab30152c438a17b216ec401ebb3a21

  Para a versão OVA 1.0.10.9

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 169f6449cc1955f1514059a4c30d138b
  SHA1 | f8d0a1d40c46bbbf78cd0caa594d979f1b587c8f
  SHA256 | d68fe7d94be3127eb35dd80fc5ebc60434c8571dcd0e114b87587f24d6b4ee4d

  Para a versão OVA 1.0.10.4

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
  SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
  SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be


#### <a name="one-time-discovery-deprecated-now"></a>Descoberta avulsa (já preterido)

Esse modelo já foi preterido. Será oferecido suporte para dispositivos existentes.

  Para a versão OVA 1.0.9.15

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
  SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
  SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

  Para a versão OVA 1.0.9.14

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
  SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
  SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

  Para a versão OVA 1.0.9.12

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | d0363e5d1b377a8eb08843cf034ac28a
  SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
  SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

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
4. Clique em **Verificar se há atualizações** na barra superior da interface do usuário do coletor e verifique se o coletor está executando a versão mais recente. Caso contrário, você pode optar por baixar o pacote de atualização mais recente do link e atualizar o coletor.
5. No Coletor de Migrações para Azure, abra **Configurar Pré-Requisitos**.
   - Selecione a nuvem do Azure para a qual você planeja migrar (Azure Global ou Azure Governamental).
   - Aceite os termos de licença e leia as informações de terceiros.
   - O coletor verifica se a VM tem acesso à Internet.
   - Se a VM acessa a internet através de um proxy, clique em **Configurações de proxy** e especifique o endereço de proxy e a porta de escuta. Especifique as credenciais caso o proxy exija autenticação. [Saiba mais](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-prerequisites) sobre os requisitos de conectividade com a Internet e a [lista de URLs](https://docs.microsoft.com/azure/migrate/concepts-collector) acessada pelo coletor.

     > [!NOTE]
     > O endereço de proxy precisa ser inserido na forma http:\//ProxyIPAddress ou http:\//ProxyFQDN. Há suporte apenas para o proxy HTTP. Se você tiver um proxy de interceptação, a conexão de internet poderá falhar no início se o certificado de proxy não foi importado. [Saiba mais](https://docs.microsoft.com/azure/migrate/concepts-collector) sobre como pode corrigir isso importando o certificado de proxy como um certificado confiável na VM do coletor.

   - O coletor verifica se o serviço coletor está em execução. O serviço é instalado por padrão na VM do coletor.
   - Baixe e instale o VMware PowerCLI.

6. Em **Especificar detalhes do vCenter Server**, faça o seguinte:
    - Especifique o nome (FQDN) ou endereço IP do servidor do vCenter.
    - Em **Nome de usuário** e **Senha**, especifique as credenciais de conta de somente leitura que o coletor usará para descobrir VMs no servidor do vCenter.
    - Em **Escopo de coleção**, selecione um escopo de descoberta de VM. O coletor só pode descobrir VMs dentro do escopo especificado. O escopo pode ser definido para uma pasta, datacenter ou cluster específicos. Ele não deve conter mais de 1500 VMs. [Saiba mais](how-to-scale-assessment.md) sobre como você pode descobrir um ambiente maior.

7. Em **Especificar projeto de migração**, especifique a ID do projeto de Migrações para Azure e da chave que você copiou do portal. Se não as copiar, abra o portal do Azure da VM do coletor. Na página de **Visão geral** do projeto, clique em **Descobrir Máquinas** e copie os valores.  
8. Em **Exibir progresso da coleção**, monitore o status da descoberta. [Saiba mais](https://docs.microsoft.com/azure/migrate/concepts-collector) sobre quais dados são coletados pelo Coletor de Migrações para Azure.

> [!NOTE]
> O coletor só oferece suporte para "Inglês (Estados Unidos)" como o idioma do sistema operacional e o idioma da interface do coletor.
> Se você alterar as configurações em um computador que deseja avaliar, dispare a descoberta novamente antes de executar a avaliação. No coletor, use a opção **Iniciar coleta novamente** para fazer isso. Depois que a coleção for concluída, selecione a opção **Recalcular** para a avaliação no portal, para obter os resultados atualizados da avaliação.


### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

O dispositivo do coletor criará o perfil do ambiente local continuamente e continuará enviando os dados de desempenho a cada hora. Você pode examinar os computadores no portal após uma hora a contar do início da descoberta.

1. No projeto de migração, clique em **Gerenciar** > **Computadores**.
2. Verifique se as VMs que você deseja descobrir aparecem no portal.


## <a name="create-and-view-an-assessment"></a>Criar e exibir uma avaliação

Após as VMs terem sido descobertas no portal, você pode agrupá-las e criar uma avaliações. Crie imediatamente como avaliações locais depois que as VMs forem descobertas no portal. É recomendável aguardar pelo menos um dia antes de criar avaliações baseadas em desempenho para obter recomendações de tamanho confiável.

1. Na página **Visão geral** do projeto, clique em **Criar avaliação**.
2. Clique em **Exibir tudo** para examinar as propriedades da avaliação.
3. Crie o grupo e especifique um nome de grupo.
4. Selecione as máquinas que deseja adicionar ao grupo.
5. Clique em **Criar avaliação**, para criar o grupo e a avaliação.
6. Depois que a avaliação é criada, é possível exibi-la em **Visão geral** > **Painel**.
7. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.

> [!NOTE]
> É altamente recomendável aguardar pelo menos um dia, depois de iniciar a descoberta, para criar uma avaliação. Se você quiser atualizar uma avaliação existente com os dados de desempenho mais recentes, poderá usar o comando **Recalcular** na avaliação para atualizá-la.

### <a name="assessment-details"></a>Detalhes da avaliação

Uma avaliação inclui informações sobre se as VMs locais são compatíveis para o Azure, qual seria o tamanho correto de VM para executar a VM no Azure e os custos estimados de mensais do Azure.

![Relatório de avaliação](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Preparação do Azure

O modo de exibição da preparação para o Azure na avaliação mostra o status de preparação de cada VM. Dependendo das propriedades da VM, cada VM pode ser marcada como:
- Pronta para o Azure
- Condicionalmente pronta para o Azure
- Não pronta para o Azure
- Preparação desconhecida

Para VMs que estão prontas, o Migrações para Azure recomenda um tamanho de VM no Azure. A recomendação de tamanho feita pelas Migrações para Azure depende do critério de tamanho especificado nas propriedades de avaliação. Se o critério de dimensionamento for baseado no desempenho do dimensionamento, a recomendação de tamanho é feita levando em consideração o histórico de desempenho das VMs (CPU e memória) e dos discos (IOPS e taxa de transferência). Se o critério de dimensionamento for “como o local”, o serviço Migrações para Azure não considera os dados de desempenho para a VM e os discos. A recomendação para o tamanho da VM no Azure é feita observando o tamanho da VM local e o tamanho do disco é feito com base no tipo de armazenamento especificado nas propriedades de avaliação (o padrão é discos premium). [Saiba mais](concepts-assessment-calculation.md) sobre como o dimensionamento é feito nas Migrações para Azure.

Para VMs que não estão prontas ou condicionalmente prontas para o Azure, as Migrações para Azure explicam os problemas de preparação e fornecem etapas de correção.

As VMs para as quais as Migrações para Azure não conseguem identificar a Preparação para o Azure (devido à indisponibilidade de dados) são marcadas como preparação desconhecida.

Além do dimensionamento e da Preparação para o Azure, as Migrações para Azure também sugerem ferramentas que você pode usar para a migração da VM. Isso requer uma descoberta mais profunda no ambiente local. [Saiba mais](how-to-get-migration-tool.md) sobre como fazer uma descoberta mais profunda com a instalação de agentes nos computadores locais. Se os agentes não estiverem instalados nos computadores locais, sugere-se a migração fácil usando do [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Se os agentes estiverem instalados no computador local, as Migrações para Azure analisam os processos em execução no computador e identificam se ele é um computador de banco de dados ou não. Se o computador for um computador de banco de dados, sugere-se o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview), caso contrário, sugere-se o Azure Site Recovery como a ferramenta de migração.

  ![Preparação para avaliação](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Estimativa de custo mensal

Essa exibição mostra o custo total de computação e armazenamento da execução das máquinas virtuais no Azure junto com os detalhes de cada máquina. As estimativas de custo são calculadas com base nas recomendações de tamanho feitas pelas Migrações para Azure para um computador, seus discos e as propriedades de avaliação.

> [!NOTE]
> A estimativa de custo fornecida pelo serviço Migrações para Azure serve para executar as VMs locais como VMs de infraestrutura como serviço (IaaS) do Azure. As Migrações para Azure não consideram nenhum custo de Plataforma como Serviço (PaaS) ou Software como Serviço (SaaS).

Os custos mensais estimados de computação e armazenamento são agregados para todas as VMs no grupo.

![Avaliação de custo de VM](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>Classificação de confiança

Cada avaliação baseada em desempenho das Migrações para Azure está associada a uma classificação de confiança que varia de 1 a 5 estrelas (1 estrela sendo a mais baixa e 5 estrelas sendo a mais alta). A classificação de confiança é atribuída a uma avaliação com base na disponibilidade de pontos de dados necessários para calcular a avaliação. A classificação de confiança de uma avaliação ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pelas Migrações para Azure. A classificação de confiança não é aplicável a avaliações locais "no estado em que se encontram".

Para o dimensionamento com base no desempenho, as Migrações para Azure precisam de dados de utilização da CPU e memória da VM. Além disso, para cada disco anexado à VM, é necessário ter os dados da taxa de transferência e a IOPS do disco. Da mesma forma, para cada adaptador de rede conectado à VM, as Migrações para Azure precisam da entrada/saída da rede para fazer o dimensionamento com base no desempenho. Se qualquer um dos números de utilização acima não estiver disponível no vCenter Server, a recomendação de tamanho feita pelas Migrações para Azure pode não ser confiável. Dependendo da porcentagem de pontos de dados disponível, o nível de confiança para as avaliações é fornecido conforme abaixo:

   **Disponibilidade dos pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 estrela
   21%-40% | 2 estrelas
   41%-60% | 3 estrelas
   61%-80% | 4 estrelas
   81%-100% | 5 estrelas

Uma avaliação pode não ter todos os pontos de dados disponíveis devido a um dos seguintes motivos:

- Você não criou o perfil do ambiente pelo tempo para o qual está criando a avaliação. Por exemplo, se você está criando a avaliação com duração de desempenho definida como 1 dia, precisa aguardar pelo menos um dia após iniciar a descoberta para que todos os pontos de dados sejam coletados.

- Algumas VMs foram desativadas durante o período para o qual a avaliação é calculada. Se todas as VMs foram desligadas por algum tempo, o vCenter Server não poderá coletar os dados de desempenho daquele período.

- Algumas VMs foram criadas durante o período para o qual a avaliação é calculada. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente somente há uma semana. Nesses casos, o histórico de desempenho das novas VMs não estará lá durante todo o período.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação estiver abaixo de 5 estrelas, aguarde pelo menos um dia para que o dispositivo crie o perfil do ambiente e, em seguida, *Recalcule* a avaliação. Se não for possível fazer isso, o dimensionamento com base no desempenho poderá não ser confiável e é recomendável alternar para *conforme dimensionamento local* alterando as propriedades de avaliação.

## <a name="next-steps"></a>Próximas etapas

- [Saiba](how-to-modify-assessment.md) como personalizar uma avaliação de acordo com suas necessidades.
- Saiba como criar grupos de avaliação de alta confiança usando o [mapeamento de dependência do computador](how-to-create-group-machine-dependencies.md)
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
- [Saiba](how-to-scale-assessment.md) como descobrir e avaliar um grande ambiente VMware.
- [Saiba mais](resources-faq.md) sobre as perguntas frequentes sobre o serviço Migrações para Azure
