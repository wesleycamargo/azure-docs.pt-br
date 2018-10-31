---
title: Dimensionar descoberta e avaliação usando o Migrações para Azure | Microsoft Docs
description: Descreve como avaliar grandes números de computadores locais usando o serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: raynew
ms.openlocfilehash: 32bed3a60c40b93471b75b9d54dccd822ccc3be8
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025864"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Descobrir e avaliar um grande ambiente VMware

As Migrações para Azure têm um limite de 1500 computadores por projeto e este artigo descreve como avaliar um grande número de VMs (máquinas virtuais) locais usando as [Migrações para Azure](migrate-overview.md).   

## <a name="prerequisites"></a>Pré-requisitos

- **VMware**: as VMs que você planeja migrar devem ser gerenciadas por um vCenter Server versão 5.5, 6.0 ou 6.5. Além disso, é necessário um host ESXi executando a versão 5.0 ou posterior para implantar a VM de coletor.
- **Conta do vCenter**: você precisa de uma conta de somente leitura para acessar o vCenter Server. O Migrações para Azure usa essa conta para descobrir as VMs locais.
- **Permissões**: no vCenter Server, você precisa de permissões para criar uma VM importando um arquivo no formato OVA.
- **Configurações de estatísticas**: esse requisito é aplicável apenas ao [modelo de descoberta única](https://docs.microsoft.com/azure/migrate/concepts-collector#discovery-methods). Para o modelo de descoberta única, as configurações de estatísticas para o vCenter Server devem ser definidas como nível 3 antes de você iniciar a implantação. O nível de estatísticas deve ser definido como 3 para cada um dos intervalos de coleta do dia, da semana e do mês. Se o nível for inferior ao 3 para cada um dos três intervalos de coleta, a avaliação funcionará, mas os dados de desempenho para armazenamento e rede não serão coletados. As recomendações de tamanho serão feitas então com base nos dados de desempenho para CPU e memória e nos dados de configuração para os adaptadores de rede e de disco.

### <a name="set-up-permissions"></a>Configurar permissões

As Migrações para Azure precisam de acesso aos servidores VMware para descobrir automaticamente as VMs para avaliação. A conta do VMware precisa das seguintes permissões:

- Tipo de usuário: pelo menos um usuário somente leitura
- Permissões: Objeto de Data Center –> Propagar para o Objeto Filho, role=Read-only
- Detalhes: usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.
- Para restringir o acesso, atribua aos objetos filho a função Nenhum acesso com o objeto filho Propagar para (hosts vSphere, datastores, VMs e redes).

Se você estiver implantando em um ambiente de locatário, está é uma maneira de configurar isso:

1.  Crie um usuário por locatário e, usando [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), atribua permissões somente leitura a todas as VMs que pertencem a um locatário específico. Em seguida, use essas credenciais para a descoberta. O RBAC garante que o usuário correspondente do vCenter tenha acesso apenas às VMs específicas ao locatário.
2. Você pode configurar o RBAC para usuários de locatários diferentes, conforme descrito no exemplo a seguir para o Usuário 1 e o Usuário 2:

    - Em **Nome de usuário** e **Senha**, especifique as credenciais de conta somente leitura que o coletor usará para descobrir VMs
    - Datacenter1 – dê permissões somente leitura para o Usuário 1 e o Usuário 2. Não propague as permissões para todos os objetos filho, pois você definirá permissões nas VMs individuais.

      - VM1 (Locatário 1) (permissão somente leitura para o Usuário 1)
      - VM2 (Locatário 1) (permissão somente leitura para o Usuário 1)
      - VM3 (Locatário 2) (permissão somente leitura para o Usuário 2)
      - VM4 (Locatário 2) (permissão somente leitura para o Usuário 2)

   - Se você executar a descoberta usando as credenciais do Usuário 1, apenas a VM1 e a VM2 serão descobertas.

## <a name="plan-your-migration-projects-and-discoveries"></a>Planejar descobertas e projetos de migração

Um único coletor das Migrações para Azure dá suporte para descoberta de vários servidores vCenter (um após o outro) e, também, dá suporte para descoberta em vários projetos de migração (um após o outro).

No caso de descoberta única, o coletor funciona em um modelo fire-and-forget e, uma vez que uma descoberta for feita, será possível usar o mesmo coletor para coletar dados de um vCenter Server diferente ou enviá-lo para um projeto de migração diferente. No caso de descoberta contínua, um dispositivo está conectado a um único projeto, para que você não possa usar o mesmo coletor para disparar uma segunda descoberta.

Planeje suas descobertas e avaliações com base nos limites a seguir:

| **Entidade** | **Limite de máquinas** |
| ---------- | ----------------- |
| Project    | 1.500             |
| Descoberta  | 1.500             |
| Avaliação | 1.500             |

Lembre-se dessas considerações de planejamento:

- Quando você faz uma descoberta usando o Coletor de Migrações para Azure, você pode definir o escopo de descoberta para um datacenter, cluster, host ou pasta do vCenter Server.
- Para fazer mais de uma descoberta, verifique no vCenter Server se as VMs que você deseja descobrir estão em pastas, datacenters, clusters ou hosts com suporte para limite de 1.500 máquinas.
- Recomendamos que, para fins de avaliação, você mantenha máquinas com interdependências dentro dos mesmos projeto e avaliação. No vCenter Server, verifique se as máquinas dependentes estão na mesma pasta, datacenter ou cluster para a avaliação.

Dependendo do cenário, será possível dividir as descobertas conforme abaixo:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>Vários servidores vCenter com menos de 1500 VMs
Se você tiver vários servidores do vCenter em seu ambiente e o número total de máquinas virtuais for menor que 1.500, você poderá usar a abordagem a seguir com base em seu cenário:

**Descoberta única:** você pode usar um único coletor e um projeto de migração única para descobrir todas as máquinas virtuais em todos os Servidores vCenter. Como o coletor de descoberta única descobre um vCenter Server por vez, você pode executar o mesmo coletor em todos os vCenter Servers, um após o outro, e apontar o coletor para o mesmo projeto de migração. Depois que todas as descobertas estiverem concluídas, será possível criar avaliações para os computadores.

**Descoberta contínua:** no caso de descoberta contínua, um dispositivo pode ser conectado a apenas um único projeto. Portanto, você precisa implantar um dispositivo para cada um dos seu servidores vCenter e, em seguida, criar um projeto para cada dispositivo e disparar descobertas de acordo.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>Vários servidores vCenter com mais de 1500 VMs

Se você tiver vários vCenter Servers com menos de 1500 máquinas virtuais por vCenter Server, mas mais de 1500 VMs em todos os vCenter Servers, será necessário criar vários projetos de migração (um projeto de migração pode conter apenas 1500 VMs). É possível conseguir isso criando um projeto de migração por vCenter Server e dividindo as descobertas.

**Descoberta única:** você pode usar um único coletor para descobrir cada vCenter Server (um após o outro). Caso queira que as descobertas iniciem ao mesmo tempo, também será possível implementar vários dispositivos e executar as descobertas em paralelo.

**Descoberta contínua:** você precisará criar vários dispositivos coletores (uma para cada Servidor do vCenter), conectar cada dispositivo a um projeto e disparar a descoberta de acordo.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Mais de 1500 computadores em um único vCenter Server

Se tiver mais de 1500 máquinas virtuais em um único vCenter Server, será necessário dividir a descoberta em vários projetos de migração. Para dividir as descobertas, é possível aproveitar o campo Escopo no dispositivo e especificar o host, o cluster, a pasta ou o datacenter que deseja descobrir. Por exemplo, se você tiver duas pastas no vCenter Server, uma com 1.000 VMs (Pasta1) e outra com 800 VMs (Pasta2), você poderá usar o campo de escopo para dividir as descobertas entre essas pastas.

**Descoberta única:** você pode usar o mesmo coletor para disparar as duas descobertas. Na primeira descoberta, é possível especificar a Pasta1 como o escopo e apontá-la para o primeiro projeto de migração. Assim que a primeira descoberta for concluída, você poderá usar o mesmo coletor, alterar o escopo para Pasta2 e detalhes do projeto de migração para o segundo projeto de migração e fazer a segunda descoberta.

**Descoberta contínua:** neste caso, você precisa criar dois dispositivos coletores; para o primeiro coletor, especifique o escopo como Pasta1 e conecte-o ao primeiro projeto de migração. Você pode, em paralelo, iniciar a descoberta da Pasta2 usando o segundo dispositivo coletor e conectá-lo ao segundo projeto de migração.

### <a name="multi-tenant-environment"></a>Ambiente multilocatário

Se você tiver um ambiente compartilhado entre locatários e não quiser descobrir as VMs de um locatário na assinatura de outro locatário, poderá usar o campo Escopo no coletor para escopo da descoberta. Se os locatários estiverem compartilhando hosts, crie uma credencial que tenha acesso somente leitura somente às VMs pertencentes ao locatário específico e, em seguida, use essa credencial no dispositivo do coletor e especifique o Escopo como o host a realizar a descoberta.

## <a name="discover-on-premises-environment"></a>Descobrir ambiente local

Quando estiver pronto com o plano, você poderá iniciar a descoberta das máquinas virtuais no local:

### <a name="create-a-project"></a>Criar um projeto

Crie um projeto Migrações para Azure de acordo com suas necessidades:

1. No Portal do Azure, selecione **Criar um recurso**.
2. Procure **Migrações para Azure** e selecione o serviço de **Migrações para Azure** nos resultados da pesquisa. Em seguida, selecione **Criar**.
3. Especifique um nome de projeto e a assinatura do Azure para o projeto.
4. Crie um novo grupo de recursos.
5. Especifique o local no qual você deseja criar o projeto e, em seguida, selecione **Criar**. Observe que você ainda pode avaliar suas VMs para um local de destino diferente. O local especificado para o projeto é usado para armazenar os metadados coletados das VMs locais.

### <a name="set-up-the-collector-appliance"></a>Configurar o dispositivo coletor

O Migrações para Azure cria uma VM local conhecida como o dispositivo coletor. Essa VM descobre as VMs do VMware locais e envia os metadados sobre elas para o serviço Migrações para Azure. Para configurar o dispositivo coletor, você baixa um arquivo OVA e o importa para a instância local do vCenter Server.

#### <a name="download-the-collector-appliance"></a>Baixar o dispositivo coletor

Se você tiver vários projetos, você precisará baixar o dispositivo coletor somente uma vez no vCenter Server. Depois de baixar e configurar o dispositivo, execute-o para cada projeto e especifique a ID e a chave exclusivas do projeto.

1. No projeto do Migrações para Azure, clique em **Introdução** > **Descobrir e Avaliar** > **Descobrir Máquinas**.
2. Em **Descobrir computadores**, há duas opções disponíveis para o dispositivo: clique em **Baixar** para baixar o dispositivo apropriado de acordo com sua preferência.

    a. **Descoberta única:** o dispositivo para esse modelo se comunica com o vCenter Server para reunir metadados sobre as VMs. Para coletar dados de desempenho das VMs, ele se baseia nos dados de desempenho histórico armazenados no vCenter Server e coleta o histórico de desempenho do último mês. Nesse modelo, o Azure Migrate coleta o contador médio (vs. contador de picos) para cada métrica, [saiba mais](https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected). Uma vez que esta é uma descoberta única, as alterações ao ambiente local não serão refletidas quando a descoberta for concluída. Se você quiser que as alterações sejam refletidas, precisará fazer uma redescoberta do mesmo ambiente no mesmo projeto.

    b. **Descoberta contínua:** o dispositivo para esse modelo cria o perfil do ambiente local continuamente para coletar dados de utilização em tempo real para cada VM. Neste modelo, as contagens de pico são coletadas para cada métrica (utilização da CPU, utilização de memória etc.). O modelo não depende das configurações de estatísticas do vCenter Server para a coleta de dados de desempenho. Você pode interromper a criação de perfil contínua a qualquer momento no dispositivo.

    > [!NOTE]
    > A funcionalidade de descoberta contínua está em versão prévia.

3. Em **Copiar credenciais do projeto**, copie a ID e a chave do projeto. Você precisará delas quando configurar o coletor.


#### <a name="verify-the-collector-appliance"></a>Verificar o dispositivo coletor

Verifique se o arquivo OVA é seguro antes de implantá-lo:

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.

2. Execute o seguinte comando para gerar o hash para o arquivo OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Verifique se o hash gerado corresponde às configurações a seguir.

#### <a name="one-time-discovery"></a>Descoberta única

<<<<<<< HEAD para OVA versão 1.0.9.15 (lançado em 23/10/2018)

 <a name="algorithm--hash-value"></a>**Algoritmo** | **o valor de Hash**
=======
Para a versão OVA 1.0.9.15

**Algoritmo** | **o valor de Hash**
>>>>>>> 20dc93529e7c0a4d17f2f4524752b5e2bead4e37 --- | --- MD5 | e9ef16b0c837638c506b5fc0ef75ebfa SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864 SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

<<<<<<< HEAD For OVA version 1.0.9.14 (Released on 8/24/2018) ======= For OVA version 1.0.9.14
>>>>>>> 20dc93529e7c0a4d17f2f4524752b5e2bead4e37

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

#### <a name="continuous-discovery"></a>Descoberta contínua

Para a versão OVA 1.0.10.4

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be

### <a name="create-the-collector-vm"></a>Criar a VM do coletor

Importe o arquivo baixado para o vCenter Server:

1. No console do cliente do vSphere, selecione **Arquivo** > **Implantar o Modelo de OVF**.

    ![Implantar o OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM do coletor e o objeto do inventário no qual a VM será hospedada.
4. Em **Host/Cluster**, especifique o host ou cluster no qual a VM do coletor será executada.
5. No armazenamento, especifique o destino de armazenamento para a VM do coletor.
6. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
7. Em **Mapeamento de rede**, especifique a rede à qual a VM do coletor se conectará. A rede precisa de conectividade com a Internet para poder enviar metadados para o Azure.
8. Examine e confirme as configurações e selecione **Concluir**.

### <a name="identify-the-id-and-key-for-each-project"></a>Identificar a chave e a ID para cada projeto

Se você tiver vários projetos, não deixe de identificar a ID e a chave para cada um. Você precisará da chave ao executar o coletor para descobrir as VMs.

1. No projeto, selecione **Introdução** > **Descobrir e Avaliar** > **Descobrir Máquinas**.
2. Em **Copiar credenciais do projeto**, copie a ID e a chave do projeto.
    ![Copiar credenciais do projeto](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="set-the-vcenter-statistics-level"></a>Definir o nível de estatísticas do vCenter

O dispositivo coletor descobre os seguintes metadados estáticos sobre as máquinas virtuais selecionadas.

1. Nome de exibição da VM (no vCenter)
2. Caminho de inventário da VM (host/pasta no vCenter)
3. Endereço IP
4. Endereço MAC
5. Sistema operacional
5. Número de núcleos, discos, NICs
6. Tamanho da memória, tamanhos de disco
7. E contadores de desempenho de VM, disco e rede, conforme listado na tabela a seguir.

Para descoberta única, a tabela a seguir lista os contadores de desempenho exatos que são coletados e também lista os resultados da avaliação que serão afetados se um determinado contador não é coletado.

Para a descoberta contínua, os mesmos contadores são coletados em tempo real (intervalo de 20 segundos), portanto, não há nenhuma dependência no nível de estatísticas do vCenter. O dispositivo então distribui os exemplos de 20 segundos para criar um único ponto de dados para cada 15 minutos, selecionando o valor de pico dos exemplos de 20 segundos, e os envia para o Azure.

|Contador                                  |Nível    |Nível por dispositivo  |Impacto de avaliação                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |ND                |Tamanho de VM recomendado e custo                    |
|mem.usage.average                        | 1       |ND                |Tamanho de VM recomendado e custo                    |
|virtualDisk.read.average                 | 2       |2                 |Tamanho do disco, custo de armazenamento e tamanho da VM         |
|virtualDisk.write.average                | 2       |2                 |Tamanho do disco, custo de armazenamento e tamanho da VM         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Tamanho do disco, custo de armazenamento e tamanho da VM         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Tamanho do disco, custo de armazenamento e tamanho da VM         |
|net.received.average                     | 2       |3                 |Tamanho da VM e custo da rede                        |
|net.transmitted.average                  | 2       |3                 |Tamanho da VM e custo da rede                        |

> [!WARNING]
> Para descoberta única, se você tiver configurado apenas um nível mais alto de estatísticas, levará até um dia para gerar os contadores de desempenho. Portanto, é recomendável que você execute a descoberta no dia seguinte. Para o modelo de descoberta contínua, aguarde pelo menos um dia depois de iniciar a descoberta para o dispositivo criar um perfil do ambiente e então criar avaliações.

### <a name="run-the-collector-to-discover-vms"></a>Executar o coletor para descobrir VMs

Para cada descoberta que você precisa executar, execute o coletor para descobrir VMs no escopo necessário. Execute as descobertas uma após a outra. Não há suporte para descobertas simultâneas e cada descoberta deve ter um escopo diferente.

1.  No console do cliente do vSphere, clique com botão direito do mouse na VM > **Abrir console**.
2.  Forneça o idioma, fuso horário e preferências de senha para o dispositivo.
3.  Na área de trabalho, selecione o atalho **Executar coletor**.
4.  No Coletor de Migrações para Azure, abra **Configurar pré-requisitos** e:

    a. Aceite os termos de licença e leia as informações de terceiros.

    O coletor verifica se a VM tem acesso à Internet.

    b. Se a VM acessa a Internet através de um proxy, selecione **Configurações de proxy** e especifique o endereço de proxy e a porta de escuta. Especifique as credenciais caso o proxy exija autenticação.

    O coletor verifica se o serviço coletor está em execução. O serviço é instalado por padrão na VM do coletor.

    c. Baixe e instale o VMware PowerCLI.

5.  Em **Especificar detalhes do vCenter Server**, faça o seguinte:
    - Especifique o nome (FQDN) ou endereço IP do vCenter Server.
    - Em **Nome de usuário** e **Senha**, especifique as credenciais de conta de somente leitura que o coletor usará para descobrir VMs no vCenter Server.
    - Em **Escopo de seleção**, selecione um escopo de descoberta de VM. O coletor só pode descobrir VMs dentro do escopo especificado. O escopo pode ser definido para uma pasta, datacenter ou cluster específicos. Ele não deve conter mais de 1.000 VMs.

6.  Em **Especificar projeto de migração**, especifique a ID e a chave do projeto. Se você não as copiou, abra o Portal do Azure da VM do coletor. Na página de **Visão geral** do projeto, selecione **Descobrir Máquinas** e copie os valores.  
7.  Em **Visualizar progresso de coleção**, monitore o processo de descoberta e verifique se os metadados coletados das VMs estão no escopo. O coletor fornece um tempo aproximado de descoberta.


#### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

No caso de descoberta única, o tempo de descoberta depende de quantas VMs estão sendo descobertas. Normalmente, para 100 VMs, depois que o coletor termina a execução, ele leva em torno de uma hora para concluir a configuração e a coleta dos dados de desempenho. Você pode criar avaliações (tanto baseadas em desempenho quanto em avaliações locais) imediatamente após a execução da descoberta.

No caso da descoberta contínua (em versão prévia), o coletor criará o perfil do ambiente local continuamente e permanecerá enviando os dados de desempenho a cada hora. Você pode examinar os computadores no portal após uma hora a contar do início da descoberta. É altamente recomendável aguardar pelo menos um dia antes de criar avaliações de desempenho para as VMs.

1. No projeto de migração, clique em **Gerenciar** > **Computadores**.
2. Verifique se as VMs que você deseja descobrir aparecem no portal.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um grupo](how-to-create-a-group.md) para avaliação.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
