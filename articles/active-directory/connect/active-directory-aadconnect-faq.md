---
title: Perguntas frequentes sobre o Azure Active Directory | Microsoft Docs
description: "Esta página tem perguntas frequentes sobre o Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 85458f4477dadb83a6a2627ef490471ca38ac634
ms.openlocfilehash: c2b78731feb1993e5c7123ff676f38704120ccff


---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Perguntas frequentes sobre o Azure Active Directory Connect

## <a name="general-installation"></a>Instalação geral
**P: a instalação funcionará se o Administrador Global do AD do Azure tem 2FA habilitado?**  
Há suporte para isso nas compilações de fevereiro de 2016.

**P: existe uma forma de instalar o Azure Connect AD autônomo?**  
Somente há suporte para instalar o Azure Connect AD usando o assistente de instalação. Não há suporte para uma instalação silenciosa e autônoma.

**P: Eu tenho uma floresta em que um domínio não pode ser contatado. Como instalo o Azure AD Connect?**  
Há suporte para isso nas compilações de fevereiro de 2016.

**P: O agente de integridade do AD DS funciona no núcleo do servidor?**  
Sim. Depois de instalar o agente, você pode concluir o processo de registro usando o seguinte commandlet do PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>Rede
**P: Tenho um firewall, dispositivo de rede ou outra coisa que limita o tempo máximo que as conexões podem permanecer abertas na minha rede. Qual deve ser o limiar de tempo limite no lado do cliente ao usar o Azure Connect AD?**  
Todos os softwares de rede, dispositivos físicos ou qualquer outra coisa que limite o tempo máximo que as conexões podem permanecer abertas deve usar um limiar de pelo menos 5 minutos (300 segundos) para conectividade entre o servidor no qual o cliente do Azure AD Connect está instalado e o Active Directory do Azure. Isso também se aplica a todas as ferramentas de sincronização do Microsoft Identity lançadas anteriormente.

**P: Os SLDs (domínios de rótulo único) têm suporte?**  
Não, o Azure AD Connect não oferece suporte a florestas/domínios locais usando SLDs.

**P: Há suporte a nomes NetBios com pontos?**  
Não, o Azure AD Connect não oferece suporte a florestas/domínios locais em que o nome NetBios contém um ponto "." no nome.

## <a name="federation"></a>Federação
**P: O que devo fazer se receber um email me pedindo para renovar o certificado do Office 365?**  
Use as diretrizes descritas no tópico [Renovar certificados](active-directory-aadconnect-o365-certs.md) para saber como renovar o certificado.

**P: Defini a opção “Atualizar automaticamente terceira parte confiável” para a terceira parte confiável do O365. Preciso realizar alguma ação quando meu certificado de autenticação de tokens é substituído automaticamente?**  
Use as diretrizes descritas no artigo [Renovar certificados](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Ambiente
**P: É possível renomear o servidor após a instalação do Azure AD Connect?**  
Não. Alterar o nome do servidor fará com que o mecanismo de sincronização não consiga se conectar ao banco de dados SQL e o serviço não poderá iniciar.

## <a name="identity-data"></a>Dados de identidade
**P: O atributo UPN (userPrincipalName) no AD do Azure não coincide com o UPN local - por quê?**  
Consulte estes artigos:

* [Os nomes de usuário no Office 365, Azure ou Intune não coincidem com o UPN local ou a ID de logon alternativa](https://support.microsoft.com/en-us/kb/2523192)
* [As alterações não são sincronizadas pela ferramenta de sincronização do Azure Active Directory depois que você altera o UPN de uma conta de usuário para usar um domínio federado diferente](https://support.microsoft.com/en-us/kb/2669550)

Você também pode configurar o Azure AD para permitir que o mecanismo de sincronização atualize o userPrincipalName da forma descrita em [Azure AD Connect sync service features (Recursos do serviço de sincronização do Azure AD Connect)](active-directory-aadconnectsyncservice-features.md).

**P: Há suporte para a correspondência suave de objetos de Grupo/Contato do Azure AD no local com objetos de Grupo/Contato existentes do Azure AD?**  
Não, não há suporte para esse recurso no momento.

**P: Há suporte para o atributo ImmutableId definido manualmente em objetos de Grupo/Contato existentes do Azure AD para correspondência rígida com objetos de Grupo/Contato do AD locais?**  
Não, não há suporte para esse recurso no momento.

## <a name="custom-configuration"></a>Configuração personalizada
**P: Onde os cmdlets do PowerShell para o Azure AD Connect estão documentados?**  
Com exceção dos cmdlets documentados neste site, não há suporte para outros cmdlets do PowerShell encontrados no Azure AD Connect para uso do cliente.

**P: Posso usar a opção "Exportação do servidor/importação do servidor" encontrada no *Synchronization Service Manager* para mover a configuração entre servidores?**  
Não. Essa opção não irá recuperar todos os parâmetros de configuração e não deve ser usada. Em vez disso, você deve usar o assistente para criar a configuração básica no segundo servidor e usar o editor de regra de sincronização para gerar scripts do PowerShell para mover qualquer regra personalizada entre servidores. Veja [Mover configuração personalizada do servidor ativo para o servidor de preparo](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server).

## <a name="troubleshooting"></a>Solucionar problemas
**P: Como posso obter ajuda com o Azure AD Connect?**

[Pesquise a Base de Dados de Conhecimento (KB) Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Pesquise a KB (Base de Dados de Conhecimento) da Microsoft para obter soluções técnicas de conserto de problemas comuns de suporte ao Azure AD Connect.

[Fóruns do Active Directory do Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Você pode pesquisar e procurar perguntas e respostas técnicas na comunidade ou fazer sua própria pergunta clicando [aqui](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Atendimento ao cliente do Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

* Use este link para obter suporte por meio do Portal do Azure.




<!--HONumber=Jan17_HO4-->


