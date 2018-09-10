---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 08/06/2018
ms.author: ramamill
ms.openlocfilehash: 81390d38b4c0c38b7ac6883ae2bf18c64542fa00
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582774"
---
As etapas para cancelar o registro de um servidor de processo difere dependendo de seu status de conexão com o servidor de configuração.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Cancelar o registro de um servidor de processo que está em um estado conectado

1. Remoto para o servidor de processo como administrador.
2. Inicie o **painel de controle** e abra **programas > desinstalar um programa**
3. Desinstalar um programa pelo nome **servidor/processo de configuração do Microsoft Azure Site Recovery**
4. Após a conclusão da etapa 3, você pode desinstalar **dependências de servidor do Microsoft Azure Site Recovery/processo de configuração**

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Cancelar o registro de um servidor de processo que está em um estado desconectado

> [!WARNING]
> Use as etapas a seguir deve ser usado se não houver nenhuma maneira de reviver a máquina virtual na qual o servidor de processo foi instalado.

1. Faça logon para o servidor de configuração como um administrador.
2. Abra um prompt de comando administrativo e navegue até o diretório `%ProgramData%\ASR\home\svsystems\bin`.
3. Agora execute o comando.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. O comando acima fornecerá a lista de servidores de processo (pode ser mais de um, no caso de entradas duplicadas) com número de série (S.Nº), endereço IP (IP), nome da VM em que o servidor de processo está implantado (Nome), pulsação da VM (Pulsação) conforme mostrado abaixo.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. Agora, insira o número de série do servidor de processo do qual você deseja cancelar o registro.
6. Isso limpa os detalhes do servidor de processo do sistema e exibe a mensagem: **Cancelamento de registro do server-name realizado com êxito> (server-IP-address)**

