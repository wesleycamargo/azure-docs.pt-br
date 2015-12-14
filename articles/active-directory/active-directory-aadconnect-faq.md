<properties
	pageTitle="Azure AD Connect: perguntas frequentes | Microsoft Azure"
	description="Esta página tem perguntas frequentes sobre o Azure AD Connect."
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
	ms.date="12/02/2015"
	ms.author="billmath"/>

# Perguntas frequentes do Azure AD Connect

## Instalação geral
**P: a instalação funcionará se o Administrador Global do AD do Azure tem 2FA habilitado?**

Instalação não funcionará neste caso. O Administrador Global que instalou o Azure AD Connect não pode ter MFA habilitado. Estamos cientes dessa limitação e isso dará suporte no futuro.

**P: existe uma forma de instalar o Azure Connect AD autônomo?**

Somente há suporte para instalar o Azure Connect AD usando o assistente de instalação. Não há suporte para uma instalação silenciosa e autônoma.

**P: Eu tenho uma floresta em que um domínio não pode ser contatado. Como instalo o Azure AD Connect?**

Ouvimos esses comentários e ofereceremos suporte a isso em uma versão futura.

## Rede
**P: Tenho um firewall, dispositivo de rede ou outra coisa que limita o tempo máximo que as conexões podem permanecer abertas na minha rede. Qual deve ser o limiar de tempo limite no lado do cliente ao usar o Azure Connect AD?**

Todos os softwares de rede, dispositivos físicos ou qualquer outra coisa que limite o tempo máximo que as conexões podem permanecer abertas deve usar um limiar de pelo menos 5 minutos (300 segundos) para conectividade entre o servidor no qual o cliente do Azure AD Connect está instalado e o Active Directory do Azure. Isso também se aplica a todas as ferramentas de sincronização do Microsoft Identity lançadas anteriormente.

**P: O que devo fazer se receber um email me pedindo para renovar o certificado do Office 365**

Use as diretrizes descritas no artigo [aqui](active-directory-aadconnect-o365-certs.md) para resolver os problemas referentes à renovação do certificado.

**P: Os SLDs (domínios de rótulo único) têm suporte?**

Não, o Azure AD Connect não oferece suporte a florestas/domínios locais usando SLDs.

**P: Há suporte a nomes NetBios com pontos?**

Não, o Azure AD Connect não oferece suporte a florestas/domínios locais em que o nome NetBios contém um ponto "." no nome.

## Ambiente

**P: É possível renomear o servidor após a instalação do Azure AD Connect?**

Não. Alterar o nome do servidor fará com que o mecanismo de sincronização não consiga se conectar ao banco de dados SQL e o serviço não poderá iniciar.

## Dados de identidade

**P: O atributo UPN (userPrincipalName) no AD do Azure não coincide com o UPN local - por quê?**

Consulte estes artigos:

- [Os nomes de usuário no Office 365, Azure ou Intune não coincidem com o UPN local ou ID de logon alternativo](https://support.microsoft.com/pt-BR/kb/2523192)
- [As alterações não são sincronizadas pela ferramenta de sincronização do Active Directory do Azure depois que você altera o UPN de uma conta de usuário para usar um domínio diferente](https://support.microsoft.com/pt-BR/kb/2669550)

## Configuração personalizada

**P: Onde os cmdlets do PowerShell para o Azure AD Connect estão documentados?**

Com exceção dos cmdlets documentados neste site, não há suporte para outros cmdlets do PowerShell encontrados no Azure AD Connect para uso do cliente.

**P: Posso usar "Exportar servidor/importar servidor" encontrado no gerenciador de serviços para mover a configuração entre servidores?**

Não. Essa opção não irá recuperar todos os parâmetros de configuração e não deve ser usada. Em vez disso, você deve usar o assistente para criar a configuração básica no segundo servidor e usar o editor de regra de sincronização para gerar scripts do PowerShell para mover qualquer regra personalizada entre servidores.

## Solucionar problemas

**P: Como posso obter ajuda com o Azure AD Connect?**

[Pesquise a Base de Dados de Conhecimento (KB) Microsoft](https://www.microsoft.com/pt-BR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Pesquise a KB (Base de Dados de Conhecimento) da Microsoft para obter soluções técnicas de conserto de problemas comuns de suporte ao Azure AD Connect.

[Fóruns do Active Directory do Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=WindowsAzureAD)

- Você pode pesquisar e procurar perguntas e respostas técnicas na comunidade ou fazer sua própria pergunta clicando [aqui](https://social.msdn.microsoft.com/Forums/azure/pt-BR/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).


[Atendimento ao cliente do Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

- Use este link para obter suporte por meio do Portal do Azure.

<!---HONumber=AcomDC_1203_2015-->