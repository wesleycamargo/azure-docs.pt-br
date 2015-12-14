<properties
	pageTitle="Azure AD Connect: Habilitando write-back de dispositivo | Microsoft Azure"
	description="Este documento fornece detalhes sobre como habilitar write-back de dispositivo usando o Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="StevenPo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/12/2015"
	ms.author="billmath;andkjell"/>

# Habilitando write-back de dispositivo no Azure AD Connect

A documentação a seguir fornece informações sobre como habilitar o recurso de write-back do dispositivo no Azure AD Connect. Write-back de dispositivo é usado nas seguintes situações:

Habilitar acesso condicional com base em dispositivos aos aplicativos protegidos por ADFS (2012 R2 ou superior) (terceira parte confiável).

Isso fornece segurança adicional e a garantia de que o acesso aos aplicativos é concedido somente a dispositivos confiáveis. Para obter mais informações sobre acesso condicional, consulte [Gerenciando risco com acesso condicional](active-directory-conditional-access.md) e [Configurando acesso condicional local usando o registro de dispositivo do Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.NOTE]Uma assinatura do Azure AD Premium é necessária para write-back do dispositivo.


## Parte 1: instalar o Azure AD Connect
1. Instale o Azure AD Connect usando configurações expressas ou personalizadas. A recomendação é iniciar para ter todos os usuários e grupos sincronizados com êxito antes de habilitar o write-back do dispositivo.

## Parte 2: preparar o Active Directory
Use as etapas a seguir para preparar para o uso de write-back do dispositivo.

1.	No computador no qual o Azure AD Connect está instalado, inicie o PowerShell no modo elevado.

2.	Se o módulo PowerShell do Active Directory NÃO estiver instalado. Instale-o usando o comando a seguir:

	`Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools`

3.	Com credenciais de administrador corporativo, execute os seguintes comandos e, em seguida, saia do PowerShell.

	`Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

	`Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`


![Powershell](./media/active-directory-aadconnect-get-started-custom-device-writeback/powershell.png)

Descrição:

- Se não existente, ele cria e configura novos contêineres e objetos em CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn].
- Se não existente, ele cria e configura novos contêineres e objetos em CN=RegisteredDevices,[domain-dn]. Objetos de dispositivo serão criados neste contêiner.
- Define as permissões necessárias na conta do Azure AD Connector para gerenciar dispositivos em seu Active Directory.
- Só precisa ser executado em uma floresta, mesmo que o Azure AD Connect esteja instalado em várias florestas.

Parâmetros:

- DomainName: domínio do Active Directory no qual os objetos do dispositivo serão criados. Observação: todos os dispositivos para determinada floresta do Active Directory serão criados em um único domínio.
- AdConnectorAccount: conta do Active Directory que será usada pelo Azure AD Connect para gerenciar objetos no diretório. Esta é a conta usada pela sincronização do Azure AD Connect para se conectar ao AD. Se você instalou usando configurações expressas, é a conta prefixada com MSOL\_.

## Parte 3: habilitar o dispositivo write-back na conexão do AD do Azure
Use o procedimento a seguir para habilitar write-back do dispositivo no Azure AD Connect.

1.	Execute o assistente de instalação novamente. Selecione **personalizar opções de sincronização na página **Tarefas Adicionais e clique em **Próximo**. ![Instalação personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
2.	Na página Recursos Opcionais, o write-back do dispositivo não estará mais esmaecido. Observe que se as etapas de preparação do Azure AD Connect não forem concluídas, o write-back do dispositivo ficará indisponível na página Recursos Opcionais. Marque a caixa para write-back do dispositivo e clique em **avançar**. Se a caixa de seleção ainda estiver desabilitada, consulte a [seção de solução de problemas](#the-writeback-checkbox-is-still-disabled). ![Write-back de dispositivo](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
3.	Na página de write-back, você verá o domínio fornecido como a floresta de write-back de dispositivo padrão. ![Instalação personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
4.	Conclua a instalação do assistente sem alterações de configuração adicionais. Se necessário, consulte [Instalação personalizada do Azure AD Connect.](active-directory-aadconnect-get-started-custom.md)


## Habilitar o acesso condicional
Instruções detalhadas para habilitar esse cenário estão disponíveis em [Definindo o acesso condicional local usando o registro do dispositivo do Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## Verifique se que dispositivos estão sincronizados com o Active Directory
O write-back do dispositivo agora deve estar funcionando corretamente. Pode levar até 3 horas para que os objetos do dispositivo sejam gravados no Active Directory. Para verificar se os dispositivos estão sendo sincronizados corretamente, faça o seguinte, depois de concluir as regras de sincronização:

1.	Inicie o Centro Administrativo do Active Directory.
2.	Expanda RegisteredDevices dentro do domínio que está sendo federado. ![Instalação personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	Dispositivos registrados atuais serão listados lá.

![Instalação personalizada](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## Solucionar problemas

### A caixa de seleção de write-back é desabilitada
Se a caixa de seleção para write-back do dispositivo não estiver habilitada, mesmo se você tiver seguido as etapas acima, as etapas seguir o guiarão através do que o assistente de instalação estiver verificando antes de a caixa ser habilitada.

Primeiro as prioridades:

- Verifique se pelo menos uma floresta tem 2012R2 do Windows Server. O tipo de objeto do dispositivo deve estar presente.
- Se já estiver executando o assistente de instalação, as alterações não serão detectadas. Nesse caso, conclua o assistente de instalação e execute-o novamente.
- Verifique se a conta fornecida no script de inicialização é, na verdade, o usuário correto usado pelo Active Directory Connector. Para fazer isso, siga essas etapas:
	- No menu Iniciar, abra **Serviço de sincronização**.
	- Abra a guia **Conectores**.
	- Localize o conector com o tipo de Serviços de Domínio do Active Directory e selecione-o.
	- Em **Ações**, selecione **Propriedades**.
	- Vá para **Conectar-se à floresta do Active Directory**. Verifique o nome de usuário e domínio especificado nessa correspondência de tela a conta fornecida para o script. ![Conta do conector](./media/active-directory-aadconnect-get-started-custom-device-writeback/connectoraccount.png)

Verifique a configuração no Active Directory:-Verifique se o serviço de registro do dispositivo está localizado no local abaixo (CN = DeviceRegistrationService, CN = Serviços de registro de dispositivo, CN = Configuração do registro de dispositivo, CN = Services, CN = Configuration) no contexto de nomenclatura de configuração.

![Solução de problemas1](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot1.png)

- Verifique se há apenas um objeto de configuração pesquisando o namespace de configuração. Se houver mais de um, exclua a cópia.

![Solução de problemas2](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot2.png)

- No objeto do serviço de registro de dispositivo, verifique se o atributo msDS-DeviceLocation está presente e tem um valor. Pesquise esse local e verifique se ele está presente com o atributo msDS-DeviceContainer objectType.

![Solução de problemas3](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot3.png)

![Solução de problemas4](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot4.png)

- Verifique se a conta usada pelo conector do Active Directory tem as permissões necessárias no contêiner dos Dispositivos Registrados encontradas pela etapa anterior. Permissões esperadas neste contêiner:

![Solução de problemas5](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot5.png)

- Verifique se a conta do Active Directory tem permissões no objeto CN = Configuração do registro de dispositivo, CN = Services, CN= Configuração.

![Solução de problemas6](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot6.png)

## Informações adicionais
- [Gerenciamento de riscos com acesso condicional](active-directory-conditional-access.md)
- [Configurando o acesso condicional no local usando o registro do dispositivo do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1203_2015-->