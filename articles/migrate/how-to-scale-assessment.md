---
title: "Dimensionar descoberta e avaliação com o Migrações para Azure | Microsoft Docs"
description: "Descreve como avaliar grandes números de computadores locais com o serviço Migrações para Azure."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dde0d07f-94b7-4b6a-a158-a89aa9324a35
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 930ec182cf329e7dda072dc49bd7f70abb413f2d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Descobrir e avaliar um grande ambiente VMware

Este artigo descreve como avaliar grandes números de computadores locais com o [Migrações para Azure](migrate-overview.md). As Migrações para Azure avaliam máquinas para verificar se são adequadas para a migração para o Azure, e fornecem estimativas de dimensionamento e custo para execução da máquina no Azure.

## <a name="prerequisites"></a>Pré-requisitos

- **VMware**: você precisa de pelo menos uma VM do VMware localizada em um host de ESXi ou cluster que executa a versão 5.0 ou posterior. O host ou cluster deve ser gerenciado por um servidor do vCenter executando a versão 5.5 ou 6.0.
- **Conta do vCenter**: você precisa de uma conta de somente leitura com credenciais de administrador para o servidor do vCenter. O Migrações para Azure usa essa conta para descobrir VMs.
- **Permissões**: no servidor do vCenter, você precisa de permissões para criar uma VM importando um arquivo no formato .OVA.
- **Configurações de estatísticas**: As configurações de estatísticas para o servidor do vCenter devem ser definidas para o nível 2 ou acima antes de iniciar a implantação.

## <a name="plan-azure-migrate-projects"></a>Planejar projetos do Migrações para Azure

Um projeto do Migrações para Azure pode avaliar até 1500 máquinas. Uma única descoberta em um projeto pode descobrir até 1000 computadores.

- Se você tiver menos de 1000 máquinas para descobrir, precisará de um único projeto com uma única descoberta.
- Se você tiver entre 1000 e 1500 máquinas, precisará de um único projeto com duas descobertas.
- Se você tiver mais de 1500 máquinas, precisará criar vários projetos e executar várias descobertas, de acordo com suas necessidades. Por exemplo:
    - Se você tiver 3000 máquinas, poderá configurar dois projetos com duas descobertas ou três projetos com uma única descoberta.
    - Se você tiver 5000 máquinas, poderá configurar quatro projetos. Dois com uma descoberta de 1500 máquinas e um com uma descoberta de 500 máquinas. Como alternativa, você pode configurar cinco projetos com uma única descoberta em cada um. 
- Ao fazer uma descoberta nas Migrações para Azure, você poderá definir o escopo de descoberta para uma pasta, datacenter ou cluster do VMware.
- Para fazer mais de uma descoberta, verifique no vCenter se as VMs que você deseja descobrir estão em pastas, datacenters ou clusters que dão suporte ao limite de 1000 computadores.
- Recomendamos que, para fins de avaliação, você mantenha máquinas com interdependências dentro dos mesmos projeto e avaliação. Portanto, no vCenter, verifique se as máquinas dependentes estão na mesma pasta, datacenter ou cluster para fins de avaliação.


## <a name="create-a-project"></a>Criar um projeto

Crie um projeto Migrações para Azure de acordo com suas necessidades.

1. No portal do Azure, clique em **Criar um recurso**.
2. Procure **Migrações para Azure** e selecione o serviço (**Migrações para Azure (versão prévia)** nos resultados da pesquisa. Em seguida, clique em **Criar**.
3. Especifique um nome de projeto e a assinatura do Azure para o projeto.
4. Crie um novo grupo de recursos.
5. Especifique a região na qual deseja criar o projeto e clique em **Criar**. Os metadados coletados de VMs locais são armazenados nessa região.

## <a name="set-up-the-collector-appliance"></a>Configurar o dispositivo coletor

O Migrações para Azure cria uma VM local conhecida como o dispositivo coletor. Essa VM descobre as VMs do VMware locais e envia os metadados sobre elas para o serviço Migrações para Azure. Para configurar o dispositivo coletor, você baixa um arquivo .OVA e o importa para o servidor do vCenter local a fim de criar a VM.

### <a name="download-the-collector-appliance"></a>Baixar o dispositivo coletor

Se você tiver vários projetos, precisará baixar o dispositivo coletor uma vez no servidor do vCenter. Depois de baixar e configurar o dispositivo, execute-o para cada projeto e especifique a ID e a chave exclusivos do projeto.

1. No projeto do Migrações para Azure, clique em **Introdução** > **Descobrir e Avaliar** > **Descobrir Máquinas**.
2. Em **Descobrir máquinas**, clique em **Baixar** para baixar o arquivo .OVA.
3. Em **Copiar credenciais do projeto**, copie a ID e a chave do projeto. Você precisará delas quando configurar o coletor.

   
### <a name="verify-the-collector-appliance"></a>Verificar o dispositivo coletor

Verifique se o arquivo .OVA é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para a OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. O hash gerado deve corresponder a estas configurações.

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | c283f00f46484bf673dc8383b01d0741 
    SHA1 | 8f49b47f53d051af1780bbc725659ce64e717ed4
    SHA256 | 7aecdbdb2aea712efe99d0c1444503f52d16de5768e783465e226fbbca71501d

## <a name="create-the-collector-vm"></a>Criar a VM do coletor

Importe o arquivo baixado para o servidor do vCenter.

1. No console do cliente do vSphere, clique em **Arquivo** > **Implantar o Modelo de OVF**.

    ![Implantar o OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo .ova.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM do coletor e o objeto do inventário no qual a VM será hospedada.
5. Em **Host/Cluster**, especifique o host ou cluster no qual a VM do coletor será executada.
7. No armazenamento, especifique o destino de armazenamento para a VM do coletor.
8. Em **Formato de disco**, especifique o tipo e o tamanho do disco.
9. Em **Mapeamento de rede**, especifique a rede à qual a VM do coletor se conectará. A rede precisa de conectividade com a Internet, para enviar metadados para o Azure. 
10. Revise e confirme as configurações e clique em **Concluir**.

## <a name="identify-the-key-and-id-for-each-project"></a>Identificar a chave e a ID para cada projeto

Se você tiver vários projetos, identifique a ID e a chave para cada um. Você precisará da chave ao executar o coletor para descobrir as VMs.

1. No projeto, clique em **Introdução** > **Descobrir e Avaliar** > **Descobrir Máquinas**.
2. Em **Copiar credenciais do projeto**, copie a ID e a chave do projeto. 
    ![ID do projeto](./media/how-to-scale-assessment/project-id.png)

## <a name="vcenter-statistics-level-to-collect-the-performance-counters"></a>Nível de estatísticas do vCenter para coletar os contadores de desempenho
Abaixo está a lista de contadores coletados durante a descoberta. Os contadores estão, por padrão, disponíveis em vários níveis do servidor do vCenter. Recomendamos que você defina o nível mais alto comum (nível 3) como o nível de estatísticas para que todos os contadores sejam coletados corretamente. Se você tiver definido o vCenter em um nível inferior, apenas alguns contadores poderão ser coletados completamente, e o restante serão definidos como 0. Portanto, a avaliação pode mostrar dados incompletos. A tabela a seguir também lista os resultados da avaliação que serão afetados se um determinado contador não for coletado.

|Contador                                  |Nível    |Por nível de dispositivo  |Impacto de avaliação                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |ND                |Tamanho da VM recomendado e custo                    |
|mem.usage.average                        | 1       |ND                |Tamanho da VM recomendado e custo                    |
|virtualDisk.read.average                 | 2       |2                 |Tamanho do disco, custo de armazenamento e tamanho da VM         |
|virtualDisk.write.average                | 2       |2                 |Tamanho do disco, custo de armazenamento e tamanho da VM         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Tamanho do disco, custo de armazenamento e tamanho da VM         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Tamanho do disco, custo de armazenamento e tamanho da VM         |
|net.received.average                     | 2       |3                 |Tamanho da VM e custo da rede                        |
|net.transmitted.average                  | 2       |3                 |Tamanho da VM e custo da rede                        |

> [!WARNING]
> Se você configurou apenas um nível mais alto de estatísticas, levará até um dia para gerar os contadores de desempenho. Portanto, é recomendável que você execute a descoberta no dia seguinte.

## <a name="run-the-collector-to-discover-vms"></a>Executar o coletor para descobrir VMs

Para cada descoberta que você precisa executar, execute o coletor para VMs de descoberta no escopo necessário. Execute as descobertas uma após a outra. Não há suporte para descobertas simultâneas e cada descoberta deve ter um escopo diferente.

1. No console do cliente do vSphere, clique com botão direito do mouse na VM > **Abrir console**.
2. Forneça o idioma, fuso horário e preferências de senha para o dispositivo.
3. Na área de trabalho, clique no atalho **Executar coletor**.
4. No Coletor de Migrações para Azure, abra **Configurar Pré-requisitos**.
    - Aceite os termos de licença e leia as informações de terceiros.
    - O coletor verifica se a VM tem acesso à Internet.
    - Se a VM acessa a internet através de um proxy, clique em **Configurações de proxy** e especifique o endereço de proxy e a porta de escuta. Especifique as credenciais caso o proxy exija autenticação.
    - O coletor verifica se o serviço de criador de perfil do Windows está em execução. O serviço é instalado por padrão na VM do coletor.
    - Baixe e instale o VMware PowerCLI.
. Em **Descobrir Máquinas**, faça o seguinte:
    - Especifique o nome (FQDN) ou endereço IP do servidor do vCenter.
    - Em **Nome de usuário** e **Senha**, especifique as credenciais de conta de somente leitura que o coletor usará para descobrir VMs no servidor do vCenter.
    - Em **Escopo de coleção**, selecione um escopo de descoberta de VM. O coletor só pode descobrir VMs dentro do escopo especificado. O escopo pode ser definido para uma pasta, datacenter ou cluster específicos. Ele não deve conter mais de 1000 VMs. 
    - Em n **Marcar categoria para agrupamento**, selecione **Nenhuma**.

        ![Selecionar escopo](./media/how-to-scale-assessment/select-scope.png)

1. Em **Selecionar Projeto**, especifique a ID e a chave do projeto. Se você não as copiou, abra o portal do Azure da VM do coletor. Na página de **Visão geral** do projeto, clique em **Descobrir Máquinas** e copie os valores.  
Em **Descoberta completa**, monitore o processo de descoberta e verifique se os metadados coletados das VMs estão no escopo. O coletor fornece um tempo aproximado de descoberta.


### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

O tempo de descoberta depende de quantas VMs estão sendo descobertas. Geralmente, para 100 VMs, após o coletor terminar a execução, leva em torno de uma hora para a descoberta ser concluída. 

1. No projeto do Planejador de Migrações, clique em **Gerenciar** > **Máquinas**.
2. Verifique se as VMs que você deseja descobrir aparecem no portal.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um grupo](how-to-create-a-group.md) para avaliação.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.