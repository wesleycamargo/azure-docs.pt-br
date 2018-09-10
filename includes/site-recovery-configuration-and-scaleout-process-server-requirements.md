---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: ade53ba29d165b3b33ef25dabda25c4e60022608
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2018
ms.locfileid: "40133510"
---
**Configuração/Requisitos do servidor de processo**

**Componente** | **Requisito** 
--- | ---
**CONFIGURAÇÕES DE HARDWARE** | 
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | Três, incluindo o disco de OS, disco de cache do servidor de processo e unidade de retenção para failback 
Espaço livre em disco (cache do servidor de processo) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
 | 
**CONFIGURAÇÕES DE SOFTWARE** | 
Sistema operacional | Windows Server 2012 R2 <br> Windows Server 2016
Localidade do sistema operacional | Inglês (en-us)
Funções do Windows Server | Não habilite essas funções: <br> - Active Directory Domain Services <br>- Serviços de Informações da Internet <br> - Hyper-V 
Políticas de grupo | Não habilite estas políticas de grupo: <br> - Impedir o acesso ao prompt de comando. <br> - Impedir o acesso às ferramentas de edição do registro. <br> - Lógica de confiança para anexos de arquivo. <br> - Ativar a execução do script. <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nenhum site da Web padrão já existente <br> - Nenhum aplicativo/site da Web pré-existente escutando na porta 443 <br>- Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Habilitar configuração [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
| 
**CONFIGURAÇÕES DE REDE** | 
Tipo de endereço IP | estático 
Acesso à Internet | O servidor precisa ter acesso a estas URLs (diretamente ou via proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> O OVF também precisa ter acesso às seguintes URLs: <br> - https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Portas | 443 (orquestração do canal de controle)<br>9443 (transporte de dados) 
Tipo de NIC | VMXNET3 (se o Servidor de Configuração for uma VM do VMware)
 | 
**SOFTWARE PARA INSTALAÇÃO** | 
VMware vSphere PowerCLI 6.0 R3 | [PowerCLI versão 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalado se o servidor de configuração estiver em execução em uma VM do VMware.
MySQL | MySQL deve ser instalado. Você pode instalar manualmente ou o Azure Site Recovery pode instalá-lo.

**Requisitos de dimensionamento do servidor de processo/configuração**

**CPU** | **Memória** | **Cache de disco** | **Taxa de alteração de dados** | **Computadores replicados**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 soquetes * 4 núcleos \@ 2,5 GHz | 16 GB | 300 GB | 500 GB ou menos | < 100 computadores
12 vCPUs<br/><br/> 2 soquetes  * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB -1 TB | 100 a 150 computadores
16 vCPUs<br/><br/> 2 soquetes  * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 a 200 computadores

