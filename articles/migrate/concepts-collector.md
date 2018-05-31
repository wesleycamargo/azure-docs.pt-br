---
title: Dispositivo Coletor no Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral do dispositivo Coletor e como configurá-lo.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: d0dd310a1f6dff389a4d3dd41dc389b7117272fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203650"
---
# <a name="collector-appliance"></a>Dispositivo Coletor

As [Migrações para Azure](migrate-overview.md) avaliam as cargas de trabalho locais para migração para o Azure. Este artigo fornece informações sobre como usar o dispositivo Coletor.



## <a name="overview"></a>Visão geral

Um Coletor de Migrações para Azure é um dispositivo leve que pode ser usado para descobrir o seu ambiente do vCenter local. Esse dispositivo descobre as VMs do VMware local e envia os metadados sobre elas para o serviço Migrações para Azure.

O dispositivo Coletor é um OVF que você pode baixar do projeto de Migrações para Azure. Ele cria uma instância de uma máquina virtual do VMware com 4 núcleos, 8 GB de RAM e um disco de 80 GB. O sistema operacional do dispositivo é Windows Server 2012 R2 (64 bits).

Você pode criar o Coletor seguindo as etapas aqui - [Como criar a VM do Coletor](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="collector-communication-diagram"></a>Diagrama de comunicação do coletor

![Diagrama de comunicação do coletor](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Componente      | Para comunicar-se com   | Porta obrigatória                            | Motivo                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Coletor      | Serviço Migrações para Azure | TCP 443                                  | O coletor deve ser capaz de comunicar-se com o serviço pela porta SSL 443 |
| Coletor      | vCenter Server        | Padrão 443                             | O coletor deve ser capaz de comunicar-se com o vCenter Server. Por padrão, ele conecta ao vCenter na 443. Se o vCenter escutar em uma porta diferente, essa porta deverá estar disponível como porta de saída no coletor |
| Coletor      | RDP|   | TCP 3389 | Para capacidade de RDP no computador do coletor |





## <a name="collector-pre-requisites"></a>Pré-requisitos do Coletor

O coletor precisa passar por algumas verificações de pré-requisito para garantir que ele possa se conectar ao serviço Migrações para Azure e carregar os dados descobertos. Este artigo analisa cada um dos pré-requisitos e a sua necessidade.

### <a name="internet-connectivity"></a>Conectividade com a Internet

O dispositivo do coletor precisa estar conectado à internet para enviar as informações dos computadores descobertos. Você pode conectar o computador à internet de uma das duas formas a seguir.

1. Você pode configurar o Coletor para ter conectividade direta com a internet.
2. Você pode configurar o Coletor para se conectar por meio de um servidor proxy.
    * Se o servidor proxy precisar de autenticação, você poderá especificar o nome de usuário e a senha nas configurações de conexão.
    * O endereço IP/FQDN do servidor Proxy deverá estar no formato http://IPaddress ou http://FQDN. Há suporte apenas para o proxy http.

> [!NOTE]
> Os servidores proxy baseados em HTTPS não são compatíveis com o coletor.

#### <a name="whitelisting-urls-for-internet-connection"></a>URLs de lista de permissões para conexão com a internet

A verificação de pré-requisito será bem-sucedida se o Coletor puder se conectar à internet por meio das configurações fornecidas. A verificação de conectividade é validada por meio da conexão com uma lista de URLs, conforme indicado na tabela a seguir. Se você estiver usando qualquer proxy firewall baseado em URL para controlar a conectividade de saída, certifique-se de permitir estes URLs exigidos:

**URL** | **Finalidade**  
--- | ---
*. portal.azure.com | É necessário verificar a conectividade com o serviço do Azure e validar problemas sincronização de hora.

Além disso, a verificação também tenta validar a conectividade com as seguintes URLs, mas não falhará na verificação se não estiver acessível. Configurar a lista de permissões para as URLs a seguir é opcional, mas é necessário executar etapas manuais para reduzir a verificação de pré-requisito.

**URL** | **Finalidade**  | **E se você não colocar na lista de permissões**
--- | --- | ---
*.oneget.org:443 | É necessário fazer o download do powershell com base no módulo vCenter PowerCLI. | Falha na instalação do PowerCLI. Instale manualmente o módulo.
*.windows.net:443 | É necessário fazer o download do powershell com base no módulo vCenter PowerCLI. | Falha na instalação do PowerCLI. Instale manualmente o módulo.
*.windowsazure.com:443 | É necessário fazer o download do powershell com base no módulo vCenter PowerCLI. | Falha na instalação do PowerCLI. Instale manualmente o módulo.
*.powershellgallery.com:443 | É necessário fazer o download do powershell com base no módulo vCenter PowerCLI. | Falha na instalação do PowerCLI. Instale manualmente o módulo.
*.msecnd.net:443 | É necessário fazer o download do powershell com base no módulo vCenter PowerCLI. | Falha na instalação do PowerCLI. Instale manualmente o módulo.
*.visualstudio.com:443 | É necessário fazer o download do powershell com base no módulo vCenter PowerCLI. | Falha na instalação do PowerCLI. Instale manualmente o módulo.

### <a name="time-is-in-sync-with-the-internet-server"></a>O horário não está sincronizado com o servidor de internet

O Coletor deve ser sincronizado com o servidor de horário de internet para garantir que as solicitações para o serviço sejam autenticadas. A URL portal.azure.com deverá estar acessível no Coletor para que o horário possa ser validado. Se o computador estiver dessincronizado, você precisara alterar o horário do relógio na VM do Coletor para corresponder ao horário atual da seguinte maneira:

1. Abra um prompt de comando de administrador na VM.
1. Para verificar o fuso horário, execute w32tm /tz.
1. Para sincronizar a hora, execute w32tm /resync.

### <a name="collector-service-should-be-running"></a>O serviço do Coletor deve estar em execução

O serviço Coletor de Migrações para Azure deve estar em execução na máquina. Esse serviço é iniciado automaticamente quando o computador é inicializado. Se o serviço não estiver em execução, você poderá iniciar o serviço do *Coletor de Migrações para Azure* por meio do painel de controle. O serviço do Coletor é responsável pela conexão com o servidor vCenter, pela coleta dos metadados e dos dados de desempenho do computador e pelo seu envio ao serviço.

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5

O módulo do powershell do VMware PowerCLI precisa ser instalado para que o Coletor possa se comunicar com o servidor do vCenter e consultar os detalhes do computador e seus dados de desempenho. O módulo do powershell é baixado e instalado automaticamente como parte da verificação de pré-requisito. O download automático requer algumas URLs na lista de permissões. Em caso de falha, será necessário fornecer acesso colocando-as na lista de permissões ou instalando o módulo manualmente.

Instale o módulo manualmente seguindo estas etapas:

1. Para instalar o PowerCli no Coletor sem conexão com a internet, siga as etapas descritas [neste link](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html).
2. Depois de instalar o módulo do PowerShell em um computador diferente com acesso à internet, copie os arquivos VMware.* desse computador para o computador do Coletor.
3. Reinicie as verificações de pré-requisito e confirme se o PowerCLI está instalado.

## <a name="connecting-to-vcenter-server"></a>Conectar-se ao vCenter Server

O Coletor deve se conectar ao vCenter Server e ser capaz de consultar as máquinas virtuais, seus metadados e seus contadores de desempenho. Esses dados são usados pelo projeto para calcular uma avaliação.

1. Para se conectar ao vCenter Server, uma conta somente leitura com permissões conforme indicado na tabela a seguir pode ser usada para executar a descoberta.

    |Tarefa  |Função/conta necessária  |Permissões  |
    |---------|---------|---------|
    |Descoberta baseada em dispositivo do Coletor    | Você precisa de pelo menos um usuário somente leitura        |Objeto de data center –> Propagar para o objeto filho, função = somente leitura         |

2. Somente os data centers acessíveis para a conta do vCenter especificada podem ser acessados para descoberta.
3. Você precisa especificar o endereço FQDN/IP do vCenter para se conectar ao vCenter Server. Por padrão, ele se conectará à porta 443. Se tiver configurado o vCenter para escutar em um número de porta diferente, você poderá especificá-lo como parte do endereço do servidor na forma IPAddress:Port_Number ou FQDN:Port_Number.
4. As configurações de estatísticas para o vCenter Server devem ser definidas para o nível 3 antes de se iniciar a implantação. Se o nível for inferior a 3, a descoberta será concluída, mas os dados de desempenho para armazenamento e rede não serão coletados. As recomendações de tamanho da avaliação nesse caso serão feitas com base nos dados de desempenho para CPU e memória e somente nos dados de configuração para os adaptadores de rede e de disco. [Leia mais](./concepts-collector.md) sobre quais dados são coletados e como eles afetam a avaliação.
5. O Coletor deve ter uma linha de visão de rede para o vCenter Server.

> [!NOTE]
> Somente o vCenter Server versões 5.5, 6.0 e 6.5 têm suporte oficialmente.

> [!IMPORTANT]
> Recomendamos que você defina o nível mais alto comum (3) como o nível de estatísticas para que todos os contadores sejam coletados corretamente. Se você tiver definido o vCenter em um nível inferior, apenas alguns contadores poderão ser coletados completamente e o restante deles será definido como 0. A avaliação poderá então mostrar dados incompletos.

### <a name="selecting-the-scope-for-discovery"></a>Seleção do escopo para descoberta

Após a conexão com o vCenter, você poderá selecionar um escopo de descoberta. Selecionar um escopo resulta na descoberta de todas as máquinas virtuais do caminho especificado do estoque do vCenter.

1. O escopo pode ser um datacenter, uma pasta ou um host ESXi.
2. Você só pode selecionar um escopo de cada vez. Para selecionar mais máquinas virtuais, você pode concluir uma descoberta e reiniciar o processo de descoberta com um novo escopo.
3. Você só pode selecionar um escopo que tenha *menos de 1500 máquinas virtuais*.

## <a name="specify-migration-project"></a>Especificar projeto de migração

Quando o vCenter local estiver conectado, e um escopo for especificado, você poderá agora especificar os detalhes do projeto de migração que precisam ser usados para descoberta e avaliação. Especifique a ID do projeto e a chave e conecte.

## <a name="start-discovery-and-view-collection-progress"></a>Inicie a descoberta e veja o progresso da coleta

Depois de iniciada a descoberta, as máquinas virtuais do vCenter serão descobertas, e seus metadados e dados de desempenho, enviados ao servidor. O status do progresso também informa sobre as seguintes IDs:

1. ID do coletor: uma ID exclusiva fornecida para o computador do Coletor. Essa ID não muda para um determinado computador em diferentes descobertas. Você pode usar essa ID no caso de falhas ao relatar o problema ao Suporte da Microsoft.
2. ID da sessão: uma ID exclusiva para o trabalho de coleta em execução. Você pode se referir à mesma ID de sessão no portal quando o trabalho de descoberta for concluído. Essa ID muda para todo trabalho de coleta. No caso de falhas, você pode relatar essa ID ao Suporte da Microsoft.

### <a name="what-data-is-collected"></a>Quais dados são coletados?

O trabalho de coleta descobre os seguintes metadados estáticos sobre as máquinas virtuais selecionadas.

1. Nome de exibição da VM (no vCenter)
2. Caminho de inventário da VM (host/pasta no vCenter)
3. Endereço IP
4. Endereço MAC
5. Sistema operacional
5. Número de núcleos, discos, NICs
6. Tamanho da memória, tamanhos de disco
7. E contadores de desempenho de VM, disco e rede, conforme listado na tabela a seguir.

A tabela a seguir lista os contadores de desempenho que são coletados e também lista os resultados da avaliação que serão afetados se um determinado contador não for coletado.

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

### <a name="time-required-to-complete-the-collection"></a>O tempo necessário para concluir a coleta

O Coletor somente descobre os dados do computador e os envia para o projeto. O projeto pode levar mais tempo antes de os dados descobertos serem exibidos no portal e você poder começar a criar uma avaliação.

Com base no número de máquinas virtuais no escopo selecionado, levará até 15 minutos para enviar os metadados estáticos para o projeto. Depois que os metadados estáticos estiverem disponíveis no portal, você poderá ver a lista de máquinas no portal e iniciar a criação de grupos. Não é possível criar uma avaliação até que o trabalho de coleta seja concluído e o projeto tenha processado os dados. Uma vez que o trabalho de coleta tenha sido concluído no Coletor, poderá levar até uma hora para os dados de desempenho estarem disponíveis no portal, com base no número de máquinas virtuais no escopo selecionado.

## <a name="locking-down-the-collector-appliance"></a>Bloquear o dispositivo coletor
Recomenda-se executar atualizações contínuas do Windows no dispositivo coletor. Se um coletor não for atualizado por 45 dias, ele iniciará o desligamento automático do computador. Se uma descoberta estiver em execução, o computador não será desligado, mesmo após o período de 45 dias. Após o trabalho de descoberta ser concluído, o computador será desligado. Se você estiver usando o coletor por mais de 45 dias, é recomendável manter o computador sempre atualizado executando o Windows Update.

Também recomendamos as seguintes etapas para proteger seu dispositivo
1. Não compartilhe nem deixe as senhas de administrador com partes não autorizadas.
2. Desligue o dispositivo quando não estiver em uso.
3. Coloque o dispositivo em uma rede segura.
4. Quando o trabalho de migração for concluído, exclua a instância do dispositivo. Certifique-se de também excluir os arquivos de backup em disco (VMDKs), pois os discos podem ter credenciais de vCenter armazenadas em cache.

## <a name="how-to-upgrade-collector"></a>Como atualizar o Coletor

É possível atualizar o Coletor para a última versão sem baixar o OVA novamente.

1. Baixe a última versão do [pacote de atualização](https://aka.ms/migrate/col/latestupgrade).
2. Para garantir que o hotfix baixado é seguro, abra a janela de comando do Administrador e execute o comando a seguir para gerar o hash do arquivo ZIP. O hash gerado deve corresponder ao hash mencionado em relação à versão específica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (exemplo de uso C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.5.zip SHA256)
3. Copie o arquivo zip para a máquina virtual do Coletor de Migrações para Azure (dispositivo do coletor).
4. Clique com o botão direito do mouse no arquivo zip e selecione Extrair Tudo.
5. Clique com o botão direito do mouse em Setup.ps1, selecione Executar com o PowerShell e siga as instruções na tela para instalar a atualização.

### <a name="list-of-updates"></a>Lista de atualizações

#### <a name="upgrade-to-version-1097"></a>Upgrade para versão 1.0.9.7

Para fazer upgrade da versão 1.0.9.7, faça o download do [pacote](https://aka.ms/migrate/col/upgrade_9_7)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

#### <a name="upgrade-to-version-1095"></a>Upgrade para versão 1.0.9.5

Para fazer upgrade da versão 1.0.9.5, baixe o [pacote](https://aka.ms/migrate/col/upgrade_9_5)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | d969ebf3bdacc3952df0310d8891ffdf
SHA1 | f96cc428eaa49d597eb77e51721dec600af19d53
SHA256 | 07c03abaac686faca1e82aef8b80e8ad8eca39067f1f80b4038967be1dc86fa1

## <a name="next-steps"></a>Próximas etapas

[Configurar uma avaliação para VMs do VMware locais](tutorial-assessment-vmware.md)
