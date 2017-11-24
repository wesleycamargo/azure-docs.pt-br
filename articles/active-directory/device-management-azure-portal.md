---
title: Gerenciar dispositivos usando o portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para gerenciar dispositivos.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: cd13ecf50cf03c362f8da2da83ab698c4e8ba0cc
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2017
---
# <a name="managing-devices-using-the-azure-portal"></a>Gerenciar dispositivos usando o portal do Azure


Com o gerenciamento de dispositivos no Azure AD (Azure Active Directory), você pode garantir que os usuários acessem recursos usando dispositivos que atendam aos padrões de segurança e conformidade. 

Este tópico:

- Assuma que você está familiarizado com a [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md)

- Fornece informações sobre como gerenciar dispositivos usando o portal do Azure



## <a name="configure-device-settings"></a>Definir configurações do dispositivo

Para gerenciar seus dispositivos usando o portal do Azure, eles precisam ser [registrados ou associados](device-management-introduction.md#getting-devices-under-the-control-of-azure-ad) ao Azure Active Directory. Como administrador, você pode ajustar o processo de registro e ingresso de dispositivos definindo as configurações do dispositivo. 

![Definir configurações do dispositivo](./media/device-management-azure-portal/22.png)

A página de configurações do dispositivo permite que você configure:

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/21.png)


- **Os usuários podem associar seus dispositivos ao Azure Active Directory** – essas configurações permitem que você selecione os usuários que podem [associar dispositivos](device-management-introduction.md#azure-ad-joined-devices) ao Azure Active Directory. O padrão é **Todos**.

- **Outros administradores locais nos dispositivos associados ao Azure AD** – você pode selecionar os usuários que têm direitos de administrador local em um dispositivo. Os usuários adicionados aqui são adicionados à função *Administradores do dispositivo* no Azure AD. Os administradores globais no Azure AD e os proprietários do dispositivo recebem direitos de administrador local por padrão. Essa opção é uma funcionalidade Premium Edition disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite). 

- **Os usuários podem registrar seus dispositivos com o Azure Active Directory** – você precisa definir essa configuração para permitir que dispositivos sejam [registrados](device-management-introduction.md#azure-ad-registered-devices) com o Azure Active Directory. Se você selecionar **Nenhum**, os dispositivos não terão permissão para registro quando eles não forem associados ao Azure AD ou não forem associados ao Azure AD híbrido. O registro com o Microsoft Intune ou o MDM (Gerenciamento de Dispositivo Móvel) para o Office 365 exige registro. Se você tiver configurado qualquer um desses serviços, a opção **TODOS** estará selecionada e **NENHUM** não estará disponível.

- **Exigir autenticação multifator para associar dispositivos** – é possível definir se os usuários devem precisar fornecer um segundo fator de autenticação para [associar](device-management-introduction.md#azure-ad-joined-devices) seu dispositivo ao Azure Active Directory. O padrão é **Não**. É recomendável exigir a autenticação multifator ao registrar um dispositivo. Antes de habilitar a autenticação multifator para este serviço, você deve garantir que a autenticação multifator esteja configurada para os usuários que registram seus dispositivos. Para saber mais sobre os diferentes serviços de autenticação multifator do Azure, consulte [Introdução à autenticação multifator do Azure](../multi-factor-authentication/multi-factor-authentication-get-started.md). 

- **Número máximo de dispositivos** – essa configuração permite selecionar o número máximo de dispositivos que um usuário pode ter no Azure AD. Se um usuário atingir esta cota, ele não poderá adicionar mais dispositivos até que um ou mais dos seus dispositivos existentes sejam removidos. A cotação de dispositivo é contada para todos os dispositivos que estão associados ao Azure AD ou registrados no Azure AD atualmente. O valor padrão é **20**.

- **Os usuários podem sincronizar configurações e dados de aplicativo em dispositivos** – por padrão, essa configuração é definida como **NENHUM**. Selecionar usuários específicos ou grupos ou TODOS permite que as configurações do usuário e os dados de aplicativo sejam sincronizados em seus dispositivos Windows 10. Saiba mais sobre como a sincronização funciona no Windows 10.
Essa opção é uma funcionalidade premium disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite).
 




## <a name="locate-devices"></a>Localizar dispositivos

Você tem duas opções para localizar dispositivos registrados e associados:

- **Todos os dispositivos** na seção **Gerenciar** da página **Dispositivos**  

    ![Todos os dispositivos](./media/device-management-azure-portal/41.png)


- **Dispositivos** na seção **Gerenciar** de uma página **Usuário**
 
    ![Todos os dispositivos](./media/device-management-azure-portal/43.png)



Com as duas opções, você pode obter uma exibição que:


- Permite pesquisar dispositivos usando o nome de exibição como filtro.

- Fornece visão geral detalhada de dispositivos registrados e associados

- Permite que você execute tarefas comuns de gerenciamento de dispositivo
   

![Todos os dispositivos](./media/device-management-azure-portal/51.png)


## <a name="device-management-tasks"></a>Tarefas de gerenciamento de dispositivos

Como administrador, você pode gerenciar os dispositivos registrados ou associados. Esta seção fornece informações sobre tarefas comuns de gerenciamento de dispositivo.


### <a name="manage-an-intune-device"></a>Gerenciar um dispositivo do Intune

Se você for um administrador do Intune, será possível gerenciar dispositivos marcados como **Microsoft Intune**. Um administrador pode ver dispositivos adicionais 

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Habilitar/desabilitar um dispositivo do Azure Active Directory

Para ativar / desativar um dispositivo, você tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

    ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/71.png)

- A barra de ferramentas na página **Dispositivos**

    ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/32.png)


**Comentários:**

- Você precisa ser um administrador global no Azure Active Directory para habilitar / desabilitar um dispositivo. 
- Desabilitar um dispositivo impede que um dispositivo acesse seus recursos do Azure AD. 



### <a name="delete-an-azure-ad-device"></a>Excluir um dispositivo do Azure Active Directory

Para excluir um dispositivo, você tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

    ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/72.png)

- A barra de ferramentas na página **Dispositivos**

    ![Excluir um dispositivo](./media/device-management-azure-portal/34.png)


**Comentários:**

- Você precisa ser um administrador global no Azure Active Directory para excluir um dispositivo.  

- Excluindo um dispositivo:
 
    - Impede que um dispositivo acesse seus recursos do Azure Active Directory. 

    - Remove todos os detalhes que estão relacionados ao dispositivo, por exemplo, chaves do BitLocker para dispositivos Windows.  

    - Representa uma atividade não recuperável e não é recomendável a menos que necessário.

Se um dispositivo for gerenciado por outra autoridade de gerenciamento (por exemplo, o Microsoft Intune), certifique-se de que o dispositivo foi apagado/desativado antes de excluir o dispositivo no Azure Active Directory.

 


### <a name="view-or-copy-device-id"></a>Exibir ou copiar a ID do dispositivo

Você pode usar uma ID de dispositivo para verificar os detalhes de ID de dispositivo no dispositivo ou usar o PowerShell durante a solução de problemas. Para acessar a opção de cópia, clique no dispositivo.

![Exibir uma ID do dispositivo](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>Exibir ou copiar as chaves do BitLocker

Se você for um administrador, será possível exibir e copiar as chaves do BitLocker para ajudar os usuários a recuperar sua unidade criptografada. Essas chaves só estão disponíveis para dispositivos Windows que são criptografados e têm suas chaves armazenadas no Azure AD. Você pode copiar essas chaves ao acessar detalhes do dispositivo.
 
![Exibir chaves do BitLocker](./media/device-management-azure-portal/36.png)



## <a name="audit-logs"></a>Logs de auditoria


As atividades de dispositivo estão disponíveis por meio dos logs de atividade. Isso inclui atividades acionadas pelo serviço de registro do dispositivo e pelos usuários:

- Criação de dispositivo e adição de usuários/proprietários no dispositivo

- Alterações nas configurações do dispositivo

- Operações de dispositivo como excluir ou atualizar um dispositivo
 
O ponto de entrada para os dados de auditoria é **Logs de auditoria**, na seção **Atividade** da página **Dispositivos**.

![Logs de auditoria](./media/device-management-azure-portal/61.png)


Um log de auditoria tem um modo de exibição de lista padrão que mostra:

- A data e a hora da ocorrência

- Os destinos

- O iniciador/ator (quem) de uma atividade

- A atividade (o quê)

![Logs de auditoria](./media/device-management-azure-portal/63.png)

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.
 
![Logs de auditoria](./media/device-management-azure-portal/64.png)


Para restringir os dados relatados a um nível que funciona para você, filtre os dados de auditoria usando os seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Destino
- Iniciado por (ator)

Além dos filtros, você pode pesquisar itens específicos.

![Logs de auditoria](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md)



