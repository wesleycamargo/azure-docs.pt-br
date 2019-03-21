---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0d090f43b69b42a07f1c8949d1662e8e720f3cf4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908528"
---
As etapas para cancelar o registro de um servidor de processo difere dependendo de seu status de conexão com o servidor de configuração.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Cancelar o registro de um servidor de processo que está em um estado conectado

1. Remoto para o servidor de processo como administrador.
2. Inicie o **painel de controle** e abra **programas > desinstalar um programa**.
3. Desinstalar um programa denominado **servidor de destino do Microsoft Azure Site Recovery mobilidade mestra do serviço**.
4. Desinstalar um programa denominado **o servidor de configuração/em processo do Microsoft Azure Site Recovery**.
5. Quando os programas nas etapas 3 e 4 são desinstalados, você pode desinstalar **dependências de servidor de processo/configuração do Microsoft Azure Site Recovery**.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Cancelar o registro de um servidor de processo que está em um estado desconectado

> [!WARNING]
> Use as etapas abaixo caso não há nenhuma maneira de reviver a máquina virtual na qual o servidor de processo foi instalado.

1. Faça logon para o servidor de configuração como um administrador.
2. Abra um prompt de comando administrativo e navegue até o diretório `%ProgramData%\ASR\home\svsystems\bin`.
3. Agora execute o comando.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. O comando acima fornecerá a lista de servidores de processo (pode ser mais de um, no caso de entradas duplicadas) com número de série (S.Nº), endereço IP (IP), nome da VM em que o servidor de processo está implantado (Nome), pulsação da VM (Pulsação) conforme mostrado abaixo.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. Agora, insira o número de série do servidor de processo do qual você deseja cancelar o registro.
6. Isso descartará os detalhes do servidor de processo do sistema e exibirá a mensagem: **Cancelado com êxito o nome do servidor > (server-IP-address)**

