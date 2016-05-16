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
	ms.date="05/03/2016"
	ms.author="billmath"/>


# Renovando certificados de federação para o Office 365 e AD do Azure

Se você recebeu um email ou uma notificação no portal solicitando a renovação do certificado do Office 365, este artigo destina-se a ajudá-lo a resolver o problema e evitar que ele aconteça novamente. Este artigo pressupõe que você esteja usando o AD FS como servidor de federação.

>[AZURE.IMPORTANT] Esteja ciente de que a autenticação por meio do proxy pode falhar no Windows Server 2012 ou Windows Server 2008 R2 depois de realizar uma das ações a seguir:
>
- Seu proxy renova seu token de confiança após a substituição de certificados no AD FS
- Você substituiu manualmente os certificados do AD FS
>
Um hotfix está disponível para corrigir esse problema. Consulte [Falha de autenticação por meio do proxy no Windows Server 2012 ou Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)

## Verifique se é necessário algum procedimento

Se você estiver usando o AD FS 2.0 ou posterior, o Office 365 e o AD do Azure atualizarão automaticamente o certificado antes que ele expire. Não é necessário executar nenhuma etapa manual nem executar um script como uma tarefa agendada. Para que isso funcione, ambas as seguintes definições de configuração padrão do AD FS devem estar em vigor:

- A propriedade do AD FS AutoCertificateRollover deve ser definida como Verdadeira, indicando que o AD FS vai gerar automaticamente certificados de descriptografia e nova assinatura de token antes que os antigos expirem.
	- Se o valor for Falso, você estará usando configurações de certificado personalizadas. Vá [aqui](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert) para orientação abrangente.
- Os metadados de federação devem estar disponíveis para a Internet pública.

	Como verificar:

	- Verifique se a instalação do AD FS está usando substituição automática de certificado, executando o seguinte comando em uma janela Comando do PowerShell no servidor de federação primário:

	`PS C:\> Get-ADFSProperties`

(observe que, se você estiver usando o AD FS 2.0, terá de executar Add-Pssnapin Microsoft.Adfs.Powershell primeiro).

Na saída resultante, verifique a seguinte configuração:
	
	AutoCertificateRollover :True

Verifique se os metadados de federação são acessíveis publicamente, navegando até a seguinte URL em um computador na Internet pública (fora da rede corporativa):


https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

em que `(your_FS_name) ` é substituído pelo nome de host de serviço de federação que sua organização usa, por exemplo, fs.contoso.com. Se você puder verificar ambas as configurações com êxito, não terá de fazer mais nada.

Exemplo: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Se você decidir atualizar o certificado manualmente
Sempre que atualizar manualmente os certificados do AD FS, você deverá atualizar seu domínio do Office 365 usando o comando Update-MsolFederatedDomain do PowerShell, conforme mostrado nas etapas em Atualize manualmente as propriedades de confiança de federação do Office 365 na seção [aqui](#if-your-metadata-is-not-publicly-accessible)

## Se a propriedade AutoCertificateRollover estiver definida como Falsa

Se a propriedade AutoCertificateRollover estiver definida como Falsa, você estará usando configurações de certificado do AD FS não padrão. O motivo mais comum para isso é que sua organização gerencia certificados do AD FS inscritos de uma autoridade de certificação organizacional. Nesse caso, você terá de renovar e atualizar seus certificados por conta própria. Use as orientações fornecidas [aqui](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

## Se os metadados não forem acessíveis publicamente
Se a configuração AutocertificateRollover for Verdadeira, mas os metadados de federação não estiverem disponíveis publicamente, use o procedimento a seguir para garantir que os certificados sejam atualizados no local e na nuvem:

### Verifique se seu sistema AD FS gerou um novo certificado.

- Verifique se você efetuou logon no servidor do AD FS primário.
- Verifique os certificado de autenticação atuais no AD FS abrindo uma janela Comando do PowerShell e executando o seguinte comando:

`PS C:\>Get-ADFSCertificate –CertificateType token-signing.`

(observe que, se você estiver usando o AD FS 2.0, terá de executar Add-Pssnapin Microsoft.Adfs.Powershell primeiro).


- Examine a saída do comando em todos os certificados listados. Se o AD FS tiver gerado um novo certificado, você verá dois certificados na saída: um para o qual o valor IsPrimary é Verdadeiro e a data NotAfter é dentro de 5 dias e um para o qual IsPrimary é Falso e NotAfter é cerca de um ano no futuro.

- Se você vir apenas um certificado e a data NotAfter não for dentro de 5 dias, terá de gerar um novo certificado executando as etapas a seguir.

- Para gerar um novo certificado, execute o seguinte comando em um prompt de comando do PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

- Verifique a atualização executando o seguinte comando novamente: PS C:\>Get-ADFSCertificate –CertificateType token-signing
	- Dois certificados deverão ser listados agora, um dos quais tem uma data NotAfter de aproximadamente um ano no futuro e para o qual o valor IsPrimary é Falso.

- Em seguida, para atualizar manualmente as propriedades de confiança de federação do Office 365, siga estas etapas.




### Atualize manualmente as propriedades de confiança de federação do Office 365, seguindo estas etapas.

1.	Abra o Módulo Microsoft Azure Active Directory para Windows PowerShell.
2.	Execute $cred = Get-Credential. Quando este cmdlet solicitar credenciais, digite as credenciais da conta de administrador de serviços de nuvem.
3.	Execute Connect-MsolService –Credential $cred. Esse cmdlet conecta você ao serviço de nuvem. A criação de um contexto que conecta você ao serviço de nuvem é necessária antes de executar qualquer um dos cmdlets adicionais instalados pela ferramenta.
4.	Se você estiver executando esses comandos em um computador que não seja o servidor de federação primário do AD FS, execute Set-MSOLAdfscontext -Computer <AD FS primary server>, em que <AD FS primary server> é o nome FQDN interno do servidor primário do AD FS. Esse cmdlet cria um contexto que conecta você ao AD FS.
5.	Execute Update-MSOLFederatedDomain –DomainName <domain>. Esse cmdlet atualiza as configurações do AD FS no serviço de nuvem e configura a relação de confiança entre os dois.

>[AZURE.NOTE] Se você precisar oferecer suporte a vários domínios de nível superior, como contoso.com e fabrikam.com, deve usar a opção SupportMultipleDomain com todos os cmdlets. Para obter mais informações, veja [Suporte para vários domínios de nível superior](active-directory-aadconnect-multiple-domains.md). Por fim, certifique-se de que todos os servidores Proxy do aplicativo Web sejam atualizados com o rollup [Windows Server de maio de 2014](http://support.microsoft.com/kb/2955164), caso contrário, os proxies poderão não atualizar a si mesmos com o novo certificado, resultando em interrupção.

<!---HONumber=AcomDC_0504_2016-->