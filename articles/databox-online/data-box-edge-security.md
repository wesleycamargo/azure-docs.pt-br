---
title: Segurança de borda da caixa de dados | Microsoft Docs
description: Descreve os recursos de segurança e privacidade que protegem seu dispositivo de borda da caixa de dados, serviço e dados locais e na nuvem.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/02/2019
ms.author: alkohli
ms.openlocfilehash: de737f20147e8208dd18388eedcac11583c8cb97
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891796"
---
# <a name="data-box-edge-security-and-data-protection"></a>Segurança de borda da caixa de dados e proteção de dados

Segurança é uma preocupação importante ao adotar uma nova tecnologia, especialmente se a tecnologia é usada com dados confidenciais ou proprietários. Solução de borda da caixa de dados do Microsoft Azure ajuda a garantir que somente entidades autorizadas podem exibir, modificar ou excluir seus dados.

Este artigo descreve os recursos de segurança de borda da caixa de dados que ajudam a proteger cada um dos componentes da solução e os dados armazenados nelas.

A solução de borda de caixa de dados do Azure consiste em quatro componentes principais que interagem entre si:

- **Borda da caixa de dados / serviço de Gateway da caixa de dados hospedado no Azure** – o recurso de gerenciamento que você usa para criar a ordem de dispositivo, configurar o dispositivo e, em seguida, controlar a ordem até a conclusão.
- **O dispositivo de borda da caixa de dados** – o dispositivo de transferência que é enviado a você para importar seus dados locais para o Azure.
- **Clientes/hosts conectados ao dispositivo** – os clientes em sua infraestrutura que se conectar ao dispositivo de borda da caixa de dados e contêm dados que precisam ser protegidos.
- **Armazenamento em nuvem** – O local na nuvem do Azure onde os dados são armazenados. Normalmente, esse local é a conta de armazenamento vinculada ao recurso de borda da caixa de dados que você criou.


## <a name="data-box-edgedata-box-gateway-service-protection"></a>Proteção de serviço de Gateway de caixa de borda/dados de caixa de dados

O serviço de Gateway de caixa de borda/dados de caixa de dados é um serviço de gerenciamento hospedado no Microsoft Azure. O serviço é usado para configurar e gerenciar o dispositivo.

- Acesso ao serviço de Gateway de caixa de borda/dados de caixa de dados exige que sua organização tenha um Enterprise Agreement (EA) ou uma assinatura do provedor de solução de nuvem (CSP). Para obter mais informações, acesse [inscrever-se para uma assinatura do Azure](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)!
- Como o serviço de gerenciamento está hospedado no Azure, ele é protegido pelos recursos de segurança do Azure. Para obter mais informações sobre os recursos de segurança fornecidos pelo Microsoft Azure, acesse a [Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).
- Para operações de gerenciamento do SDK, chave de criptografia está disponível para a borda da caixa de dados / recurso de Gateway da caixa de dados sob **propriedades do dispositivo**. Você pode exibir a chave de criptografia somente se você tiver permissões para a API do Graph de recurso.

## <a name="data-box-edge-device-protection"></a>Proteção de dispositivo de borda da caixa de dados

O dispositivo de borda da caixa de dados é um dispositivo local que ajuda a transformar os dados por processá-lo localmente e, em seguida, enviá-la para o Azure. Seu dispositivo:

- Precisa de uma chave de ativação para acessar o serviço de Gateway de caixa de borda/dados de caixa de dados.
- É sempre protegidas por uma senha de dispositivo.
- É um dispositivo bloqueado. O dispositivo BMC e BIOS são protegidos por senha com acesso de usuário limitado para o BIOS.
- Inicialização segura foi habilitada.
- Executa o Windows Defender Device Guard. Proteção do dispositivo permite que você execute apenas os aplicativos confiáveis que você define em suas políticas de integridade de código.
- Tem uma chave dentro de capa que pode ser usada para bloquear o dispositivo. Nós recomendamos que depois de configurar o dispositivo, abra a tampa. Localize a chave e, em seguida, bloqueie a tampa para evitar qualquer acesso não autorizado aos discos de dados localizados na frente do dispositivo.

### <a name="protect-the-device-via-activation-key"></a>Proteger o dispositivo por meio da chave de ativação

Somente um dispositivo de borda da caixa de dados autorizado tem permissão para ingressar no serviço de Gateway de caixa de borda/dados de caixa de dados que você criou na sua assinatura do Azure. Para autorizar um dispositivo, você deve usar uma chave de ativação para ativar o dispositivo com o serviço de Gateway de caixa de borda/dados de caixa de dados. Para obter mais informações, acesse [obter uma chave de ativação](data-box-edge-deploy-prep.md#get-the-activation-key).

A chave de ativação que você usa:

- É uma chave de autenticação baseada em Azure Active Directory (AAD).
- Expira após três dias.
- Não é usada após a ativação do dispositivo.
 
Depois de ativar um dispositivo, ele utilizará tokens para se comunicar com o Microsoft Azure.

### <a name="protect-the-device-via-password"></a>Proteger o dispositivo por meio de senha

Senhas de garantir que seus dados fiquem acessíveis apenas aos usuários autorizados. Borda da caixa de dados e dados de caixa de Gateway dispositivos inicialização em um estado bloqueado.

Você pode:

- Conectar-se para a interface do usuário do dispositivo por meio de um navegador da web local e, em seguida, forneça uma senha para entrar no dispositivo.
- Conectar-se remotamente à interface do PowerShell do dispositivo via HTTP. Gerenciamento remoto é ativado por padrão. Em seguida, você pode fornecer a senha do dispositivo para entrar no dispositivo. Para obter mais informações, acesse [conectar remotamente ao seu dispositivo de borda da caixa de dados](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

Considere as seguintes práticas recomendadas:

- O serviço de borda da caixa de dados não pode recuperar senhas existentes: ele só poderá redefini-las por meio do portal do Azure. É recomendável armazenar todas as senhas em um local seguro para não precisar redefinir uma senha se ela for esquecida. Se você redefinir uma senha, certifique-se de notificar todos os usuários antes de você redefini-lo.
- Use o local da web a interface do usuário para [alterar a senha](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Se você alterar a senha, certifique-se de notificar todos os usuários de acesso remoto para que eles não tenham uma falha de entrada.
- Você pode acessar a interface do Windows PowerShell de seu dispositivo remotamente por HTTP. Como prática recomendada de segurança, você deve usar HTTP apenas em redes confiáveis.
- Certifique-se de que as senhas do dispositivo são fortes e bem protegidas. Siga as [práticas recomendadas de senha](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).

## <a name="protect-the-data"></a>Proteger os dados

Esta seção descreve os recursos de segurança de borda da caixa de dados que protegem os dados em trânsito e os dados armazenados.

### <a name="protect-data-at-rest"></a>Proteger dados em repouso

Para os dados em repouso:

- Para os dados em repouso, a borda da caixa de dados usa a criptografia BitLocker XTS AES-256 para proteger os dados locais.
- Para os dados que residem nos compartilhamentos, o acesso aos compartilhamentos é restrito.

    - Para clientes SMB que acessam os dados de compartilhamento, eles precisam de credenciais de usuário associadas ao compartilhamento. Essas credenciais são definidas no momento da criação do compartilhamento.
    - Para clientes NFS que acessar os compartilhamentos, os endereços IP dos clientes precisarão ser adicionados no momento da criação do compartilhamento.


### <a name="protect-data-in-flight"></a>Proteger dados em trânsito

Para os dados em trânsito:

- Para dados que passa entre o dispositivo e o Azure, o TLS 1.2 padrão é usado. Não há nenhum fallback para o TLS 1.1 e versões anteriores. A comunicação do agente será bloqueada se não há suporte para o TLS 1.2. O TLS 1.2 também é necessário para operações de gerenciamento de SDK e portal.
- Quando os clientes acessam seu dispositivo por meio da interface do usuário web local em um navegador, o TLS 1.2 padrão é usado como o protocolo de segurança padrão.

    - A prática recomendada é configurar seu navegador para usar o TLS 1.2.
    - Se o navegador não oferece suporte a TLS 1.2, você pode usar o TLS 1.1 ou TLS 1.0.
- Para proteger os dados ao copiar de seus servidores de dados, é recomendável que você use o SMB 3.0 com criptografia.

### <a name="protect-data-via-storage-accounts"></a>Proteger dados por meio de contas de armazenamento

Seu dispositivo está associado uma conta de armazenamento que é usada como um destino para seus dados no Azure. O acesso à conta de armazenamento é controlado pela assinatura e o armazenamento de 512 bits duas chaves associadas à conta de armazenamento de acesso.

Uma das chaves é usada para autenticação quando o dispositivo de borda da caixa de dados acessa a conta de armazenamento. A outra chave é mantida em reserva, permitindo alternar as chaves periodicamente.

Por motivos de segurança, muitos data centers exigem a rotação de chaves. Recomendamos seguir estas práticas recomendadas para a rotação de chaves:

- Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Protege com cuidado sua chave de conta. Não distribua a senha para outros usuários, duro codificá-lo ou salvá-lo em qualquer lugar em texto não criptografado que é acessível a outras pessoas.
- [Regenerar a chave de conta](../storage/common/storage-account-manage.md#regenerate-access-keys) usar o portal do Azure se você achar que ele pode ter sido comprometida.
- Girar e, em seguida [sincronizar as chaves da conta de armazenamento](data-box-gateway-manage-shares.md#sync-storage-keys) regularmente para ajudar a garantir que sua conta de armazenamento não é acessada por usuários não autorizados.
- Periodicamente, o administrador do Azure deve alterar ou regenerar a chave primária ou secundária usando a seção Armazenamento do portal do Azure para acessar diretamente a conta de armazenamento.


## <a name="manage-personal-information"></a>Gerenciar informações pessoais

A borda da caixa de dados / serviço de Gateway da caixa de dados coleta informações pessoais nas instâncias principais a seguir:

- **Detalhes do pedido** - após a ordem ser criada, o endereço de envio, email e as informações de contato dos usuários são armazenadas no portal do Azure. As informações salvas incluem:
  - Nome de contato
  - Número de telefone
  - Email
  - Endereço
  - City
  - CEP/Código postal
  - Estado
  - Região/País/Região
  - Enviar número de controle

    Os detalhes do pedido são criptografados e armazenados no serviço. O serviço mantém as informações até que você exclua o recurso ou ordem explicitamente. Além disso, a exclusão de recurso e a ordem de correspondente é bloqueada desde o momento em que o dispositivo é enviado até que o dispositivo volta à Microsoft.

- **Endereço para remessa** – depois que o pedido for feito, o serviço Data Box fornece o endereço de envio para operadoras de terceiros, como no-break.

- **Compartilhar usuários** -os usuários em seu dispositivo também podem acessar os dados que residem nos compartilhamentos. Uma lista de usuários que podem acessar os dados de compartilhamento é exibida e pode ser vista. Essa lista também é excluída quando os compartilhamentos são excluídos. Para exibir a lista de usuários que podem acessar ou excluir um compartilhamento, siga as etapas em [gerenciar compartilhamentos na caixa de borda dados](data-box-gateway-manage-shares.md).

Para mais informações, revise a Política de Privacidade da Microsoft na [Central de Confiabilidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Próximas etapas

[Implantar seu dispositivo de borda da caixa de dados](data-box-edge-deploy-prep.md).

