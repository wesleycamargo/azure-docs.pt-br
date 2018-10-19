---
title: Atualizar o dispositivo Coletor nas Migrações para Azure | Microsoft Docs
description: Fornece informações sobre atualizações para o dispositivo Coletor de Migrações para Azure.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 6080096194ec5b11f1d5593fcbb6732d1551667f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431385"
---
# <a name="collector-update-release-history"></a>Histórico de versões de atualização do Coletor

Este artigo resume as informações de atualização para o dispositivo Coletor em [Migrações para Azure](migrate-overview.md).

O Coletor de Migrações para Azure é um dispositivo leve usado para descobrir um ambiente do vCenter local para fins de avaliação antes da migração para o Azure. [Saiba mais](concepts-collector.md).


## <a name="one-time-discovery-upgrade-versions"></a>Descoberta única: atualizar as versões

### <a name="version-10914"></a>Versão 1.0.9.14

Valores de hash para o pacote de [atualização 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>Versão 1.0.9.13

Valores de hash para o pacote de [atualização 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

### <a name="version-10911"></a>Versão 1.0.9.11

Valores de hash para o pacote de [atualização 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

### <a name="version-1097"></a>Versão 1.0.9.7

Valores de hash para o pacote de [atualização 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="continuous-discovery-upgrade-versions"></a>Descoberta contínua: atualizar as versões

Nenhuma atualização para o dispositivo de descoberta contínua está disponível no momento.

## <a name="run-an-upgrade"></a>Executar uma atualização

É possível atualizar o Coletor para a última versão sem baixar o OVA novamente.

1. Você faz o download do pacote de atualizações mais recente na lista abaixo.
2. Para ter certeza de que você fez o download de um hotfix seguro, abra a janela Comando do Administrador e execute o comando a seguir a fim de gerar o hash do arquivo ZIP. O hash gerado deve corresponder ao hash mencionado em relação à versão específica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Exemplo: **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. Copie o arquivo zip para a VM do dispositivo Coletor.
4. Clique com o botão direito do mouse no arquivo zip > **Extrair tudo**.
5. Clique com o botão direito do mouse em **Setup.ps1** > **Executar com PowerShell** e siga as instruções de instalação.


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](concepts-collector.md) sobre o dispositivo Coletor.
- [Execute uma avaliação](tutorial-assessment-vmware.md) para VMs do VMware.
