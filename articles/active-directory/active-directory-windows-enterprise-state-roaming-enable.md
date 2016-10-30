<properties
    pageTitle="Habilitar o Enterprise State Roaming no Active Directory do Azure | Microsoft Azure"
    description="Perguntas frequentes sobre as configurações do Enterprise State Roaming em dispositivos do Windows. O Enterprise State Roaming fornece aos usuários uma experiência unificada em seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo."
    services="active-directory"
    keywords="enterprise state roaming, nuvem do windows, como habilitar o enterprise state roaming"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>




# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Habilitar o Enterprise State Roaming no Active Directory do Azure

O Enterprise State Roaming está disponível para todas as organizações com uma assinatura Premium do Azure AD (Active Directory). Para obter mais detalhes sobre como obter uma assinatura do Azure AD, confira a [página de produto do Azure AD](https://azure.microsoft.com/services/active-directory).

Quando você habilita o Enterprise State Roaming, sua organização recebe automaticamente as licenças concedidas para uma assinatura gratuita de uso limitado do Azure Rights Management. Essa assinatura gratuita é limitada à criptografia e à descriptografia de dados de aplicativos e configurações empresariais sincronizadas pelo serviço Enterprise State Roaming. Você deverá ter uma assinatura paga para usar os recursos completos do Azure Rights Management.

Depois de obter uma assinatura Premium do Azure AD, siga estas etapas para habilitar o Enterprise State Roaming:

1. Faça logon no portal clássico do Azure.
2. À esquerda, selecione **ACTIVE DIRECTORY**, e escolha o diretório para o qual você deseja habilitar o Enterprise State Roaming.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. Vá para a guia **CONFIGURAR** na parte superior.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.  Role para baixo a página e selecione **OS USUÁRIOS PODEM SINCRONIZAR CONFIGURAÇÕES E DADOS CORPORATIVOS DO APLICATIVO** e clique em **Salvar**.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Para que um dispositivo Windows 10 mova as configurações com o serviço Enterprise State Roaming, o dispositivo deverá se autenticar usando uma identidade do AD do Azure. Para os dispositivos ingressados no AD do Azure, o logon principal do usuário será a identidade do Azure AD e, portanto, não será necessária nenhuma configuração adicional. Para dispositivos que usam um Active Directory local tradicional, o administrador de TI deve [conectar os dispositivos de domínio ao Azure AD para experiências do Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="sync-data-storage"></a>Sincronizar armazenamento de dados
Os dados do Enterprise State Roaming são hospedados em uma ou mais [regiões do Azure](https://azure.microsoft.com/regions/ ) que se alinhem melhor ao valor de país/região definido na instância do Azure Active Directory. Os dados do Enterprise State Roaming são particionados com base em três regiões geográficas principais: América do Norte, EMEA e APAC. Dados de Enterprise State Roaming para o locatário estão localizados localmente com a região geográfica e não são replicados entre regiões.  Por exemplo, os clientes que têm o seu valor de país/região definido para um dos países da EMEA, como "França" ou "Zâmbia" terão seus dados hospedados em uma ou das regiões do Azure na Europa.  Clientes que definirem seu valor de país/região no Azure AD para um dos países da América do Norte, como "Estados Unidos" ou "Canadá" terão seus dados hospedados em uma ou mais das regiões do Azure dentro dos EUA.  Clientes que definirem seu valor de país/região no Azure AD para um dos países da APAC, como "Austrália" ou "Nova Zelândia" terão seus dados hospedados em uma ou mais das regiões do Azure na Ásia.  Dados da Antártida e de países da América do Sul serão hospedados em uma ou mais regiões do Azure dentro dos EUA.  O valor de país/região é definido como parte do processo de criação de domínio do Azure AD e não pode ser modificado posteriormente. 

Se você precisar de mais detalhes sobre o local de armazenamento de dados, crie um tíquete no [suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="manage-enterprise-state-roaming"></a>Gerenciar o Enterprise State Roaming
Os administradores globais do Azure AD podem habilitar e desabilitar o Enterprise State Roaming no portal clássico do Azure.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Os administradores globais podem limitar a sincronização de configurações de grupos de segurança específicos.

Administradores globais também podem exibir um relatório de status de sincronização de dispositivo por usuário selecionando um usuário específico na lista **USUÁRIOS** na instância do Active Directory, clicando na guia **DISPOSITIVOS** e selecionando a para exibir **Dispositivos sincronizando configurações e dados de aplicativo corporativo**.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

##<a name="data-retention"></a>Retenção de dados
Os dados sincronizados com o Azure por meio do Enterprise State Roaming serão mantidos indefinidamente, a menos que uma operação de exclusão manual seja executada ou os dados em questão sejam considerados obsoletos. 

**Exclusão explícita:** os dados são excluídos quando um administrador do Azure exclui um usuário ou um diretório ou um administrador solicita explicitamente que dados devem ser excluídos.

- **Exclusão de usuário**: quando um usuário é excluído do Azure AD, os dados de roaming da conta de usuário serão marcados para exclusão e serão excluídos entre 90 a 180 dias. 
- **Exclusão de diretório**: a exclusão de um diretório inteiro no Azure AD é uma operação imediata. Todos os dados de configurações associados ao diretório serão marcados para exclusão e serão excluídos entre 90 a 180 dias. 
- **Exclusão mediante solicitação**: se o administrador do Azure AD quiser excluir manualmente os dados de configuração de um usuário específico, poderá criar um [tíquete no suporte do Azure](https://azure.microsoft.com/support/). 

**Exclusão de dados obsoletos**: os dados que não forem acessados para um ano ("o período de retenção") serão tratados como obsoletos e poderão ser excluídos do Azure. O período de retenção está sujeito a alterações, mas não será menos de 90 dias. Os dados obsoletos podem ser um conjunto específico de configurações do Windows/aplicativo ou todas as configurações para um usuário específico. Por exemplo:
 
- Se nenhum dispositivo acessar uma coleção de configurações específicas (por exemplo, um aplicativo é removido do dispositivo ou um grupo de configurações, como "Tema", está desabilitado para todos os dispositivos do usuário), essa coleção se tornará obsoleta após o período de retenção e poderá ser excluída. 
- Se um usuário tiver desativado a sincronização de configurações em todos os dispositivos, nenhum dos dados de configuração será acessado e todos os dados de configuração do usuário se tornarão obsoletos e poderão ser excluídos após o período de retenção. 
- Se o administrador de diretório do AD do Azure desativar o Enterprise State Roaming para o diretório inteiro, todos os usuários desse diretório terão a sincronização de configurações interrompida, e todos os dados de configuração de todos os usuários se tornarão obsoletos e poderão ser excluídos após o período de retenção. 

**Recuperação de dados excluídos**: a política de retenção de dados não é configurável. Depois que os dados forem excluídos permanentemente, não poderão ser recuperados. No entanto, é importante observar que os dados de configuração só serão excluídos do Azure e não do dispositivo do usuário final. Se qualquer dispositivo se reconectar mais tarde ao serviço Enterprise State Roaming, as configurações serão novamente sincronizadas e armazenadas no Azure.


## <a name="related-topics"></a>Tópicos relacionados
- [Visão geral do Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Perguntas frequentes sobre configurações e roaming de dados](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Política de grupo e as configurações do MDM para a sincronização de configurações](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Referência de configurações de roaming do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)



<!--HONumber=Oct16_HO2-->


