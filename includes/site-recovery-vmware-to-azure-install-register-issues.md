---
author: rockboyfor
ms.service: site-recovery
ms.topic: include
origin.date: 10/26/2018
ms.date: 12/10/2018
ms.author: v-yeche
ms.openlocfilehash: 9919521c8cb77f23f50a8097c4e630b4467dc725
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119717"
---
### <a name="installation-failures"></a>Falhas de instalação
| **Mensagem de erro de exemplo** | **Ação recomendada** |
|--------------------------|------------------------|
|ERRO   Falha ao carregar Contas. Erro: System.IO.IOException: não é possível ler dados da conexão de transporte ao instalar e registrar o servidor CS.| Verifique se TLS 1.0 está habilitado no computador. |

### <a name="registration-failures"></a>Falhas de registro
As falhas de registro podem ser depuradas examinando os logs da pasta **%ProgramData%\ASRLogs**.

| **Mensagem de erro de exemplo** | **Ação recomendada** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Mensagem: ACS50008: o token SAML é inválido.<br>ID de rastreamento: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>ID de correlação: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Carimbo de data/hora: **2016-12-12 14:50:08Z<br>** | Verifique se o horário no relógio do sistema não está mais de 15 minutos antes/depois do horário local. Execute novamente o instalador para concluir o registro.|
|**09:35:27** :DRRegistrationException ao tentar obter todos os cofres de recuperação de desastre para o certificado selecionado: Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: o token SAML é inválido.<br>ID de rastreamento: e5ad1af1-2d39-4970-8eef-096e325c9950<br>ID de correlação: abe9deb8-3e64-464d-8375-36db9816427a<br>Carimbo de data/hora: **2016-05-19 01:35:39Z**<br> | Verifique se o horário no relógio do sistema não está mais de 15 minutos antes/depois do horário local. Execute novamente o instalador para concluir o registro.|
|06:28:45: Falha ao criar certificado<br>06:28:45: A instalação não pode continuar. Não é possível criar um certificado necessário para autenticar a Recuperação de Site. Executar a instalação novamente | Verifique se você está executando a instalação como um administrador local. |