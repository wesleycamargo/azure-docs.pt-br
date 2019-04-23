---
title: Visão geral do Microsoft Azure Data Box Edge | Microsoft Docs
description: Descreve o Azure Data Box Edge, uma solução de armazenamento que usa um dispositivo físico para transferência baseada em rede para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 04/12/2019
ms.author: alkohli
ms.openlocfilehash: cd4d84d6698ddeda2fb00a8452fbe8cd02771e4b
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544561"
---
# <a name="what-is-azure-data-box-edge"></a>O que é o Azure Data Box Edge? 

O Azure Data Box Edge é uma solução de armazenamento que permite processar dados e enviá-los pela rede para o Azure. Este artigo fornece uma visão geral da solução, dos benefícios, das principais funcionalidades e dos cenários do Data Box Edge em que é possível implantar esse dispositivo. 

O Data Box Edge usa um dispositivo físico fornecido pela Microsoft para acelerar a transferência de dados segura. O dispositivo físico reside em seu local e você grava os dados nele usando os protocolos NFS e SMB. 

O Data Box Edge tem todas as funcionalidades do Data Box Gateway. Além disso, o Data Box é equipado com funcionalidades de computação borda habilitadas para IA que ajudam a analisar, processar ou filtrar dados à medida em que são movidos para o blob de blocos do Azure, blob de páginas ou para os Arquivos do Azure.  

## <a name="use-cases"></a>Casos de uso

O Azure Data Box Edge é um dispositivo de computação de borda habilitado para IA com funcionalidades de transferência de dados de rede. Veja a seguir os vários cenários em que o Data Box Edge pode ser usado para a transferência de dados.

- **Pré-processar dados** – Analise dados de dispositivos IoT ou locais para chegar ao resultado rapidamente e fique perto do local em que os dados são gerados. O Data Box Edge transfere o conjunto de dados completo para a nuvem para realizar um processamento mais avançado ou análise mais aprofundada.  O pré-processamento pode ser usado para: 

    - Agregar dados.
    - Modificar dados, por exemplo, para remover PII (Informações de identificação pessoal).
    - Colocar em subconjunto e transferir os dados necessários para realizar análises mais aprofundadas na nuvem.
    - Analisar e reagir a Eventos de IoT. 

- **Azure Machine Learning de Inferência** – Com o Data Box Edge, é possível executar modelos de ML (Machine Learning) para obter resultados rápidos que podem ser tratados antes que os dados sejam enviados para a nuvem. O conjunto de dados completo é transferido para continuar a treinar novamente e melhorar os modelos de ML.

- **Transferir dados pela rede para o Azure** – Use o Data Box Edge para transferir dados de maneira rápida e fácil para o Azure para habilitar ainda mais computação e análise ou para fins de arquivamento. 

## <a name="benefits"></a>Benefícios

O Data Box Edge tem os seguintes benefícios:

- **Transferência de dados fácil** – Torna a movimentação de dados para dentro e fora do Azure tão fácil quanto trabalhar em um compartilhamento de rede local.  
- **Alto desempenho** – Permite transferências de alto desempenho para o Azure e dele. 
- **Acesso rápido** – armazena em cache os arquivos mais recentes para acesso rápido de arquivos locais.  
- **Uso de largura de banda limitado** – Os dados podem ser gravados no Azure, mesmo quando a rede tem uma restrição para limitar o uso durante o horário comercial de pico.  
- **Transformar dados** – Habilita a análise, processamento ou filtragem de dados conforme eles são movidos para o Azure.

## <a name="key-capabilities"></a>Principais recursos

O Data Box Edge tem as seguintes funcionalidades:

|Recurso |DESCRIÇÃO  |
|---------|---------|
|Alto desempenho     | Largura de banda e transferência de dados totalmente automatizada e altamente otimizada.|
|Protocolos com suporte     | Suporte para os protocolos SMB e NFS padrão para ingestão de dados. <br> Para obter mais informações sobre as versões com suporte, acesse [Data Box Edge system requirements](https://aka.ms/dbe-docs) (Requisitos do sistema do Data Box Edge).|
|Computação       |Permite a análise, o processamento e a filtragem de dados.|
|Acesso de dados     | Acesso direto a dados dos Blobs de Armazenamento do Microsoft Azure e Arquivos do Azure usando APIs de nuvem para processamento adicional de dados na nuvem.|
|Acesso rápido     | Cache local no dispositivo para acesso rápido dos arquivos usados mais recentemente.|
|Upload offline     | O modo desconectado é compatível com cenários de upload offline.|
|Atualização dedados     | Capacidade de atualizar arquivos locais com a versão mais recente da nuvem.|
|Criptografia    | Suporte ao BitLocker para criptografar dados localmente e proteger a transferência de dados para a nuvem por meio do *https*.       |
|Resiliência     | Resiliência de rede interna.        |


## <a name="components"></a>Componentes

A solução Data Box Edge é composta pelo recurso do Data Box Edge, dispositivo físico Data Box Edge e uma interface do usuário da Web local.

* **Dispositivo físico Data Box Edge** – Um servidor montado em rack de 1U fornecido pela Microsoft que pode ser configurado para enviar dados ao Azure. 
    
* **Recurso do Data Box Edge** – um recurso no portal do Azure que permite a você gerenciar um dispositivo Data Box Edge em uma interface da Web que pode ser acessada em diferentes localizações geográficas. Use o recurso do Data Box Edge para criar e gerenciar recursos, exibir e gerenciar dispositivos e alertas e gerenciar compartilhamentos.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Para obter mais informações, acesse [Usar o serviço do Data Box Edge para administrar seu dispositivo Data Box Edge](https://aka.ms/dbe-docs).

* **Interface do usuário da Web local do Data Box** – Use a interface do usuário da Web local para executar diagnósticos, desligar e reiniciar o dispositivo Data Box Edge, exibir logs de cópia e contatar o Suporte da Microsoft para apresentar uma solicitação de serviço.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Para obter informações sobre como usar a interface do usuário baseada na Web, acesse [Use the web-based UI to administer your Data Box](https://aka.ms/dbe-docs) (Usar a interface do usuário baseada na Web para administrar seu Data Box).


## <a name="region-availability"></a>Disponibilidade de região

O dispositivo físico Data Box Edge, o recurso do Azure e a conta de armazenamento de destino para a qual os dados são transferidos não precisam estar na mesma região.

- **Disponibilidade do recurso** – Para esta versão, o recurso Data Box Edge está disponível nas seguintes regiões:
    - **Estados Unidos** – Leste dos EUA
    - **União Europeia** – Europa Ocidental
    - **Pacífico Asiático** – Sudeste da Ásia
    
    O Data Box Edge também pode ser implantado na Nuvem do Azure Governamental. Para obter mais informações, confira [O que é o Azure Governamental?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Contas de Armazenamento de destino** – as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. 

    As regiões em que as contas de armazenamento armazenam os dados do Data Box devem estar localizadas perto do local em que o dispositivo está localizado para render desempenho ideal. Uma conta de armazenamento localizada longe do dispositivo resulta em longas latências de desempenho mais lentos. 


## <a name="next-steps"></a>Próximas etapas

- Examine os [Requisitos de sistema do Data Box Edge](data-box-edge-system-requirements.md).
- Entenda os [limites do Data Box Edge](data-box-edge-limits.md).
- Implante o [Azure Data Box Edge](data-box-edge-deploy-prep.md) no portal do Azure.




