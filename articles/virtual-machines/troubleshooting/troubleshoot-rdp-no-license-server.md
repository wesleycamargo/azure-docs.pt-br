---
title: O servidor de licenças de Área de Trabalho Remota não está disponível quando você se conecta à VM do Azure | Microsoft Docs
description: Saiba como solucionar problemas de falhas de RDP porque nenhum servidor de licenças de Área de Trabalho Remota está disponível | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 5e54579a35140ee7cfc06358d60cf7a63292e107
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50088234"
---
# <a name="remote-desktop-license-server-is-not-available-when-you-connect-to-azure-vm"></a>O servidor de licenças de Área de Trabalho Remota não está disponível quando você se conecta à VM do Azure

Este artigo ajuda a resolver o problema no qual não é possível conectar uma VM (máquina virtual) do Azure porque nenhum servidor de licenças de Área de Trabalho Remota está disponível para fornecer uma licença.

## <a name="symptoms"></a>Sintomas

Ao tentar conectar uma VM, você enfrenta os seguintes cenários:

- A captura de tela da VM (Máquina Virtual) mostra que o sistema operacional está totalmente carregado e aguardando credenciais.
- As seguintes mensagens de erro são exibidas ao tentar fazer uma conexão de RDP (protocolo RDP):

  - **A sessão remota foi desconectada porque não há servidor de licenças de Área de Trabalho Remota disponíveis para fornecer uma licença.**

  - **Nenhum servidor de licenças de Área de Trabalho Remota está disponível. Os serviços de Área de Trabalho Remota deixarão de funcionar porque este computador passou do período de tolerância e não contatou pelo menos um servidor de licenças válido do Windows Server 2008. Clique nesta mensagem para abrir a Configuração do Servidor Host da Sessão da Área de Trabalho Remota para usar o Diagnóstico de Licenciamento.**

No entanto, é possível conectar-se à VM normalmente usando uma sessão administrativa:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Causa

Esse problema ocorre se um servidor de licenças de Área de Trabalho Remota não estiver disponível para fornecer uma licença e iniciar uma sessão remota. Esse problema poderá ser causado por vários cenários, mesmo que uma função do Host da Sessão da Área de Trabalho Remota tenha sido configurada na VM:

- Nunca houve uma função de licenciamento de Área de Trabalho Remota no ambiente e o período de carência (180 dias) acabou.
- Uma licença de Área de Trabalho Remota foi instalada no ambiente, mas nunca é ativada.
- Uma licença de Área de Trabalho Remota no ambiente não tem CALs (Licenças de Acesso para Cliente) injetadas para configurar a conexão.
- Uma licença de Área de Trabalho Remota foi instalada no ambiente. Há CALs disponíveis, mas elas não foram configuradas corretamente.
- Uma licença de Área de Trabalho Remota tem CALs e foi ativada. No entanto, alguns outros problemas no servidor de licenças de Área de Trabalho Remota evitam que ele forneça licenças no ambiente.

## <a name="solution"></a>Solução

Para resolver esse problema, [faça backup do disco de SO](../windows/snapshot-copy-managed-disk.md) e siga estas etapas:

1. Conecte-se à VM usando uma sessão administrativa:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

  Se você não conseguir conectar-se à VM usando uma sessão administrativa, poderá usar o [Console Serial](serial-console-windows.md) para acessar a VM da seguinte maneira:

  1. Acesse o Console Serial, selecionando **Suporte e Resolução de problemas** > **Console serial (versão prévia)**. Se o recurso estiver habilitado na VM, você poderá conectar a VM com êxito.

  2. Crie um novo canal para uma instância de CMD. Digite **CMD** para iniciar o canal e obter o nome do canal.

  3. Alterne para o canal que estiver executando a instância de CMD, nesse caso, deve ser o canal 1.

    ```
    ch -si 1
    ```

  4. Pressione **Enter** novamente e digite um nome de usuário e senha válidos (ID de domínio ou local) para a VM.

2. Verifique se a VM tem uma função de Host da Sessão da Área de Trabalho Remota habilitada. Se a função estiver habilitada, verifique se ela está funcionando corretamente. Abra uma instância de CMD com privilégios e siga estas etapas:

  1. Use o seguinte comando para verificar o status da função do Host da Sessão da Área de Trabalho Remota:

    ```
    reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
    ```

    Se esse comando retornar um valor 0, isso significa que a função está desabilitada e você pode ir para a etapa 3.

  2. Use o seguinte comando para verificar as políticas e reconfigurar conforme necessário:

    ```
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
    ```

    Se o valor **LicensingMode** estiver definido como qualquer outro valor diferente de 4 (por usuário), defina o valor como 4:

    ```
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
    ```

    Se o valor **SpecifiedLicenseServers** não existir ou tiver uma informação incorreta do servidor de licenças, altere-o, conforme a seguir:

    ```
    reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
    ```

  3. Após fazer quaisquer alterações no registro, reinicie a VM.

  4. Se você não tiver CALs, remova a função de Host da Sessão da Área de Trabalho Remota. Em seguida, o RDP será definido de volta ao normal e somente permitirá duas conexões RDP simultâneas para a VM.

    ```
    dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
    ```

    Se a VM tiver a função de licenciamento da Área de Trabalho Remota e ela não for usada, você também poderá remover essa função.

    ```
    dism /ONLINE /Disable-feature /FeatureName:Licensing
    ```

  5. Certifique-se de que a VM possa conectar-se ao servidor de licenças da Área de Trabalho Remota. Você pode testar a conectividade com a porta 135 entre a VM e o servidor de licenças. 

    ```
    telnet <FQDN / IP License Server> 135
    ```

3. Se não houver um servidor de licenças de Área de Trabalho Remota no ambiente e você quiser um, poderá [instalar um serviço de função de licenciamento de Área de Trabalho Remota ](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)) e, em seguida, [configurar o licenciamento do RDS](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/).

4. Se um servidor de licenças de Área de Trabalho Remota estiver configurado e íntegro, certifique-se de que o servidor de licenças de Área de Trabalho Remota está ativado com CALs.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
