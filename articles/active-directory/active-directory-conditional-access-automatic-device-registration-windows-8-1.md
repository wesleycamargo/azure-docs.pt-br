---
title: Configurar o Registro Automático de Dispositivo para dispositivos ingressados no domínio do Windows 8.1| Microsoft Docs
description: " Etapas para configurar a política de grupo para dispositivos Windows 8.1 ingressados no domínio para registrarem-se automaticamente com o Azure AD. "
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: Markvi

---
# <a name="configure-automatic-device-registration-for-windows-8.1-domain-joined-devices"></a>Configurar o registro automático de dispositivo para dispositivos ingressados no domínio do Windows 8.1
Você pode usar uma Política de Grupo do Active Directory para configurar os dispositivos associados ao domínio do Windows 8.1 para registro automático com o AD do Azure. Para configurar a Política de Grupo, você deve ter pelo menos um computador unido ao domínio do Windows Server 2012 R2 ou Windows 8.1 com o recurso de Gerenciamento de Política de Grupo instalado. Quando essa Política de Grupo for habilitada para seu domínio, qualquer usuário que fizer logon no computador do domínio será registrado de forma automática e silenciosa com um objeto de dispositivo no Azure AD. Haverá um objeto de dispositivo no AD do Azure para todos os usuários registrados do dispositivo físico. Certifique-se de ler e concluir os pré-requisitos do Registro Automático de Dispositivo com Active Directory do Azure para dispositivos ingressados no domínio do Windows.

> [!NOTE]
> Para obter instruções mais recentes sobre como configurar o registro automático de dispositivos, veja [Como configurar o registro automático de domínio do Windows associado a dispositivos com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
> 
> 

## <a name="configure-the-group-policy-for-your-windows-8.1-domain-joined-devices"></a>Configurar a Política de Grupo para dispositivos ingressados no domínio do Windows 8.1
1. Abra o Gerenciador do Servidor e navegue até **Ferramentas** > **Gerenciamento de Política de Grupo**.
2. No Gerenciamento de Política de Grupo, navegue até o nó de domínio que corresponde ao domínio no qual você deseja habilitar o **Ingresso no Local de Trabalho Automático**.
3. Clique com o botão direito do mouse em **Objetos de Política de Grupo** e selecione **Novo**. Nomeie seu objeto de Política de Grupo como **Ingresso no Local de Trabalho Automático**, por exemplo. Clique em **OK**.
4. Clique com o botão direito do mouse no seu novo objeto de Política de Grupo e selecione **Editar**.
5. Navegue até **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Componentes do Windows** > **Workplace Join**.
6. Clique com o botão direito do mouse nos computadores cliente do ingresso no local de trabalho e selecione **Editar**.
7. Selecione o botão de opção Habilitado e clique em Aplicar. Clique em **OK**.
8. Agora você pode vincular o objeto de Política de Grupo para um local de sua escolha. Para habilitar essa política para todos os dispositivos do Windows 8.1 associados ao domínio em sua organização, vincule a política de grupo no domínio.

## <a name="unregistering-your-windows-8.1-domain-joined-devices"></a>Cancelando o registro dos dispositivos ingressados no domínio do Windows 8.1
Você pode optar por cancelar o registro de dispositivos Windows 8.1 ingressados no domínio fazendo o seguinte: modificar as configurações de Política de Grupo de Ingresso no Local de Trabalho criadas na seção anterior. Defina a política de computadores cliente de ingresso no local de trabalho automático a ser desabilitado. Isso impedirá que novos dispositivos ingressem automaticamente no local de trabalho.

Cancele o registro de computadores Windows 8.1 existentes ingressados no domínio seguindo uma das duas opções:

* Opção 1: **cancelar o registro de um dispositivo ingressado do domínio do Windows 8.1 usando as Configurações do Computador**
  
  1. No dispositivo Windows 8.1, navegue até **Configurações do PC** > **Rede** > **Local de Trabalho**
  2. Selecione **Sair**.
     Esse processo deve ser repetido para cada usuário do domínio que entrou no computador e foi automaticamente ingressados no local.
* Opção 2: cancelar o registro de um dispositivo ingressado do domínio do Windows 8.1 usando um script
  
  1. Abra um prompt de comando no computador do Windows 8.1 e execute o seguinte comando: ` %SystemRoot%\System32\AutoWorkplace.exe leave`

Este comando deve ser executado no contexto de cada usuário do domínio que tenha entrado no computador.

## <a name="event-viewer-&-errors-for-windows-8.1-domain-joined-devices"></a>Visualizador de Eventos e Erros de dispositivos ingressados no domínio do Windows 8.1
O Log de Eventos do Windows em um computador Windows 8.1 exibe mensagens relacionadas ao registro de dispositivos. Você encontrará mensagens para eventos bem-sucedidos e malsucedidos. 

O Log de Eventos pode ser encontrado no Visualizador de Eventos em Aplicativos e Serviços **Logs** > **Microsoft** > **Windows > Workplace Join**.

## <a name="additional-details"></a>Detalhes adicionais
A Política de Grupo permite que uma Tarefa Agendada no sistema que é executada no contexto do usuário seja disparada no momento da entrada do usuário. A tarefa registrará silenciosamente o usuário e o dispositivo com o Azure AD depois que a entrada for concluída. A Tarefa Agendada pode ser encontrada em dispositivos Windows 8.1 na Biblioteca do Agendador de Tarefas em **Microsoft** > **Windows** > **Workplace Join**. A tarefa será executada e registrada em todo e qualquer usuário do Active Directory que entrar. 

## <a name="additional-topics"></a>Tópicos adicionais
* [Visão geral do registro de dispositivos do Active Directory do Azure](active-directory-conditional-access-device-registration-overview.md)
* [Registro de dispositivo automático com o Azure Active Directory para dispositivos ingressados no domínio do Windows 10](active-directory-conditional-access-automatic-device-registration.md)
* [Configurar o registro automático de dispositivo para dispositivos ingressados no domínio do Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)

<!--HONumber=Oct16_HO2-->


