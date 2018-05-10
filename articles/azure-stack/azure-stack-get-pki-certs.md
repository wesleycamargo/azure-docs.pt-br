---
title: Gerar certificados de infraestrutura de chave pública do Azure pilha para implantação de sistemas de pilha do Azure integradas | Microsoft Docs
description: Descreve o processo de implantação de certificado PKI de pilha do Azure para sistemas de pilha do Azure integradas.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 17737c2b272f2a123df3d58c62c471b3da5bebe1
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Geração de solicitação de assinatura de certificados de pilha do Azure

A ferramenta do verificador de preparação do Azure pilha descrita neste artigo está disponível [da Galeria do PowerShell](https://aka.ms/AzsReadinessChecker). A ferramenta cria solicitações de assinatura de certificado (SAC) adequada para uma implantação de pilha do Azure. Certificados devem ser solicitados, gerados e validados com tempo suficiente para testar antes da implantação.

A ferramenta do verificador de preparação de pilha do Azure (AzsReadinessChecker) executa as solicitações de certificado a seguir:

 - **Solicitações de certificado padrão**  
    Solicitação de acordo com a [gerar certificados PKI para implantação de pilha do Azure](azure-stack-get-pki-certs.md).
 - **Tipo de solicitação**  
    Especifica se a solicitação de assinatura de certificado será uma única solicitação, ou várias solicitações.
 - **Plataforma como serviço**  
    Se desejar solicitar nomes do plataforma como serviço (PaaS) a certificados como especificado na [requisitos de certificado da infraestrutura de chave pública do Azure pilha - certificados opcionais de PaaS](azure-stack-pki-certs.md#optional-paas-certificates).



## <a name="prerequisites"></a>Pré-requisitos

O sistema deve atender aos seguintes pré-requisitos antes de gerar o CSR(s) para certificados PKI para uma implantação de pilha do Azure:

 - Verificador de preparação de pilha do Microsoft Azure
 - Atributos de certificado:
    - Nome de região
    - Nome de domínio totalmente qualificado (FQDN) externo
    - Assunto
 - Windows 10 ou Windows Server 2016
 
  > [!NOTE]
  > Quando você receber seus certificados volta da autoridade de certificação as etapas em [certificados PKI de pilha do Azure preparar](azure-stack-prepare-pki-certs.md) precisará ser concluída no mesmo sistema!

## <a name="generate-certificate-signing-requests"></a>Gerar solicitações de assinatura de certificado

Siga estas etapas para preparar e validar os certificados PKI de pilha do Azure: 

1.  Instale AzsReadinessChecker em um prompt do PowerShell (5.1 ou superior), executando o seguinte cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  Declare o **assunto** como um dicionário ordenado. Por exemplo:  

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > Se for fornecido um nome comum (CN) será substituído pelo primeiro nome DNS da solicitação de certificado.

3.  Declare um diretório de saída que já existe:

    ````PowerShell  
    $outputDirectory = "$ENV:USERNAME\Documents\AzureStackCSR" 
    ````
4.  Declarar identifique o sistema

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ````

    Serviços de Federação do Active Directory (AD FS)

    ```PowerShell
    $IdentitySystem = "ADFS"
    ````

5. Declarar **nome da região** e um **FQDN externo** destinado para a implantação de pilha do Azure.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` constitui a base na qual todos os nomes DNS externos na pilha do Azure são criados, neste exemplo, o portal seria `portal.east.azurestack.contoso.com`.

6. Para gerar uma solicitação de certificado único com vários nomes de alternativo da entidade:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Para incluir serviços de PaaS especificar a opção ```-IncludePaaS```

7. Para gerar solicitações para cada nome DNS de assinatura de certificado individual:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType MultipleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Para incluir serviços de PaaS especificar a opção ```-IncludePaaS```

8. Analise a saída:

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

9.  Enviar o **. SOL** arquivo gerado para sua autoridade de certificação (interna ou pública).  O diretório de saída de **AzsReadinessChecker início** contém o CSR(s) necessárias ao envio de uma autoridade de certificação.  Ele também contém um diretório filho que contém o arquivo INF (s) usado durante a geração de solicitação de certificado, como uma referência. Certifique-se de que a autoridade de certificação gera certificados usando a solicitação gerada que atendem a [requisitos de PKI de pilha do Azure](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Próximas etapas

[Preparar certificados PKI de pilha do Azure](azure-stack-prepare-pki-certs.md)
