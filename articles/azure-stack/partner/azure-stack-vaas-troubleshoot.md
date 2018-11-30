---
title: Solucionar problemas de validação de pilha do Azure como um serviço | Microsoft Docs
description: Solucionar problemas de validação como um serviço para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c6884ff0949949c03787ff5c0b8c7f8c1397400f
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334016"
---
# <a name="troubleshoot-validation-as-a-service"></a>Solucionar problemas de validação como um serviço

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Veja a seguir não relacionados a versões de software e suas soluções de problemas comuns.

## <a name="local-agent"></a>Agente local

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>O portal mostra o agente local no modo de depuração

Isso provavelmente ocorre porque o agente não consegue enviar pulsações para o serviço devido a uma conexão de rede instáveis. Uma pulsação é enviada a cada cinco minutos. Se o serviço não receber uma pulsação por 15 minutos, o serviço considera o agente inativo, e testes não serão agendados nele. Verifique a mensagem de erro *Agenthost.log* arquivo localizado no diretório onde o agente foi iniciado.

> [!Note]
> Todos os testes já em execução no agente continuará em execução, mas se a pulsação não é restaurada antes do término do teste, o agente não conseguirá atualizar o status de teste ou fazer upload dos logs. O teste será sempre exibido como **executando** e precisará ser cancelado.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Processo do agente no computador foi desligado durante a execução de teste. O que esperar?

Se o processo do agente for desligado maneira brusca por exemplo, computador reiniciado, processo eliminado (CTRL + C na janela de agente é considerado o desligamento normal) e em seguida, o teste que estava sendo executado nele continuará a mostrar como **executando**. Se o agente for reiniciado, o agente atualizará o status do teste para **cancelada**. Se o agente não for reiniciado, o teste é exibido como **executando** e você deve cancelar manualmente o teste.

> [!Note]
> Testes de dentro de um fluxo de trabalho estão agendadas para execução em sequência. **Pendente** testes não serão executados até que os testes em de **executando** estado no mesmo fluxo de trabalho concluído.

## <a name="vm-images"></a>Imagens de VM

### <a name="handle-slow-network-connectivity"></a>Lidar com a conectividade de rede lenta

Você pode baixar a imagem do PIR para um compartilhamento no seu datacenter local. E, em seguida, você pode verificar a imagem.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Baixar a imagem do PIR para compartilhamento local no caso de tráfego de rede lenta

1. Baixar o AzCopy do: [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. Extrair AzCopy.zip e altere o diretório que contém AzCopy.exe

3. Abra o Windows PowerShell em um prompt elevado. Execute os seguintes comandos:

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare é o caminho de compartilhamento ou caminho local.

#### <a name="verifying-pir-image-file-hash-value"></a>Verificando o valor de hash do arquivo de imagem do PIR

Você pode usar **Get-HashFile** para obter o valor de hash para o repositório de imagem pública baixado os arquivos de imagem para verificar a integridade das imagens.

| Nome do Arquivo | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604 20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Falha ao carregar a imagem de VM no `VaaSPreReq` script

Primeiro, verifique se o ambiente está íntegro:

1. Do DVM / saltar caixa, verifique se você com êxito pode entrar no portal de administração usando as credenciais de administrador.
1. Confirme que não há avisos ou alertas.

Se o ambiente esteja íntegro, carregar manualmente as imagens de VM 5 necessárias VaaS para execuções de teste:

1. Entre como o administrador de serviço para o portal de administração. Você pode encontrar o portal de administração do URL do repositório ECE ou o arquivo de informações de carimbo de data /. Para obter instruções, consulte [parâmetros de ambiente](azure-stack-vaas-parameters.md#environment-parameters).
1. Selecione **mais serviços** > **provedores de recursos** > **computação** > **imagens de VM**.
1. Selecione o **+ adicionar** botão na parte superior das **imagens de VM** folha.
1. Modificar ou verificar os valores dos campos a seguir para a primeira imagem VM:
    > [!IMPORTANT]
    > Nem todos os padrões estão corretos para o Item do Marketplace existente.

    | Campo  | Valor  |
    |---------|---------|
    | Publicador | MicrosoftWindowsServer |
    | Oferta | WindowsServer |
    | Tipo de sistema operacional |  Windows |
    | SKU | 2012-R2-Datacenter |
    | Versão | 1.0.0 |
    | URI do Blob de disco do sistema operacional | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. Selecione o botão **Criar**.
1. Repita para as imagens de VM restantes.

As propriedades de todas as imagens VM 5 são da seguinte maneira:

| Publicador  | Oferta  | Tipo de sistema operacional | SKU | Versão | URI do Blob de disco do sistema operacional |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer |  Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer |  Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer |  Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canônico | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canônico | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |

## <a name="next-steps"></a>Próximas etapas

- Revisão [notas de versão para a validação como um serviço](azure-stack-vaas-release-notes.md) para alterações em versões mais recentes.