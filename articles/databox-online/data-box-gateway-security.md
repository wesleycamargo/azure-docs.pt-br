---
title: Segurança do Gateway da caixa de dados do Azure | Microsoft Docs
description: Descreve os recursos de segurança e privacidade que protegem seu dispositivo virtual de Gateway de caixa de dados do Azure, serviço e dados locais e na nuvem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: b27b712128ddfbb07a7a7f68f616c20ec3fb53d3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685711"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Proteção de dados e segurança de Gateway da caixa de dados do Azure

Segurança é uma preocupação importante ao adotar uma nova tecnologia, especialmente se a tecnologia é usada com dados confidenciais ou proprietários. Solução de Gateway de caixa de dados do Microsoft Azure ajuda a garantir que somente entidades autorizadas podem exibir, modificar ou excluir seus dados.

Este artigo descreve os recursos de segurança do Gateway de caixa de dados do Azure que ajudam a proteger cada um dos componentes da solução e os dados armazenados nelas.

A solução de Gateway da caixa de dados consiste em quatro componentes principais que interagem entre si:

- **Serviço de Gateway da caixa de dados hospedado no Azure** – o recurso de gerenciamento que você usa para criar a ordem de dispositivo, configurar o dispositivo e, em seguida, controlar a ordem até a conclusão.
- **O dispositivo de Gateway da caixa de dados** – o dispositivo virtual que você forneceu no hipervisor do sistema que você forneceu. Este dispositivo virtual é usado para importar seus dados locais para o Azure.
- **Clientes/hosts conectados ao dispositivo** – os clientes em sua infraestrutura que se conectam ao dispositivo de Gateway da caixa de dados e contêm dados que precisam ser protegidos.
- **Armazenamento em nuvem** – O local na nuvem do Azure onde os dados são armazenados. Normalmente, esse local é a conta de armazenamento vinculada ao recurso de Gateway da caixa de dados que você criou.


## <a name="data-box-gateway-service-protection"></a>Proteção de serviço de Gateway da caixa de dados

O serviço de Gateway da caixa de dados é um serviço de gerenciamento hospedado no Microsoft Azure. O serviço é usado para configurar e gerenciar o dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Proteção de dispositivo de Gateway da caixa de dados

O dispositivo de Gateway da caixa de dados é um dispositivo virtual provisionado no hipervisor de um sistema local que você fornecer. O dispositivo ajuda a enviar dados para o Azure. Seu dispositivo:

- Precisa de uma chave de ativação para acessar o serviço de Gateway de caixa de borda/dados de caixa de dados.
- É sempre protegidas por uma senha de dispositivo.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Proteger o dispositivo por meio da chave de ativação

Somente um dispositivo de Gateway da caixa de dados autorizado tem permissão para ingressar no serviço de Gateway da caixa de dados que você criou na sua assinatura do Azure. Para autorizar um dispositivo, você deve usar uma chave de ativação para ativar o dispositivo com o serviço de Gateway da caixa de dados.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Para obter mais informações, acesse [obter uma chave de ativação](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteger o dispositivo por meio de senha

Senhas de garantir que seus dados fiquem acessíveis apenas aos usuários autorizados. Dados caixa Gateway dispositivos inicialização em um estado bloqueado.

Você pode:

- Conectar-se para a interface do usuário do dispositivo por meio de um navegador da web local e, em seguida, forneça uma senha para entrar no dispositivo.
- Conectar-se remotamente à interface do PowerShell do dispositivo via HTTP. Gerenciamento remoto é ativado por padrão. Em seguida, você pode fornecer a senha do dispositivo para entrar no dispositivo. Para obter mais informações, acesse [conectar remotamente ao seu dispositivo de Gateway da caixa de dados](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Use o local da web a interface do usuário para [alterar a senha](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Se você alterar a senha, certifique-se de notificar todos os usuários de acesso remoto para que eles não tenham uma falha de entrada.


## <a name="protect-the-data"></a>Proteger os dados

Esta seção descreve os recursos de segurança do Gateway da caixa de dados que protegem os dados em trânsito e os dados armazenados.

### <a name="protect-data-at-rest"></a>Proteger dados em repouso

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteger dados em trânsito

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteger dados por meio de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Girar e, em seguida [sincronizar as chaves da conta de armazenamento](data-box-gateway-manage-shares.md#sync-storage-keys) regularmente para ajudar a garantir que sua conta de armazenamento não é acessada por usuários não autorizados.

## <a name="manage-personal-information"></a>Gerenciar informações pessoais

O serviço de Gateway da caixa de dados coleta informações pessoais nas instâncias principais a seguir:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para exibir a lista de usuários que podem acessar ou excluir um compartilhamento, siga as etapas em [gerenciar compartilhamentos no Gateway de dados de caixa](data-box-gateway-manage-shares.md).

Para mais informações, revise a Política de Privacidade da Microsoft na [Central de Confiabilidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Próximas etapas

[Implantar seu dispositivo de Gateway da caixa de dados](data-box-gateway-deploy-prep.md).
