<properties
	pageTitle="Diretriz de renovação de certificado para usuários do Office 365 e do Azure AD. | Microsoft Azure"
	description="Este artigo explica aos usuários do Office 365 como resolver problemas com emails que notificam sobre a renovação de um certificado."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="billmath"/>


# Renovando certificados de federação para o Office 365 e AD do Azure

##Visão geral

Para uma federação bem-sucedida entre o Azure AD e o AD FS, os certificados usados pelo AD FS para assinar tokens de segurança para o Azure AD devem corresponder ao que está configurado no Azure AD. Qualquer incompatibilidade pode ocasionar a interrupção da relação de confiança entre o AD FS e o AAD. O Azure AD garante que essas informações sejam mantidas em sincronia ao implantar o AD FS e o Proxy de Aplicativo Web (para acesso à extranet).

Este artigo fornece informações adicionais para o gerenciamento de seus certificados de assinatura de token e as mantém em sincronia com o Azure AD nestes casos:

* Você não está implantando o Proxy de Aplicativo Web e, portanto, os metadados de federação não estão disponíveis na extranet
* Você não está usando a configuração padrão do AD FS para certificados de assinatura de token
* Você está usando um provedor de identidade de terceiros

## Configuração padrão do AD FS para certificados de assinatura de token

Os certificados de assinatura de token e de descriptografia de token geralmente são autoassinados e têm validade de um ano. A configuração padrão do AD FS em relação aos certificados de assinatura de token e de descriptografia de token inclui um processo de renovação automática chamado **AutoCertificateRollover**. Se você estiver usando o AD FS 2.0 ou posterior, o Office 365 e o AD do Azure atualizarão automaticamente o certificado antes que ele expire.

### Notificação de renovação - portal do O365 e notificação por email

>[AZURE.NOTE] Se você tiver recebido um email ou uma notificação no portal solicitando para renovar seu certificado para o Office, poderá seguir as etapas mencionadas abaixo em [Gerenciamento de alterações para certificados de autenticação de token](#managecerts) para verificar se é necessário tomar alguma providência. A Microsoft está ciente de um possível problema que pode ocasionar o envio de notificações para o usuário sobre a renovação de certificados, mesmo quando nenhuma ação for necessária.

O Azure AD tenta monitorar os metadados de federação e atualizar os certificados de assinatura de token como indicado pelos metadados de federação. Trinta dias antes da expiração dos certificados de assinatura de token, o Azure AD verificará se há novos certificados disponíveis por meio de sondagem dos metadados de federação.

* Se ele puder sondar os metadados de federação com êxito e recuperar os novos certificados, não haverá notificação por email ou um aviso no portal do O365 para o usuário
* Se não for possível recuperar os novos certificados de assinatura de token, seja porque os metadados de federação não estejam acessíveis ou porque a sobreposição de certificado automático não esteja habilitada - ele emitirá uma notificação por email e um aviso será mostrado no portal do O365

![Notificação do portal do O365](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] Se você estiver usando o AD FS para garantir a continuidade de negócios, verifique se seus servidores têm os seguintes patches para que não ocorram falhas de autenticação causadas por problemas conhecidos. Isso reduzirá os problemas conhecidos de servidor de proxy do AD FS para essa renovação e períodos futuros de renovação:
>
>Server 2012 R2 - [Pacote cumulativo de atualizações do Windows Server de maio de 2014](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 e 2012 - [falha de autenticação por meio do proxy no Windows Server 2008 ou no Windows 2012 R2 SP1](http://support.microsoft.com/kb/3094446)

## Verifique se os certificados precisam de atualização <a name="managecerts"></a>

### Etapa 1: verificar o estado de AutoCertificateRollover

No servidor do AD FS, abra o Microsoft Powershell. Verifique se o valor de AutoCertRollover está definido como TRUE

	Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] Se você estiver usando o AD FS 2.0, terá de executar Add-Pssnapin Microsoft.Adfs.Powershell primeiro.

### Etapa 2: confirmar se o AD FS e o Azure AD estão em sincronia

No servidor do AD FS, abra o prompt do Powershell do Azure AD e conecte-se ao Azure AD.

>[AZURE.NOTE] Caso ele ainda não esteja instalado, você poderá baixar o Powershell do Azure AD [daqui](https://technet.microsoft.com/library/jj151815.aspx).

	Connect-MsolService

Verifique os certificados configurados no AD FS e as propriedades de confiança do Azure AD para o domínio especificado.

	Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Se as impressões digitais em ambas as saídas corresponderem, ele confirmará que os certificados estão em sincronia com o Azure AD.

### Etapa 3: verificar se o certificado está prestes a expirar

Na saída de Get-MsolFederationProperty ou de Get-AdfsCertificate, verifique a data em “Não após”. Se a data estiver a menos de 30 dias, você precisará tomar providências.

### Próxima etapa

| AutoCertificateRollover | Certificados em sincronia com o Azure AD | Os Metadados de Federação estão acessíveis publicamente | Validade | Ação |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| Sim | Sim | Sim | - | Nenhuma Ação é necessária. Confira [Renovar certificado de assinatura de token automaticamente](#autorenew) | | Sim | Não | - | Menos de 15 dias | Renovar imediatamente. Veja [Renovar certificado de assinatura de token manualmente](#manualrenew) | | Sim | Não | - | Menos de 30 dias | Renovar imediatamente. Confira [Renovar certificado de assinatura de token](#manualrenew) |

[-] Não importa

## Renovar certificado de assinatura de token automaticamente (Recomendado) <a name="autorenew"></a>

Se você tiver implantado o Proxy de Aplicativo da Web que pode habilitar o acesso aos metadados de federação da extranet e se estiver usando a configuração padrão do AD FS, ou seja, se o AutoCertificateRollover estiver habilitado, então **não será necessário executar nenhuma etapa manual.** Verifique o seguinte para confirmar se a atualização automática do certificado poderá ser feita:

**Nº 1 - A propriedade AutoCertificateRollover do AD FS deve ser definida como True**

Isso indica que o AD FS gerará automaticamente novos certificados de assinatura de token e de descriptografia de token antes da expiração dos antigos.

**Nº 2 Os metadados de federação do AD FS estão acessíveis publicamente**

Verifique se os metadados de federação são acessíveis publicamente, navegando até a seguinte URL em um computador na Internet pública (fora da rede corporativa):


https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

em que `(your_FS_name) ` é substituído pelo nome de host de serviço de federação que sua organização usa, por exemplo, fs.contoso.com. Se você puder verificar ambas as configurações com êxito, não terá de fazer mais nada.

Exemplo: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Renovar certificado de assinatura de token manualmente <a name="manualrenew"></a>

Você pode optar por renovar os certificados de assinatura de token manualmente. Alguns dos cenários comuns em que você talvez queira atualizar os certificados de assinatura de token manualmente são:
* Os certificados de assinatura de token não são certificados autoassinados. O motivo mais comum para isso é que sua organização gerencia certificados do AD FS inscritos de uma autoridade de certificação organizacional. 
* A segurança de rede não permite que os metadados de federação fiquem publicamente disponíveis.

Nesses cenários, sempre que você atualizar os certificados de assinatura de token, também deverá atualizar seu domínio do Office 365 usando o comando Update-MsolFederatedDomain do PowerShell.

### Etapas para renovar o certificado de assinatura de token e atualizar a confiança de federação do O365

**Etapa 1: verifique se o AD FS tem novos certificados de assinatura de token**

### Configuração não padrão
Se você estiver em uma configuração não padrão do AD FS onde **AutoCertificateRollover** esteja definido como **False**, provavelmente estará usando certificados personalizados (não autoassinados). Leia [Diretrizes para os clientes que não estejam usando os certificados autoassinados do AD FS](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert) para obter diretrizes abrangentes sobre como renovar o certificados de assinatura de token do AD FS.

### Os metadados de federação não estão disponíveis publicamente
Por outro lado, se **AutoCertificateRollover** estiver definido como **True**, mas se os metadados de federação não estiverem acessíveis publicamente, primeiro certifique-se de que novos certificados de assinatura de token tenham sido gerados pelo AD FS. Siga as etapas abaixo para confirmar se você tem novos certificados de assinatura de token

1. Verifique se você efetuou logon no servidor do AD FS primário.
2. Verifique os certificado de autenticação atuais no AD FS abrindo uma janela Comando do PowerShell e executando o seguinte comando:

	PS C:\>Get-ADFSCertificate –CertificateType token-signing

	>[AZURE.NOTE] Se você estiver usando o AD FS 2.0, terá de executar Add-Pssnapin Microsoft.Adfs.Powershell primeiro.

3. Examine a saída do comando em todos os certificados listados. Se o AD FS tiver gerado um novo certificado, você verá dois certificados na saída: um para o qual o valor IsPrimary é Verdadeiro e a data NotAfter é dentro de 5 dias e um para o qual IsPrimary é Falso e NotAfter é cerca de um ano no futuro.

4. Se você vir apenas um certificado e a data NotAfter não for dentro de 5 dias, terá de gerar um novo certificado executando as etapas a seguir.

5. Para gerar um novo certificado, execute o seguinte comando em um prompt de comando do PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

6. Verifique a atualização executando o seguinte comando novamente: PS C:\>Get-ADFSCertificate –CertificateType token-signing

Dois certificados deverão ser listados agora, um dos quais tem uma data NotAfter de aproximadamente um ano no futuro e para o qual o valor IsPrimary é Falso.

**Etapa 2: atualizar os novos certificados de assinatura de token para a relação de confiança do O365**

Siga as etapas fornecidas abaixo para atualizar o O365 com os novos certificados de assinatura de token a serem usados para a relação de confiança.

1.	Abra o Módulo Microsoft Azure Active Directory para Windows PowerShell.
2.	Execute $cred = Get-Credential. Quando este cmdlet solicitar credenciais, digite as credenciais da conta de administrador de serviços de nuvem.
3.	Execute Connect-MsolService –Credential $cred. Esse cmdlet conecta você ao serviço de nuvem. A criação de um contexto que conecta você ao serviço de nuvem é necessária antes de executar qualquer um dos cmdlets adicionais instalados pela ferramenta.
4.	Se você estiver executando esses comandos em um computador que não seja o servidor de federação primário do AD FS, execute Set-MSOLAdfscontext -Computer <AD FS primary server>, em que <AD FS primary server> é o nome FQDN interno do servidor primário do AD FS. Esse cmdlet cria um contexto que conecta você ao AD FS.
5.	Execute Update-MSOLFederatedDomain –DomainName <domain>. Esse cmdlet atualiza as configurações do AD FS no serviço de nuvem e configura a relação de confiança entre os dois.


>[AZURE.NOTE] Se você precisar oferecer suporte a vários domínios de nível superior, como contoso.com e fabrikam.com, deve usar a opção SupportMultipleDomain com todos os cmdlets. Para obter mais informações, veja [Suporte para vários domínios de nível superior](active-directory-aadconnect-multiple-domains.md).

## Reparar confiança do Azure AD usando o AAD Connect <a name="connectrenew"></a>

Se você tiver instalado e configurado seu farm do AD FS/relação de confiança do Azure AD usando o Azure AD Connect, poderá usar o Azure AD Connect para detectar se nenhuma ação será necessária para seus certificados de assinatura de token. Se você precisar renovar os certificados, poderá usar o Azure AD Connect para fazer o necessário com alguns clique simples.

Para saber mais, leia [Reparar a relação de confiança](./active-directory-aadconnect-federation-management.md#repairing-the-trust)

<!---HONumber=AcomDC_0622_2016-->