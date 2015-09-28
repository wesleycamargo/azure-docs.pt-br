<properties 
	pageTitle="Habilitando write-back de dispositivo no Azure AD Connect" 
	description="Este documento fornece detalhes sobre como habilitar write-back de dispositivo usando o Azure AD Connect" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="msStevenPo" 
	editor="curtand"/>

<tags 
	ms.service="active-directory"  
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015"
	ms.author="billmath"/>

# Habilitando write-back de dispositivo no Azure AD Connect

A documentação a seguir fornece informações sobre como habilitar o recurso de write-back do dispositivo no Azure AD Connect. Write-back de dispositivo é usado nas seguintes situações:

Habilitar acesso condicional com base em dispositivos aos aplicativos protegidos por ADFS (2012 R2 ou superior) (terceira parte confiável).

Isso fornece segurança adicional e a garantia de que o acesso aos aplicativos é concedido somente a dispositivos confiáveis. Para obter mais informações sobre acesso condicional, consulte [Gerenciando risco com acesso condicional](active-directory-conditional-access.md) e [Configurando acesso condicional local usando o registro de dispositivo do Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.Note]A inscrição ao Office 365 ou ao AD Premium do Azure é necessária ao usar dispositivos registrados nas políticas de acesso condicional ao serviço de registro do dispositivo Active Directory do Azure. Isso inclui as políticas impostas pelo AD FS (Serviços de Federação do Active Directory) para recursos locais.

## Parte 1: Preparar o Azure AD Connect
Use as etapas a seguir para preparar para o uso de write-back do dispositivo.

1.	No computador no qual o Azure AD Connect está instalado, inicie o PowerShell no modo elevado.

2.	Se o módulo PowerShell do Active Directory NÃO estiver instalado. Instale-o usando o comando a seguir:

	Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools

3.	Com credenciais de administrador corporativo, execute os seguintes comandos e, em seguida, saia do PowerShell.

	Import-Module ‘C:\\Program Files\\Microsoft Azure Active Directory Connect\\AdPrep\\AdSyncAdPrep.psm1’

	Initialize-ADSyncDeviceWriteback –DomainName <name> -AdConnectorAccount <account>

Descrição:



- Se não existente, ele cria e configura novos contêineres e objetos em CN=Device Registration Configuration,CN=Services,CN=Configureation,<forest-dn>.



- Se não existente, ele cria e configura novos contêineres e objetos em CN=RegisteredDevices,<domain-dn>. Objetos de dispositivo serão criados neste contêiner.



- Define as permissões necessárias na conta do Azure AD Connector para gerenciar dispositivos em seu Active Directory.



- Só precisa ser executado em uma floresta, mesmo que o Azure AD Connect esteja instalado em várias florestas.

Parâmetros:


- DomainName: domínio do Active Directory no qual os objetos do dispositivo serão criados. Observação: todos os dispositivos para determinada floresta do Active Directory serão criados em um único domínio. 


- AdConnectorAccount: conta do Active Directory que será usada pelo Azure AD Connect para gerenciar objetos no diretório.

## Parte 2: habilitar write-back de dispositivo
Use o procedimento a seguir para habilitar write-back do dispositivo no Azure AD Connect.

1.	Execute o assistente do AAD Connect. Se esta for a primeira vez de uso do assistente, execute uma instalação personalizada selecionando Personalizar na tela Configurações Expressas ![Instalação personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback1.png)
2.	Se não for a primeira vez, selecione Personalizar opções de sincronização na página Tarefas Adicionais e clique em Avançar. ![Instalação personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
3.	Na página Recursos Opcionais, o write-back do dispositivo não estará mais esmaecido. Observe que se as etapas de preparação do Azure AD Connect não forem concluídas, o write-back do dispositivo ficará indisponível na página Recursos Opcionais. Marque a caixa para write-back do dispositivo e clique em Avançar. ![Write-back de dispositivo](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
4.	Na página de write-back, você verá o domínio fornecido como a floresta de write-back de dispositivo padrão. ![Instalação personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
5.	Conclua a instalação do assistente sem alterações de configuração adicionais. Se necessário, consulte [Instalação personalizada do Azure AD Connect.](active-directory-aadconnect-get-started-custom.md)



## Habilitar o acesso condicional
Instruções detalhadas para habilitar esse cenário estão disponíveis em [Definindo o acesso condicional local usando o registro do dispositivo do Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## Verifique se que dispositivos estão sincronizados com o Active Directory
O write-back do dispositivo agora deve estar funcionando corretamente. Pode levar até 3 horas para que os objetos do dispositivo sejam gravados no Active Directory. Para verificar se os dispositivos estão sendo sincronizados corretamente, faça o seguinte, depois de concluir as regras de sincronização:
 
1.	Inicie o Centro Administrativo do Active Directory. 
2.	Expanda RegisteredDevices dentro do domínio que está sendo federado. ![Instalação personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	Dispositivos registrados atuais serão listados lá. 

![Instalação personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## Informações adicionais 


- [Gerenciamento de riscos com acesso condicional](active-directory-conditional-access.md)
- [Configurando o acesso condicional no local usando o registro do dispositivo do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

<!---HONumber=Sept15_HO3-->