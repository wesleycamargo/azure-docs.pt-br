---
title: Perguntas frequentes sobre o Azure Active Directory | Microsoft Docs
description: Esta página tem perguntas frequentes sobre o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: billmath
ms.openlocfilehash: 46a9bf47b4998c4d5be47f67556fbdb3ba7b71db
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054087"
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
Sim. Depois de instalar o agente, você pode concluir o processo de registro usando o seguinte cmdlet do PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**P: AADConnect dá suporte à sincronização de dois domínios com o Azure AD?**</br>
Sim, isso é suportado. Consulte [Vários domínios](active-directory-aadconnect-multiple-domains.md)
 
**P: Damos suporte a vários conectores para o mesmo domínio do Active Directory no Azure AD?**</br> Não, não há suporte para isso 

## <a name="network"></a>Rede
**P: Tenho um firewall, dispositivo de rede ou outra coisa que limita o tempo máximo que as conexões podem permanecer abertas na minha rede. Qual deve ser o limiar de tempo limite no lado do cliente ao usar o Azure Connect AD?**  
Todos os softwares de rede, dispositivos físicos ou qualquer outra coisa que limite o tempo máximo que as conexões podem permanecer abertas deve usar um limiar de pelo menos 5 minutos (300 segundos) para conectividade entre o servidor no qual o cliente do Azure AD Connect está instalado e o Active Directory do Azure. Isso também se aplica a todas as ferramentas de sincronização do Microsoft Identity lançadas anteriormente.

**P: Os SLDs (domínios de rótulo único) têm suporte?**  
Não, o Azure AD Connect não oferece suporte a florestas/domínios locais usando SLDs.

**P: as florestas com domínios do AD não contíguos têm suporte?**  
Não, o Azure AD Connect não oferece suporte a florestas locais que contêm namespaces contíguos.

**P: Há suporte a nomes NetBios com pontos?**  
Não, o Azure AD Connect não oferece suporte a florestas/domínios locais em que o nome NetBios contém um ponto "." no nome.

**P: há suporte para o ambiente puro do IPv6?**  
Não, o Azure AD Connect não oferece suporte ao ambiente puro do IPv6.

## <a name="federation"></a>Federação
**P: O que devo fazer se receber um email me pedindo para renovar o certificado do Office 365?**  
Use as diretrizes descritas no tópico [Renovar certificados](active-directory-aadconnect-o365-certs.md) para saber como renovar o certificado.

**P: Defini a opção “Atualizar automaticamente terceira parte confiável” para a terceira parte confiável do O365. Preciso realizar alguma ação quando meu certificado de autenticação de tokens é substituído automaticamente?**  
Use as diretrizes descritas no artigo [Renovar certificados](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Ambiente
**P: É possível renomear o servidor após a instalação do Azure AD Connect?**  
Nº Alterar o nome do servidor fará com que o mecanismo de sincronização não consiga se conectar ao banco de dados SQL e o serviço não poderá iniciar.

## <a name="identity-data"></a>Dados de identidade
**P: O atributo UPN (userPrincipalName) no AD do Azure não coincide com o UPN local - por quê?**  
Consulte estes artigos:

* [Os nomes de usuário no Office 365, Azure ou Intune não coincidem com o UPN local ou a ID de logon alternativa](https://support.microsoft.com/en-us/kb/2523192)
* [As alterações não são sincronizadas pela ferramenta de sincronização do Azure Active Directory depois que você altera o UPN de uma conta de usuário para usar um domínio federado diferente](https://support.microsoft.com/en-us/kb/2669550)

Você também pode configurar o Azure AD para permitir que o mecanismo de sincronização atualize o userPrincipalName da forma descrita em [Azure AD Connect sync service features (Recursos do serviço de sincronização do Azure AD Connect)](active-directory-aadconnectsyncservice-features.md).

**P: Há suporte para a correspondência suave de objetos de Grupo/Contato do Azure AD no local com objetos de Grupo/Contato existentes do Azure AD?**  
Sim, com base no proxyAddress.  A correspondência suave não tem suporte para grupos que não são habilitados para email.

**P: Há suporte para o atributo ImmutableId definido manualmente em objetos de Grupo/Contato existentes do Azure AD para correspondência rígida com objetos de Grupo/Contato do AD locais?**  
Não, não há suporte para esse recurso no momento.

## <a name="custom-configuration"></a>Configuração personalizada
**P: Onde os cmdlets do PowerShell para o Azure AD Connect estão documentados?**  
Com exceção dos cmdlets documentados neste site, não há suporte para outros cmdlets do PowerShell encontrados no Azure AD Connect para uso do cliente.

**P: Posso usar a opção "Exportação do servidor/importação do servidor" encontrada no *Synchronization Service Manager* para mover a configuração entre servidores?**  
Nº Essa opção não irá recuperar todos os parâmetros de configuração e não deve ser usada. Em vez disso, você deve usar o assistente para criar a configuração básica no segundo servidor e usar o editor de regra de sincronização para gerar scripts do PowerShell para mover qualquer regra personalizada entre servidores. Confira [Migração Swing](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**P: As senhas podem ser armazenadas em cache para a página de entrada do Azure, e isso pode ser evitado, já que contém um elemento de entrada de senha com o atributo de preenchimento automático = "false"?**</br>
No momento, não há suporte para a modificação dos atributos HTML do campo de entrada de Senha, incluindo a marca de preenchimento automático. Estamos trabalhando em um recurso que permitirá Javascript personalizado, e permitirá que você adicione qualquer atributo ao campo de senha. Isso deve ser disponibilizado no último semestre de 2017.

**P: Na página de entrada do Azure, aparecem os nomes de usuários que se conectaram anteriormente com êxito.  Esse comportamento pode ser desativado?**</br>
Atualmente, não há suporte para a modificação dos atributos HTML da página de entrada. Estamos trabalhando em um recurso que permitirá Javascript personalizado, e permitirá que você adicione qualquer atributo ao campo de senha. Isso deve ser disponibilizado no último semestre de 2017.

**P: Existe uma maneira de evitar sessões simultâneas?**</br>
Nº

## <a name="auto-upgrade"></a>Atualização automática

**P: quais são as vantagens e as consequências do uso da atualização automática?**</br>
É recomendável que todos os clientes habilitem a atualização automática para a instalação do Azure AD Connect. Como benefício, eles sempre receberão os patches mais recentes, incluindo atualizações de segurança para vulnerabilidades encontradas no Azure AD Connect. O processo de atualização é simples e ocorrerá automaticamente assim que uma nova versão estiver disponível. Atendemos a milhares de clientes do Azure AD Connect por meio da atualização automática a cada nova versão.

No processo de atualização automática, sempre será definido primeiro se uma instalação está qualificada (isso inclui a pesquisa de alterações personalizadas das regras, fatores ambientais específicos etc.). Em caso positivo, a atualização será realizada e testada. Se os testes mostrarem que uma atualização não foi bem-sucedida, a versão anterior será restaurada automaticamente.

De acordo com o tamanho do ambiente, o processo poderá levar algumas horas. Além disso, durante a atualização, não haverá sincronização entre o Windows Server AD e o Azure AD.

**P: Recebi um email informando que a atualização de automática não funciona mais e preciso instalar a nova versão. Por que preciso fazer isso?**</br>
No ano passado, lançamos um versão do Azure AD Connect que, em algumas situações, pode ter desabilitado o recurso de atualização automática no seu servidor. Corrigimos o problema no Azure AD Connect versão 1.1.750.0, lançado no fim do mês passado. Precisamos que os clientes afetados por esse problema façam a atualização manual para a versão mais recente do Azure AD Connect, a fim de atenuar o problema. Para atualizar manualmente, é necessário baixar e executar a versão mais recente do arquivo AADConnect.msi.
 
-  Se a versão atual for anterior à 1.1.750.0, atualize para a versão mais recente, [que pode ser baixada aqui](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Se a versão do Azure AD Connect for a 1.1.750.0 ou mais recente, você não precisará realizar nenhuma ação para atenuar o problema de atualização automática, pois você já tem a versão corrigida. 

**P: Recebi um email solicitando a atualização para a versão mais recente para habilitar novamente a atualização automática. Minha versão é a 1.1.654.0, preciso atualizar?** </br>    
Sim, você precisa atualizar para a versão 1.1.750 ou mais recente para habilitar novamente a atualização automática. Este é o link que explica como atualizar para uma versão mais recente

**P: Recebi um email solicitando a atualização para a versão mais recente para habilitar novamente a atualização automática. Usei o PowerShell para habilitar a atualização automática, ainda preciso instalar a versão mais recente?**</br>    
Sim, você ainda precisa atualizar para a versão 1.1.750.0 ou mais recente. Habilitar o serviço de atualização automática com o PowerShell não atenuará o problema encontrado na versão 1.1.750 e anteriores

**P: Quero atualizar para uma versão mais recente, mas não sei quem instalou o Azure AD Connect e não tenho o nome de usuário e a senha.  Preciso disso?**</br>
Não é necessário saber o nome de usuário e a senha usada inicialmente para atualizar o Azure AD Connect – qualquer conta do Azure AD que tenha a função de administrador global pode ser usada.

**P: Como posso descobrir a versão do Azure AD Connect?**</br>   
Para verificar qual versão do Azure AD Connect está instalada no servidor, vá para o Painel de Controle e procure a versão instalada do Microsoft Azure AD Connect em "Programas > Programas e recursos":

![version](media/active-directory-aadconnect-faq/faq1.png)

**P: Como realizar uma atualização para a versão mais recente do AADConnect?**</br>    
Este [artigo](active-directory-aadconnect-upgrade-previous-version.md) explica como atualizar para uma versão mais recente. 

**P: Já atualizamos para a versão mais recente do AADConnect no ano passado, precisamos atualizar novamente?**</br> As equipes do Azure AD Connect fazem atualizações no serviço frequentemente, e é importante que seu servidor esteja atualizado com a versão mais recente para se beneficiar de correções de bug e atualizações de segurança, bem como novos recursos. Se você habilitar a atualização automática, a versão do software será atualizada automaticamente. Para localizar o histórico de lançamento de versões do Azure AD Connect, siga este [link](active-directory-aadconnect-version-history.md).

**P: Quanto tempo levará para executar a atualização e o qual é o impacto sobre meus usuários?**</br>    
O tempo necessário para a atualização depende do tamanho do locatário e, para organizações maiores, talvez seja melhor fazê-la durante a noite ou no fim de semana. Durante a atualização, não haverá atividade de sincronização.

**P: Acho que atualizei para o AADConnect, mas o portal do Office ainda menciona o DirSync.  Por quê?**</br>    
A equipe do Office está trabalhando para que as atualizações do portal reflitam o nome atual do produto – ele não reflete a ferramenta de sincronização que você está usando.

**P: Verifiquei meu status de atualização automática, que diz "suspenso". Por que está suspenso? Devo habilitá-lo?**</br>     
Houve um bug em uma versão anterior que, em algumas situações, definia o status de atualização automática como "suspenso". Habilitá-lo manualmente é tecnicamente possível, mas exige seguir várias etapas complexas. Então, o melhor a fazer é instalar a versão mais recente do Azure AD Connect

**P: Minha empresa tem requisitos rigorosos de gerenciamento de alterações e quero controlá-los quando são descumpridos. É possível controlar quando a atualização automática é iniciada?**</br> Não, não há recursos desse tipo atualmente, isso é algo que estamos considerando para uma versão futura.

**P: Receberei um email se a atualização automática falhar? Como saberei se ela foi bem-sucedida?**</br>     
Você não receberá notificações sobre o resultado da atualização, isso é algo que estamos considerando para uma versão futura.

**P: Existe um cronograma de atualizações automáticas?**</br>    
A atualização automática é a primeira etapa no nosso processo de lançamento de uma nova versão, então, sempre que houver uma nova versão, enviaremos as atualizações automáticas. As versões mais recentes do Azure AD Connect são pré-anunciadas no [Roteiro do Azure AD](../../active-directory/whats-new.md).

**P: A atualização automática atualiza o AAD Connect Health?**</br>   Sim, atualização automática também atualiza o AAD Connect Health

**P: Os servidores do AAD Connect também são atualizados automaticamente no Modo de Preparo?**</br>   
Não é possível atualizar um servidor do Azure AD Connect automaticamente no Modo de Preparo.

**P: Se a atualização automática falhar e meu servidor AAD Connect não for iniciado, o que devo fazer?**</br>   
Em casos raros, o serviço Azure AD Connect não inicia após executar a atualização. Nesses casos, reinicialize o servidor, o que geralmente corrige o problema. Se o serviço Azure AD Connect não iniciar, abra um tíquete de suporte. Aqui está uma [link](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) que explica como fazer isso. 

**P: Não sei quais são os riscos ao atualizar para uma versão mais recente do Azure AD Connect. Vocês podem me ligar para ajudar com a atualização?**</br>
Se você precisar de ajuda para atualizar para uma versão mais recente do Azure AD Connect, abra um tíquete de suporte. Aqui está um [link](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) que mostra como fazer isso.

## <a name="troubleshooting"></a>solução de problemas
**P: Como posso obter ajuda com o Azure AD Connect?**

[Pesquise a Base de Dados de Conhecimento (KB) Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Pesquise a KB (Base de Dados de Conhecimento) da Microsoft para obter soluções técnicas de conserto de problemas comuns de suporte ao Azure AD Connect.

[Fóruns do Active Directory do Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Você pode pesquisar e procurar perguntas e respostas técnicas na comunidade ou fazer sua própria pergunta clicando [aqui](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Como obter suporte para o Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* Use este link para obter suporte por meio do Portal do Azure.

