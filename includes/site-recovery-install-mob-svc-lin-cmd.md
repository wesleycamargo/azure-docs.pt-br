---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 2b92aba8b9a8d8f46ae2aeac3a7bfe60a4755f9b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166322"
---
1. Copie o instalador para uma pasta local (digamos, /tmp) no servidor que você deseja proteger. Execute os seguintes comandos em um terminal:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Para instalar o Serviço de Mobilidade, execute o seguinte comando:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Após concluir a instalação, o Serviço de Mobilidade precisa ser registrado no servidor de configuração. Execute o seguinte comando para registrar o Serviço de Mobilidade com o servidor de configuração:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Linha de comando do instalador do Serviço de Mobilidade

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parâmetro|Tipo|DESCRIÇÃO|Valores possíveis|
|-|-|-|-|
|-r |Obrigatório|Especifica se o Serviço de Mobilidade (MS) deve ser instalado ou se o MasterTarget (MT) deve ser instalado.|MS </br> MT|
|-d |Opcional|Local onde o Serviço de Mobilidade está instalado.|/usr/local/ASR|
|-v|Obrigatório|Especifica a plataforma onde o Serviço de Mobilidade está instalado. </br> </br>- **VMware**: use esse valor se instalar o Serviço de Mobilidade em uma VM em execução em *hosts VMware vSphere ESXi*, *hosts Hyper-V* e *servidores físicos*. </br> - **Azure**: use esse valor se você instalar um agente em uma VM IaaS do Azure.| VMware </br> Azure|
|-q|Opcional|Especifica a execução do instalador no modo silencioso.| N/D|


#### <a name="mobility-service-configuration-command-line"></a>Linha de comando de configuração do Serviço de Mobilidade

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parâmetro|Tipo|DESCRIÇÃO|Valores possíveis|
|-|-|-|-|
|-i |Obrigatório|IP do servidor de configuração|Qualquer endereço IP válido|
|-P |Obrigatório|Caminho completo do arquivo onde a senha de conexão é salva|Qualquer pasta válida|
