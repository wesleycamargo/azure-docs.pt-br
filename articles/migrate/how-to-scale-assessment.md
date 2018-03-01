---
title: "Dimensionar descoberta e avaliação usando o Migrações para Azure | Microsoft Docs"
description: "Descreve como avaliar grandes números de computadores locais usando o serviço Migrações para Azure."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/08/2018
ms.author: raynew
ms.openlocfilehash: d588dc6037b6295594301b577fe9df31d169a9e6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Descobrir e avaliar um grande ambiente VMware

Este artigo descreve como avaliar grandes números de VMs (máquinas virtuais) locais usando o [Migrações para Azure](migrate-overview.md). O Migrações para Azure avalia essas máquinas para verificar se elas são adequadas para a migração para o Azure. O serviço fornece estimativas de dimensionamento e custo para a execução das máquinas no Azure.

## <a name="prerequisites"></a>pré-requisitos

- **VMware**: as VMs que você planeja migrar devem ser gerenciadas por um vCenter Server versão 5.5, 6.0 ou 6.5. Além disso, é necessário um host ESXi executando a versão 5.0 ou posterior para implantar a VM de coletor.
- **Conta do vCenter**: você precisa de uma conta de somente leitura para acessar o vCenter Server. O Migrações para Azure usa essa conta para descobrir as VMs locais.
- **Permissões**: no vCenter Server, você precisa de permissões para criar uma VM importando um arquivo no formato OVA.
- **Configurações de estatísticas**: as configurações de estatísticas para o vCenter Server devem ser definidas para o nível 3 antes de se iniciar a implantação. Se o nível for inferior ao 3, a avaliação funcionará, mas os dados de desempenho para armazenamento e rede não serão coletados. As recomendações de tamanho nesse caso serão feitas com base nos dados de desempenho para CPU e memória e nos dados de configuração para os adaptadores de rede e de disco.

## <a name="plan-azure-migrate-projects"></a>Planejar projetos do Migrações para Azure

Planeje suas descobertas e avaliações com base nos limites a seguir:

| **Entidade** | **Limite de máquinas** |
| ---------- | ----------------- |
| Project    | 1.500              | 
| Descoberta  | 1.000              |
| Avaliação | 400               |

- Se você tiver menos de 400 máquinas para descobrir e avaliar, precisará de um único projeto com uma única descoberta. Dependendo dos requisitos, você pode avaliar todas as máquinas em uma única avaliação ou dividir as máquinas em várias avaliações. 
- Se você tiver entre 400 e 1.000 máquinas para descobrir, você precisará de um único projeto com uma única descoberta. Mas você precisará de várias avaliações para avaliar essas máquinas, porque uma única avaliação pode conter até 400 máquinas.
- Se você tiver entre 1.001 e 1.500 máquinas, precisará de um único projeto contendo duas descobertas.
- Se você tiver mais de 1.500 máquinas, precisará criar vários projetos e executar várias descobertas, de acordo com suas necessidades. Por exemplo: 
    - Se você tiver 3.000 máquinas, poderá configurar dois projetos com duas descobertas cada ou três projetos com uma única descoberta cada.
    - Se tiver 5.000 máquinas, você poderá configurar quatro projetos: três com uma descoberta de 1.500 máquinas e uma com uma descoberta de 500 máquinas. Como alternativa, você pode configurar cinco projetos com uma única descoberta em cada um. 

## <a name="plan-multiple-discoveries"></a>Planejar várias descobertas

Você pode usar o mesmo Coletor de Migrações para Azure para fazer várias descobertas para um ou mais projetos. Lembre-se dessas considerações de planejamento:
 
- Quando você faz uma descoberta usando o Coletor de Migrações para Azure, você pode definir o escopo de descoberta para um datacenter, cluster, host ou pasta do vCenter Server.
- Para fazer mais de uma descoberta, verifique no vCenter Server se as VMs que você deseja descobrir estão em pastas, datacenters, clusters ou hosts que compatíveis com o limite de 1.000 máquinas.
- Recomendamos que, para fins de avaliação, você mantenha máquinas com interdependências dentro dos mesmos projeto e avaliação. No vCenter Server, verifique se as máquinas dependentes estão na mesma pasta, datacenter ou cluster para a avaliação.


## <a name="create-a-project"></a>Criar um projeto

Crie um projeto Migrações para Azure de acordo com suas necessidades:

1. No Portal do Azure, selecione **Criar um recurso**.
2. Procure **Migrações para Azure** e selecione o serviço **Migrações para Azure (versão prévia)** nos resultados da pesquisa. Em seguida, selecione **Criar**.
3. Especifique um nome de projeto e a assinatura do Azure para o projeto.
4. Crie um novo grupo de recursos.
5. Especifique o local no qual você deseja criar o projeto e, em seguida, selecione **Criar**. Observe que você ainda pode avaliar suas VMs para um local de destino diferente. O local especificado para o projeto é usado para armazenar os metadados coletados das VMs locais.

## <a name="set-up-the-collector-appliance"></a>Configurar o dispositivo coletor

O Migrações para Azure cria uma VM local conhecida como o dispositivo coletor. Essa VM descobre as VMs do VMware locais e envia os metadados sobre elas para o serviço Migrações para Azure. Para configurar o dispositivo coletor, você baixa um arquivo OVA e o importa para a instância local do vCenter Server.

### <a name="download-the-collector-appliance"></a>Baixar o dispositivo coletor

Se você tiver vários projetos, você precisará baixar o dispositivo coletor somente uma vez no vCenter Server. Depois de baixar e configurar o dispositivo, execute-o para cada projeto e especifique a ID e a chave exclusivas do projeto.

1. No projeto do Migrações para Azure, selecione **Introdução** > **Descobrir e Avaliar** > **Descobrir Máquinas**.
2. Em **Descobrir máquinas**, selecione **Baixar** para baixar o arquivo OVA.
3. Em **Copiar credenciais do projeto**, copie a ID e a chave do projeto. Você precisará delas quando configurar o coletor.

   
### <a name="verify-the-collector-appliance"></a>Verificar o dispositivo coletor

Verifique se o arquivo OVA é seguro antes de implantá-lo:

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Verifique se o hash gerado corresponde às configurações a seguir.

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
    MD5 |afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>Criar a VM do coletor

Importe o arquivo baixado para o vCenter Server:

1. No console do cliente do vSphere, selecione **Arquivo** > **Implantar o Modelo de OVF**.

    ![Implantar o OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM do coletor e o objeto do inventário no qual a VM será hospedada.
5. Em **Host/Cluster**, especifique o host ou cluster no qual a VM do coletor será executada.
7. No armazenamento, especifique o destino de armazenamento para a VM do coletor.
8. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
9. Em **Mapeamento de rede**, especifique a rede à qual a VM do coletor se conectará. A rede precisa de conectividade com a Internet para poder enviar metadados para o Azure. 
10. Examine e confirme as configurações e selecione **Concluir**.

## <a name="identify-the-id-and-key-for-each-project"></a>Identificar a chave e a ID para cada projeto

Se você tiver vários projetos, não deixe de identificar a ID e a chave para cada um. Você precisará da chave ao executar o coletor para descobrir as VMs.

1. No projeto, selecione **Introdução** > **Descobrir e Avaliar** > **Descobrir Máquinas**.
2. Em **Copiar credenciais do projeto**, copie a ID e a chave do projeto. 
    ![Copiar credenciais do projeto](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>Definir o nível de estatísticas do vCenter
Abaixo está a lista de contadores de desempenho coletados durante a descoberta. Os contadores estão, por padrão, disponíveis em vários níveis do vCenter Server. 

Recomendamos que você defina o nível mais alto comum (3) como o nível de estatísticas para que todos os contadores sejam coletados corretamente. Se você tiver definido o vCenter em um nível inferior, apenas alguns contadores poderão ser coletados completamente e o restante deles será definido como 0. A avaliação poderá então mostrar dados incompletos. 

A tabela a seguir também lista os resultados da avaliação que serão afetados se um determinado contador não for coletado.

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
> Se você configurou apenas um nível mais alto de estatísticas, levará até um dia para gerar os contadores de desempenho. Portanto, é recomendável que você execute a descoberta no dia seguinte.

## <a name="run-the-collector-to-discover-vms"></a>Executar o coletor para descobrir VMs

Para cada descoberta que você precisa executar, execute o coletor para descobrir VMs no escopo necessário. Execute as descobertas uma após a outra. Não há suporte para descobertas simultâneas e cada descoberta deve ter um escopo diferente.

1. No console do cliente do vSphere, clique com botão direito do mouse na VM > **Abrir console**.
2. Forneça o idioma, fuso horário e preferências de senha para o dispositivo.
3. Na área de trabalho, selecione o atalho **Executar coletor**.
4. No Coletor de Migrações para Azure, abra **Configurar pré-requisitos** e:

   a. Aceite os termos de licença e leia as informações de terceiros.

   O coletor verifica se a VM tem acesso à Internet.
   
   b. Se a VM acessa a Internet através de um proxy, selecione **Configurações de proxy** e especifique o endereço de proxy e a porta de escuta. Especifique as credenciais caso o proxy exija autenticação.

   O coletor verifica se o serviço coletor está em execução. O serviço é instalado por padrão na VM do coletor.

   c. Baixe e instale o VMware PowerCLI.

5. Em **Especificar detalhes do vCenter Server**, faça o seguinte:
    - Especifique o nome (FQDN) ou endereço IP do vCenter Server.
    - Em **Nome de usuário** e **Senha**, especifique as credenciais de conta de somente leitura que o coletor usará para descobrir VMs no vCenter Server.
    - Em **Escopo de seleção**, selecione um escopo de descoberta de VM. O coletor só pode descobrir VMs dentro do escopo especificado. O escopo pode ser definido para uma pasta, datacenter ou cluster específicos. Ele não deve conter mais de 1.000 VMs. 

6. Em **Especificar projeto de migração**, especifique a ID e a chave do projeto. Se você não as copiou, abra o Portal do Azure da VM do coletor. Na página de **Visão geral** do projeto, selecione **Descobrir Máquinas** e copie os valores.  
7. Em **Visualizar progresso de coleção**, monitore o processo de descoberta e verifique se os metadados coletados das VMs estão no escopo. O coletor fornece um tempo aproximado de descoberta.


### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

O tempo de descoberta depende de quantas VMs estão sendo descobertas. Geralmente, para 100 VMs, a descoberta é concluída em torno de uma hora após a conclusão da execução do coletor. 

1. No projeto do Planejador de Migrações, selecione **Gerenciar** > **Máquinas**.
2. Verifique se as VMs que você deseja descobrir aparecem no portal.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um grupo](how-to-create-a-group.md) para avaliação.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
