---
title: Corrigir problemas de certificado para o Azure Stack | Microsoft Docs
description: Use o verificador de preparação do Azure Stack para revisar e corrigir problemas de certificado.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/19/2018
ms.openlocfilehash: 009eb56621f7cd395c3d2eefb29b9fa624af888b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778188"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Corrigir problemas comuns para certificados PKI de pilha do Azure

As informações neste artigo podem ajudá-lo a entender e resolver problemas comuns para certificados PKI de pilha do Azure. Você pode descobrir problemas ao usar a ferramenta de verificador de preparação do Azure Stack [validar certificados PKI de pilha do Azure](azure-stack-validate-pki-certs.md). A ferramenta verifica para garantir que os certificados de atender aos requisitos de PKI de uma implantação do Azure Stack e rotação de segredo do Azure Stack e registra os resultados em uma [report.json arquivo](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>Criptografia PFX

**Falha de** -criptografia PFX não é TripleDES-SHA1.

**Correção** -exportar PFX arquivos com **TripleDES SHA1** criptografia. Esse é o padrão para todos os clientes do Windows 10 ao exportar do certificado de snap-in ou usando `Export-PFXCertificate`.

## <a name="read-pfx"></a>Ler PFX

**Aviso** -senha só protege as informações privadas no certificado.  

**Correção** -arquivos de exportação PFX com a configuração opcional **habilitar privacidade de certificado**.  

**Falha de** -inválido do arquivo PFX.  

**Correção** -exportar novamente o certificado usando as etapas [certificados de PKI de pilha do Azure se preparar para implantação](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmo de assinatura

**Falha de** -algoritmo de assinatura é SHA1.

**Correção** -Use as etapas nos certificados do Azure Stack assinatura de geração de solicitação para regenerar o certificado de assinatura solicitação (CSR) com o algoritmo de assinatura de SHA256. Reenvie o CSR para a autoridade de certificação para emitir novamente o certificado.

## <a name="private-key"></a>Chave privada

**Falha de** -a chave privada está ausente ou não contém o atributo de máquina Local.  

**Correção** - no computador que gerou o CSR, exportar novamente o certificado usando as etapas [certificados de PKI de pilha do Azure se preparar para implantação](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment). Essas etapas incluem a exportação do repositório de certificados do computador local.

## <a name="certificate-chain"></a>Cadeia de certificados

**Falha de** -cadeia de certificados não está completa.  

**Correção** -certificados devem conter uma cadeia de certificados completa. Exportar novamente o certificado usando as etapas em [certificados PKI de pilha do Azure se preparar para implantação](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) e selecione a opção **incluir todos os certificados no caminho de certificação se possível.**

## <a name="dns-names"></a>Nomes DNS

**Falha** -o DNSNameList do certificado não contém o nome do ponto de extremidade de serviço do Azure Stack ou uma correspondência de curinga válidos. Correspondência de curinga só é válida para o namespace mais à esquerda do nome DNS. Por exemplo, _*. region.domain.com_ só é válida para *portal.region.domain.com*, e não _*. table.region.domain.com_.

**Correção** -Use as etapas nos certificados do Azure Stack a geração de solicitação para regenerar o CSR com os nomes DNS corretos de assinatura para dar suporte a pontos de extremidade do Azure Stack. Reenviar o CSR a uma autoridade de certificação e, em seguida, siga as etapas em [certificados PKI de pilha do Azure se preparar para implantação](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) para exportar o certificado do computador que gerou o CSR.  

## <a name="key-usage"></a>Uso de chave

**Falha de** – uso de chave não tem assinatura digital ou codificação de chave ou uso avançado de chave está ausente de autenticação de servidor ou autenticação de cliente.  

**Correção** -Use as etapas em [geração de solicitação de assinatura de certificados do Azure Stack](azure-stack-get-pki-certs.md) para regenerar o CSR com os atributos corretos do uso de chave. Reenviar o CSR para a autoridade de certificação e confirme que um modelo de certificado não está substituindo o uso da chave na solicitação.

## <a name="key-size"></a>Tamanho da chave

**Falha de** -tamanho da chave é menor do que 2048.

**Correção** -Use as etapas em [geração de solicitação de assinatura de certificados do Azure Stack](azure-stack-get-pki-certs.md) para regenerar o CSR com o comprimento da chave correto (2048) e, em seguida, envie novamente o CSR para a autoridade de certificação.

## <a name="chain-order"></a>Ordem da cadeia

**Falha de** -a ordem da cadeia de certificados está incorreta.  

**Correção** -exportar novamente o certificado usando as etapas [certificados de PKI de pilha do Azure se preparar para implantação](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) e selecione a opção **incluir todos os certificados no caminho de certificação se possível.** Certifique-se de que apenas o certificado de folha está selecionado para exportação.

## <a name="other-certificates"></a>Outros certificados

**Falha de** -pacote o PFX contém certificados que não sejam o certificado de folha ou parte da cadeia de certificados.  

**Correção** -exportar novamente o certificado usando as etapas [certificados de PKI de pilha do Azure se preparar para implantação](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)e selecione a opção **incluir todos os certificados no caminho de certificação se possível.** Certifique-se de que apenas o certificado de folha está selecionado para exportação.

## <a name="fix-common-packaging-issues"></a>Corrigir problemas comuns de empacotamento

O **AzsReadinessChecker** contém um cmdlet auxiliar chamado `Repair-AzsPfxCertificate`, que pode importar e, em seguida, exportar um PFX de arquivo para corrigir problemas comuns de empacotamento, incluindo:

- *Criptografia PFX* não é TripleDES-SHA1.
- *Chave privada* está faltando o atributo de máquina Local.
- *Cadeia de certificados* está incompleto ou incorreto. O computador local deve conter a cadeia de certificados se o pacote PFX não.
- *Outros certificados*

`Repair-AzsPfxCertificate` não é possível ajudar se você precisar gerar uma CSR novo e emita novamente um certificado.

### <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos devem estar em vigor no computador no qual a ferramenta é executada:

- Windows 10 ou Windows Server 2016, a conectividade com a internet.
- PowerShell 5.1 ou posterior. Para verificar sua versão, execute o seguinte cmdlet do PowerShell e, em seguida, examine os *principais* e *secundárias* versões:

   ```powershell
   $PSVersionTable.PSVersion
   ```

- Configure [PowerShell para o Azure Stack](azure-stack-powershell-install.md).
- Baixe a versão mais recente do [Verificador de preparação do Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) ferramenta.

### <a name="import-and-export-an-existing-pfx-file"></a>Importar e exportar um arquivo PFX existente

1. Em um computador que atenda aos pré-requisitos, abra um prompt do PowerShell administrativo e, em seguida, execute o seguinte comando para instalar o AzsReadinessChecker:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. No prompt do PowerShell, execute o seguinte cmdlet para definir a senha PFX. Substitua `PFXpassword` com a senha real:

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. No prompt do PowerShell, execute o seguinte para exportar um novo arquivo PFX:

   - Para `-PfxPath`, especifique o caminho para o arquivo PFX que você está trabalhando. No exemplo a seguir, o caminho é `.\certificates\ssl.pfx`.
   - Para `-ExportPFXPath`, especifique o local e o nome do arquivo PFX para exportação. No exemplo a seguir, o caminho é `.\certificates\ssl_new.pfx`:

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Depois que a ferramenta for concluída, examine a saída para o sucesso:

   ```powershell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Próximas etapas

- [Clique aqui para saber mais sobre a segurança do Azure Stack](azure-stack-rotate-secrets.md).
