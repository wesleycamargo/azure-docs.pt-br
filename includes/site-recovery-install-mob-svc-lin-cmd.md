---
ms.openlocfilehash: aa2e6d80620f0a4cf5063919a6de53e4de20f706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301438"
---
1. Copie o instalador para uma pasta local (digamos, /tmp) no servidor que você deseja proteger. Execute os seguintes comandos em um terminal:
     ```
     cd /tmp;
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

|Parâmetro|Type|DESCRIÇÃO|Valores possíveis|
|-|-|-|-|
|-r |Obrigatório|Especifica se o Serviço de Mobilidade (MS) deve ser instalado ou se o MasterTarget (MT) deve ser instalado.|MS <br /> MT|
|-d |Opcional|Local onde o Serviço de Mobilidade está instalado.|/usr/local/ASR|
|-v|Obrigatório|Especifica a plataforma onde o Serviço de Mobilidade está instalado. <br /> <br />- **VMware**: Use esse valor se você instalar o serviço de mobilidade em uma VM em execução no *hosts VMware vSphere ESXi*, *hosts Hyper-V*, e *servidores físicos*. <br /> - **Azure**: Use esse valor se você instalar um agente em uma VM de IaaS do Azure.| VMware <br /> Azure|
|-q|Opcional|Especifica a execução do instalador no modo silencioso.| N/D|

#### <a name="mobility-service-configuration-command-line"></a>Linha de comando de configuração do Serviço de Mobilidade

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parâmetro|Type|DESCRIÇÃO|Valores possíveis|
|-|-|-|-|
|-i |Obrigatório|IP do servidor de configuração|Qualquer endereço IP válido|
|-P |Obrigatório|Caminho completo do arquivo onde a senha de conexão é salva|Qualquer pasta válida|
<!--Update_Description: wording update-->
<!--ms.date: 03/05/2018-->