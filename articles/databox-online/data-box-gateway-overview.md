---
title: Visão geral do Microsoft Azure Data Box Gateway | Microsoft Docs
description: Descreve o Azure Data Box Gateway, uma solução de virtualização que permite transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 4f1ab6d955c81ce6f7b141eef42341f43bb379f6
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165310"
---
# <a name="what-is-azure-data-box-gateway-preview"></a>O que é o Azure Data Box Gateway (versão prévia)? 

O Azure Data Box Gateway é uma solução de armazenamento que lhe permite enviar dados diretamente para o Azure. Este artigo fornece uma visão geral da solução, dos benefícios, das principais funcionalidades e dos cenários do Azure Data Box Gateway em que é possível implantar esse dispositivo. 

O Data Box Gateway é um dispositivo virtual baseado em uma máquina virtual provisionada em seu ambiente virtualizado ou hipervisor. O dispositivo virtual reside em seu local e você grava dados nele usando os protocolos NFS e SMB. Em seguida, o dispositivo transfere seus dados para o blob de blocos ou blob de páginas do Azure ou Arquivos do Azure. 

> [!IMPORTANT]
> O Data Box Gateway está em versão prévia. Examine os [termos de uso para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de implantar essa solução.

## <a name="use-cases"></a>Casos de uso

O Data Box Gateway pode ser usado para transferir dados para a nuvem, como arquivamento na nuvem, recuperação de desastre ou caso haja a necessidade de processar seus dados em escala de nuvem. Veja a seguir os vários cenários em que o Data Box Gateway pode ser usado para a transferência de dados.

- **Arquivamento na nuvem** – Copie centenas de TBs de dados para o armazenamento do Azure usando o Gateway do Data Box de uma maneira segura e eficiente. Os dados podem ser ingeridos uma vez ou continuamente para cenários de arquivamento.

- **Agregação de dados** – Agregue dados de várias fontes em um único local no Armazenamento do Microsoft Azure para análise e processamento de dados.  

- **Integração com cargas de trabalho locais** – Integre-se a cargas de trabalho locais, como backup e restauração que usam o armazenamento em nuvem e precisam de acesso local para arquivos comumente usados. 

## <a name="benefits"></a>Benefícios

O Data Box Gateway tem os seguintes benefícios:

- **Transferência de dados fácil** – Facilita a movimentação de dados para dentro e fora do Azure, tanto quanto trabalhar em um compartilhamento de rede local.  
- **Alto desempenho** – Acaba com a complicação do transporte de dados de rede com transferências de alto desempenho para e do Azure. 
- **Acesso rápido** – armazena em cache os arquivos mais recentes para acesso rápido de arquivos locais.  
- **Uso de largura de banda limitado** – Os dados podem ser gravados no Azure, mesmo quando a rede tem uma restrição para limitar o uso durante o horário comercial de pico.  

## <a name="key-capabilities"></a>Principais recursos

O Data Box Gateway tem as seguintes funcionalidades:

|Recurso |DESCRIÇÃO  |
|---------|---------|
|Velocidade     | Largura de banda e transferência de dados totalmente automatizada e altamente otimizada.|
|Protocolos com suporte     | Suporte para os protocolos SMB e NFS padrão para ingestão de dados. <br> Para obter mais informações sobre as versões com suporte, acesse [Data Box Gateway system requirements](data-box-gateway-system-requirements.md) (Requisitos do sistema do Gateway do Data Box).|
|Acesso de dados     | Acesso direto a dados dos Blobs de Armazenamento do Microsoft Azure e Arquivos do Azure usando APIs de nuvem para processamento adicional de dados na nuvem.|
|Acesso rápido     | Cache local no dispositivo para acesso rápido dos arquivos usados mais recentemente.|
|Upload offline     | O modo desconectado é compatível com cenários de upload offline.|
|Atualização dedados     | Capacidade de atualizar arquivos locais com a versão mais recente da nuvem.|
|Criptografia    | Suporte ao BitLocker para criptografar dados localmente e proteger a transferência de dados para a nuvem por meio do *https*       |
|Resiliência     | Resiliência de rede interna        |


## <a name="specifications"></a>Especificações

O dispositivo virtual Data Box Gateway tem as seguintes especificações:

| Especificações                                          | DESCRIÇÃO              |
|---------------------------------------------------------|--------------------------|
| Processadores virtuais (núcleos)   | Mínimo de 4 |            
| Memória  | Mínimo de 8 GB|
| Disponibilidade|Nó único|
| Discos| Disco do sistema operacional: 250 GB <br> Disco de dados: mínimo de 2 TB, com provisionamento dinâmico e deve ser ativado por SSDs|
| Interfaces de rede|1 ou mais adaptadores de rede virtual|
| Protocolos de compartilhamento de arquivo nativo|SMB e NFS  |
| Segurança| Autenticação para desbloquear o acesso ao dispositivo e aos dados <br> Dados em trânsito criptografados usando a criptografia AES de 256 bits|
| Gerenciamento| Interface do usuário da Web local – configuração inicial, diagnóstico e gerenciamento de energia do dispositivo <br> Portal do Azure – gerenciamento diário de dispositivos de Data Box Gateway       |


## <a name="components"></a>Componentes

A solução Data Box Gateway é composta pelo recurso de Data Box Gateway, dispositivo virtual de Data Box Gateway e uma interface do usuário da Web local.

* **Dispositivo virtual de Gateway do Data Box** – Um dispositivo baseado em uma máquina virtual provisionada em seu ambiente virtualizado ou hipervisor e que permite que você envie dados para o Azure. 
    
* **Recurso de Gateway do Data Box** – um recurso no portal do Azure que permite gerenciar um dispositivo de Gateway do Data Box de uma interface da Web que você pode acessar de diferentes localizações geográficas. Use o recurso de Data Box Gateway para criar e gerenciar recursos, exibir e gerenciar dispositivos e alertas e gerenciar compartilhamentos.  

    <!--![The Data Box Gateway service in Azure portal](media/data-box-overview/data-box-Gateway-service1.png)-->

    <!--For more information, go to [Use the Data Box Gateway service to administer your Data Box Gateway device](data-box-gateway-portal-ui-admin.md).-->

* **Interface do usuário da Web local do Data Box** – Use a interface do usuário da Web local para executar diagnósticos, desligar e reiniciar o dispositivo de Gateway do Data Box, exibir logs de cópia e contatar o Suporte da Microsoft para apresentar uma solicitação de serviço.

    <!--![The Data Box Gateway local web UI](media/data-box-gateway-overview/data-box-gateway-local-web-ui.png)-->

    <!-- For information about using the web-based UI, go to [Use the web-based UI to administer your Data Box](data-box-gateway-portal-ui-admin.md).-->


## <a name="region-availability"></a>Disponibilidade de região

O dispositivo físico Data Box Edge, o recurso do Azure e a conta de armazenamento de destino para a qual os dados são transferidos não precisam estar na mesma região.

- **Disponibilidade do recurso** – Para esta versão, o recurso Data Box Edge está disponível nas seguintes regiões:
    - **Estados Unidos** – Oeste dos EUA 2 e Leste dos EUA
    - **União Europeia** – Europa Ocidental
    - **Pacífico Asiático** – Sudeste da Ásia

- **Contas de Armazenamento de destino** – as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. 

    As regiões em que as contas de armazenamento armazenam os dados do Data Box devem estar localizadas perto do local em que o dispositivo está localizado para render desempenho ideal. Uma conta de armazenamento localizada longe do dispositivo resulta em longas latências de desempenho mais lentos. 


## <a name="next-steps"></a>Próximas etapas

- Examine os [requisitos do sistema do Data Box Gateway](data-box-gateway-system-requirements.md).
- Entenda os [limites do Data Box Gateway](data-box-gateway-limits.md).
- Implante o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.




