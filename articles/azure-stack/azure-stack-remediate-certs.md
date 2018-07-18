---
title: Corrigir problemas de certificado para a pilha do Azure | Microsoft Docs
description: Use o verificador de preparação de pilha do Azure para revisar e corrigir problemas de certificado.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0d2c4d848f861e4e07dbd0de4609344955ca26f7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Corrigir problemas comuns para certificados PKI de pilha do Azure
As informações neste artigo podem ajudá-lo a compreender e resolver problemas comuns de certificados PKI de pilha do Azure. Você pode descobrir problemas ao usar a ferramenta Verificador de preparação de pilha do Azure para [validar certificados PKI de pilha do Azure](azure-stack-validate-pki-certs.md). A ferramenta verifica para garantir que os certificados atendem aos requisitos de PKI de uma implantação de pilha do Azure e a rotação de segredo de pilha do Azure e registra os resultados em uma [report.json arquivo](azure-stack-validation-report.md).  

## <a name="read-pfx"></a>Ler PFX
**Aviso** -senha só protege as informações privadas no certificado.  
**Correção** -é recomendável que você exporta arquivos PFX com a configuração opcional de **habilitar privacidade de certificado**.  

**Falha** -inválido do arquivo PFX.  
**Correção** -exportar novamente o certificado usando as etapas no [certificados PKI de pilha do Azure preparar para implantação](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmo de assinatura
**Falha** -algoritmo de assinatura é SHA1.    
**Correção** -Use as etapas na geração de solicitação para regenerar o certificado de assinatura solicitação (CSR) com o algoritmo de assinatura de SHA256 de assinatura de certificados de pilha do Azure. Reenvie o CSR à autoridade de certificação para emitir novamente o certificado.

## <a name="private-key"></a>Chave privada
**Falha de** -a chave privada está ausente ou não contém o atributo de máquina Local.  
**Correção** - no computador que gerou o CSR, exporte novamente o certificado usando as etapas em certificados PKI de pilha do Azure se preparar para implantação. Essas etapas incluem a exportação do repositório de certificados do computador Local.

## <a name="certificate-chain"></a>Cadeia de certificados
**Falha** -cadeia de certificados não está completa.  
**Correção** -certificados devem conter uma cadeia de certificados concluída.  Exportar novamente o certificado usando as etapas no [certificados PKI de pilha do Azure preparar para implantação](azure-stack-prepare-pki-certs.md) e selecione a opção **incluir todos os certificados no caminho de certificação se possível.**

## <a name="dns-names"></a>Nomes DNS
**Falha de** -DNSNameList do certificado não contém o nome de ponto de extremidade do serviço de pilha do Azure, ou uma correspondência de curinga válidos.  Correspondência de curinga só é válida para o namespace mais à esquerda do nome DNS. Por exemplo, _*. region.domain.com_ só é válida para *portal.region.domain.com*, não _*. table.region.domain.com_.  
**Correção** -Use as etapas na geração de solicitação para gerar o CSR com os nomes DNS corretos de assinatura de certificados de pilha do Azure para oferecer suporte a pontos de extremidade de pilha do Azure. Reenviar o CSR a uma autoridade de certificação e, em seguida, siga as etapas em [certificados PKI de pilha do Azure preparar para implantação](azure-stack-prepare-pki-certs.md) para exportar o certificado do computador que gerou o CSR.  

## <a name="key-usage"></a>Uso de chave
**Falha** - o uso de chave não tem assinatura Digital ou codificação de chave, orEnhanced uso de chave está ausente de autenticação de servidor ou autenticação de cliente.  
**Correção** -Use as etapas em [geração de solicitação de assinatura de certificados do Azure pilha](azure-stack-get-pki-certs.md) para gerar o CSR com os atributos de uso de chave corretos.  Reenviar o CSR à autoridade de certificação e confirme que um modelo de certificado não está substituindo o uso de chave na solicitação.

## <a name="key-size"></a>Tamanho da Chave
**Falha** -tamanho de chave é menor do que 2048    
**Correção** -Use as etapas em [geração de solicitação de assinatura de certificados do Azure pilha](azure-stack-get-pki-certs.md) para gerar o CSR com o comprimento de chave correto (2048) e reenvie o CSR à autoridade de certificação.

## <a name="chain-order"></a>Ordem da cadeia
**Falha de** -a ordem da cadeia de certificado está incorreta.  
**Correção** -exportar novamente o certificado usando as etapas no [certificados PKI de pilha do Azure preparar para implantação](azure-stack-prepare-pki-certs.md) e selecione a opção **incluir todos os certificados no caminho de certificação se possível.** Certifique-se de que apenas o certificado de folha é selecionado para exportação. 

## <a name="other-certificates"></a>Outros certificados
**Falha** -pacote o PFX contém certificados que não sejam o certificado de folha ou parte da cadeia de certificados.  
**Correção** -exportar novamente o certificado usando as etapas no [certificados PKI de pilha do Azure preparar para implantação](azure-stack-prepare-pki-certs.md)e selecione a opção **incluir todos os certificados no caminho de certificação se possível.** Certifique-se de que apenas o certificado de folha é selecionado para exportação.

## <a name="fix-common-packaging-issues"></a>Corrigir problemas comuns de empacotamento
O AzsReadinessChecker pode importar e exportar um arquivo PFX para corrigir problemas comuns de empacotamento, incluindo: 
 - *Chave privada* está faltando o atributo de máquina Local.
 - *Cadeia de certificados* está incompleto ou incorreto. (O computador local deve conter a cadeia de certificados se o pacote PFX não). 
 - *Outros certificados*.
No entanto, o AzsReadinessChecker não pode ajudar se você precisar gerar um CSR novo e emita novamente um certificado. 

### <a name="prerequisites"></a>Pré-requisitos
Os seguintes pré-requisitos devem estar em vigor no computador onde a ferramenta é executada: 
 - Windows 10 ou Windows Server 2016, a conectividade com a internet.
 - PowerShell 5.1 ou posterior. Para verificar a versão, execute o seguinte cmd do PowerShell e, em seguida, examine o *principais* versão e *secundária* versões.

   > `$PSVersionTable.PSVersion`
 - Configurar [PowerShell para Azure pilha](azure-stack-powershell-install.md). 
 - Baixar a versão mais recente do [Verificador de preparação de pilha do Microsoft Azure](https://aka.ms/AzsReadinessChecker) ferramenta.

### <a name="import-and-export-an-existing-pfx-file"></a>Importar e exportar um arquivo PFX existente
1. Em um computador que atenda aos pré-requisitos, abra um prompt do PowerShell administrativo e, em seguida, execute o seguinte comando para instalar o AzsReadinessChecker  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. No prompt do PowerShell, execute o seguinte para definir a senha PFX. Substituir *PFXpassword* com a senha real. 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. No prompt do PowerShell, execute o seguinte para exportar um novo arquivo PFX.
   - Para *- PfxPath*, especifique o caminho para o arquivo PFX que você está trabalhando.  No exemplo a seguir, o caminho é *.\certificates\ssl.pfx*.
   - Para *- ExportPFXPath*, especifique o local e o nome do arquivo PFX para exportação.  No exemplo a seguir, o caminho é *.\certificates\ssl_new.pfx*

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. Após a ferramenta, examine a saída para o sucesso: ![resultados](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre a segurança da pilha do Azure](azure-stack-rotate-secrets.md)