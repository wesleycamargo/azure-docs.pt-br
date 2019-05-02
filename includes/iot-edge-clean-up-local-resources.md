---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 2b61cc8c5c448c28e96b06afa3556688a82567ed
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866820"
---
### <a name="delete-local-resources"></a>Excluir recursos locais

Se você quiser remover o tempo de execução do IoT Edge e os recursos relacionados do dispositivo, use os comandos pertinentes do sistema operacional do dispositivo. 

#### <a name="windows"></a> Windows

Desinstale o tempo de execução do IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

Quando o tempo de execução do IoT Edge for removido, os contêineres criados por ele são interrompidos, mas ainda existem no seu dispositivo. Visualizar todos os contêineres.

   ```powershell
   docker ps -a
   ```

Exclua os contêineres de tempo de execução que foram criados no dispositivo.

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Exclua todos os contêineres adicionais que foram listados na saída `docker ps` fazendo referência aos nomes de contêiner. 

#### <a name="linux"></a>Linux

Remova o tempo de execução do IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Quando o tempo de execução do IoT Edge for removido, os contêineres criados por ele são interrompidos, mas ainda existem no seu dispositivo. Visualizar todos os contêineres.

   ```bash
   sudo docker ps -a
   ```

Exclua os contêineres de tempo de execução que foram criados no dispositivo.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Exclua todos os contêineres adicionais que foram listados na saída `docker ps` fazendo referência aos nomes de contêiner. 

Remova o tempo de execução do contêiner.

   ```bash
   sudo apt-get remove --purge moby
   ```