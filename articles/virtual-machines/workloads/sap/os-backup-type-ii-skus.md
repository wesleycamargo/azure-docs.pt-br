---
title: "Backup do sistema operacional e restauração do SAP HANA em SKUs do tipo II (Instâncias Grandes) do Azure | Microsoft Docs"
description: "Realizar backup do sistema operacional e restauração do SAP HANA em SKUs do tipo II (Instâncias Grandes) do Azure"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41349cd7fe3bf39b5b42c44ba47acf980d15ebe7
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2017
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Backup e restauração do sistema operacional para SKUs do tipo II

Este documento descreve as etapas para realizar backup e restauração do sistema operacional para o **SKUs do tipo II** de Instâncias Grandes de HANA. 

>[!NOTE]
>Os scripts de backup do sistema operacional usam o software ReaR, que vem pré-instalado no servidor.  

Depois que o provisionamento for concluído pela equipe de Gerenciamento de Serviços da Microsoft, o servidor é configurado por padrão com dois agendamentos de backup para fazer backup do sistema operacional completo. Verifique o status do agendamento do trabalho de backup usando o comando a seguir:
```
#crontab –l
```
É possível alterar o agendamento de backup a qualquer momento usando o seguinte comando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Como fazer um backup manual?

O backup do sistema operacional já é agendado usando um **trabalho cron**. No entanto, também é possível executar o backup do sistema operacional manualmente. Para fazer um backup manual, execute o seguinte comando:

```
#rear -v mkbackup
```
A tela a seguir mostra o backup manual do exemplo:

![como](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Como restaurar um backup?

É possível restaurar um backup completo ou um arquivo individual do backup. Para restaurar, use o seguinte comando:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Após a restauração, o arquivo é recuperado no diretório de trabalho atual.

O comando a seguir mostra a restauração de um arquivo */etc/fstabfrom* do arquivo de backup *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>É necessário copiar o arquivo para o local desejado após a restauração do backup.

A captura de tela a seguir mostra a restauração de um backup completo:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Como instalar a ferramenta ReaR e alterar a configuração? 

Os pacotes ReaR (Relax-and-Recover) vêm **pré-instalados** em **SKUs do tipo II** de HANA de Grandes Instâncias e nenhuma ação é necessária. É possível iniciar diretamente usando o ReaR para o backup do sistema operacional.
No entanto, caso você precise instalar os pacotes no seu próprio, siga as etapas listadas para instalar e configurar a ferramenta ReaR.

Para instalar os pacotes de backup do **ReaR**, use os seguintes comandos:

Para sistemas operacionais **SLES**, use o seguinte comando:
```
#zypper install <rear rpm package>
```
Para sistemas operacionais **RHEL**, use o seguinte comando: 
```
#yum install rear -y
```
Para configurar a ferramenta ReaR, você precisa atualizar os parâmetros **OUTPUT_URL** e **BACKUP_URL** no *arquivo /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

A captura de tela a seguir mostra a restauração de um backup completo: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
