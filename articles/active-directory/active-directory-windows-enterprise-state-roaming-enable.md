<properties
    pageTitle="Habilitar o Enterprise State Roaming no Active Directory do Azure | Microsoft Azure"
    description="Perguntas frequentes sobre as configurações do Enterprise State Roaming em dispositivos do Windows. O Enterprise State Roaming fornece aos usuários uma experiência unificada em seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo."
    services="active-directory"
    keywords="enterprise state roaming, nuvem do windows, como habilitar o enterprise state roaming"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
    ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

 

# Habilitar o Enterprise State Roaming no Active Directory do Azure

O Enterprise State Roaming está disponível para todas as organizações com uma assinatura Premium do Active Directory do Azure (AD do Azure). Para obter mais detalhes sobre como obter uma assinatura do AD do Azure, veja a [página de produto do AD do Azure](https://azure.microsoft.com/services/active-directory).

Quando você habilita o Enterprise State Roaming, sua organização recebe automaticamente as licenças concedidas para uma assinatura gratuita do Azure Rights Management. Essa assinatura gratuita é limitada à criptografia e à descriptografia de dados de configurações empresariais. Você deverá ter uma assinatura paga para usar os recursos completos do Azure Rights Management.

Depois de obter uma assinatura do AD do Azure, siga estas etapas para habilitar o Enterprise State Roaming:
 
1. Faça logon no portal clássico do Azure. 
2. À esquerda, selecione **ACTIVE DIRECTORY**, e escolha o diretório para o qual você deseja habilitar o Enterprise State Roaming. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)	 
3. Vá para a guia **Configurar** na parte superior. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.	Role para baixo a página e selecione **Os usuários podem sincronizar configurações e dados corporativos de aplicativo** e clique em **Salvar**. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Para que um dispositivo Windows 10 mova as configurações com o serviço Enterprise State Roaming, o dispositivo deverá se autenticar usando uma identidade do AD do Azure. Para os dispositivos ingressados no AD do Azure, o logon principal do usuário será a identidade do AD do Azure e, portanto, não será necessária nenhuma configuração adicional. Para os dispositivos que usam um Active Directory local tradicional, o administrador de TI deve conectar o Active Directory local ao AD do Azure usando o [Azure Connect AD](active-directory-aadconnect.md) e configurar a Política de Grupo para forçar os dispositivos clientes a sincronizar automaticamente os dados de usuário com o Azure.

## Sincronizar armazenamento de dados
Os dados do Enterprise State Roaming são hospedados em uma ou mais [regiões do Azure](https://azure.microsoft.com/regions/) que se alinhem melhor ao valor de país definido na instância do AD do Azure. Por exemplo, os clientes com o valor de país definido como "França" serão hospedados em uma ou mais das regiões do Azure na Europa, enquanto os clientes que definirem seu valor de país no AD do Azure como "EUA" terão seus dados hospedados em uma ou mais das regiões do Azure dentro nos EUA. O valor de país é definido como parte do processo de criação de domínio do AD do Azure e não pode ser modificado posteriormente.

Se você precisar de mais detalhes sobre o local de armazenamento de dados, crie um tíquete no [suporte do Azure](https://azure.microsoft.com/support/options/).

## Gerenciar o Enterprise State Roaming
Os administradores de locatário do AD do Azure podem habilitar e desabilitar o Enterprise State Roaming no portal clássico do Azure. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Os administradores de locatário também podem exibir um relatório de status de sincronização por usuário, além de poder limitar a sincronização de configurações a grupos de segurança específicos.

##Retenção de dados
Os dados sincronizados com o Azure por meio do Enterprise State Roaming serão mantidos indefinidamente, a menos que uma operação de exclusão manual seja executada ou os dados em questão sejam considerados obsoletos.
 
- **Excluindo manualmente os dados**: se quiser que o administrador do AD do Azure exclua manualmente os dados de configuração, o administrador poderá criar um tíquete no [suporte do Azure](https://azure.microsoft.com/support/options/).
 
 - **Exclusão de usuário**: quando um usuário é excluído do AD do Azure, a conta de usuário é colocada em um estado desabilitado por 30 dias. Após 30 dias, a conta será excluída e os dados de configurações associados estarão sujeitos a exclusão.
 - **Exclusão do locatário (diretório)**: a exclusão de um diretório inteiro no AD do Azure é uma operação imediata. Todos os dados de configurações associados a esse diretório estarão sujeitos a exclusão. 
 - **Exclusão de configurações**: se o administrador do AD do Azure quiser excluir imediatamente os dados de configuração de um usuário específico, poderá criar um tíquete no suporte do Azure. 
- **Dados obsoletos**: os dados que não forem acessados para um ano ("o período de retenção") serão tratados como obsoletos e poderão ser excluídos do Azure. Os dados obsoletos podem ser um conjunto específico de configurações do Windows/aplicativo ou todas as configurações para um usuário específico. Por exemplo: 
 - Se nenhum dispositivo acessar uma coleção de configurações específicas (por exemplo, um aplicativo é removido do dispositivo ou um grupo de configurações, como "Tema", está desabilitado para todos os dispositivos do usuário), essa coleção se tornará obsoleta após o período de retenção e poderá ser excluída. 
 - Se um usuário tiver desativado a sincronização de configurações em todos os dispositivos, nenhum dos dados de configuração será acessado e todos os dados de configuração do usuário se tornarão obsoletos e poderão ser excluídos após o período de retenção. 
 - Se o administrador de diretório do AD do Azure desativar o Enterprise State Roaming para o diretório inteiro, todos os usuários desse diretório terão a sincronização de configurações interrompida, e todos os dados de configuração de todos os usuários se tornarão obsoletos e poderão ser excluídos após o período de retenção. 

- **Recuperando dados excluídos**: a política de retenção de dados não é configurável. Depois que os dados forem excluídos permanentemente, não poderão ser recuperados. No entanto, é importante observar que os dados de configuração só serão excluídos do Azure e não do dispositivo do usuário final. Se qualquer dispositivo se reconectar mais tarde ao serviço Enterprise State Roaming, as configurações serão novamente sincronizadas e armazenadas no Azure.

## Tópicos relacionados
- [Visão geral do Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Perguntas frequentes sobre configurações e roaming de dados](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Política de grupo e as configurações do MDM para a sincronização de configurações](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Referência de configurações de roaming do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->