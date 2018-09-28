---
title: Visão geral do Microsoft Azure Data Box Heavy | Microsoft Docs nos dados
description: Descreve o Azure Data Box, uma solução híbrida que permite a transferência de grandes quantidades de dados para o Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 8ecef86841bcf13a469f9c0dc81f114bd54acdba
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946504"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>O que é o Azure Data Box Heavy? (Visualização)

A solução híbrida do Microsoft Azure Data Box permite que você envie centenas de terabytes de dados para o Azure de forma rápida, barata e confiável. A transferência de dados segura é acelerada pela entrega de um dispositivo de armazenamento proprietário com capacidade de armazenamento de 1 PB por meio de frete. O dispositivo tem uma caixa reforçada para proteger os dados durante o transporte.

Atualmente, o Data Box Heavy está em versão prévia e você pode se inscrever para solicitar um dispositivo por meio do portal do Azure. Depois que o dispositivo é recebido em seu datacenter, você pode configurá-lo usando a interface do usuário da Web local. Copie os dados de seus servidores para o dispositivo e envie o dispositivo novamente para o Azure. No datacenter do Azure, seus dados são carregados automaticamente do dispositivo para o Azure. Todo o processo é acompanhado de ponta a ponta pelo serviço Data Box no portal do Azure.


> [!IMPORTANT]
> - O Data Box Heavy está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de implantar essa solução. 
> - Para solicitar um dispositivo, inscreva-se na [Versão Prévia do portal](http://aka.ms/).
> - Durante a versão prévia, o Data Box Heavy pode ser entregue aos clientes nos EUA e na União Europeia. Para saber mais, acesse [Disponibilidade por região](#region-availability).

## <a name="use-cases"></a>Casos de uso

O Data Box Heavy é ideal para a transferência de tamanhos de dados superiores a 500 TB em cenários com conectividade de rede limitada ou inexistente. A movimentação de dados pode ser única, periódica ou uma transferência de dados em massa inicial seguida por transferências periódicas. Veja a seguir os vários cenários em que o Data Box Heavy pode ser usado para a transferência de dados.

 - **Migração única** - quando uma grande quantidade de dados local é transferida para o Azure. 
     - Movimentação de uma biblioteca de mídia de fitas offline para o Azure para a criação de uma biblioteca de mídia online.
     - Migração do farm de VMs, do SQL Server e de aplicativos para o Azure
     - Movimentação de dados históricos para o Azure para análise aprofundada e geração de relatórios usando o HDInsight

 - **Transferência em massa inicial** – quando uma transferência em massa inicial é feita usando o Data Box Heavy (semente) seguida por transferências incrementais pela rede. 
     - Por exemplo, os parceiros de soluções de backup, como o Commvault e o Data Box Heavy, são usados para mover o backup histórico grande inicial para o Azure. Após a conclusão, os dados incrementais são transferidos pela rede para o armazenamento do Azure.

 - **Carregamentos periódicos** - quando grandes quantidades de dados são geradas periodicamente e precisam ser movidas para o Azure. Por exemplo, na exploração de energia, em que o conteúdo de vídeo é gerado em plataformas de petróleo e em produções de energia eólica.      

## <a name="benefits"></a>Benefícios

O Data Box Heavy foi projetado para mover grandes quantidades de dados para o Azure com pouco ou nenhum impacto sobre a rede. A solução oferece as seguintes vantagens:

- **Velocidade** – o Data Box Heavy usa adaptadores de rede de 40 Gbps de alto desempenho.

- **Seguro** – o Data Box Heavy tem proteções de segurança internas para o dispositivo, os dados e o serviço.
    - O dispositivo tem uma caixa reforçada protegida por parafusos resistentes a adulteração e adesivos com evidência de adulteração. 
    - Os dados do dispositivo são sempre protegidos por uma criptografia AES de 256 bits.
    - O dispositivo só pode ser desbloqueado com uma senha fornecida no portal do Azure.
    - O serviço é protegido pelos recursos de segurança do Azure.
    - Após o upload dos dados no Azure, os discos do dispositivo são apagados, de acordo com os padrões NIST 800-88r1.


<!--## Features and specifications

The Data Box Heavy device has the following features in this release.

| Specifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | < 50 lbs.                |
| Dimensions                                              | Device - Width: 309.0 mm Height: 430.4 mm Depth: 502.0 mm |            
| Rack space                                              | 7 U when placed in the rack on its side (cannot be rack-mounted)|
| Cables required                                         | 1 X power cable (included) <br> 2 RJ45 cables <br> 2 X SFP+ Twinax copper cables|
| Storage capacity                                        | 100 TB <br> 80 TB usable capacity after RAID 5 protection|
| Network interfaces                                      | 2 X 1 GbE interface - MGMT, DATA 3. <br> MGMT - for management, not user configurable, used for initial setup <br> DATA3 - for data, user configurable, and is dynamic by default <br> MGMT and DATA 3 can also work as 10 GbE <br> 2 X 10 GbE interface - DATA 1, DATA 2 <br> Both are for data, can be configured as dynamic (default) or static |
| Data transfer media                                     | RJ45, SFP+ copper 10 GbE Ethernet  |
| Security                                                | Rugged device casing with tamper-proof custom screws <br> Tamper-evident stickers placed at the bottom of the device|
| Data transfer rate                                      | Up to 80 TB in a day over 10 GbE network interface        |
| Management                                              | Local web UI - one-time initial setup and configuration <br> Azure portal - day-to-day device management        |-->

## <a name="components"></a>Componentes

O Data Box Heavy inclui os seguintes componentes:

* **Dispositivo Data Box Heavy** – um dispositivo físico com uma parte externa reforçada que armazena os dados com segurança. Este dispositivo tem uma capacidade de armazenamento utilizável de 800 TB. 

    
* **Serviço Data Box** – uma extensão do portal do Azure que permite gerenciar um dispositivo Data Box Heavy por meio uma interface da Web que você pode acessar em diferentes locais geográficos. Use o serviço Data Box para realizar a administração diária do dispositivo Data Box Heavy. As tarefas do serviço incluem como criar e gerenciar pedidos, exibir e gerenciar alertas e gerenciar compartilhamentos.  

* **Interface do usuário da Web local** – uma interface do usuário baseada na Web usada para configurar o dispositivo, de modo que ele se conecte à rede local e, em seguida, registrá-lo no serviço Data Box. Use também a interface do usuário da Web local para desligar e reiniciar o dispositivo, exibir logs de cópia e contatar o Suporte da Microsoft para arquivar uma solicitação de serviço.


## <a name="the-workflow"></a>O fluxo de trabalho

Um fluxo de trabalho típico inclui as seguintes etapas:

1. **Ordem** - Crie um pedido no Portal do Azure, forneça informações de envio e a conta de armazenamento de destino do Azure para seus dados. Se o dispositivo estiver disponível, o Azure preparará e enviará o dispositivo com uma ID de acompanhamento de remessa.

2. **Recebimento** – após a entrega do dispositivo, conecte o dispositivo à rede e à rede elétrica usando os cabos especificados. Ative e conecte-se ao dispositivo. Configure a rede do dispositivo e monte os compartilhamentos no computador host do qual deseja copiar os dados.

3. **Cópia dos dados** – copie os dados para os compartilhamentos do Data Box Heavy.

4. **Devolução** – prepare, desligue e envie o dispositivo novamente para o datacenter do Azure.

5. **Upload** – os dados são copiados automaticamente do dispositivo para o Azure. Os discos do dispositivo são apagados com segurança, de acordo com as diretrizes do NIST (National Institute of Standards and Technology).

Ao longo desse processo, você receberá uma notificação por email sobre todas as alterações de status. 

## <a name="region-availability"></a>Disponibilidade de região

O Data Box Heavy pode transferir dados de acordo com a região na qual o serviço foi implantado, o país ao qual o dispositivo é enviado e a conta de armazenamento do Azure de destino usada para a transferência dos dados. 

- **Disponibilidade do serviço** – para esta versão, o Data Box Heavy está disponível nas seguintes regiões:
    - Todas as regiões da nuvem pública nos Estados Unidos – Centro-oeste dos EUA, Oeste dos EUA 2, Oeste dos EUA, Centro-Sul dos EUA, Centro dos EUA, Centro-Norte dos EUA, Leste dos EUA e Leste dos EUA 2.
    - União Europeia – Europa Ocidental e Europa Setentrional.
    - Reino Unido – Sul do Reino Unido e Oeste do Reino Unido.
    - França – França Central e Sul da França.

- **Contas de Armazenamento de Destino** – as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure nas quais o serviço está disponível. 

## <a name="sign-up"></a>Inscrição

O Data Box Heavy está em versão prévia e uma inscrição é necessária. Execute as seguintes etapas para se inscrever no Data Box Heavy:

1. Entre no portal do Azure em: https://aka.ms/azuredatabox.
2. Clique em **+** para criar um recurso. Pesquise **Azure Data Box**. Selecione o serviço **Azure Data Box**.

    <!--![The Data Box Heavy sign up 1]()-->

3. Clique em **Criar**.

    <!--![The Data Box Heavy sign up 2]()-->

4. Escolha a assinatura que você deseja usar para a versão prévia do Data Box Heavy. Selecione a região na qual você deseja implantar o recurso Data Box Heavy. Na opção **Data Box Heavy**, clique em **Inscrever-se**.

   <!--![The Data Box Heavy sign up 3]()-->

5. Responda às perguntas sobre país de residência dos dados, período, serviço de destino do Azure para transferência de dados, largura de banda de rede e frequência da transferência de dados. Examine a Privacidade e os termos e marque a caixa de seleção A Microsoft pode usar seu endereço de email para contatá-lo.

    <!--![The Data Box Heavy sign up 4]()-->

Depois de se inscrever e estar habilitado para a versão prévia, você poderá solicitar um Data Box Heavy.




