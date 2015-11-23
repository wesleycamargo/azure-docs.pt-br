<properties
	pageTitle="Perguntas frequentes sobre o Azure AD Connect | Microsoft Azure"
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
	ms.date="11/10/2015"
	ms.author="billmath"/>

# Perguntas frequentes do Azure Active Directory Connect

## Instalação geral
**P: a instalação funcionará se o Administrador Global do AD do Azure tem 2FA habilitado?**

Instalação não funcionará neste caso. O Administrador Global que instalou o Azure AD Connect não pode ter MFA habilitado. Estamos cientes dessa limitação e isso dará suporte no futuro.

**P: existe uma forma de instalar o Azure Connect AD autônomo?**

Somente há suporte para instalar o Azure Connect AD usando o assistente de instalação. Não há suporte para uma instalação silenciosa e autônoma.

## Instalação do Express

## Instalação personalizada

## Rede
**P: Tenho um firewall, dispositivo de rede ou outra coisa que limita o tempo máximo que as conexões podem permanecer abertas na minha rede. Qual deve ser o limiar de tempo limite no lado do cliente ao usar o Azure Connect AD?**

Todos os softwares de rede, dispositivos físicos ou qualquer outra coisa que limite o tempo máximo que as conexões podem permanecer abertas deve usar um limiar de pelo menos 5 minutos (300 segundos) para conectividade entre o servidor no qual o cliente do Azure AD Connect está instalado e o Active Directory do Azure. Isso também se aplica a todas as ferramentas de sincronização do Microsoft Identity lançadas anteriormente.

**P: O que devo fazer se receber um email me pedindo para renovar o certificado do Office 365**

Use as diretrizes descritas no artigo [aqui](active-directory-aadconnect-o365-certs.md) para resolver os problemas referentes à renovação do certificado.

## Solucionar problemas

**P: Como posso obter ajuda com o Azure AD Connect?**

[Pesquise a Base de Dados de Conhecimento (KB) Microsoft](https://www.microsoft.com/pt-BR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Pesquise a KB (Base de Dados de Conhecimento) da Microsoft para obter soluções técnicas de conserto de problemas comuns de suporte ao Azure AD Connect.

[Fóruns do Active Directory do Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=WindowsAzureAD)

- Você pode pesquisar e procurar perguntas e respostas técnicas na comunidade ou fazer sua própria pergunta clicando [aqui](https://social.msdn.microsoft.com/Forums/azure/pt-BR/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).


[Atendimento ao cliente do Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

- Use este link para obter suporte por meio do Portal do Azure.

<!---HONumber=Nov15_HO3-->