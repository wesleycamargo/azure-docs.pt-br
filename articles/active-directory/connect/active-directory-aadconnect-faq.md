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
ms.date: 06/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2e5a7cab5c9db0c13ca0c0986c18c86adf675562
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850279"
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Perguntas frequentes sobre o Azure Active Directory Connect

## <a name="general-installation"></a>Instalação geral
**P: a instalação funcionará se o Administrador Global do AD do Azure tem 2FA habilitado?**  
Com as versões de fevereiro de 2016, há suporte para esse cenário.

**P: existe uma forma de instalar o Azure Connect AD autônomo?**  
Somente há suporte para instalar o Azure Connect AD usando o assistente de instalação. Não há suporte para uma instalação silenciosa e autônoma.

**P: Eu tenho uma floresta em que um domínio não pode ser contatado. Como instalo o Azure AD Connect?**  
Com as versões de fevereiro de 2016, há suporte para esse cenário.

**P: O agente de integridade do AD DS funciona no núcleo do servidor?**  
Sim. Depois de instalar o agente, você pode concluir o processo de registro usando o seguinte cmdlet do PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**P: AADConnect dá suporte à sincronização de dois domínios com o Azure AD?**</br>
Sim, há suporte para esse cenário. Consulte [Vários domínios](active-directory-aadconnect-multiple-domains.md)
 
**P: é possível ter vários conectores para o mesmo domínio do Active Directory no AD do Azure se conectar?**</br> Não há suporte para nenhum, vários conectores para o mesmo domínio do AD. 

**P: posso mover o banco de dados do Azure AD Connect do banco de dados local para um SQL Server remoto?**</br> Sim, as etapas a seguir fornecem diretrizes gerais sobre como fazer isso.  Estamos trabalhando atualmente em um documento mais detalhado que estará disponível em breve.


   1. Faça backup do banco de dados de "ADSync" de LocalDB a maneira mais simples de fazer isso é usar o SQL Server Management Studio instalado no mesmo computador que o Azure AD Connect. Conecte-se a "(localdb)\.\ADSync" – em seguida, faça backup do banco de dados ADSync
   2. Restaurar o banco de dados "ADSync" para a instância remota do SQL
   3. Instalar o Azure AD Connect contra existente [banco de dados SQL remoto](active-directory-aadconnect-existing-database.md) o link mostra as etapas necessárias ao migrar para o uso de um banco de dados Local do SQL. Se você estiver migrando para o uso de um banco de dados remoto do SQL, em seguida, na etapa 5 deste processo você também precisará inserir uma conta de serviço que o serviço de sincronização do Windows serão executadas. Essa conta de serviço do mecanismo de sincronização é descrita aqui:</br></br>
   **Usar uma conta de serviço existente**- por padrão, o Azure AD Connect usa uma conta de serviço virtual para os serviços de sincronização para usar. Se você usa um SQL server remoto ou usa um proxy que requer autenticação, você precisa usar uma conta de serviço gerenciado ou usar uma conta de serviço no domínio e saber a senha. Nesses casos, insira a conta a usar. Verifique se o usuário que está executando a instalação é um SA no SQL para que um logon para a conta de serviço possa ser criado. Confira [Contas e permissões do Azure AD Connect](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account).</br></br> Com a compilação mais recente, o provisionamento do banco de dados agora pode ser executado fora da banda pelo administrador do SQL e, em seguida, instalado pelo administrador do Azure AD Connect com direitos de proprietário de banco de dados. Para obter mais informações, confira [Instale o Azure AD Connect usando permissões de administrador do SQL delegado](active-directory-aadconnect-sql-delegation.md).

Para manter as coisas simples é recomendável que o usuário instalando o Azure AD Connect seja um SA no SQL. (No entanto com builds recentes agora você pode usar delegadas administrador do SQL, conforme descrito [aqui](active-directory-aadconnect-sql-delegation.md).

## <a name="network"></a>Rede
**P: Tenho um firewall, dispositivo de rede ou outra coisa que limita o tempo máximo que as conexões podem permanecer abertas na minha rede. Quanto tempo limite do lado do cliente ficará quando usando o Azure AD Connect?**  
Todos os softwares de rede, dispositivos físicos ou qualquer outra coisa que limite o tempo máximo que as conexões podem permanecer abertas deve usar um limiar de pelo menos 5 minutos (300 segundos) para conectividade entre o servidor no qual o cliente do Azure AD Connect está instalado e o Active Directory do Azure. Esta recomendação também se aplica a todas as ferramentas de sincronização do Microsoft Identity lançadas anteriormente.

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
Use a orientação é descrita no [renovar certificados](active-directory-aadconnect-o365-certs.md) documento sobre como renovar o certificado.

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
Sim, essa correspondência flexível se baseará o proxyAddress.  A correspondência suave não tem suporte para grupos que não são habilitados para email.

**P: Há suporte para o atributo ImmutableId definido manualmente em objetos de Grupo/Contato existentes do Azure AD para correspondência rígida com objetos de Grupo/Contato do AD locais?**  
Não, definindo manualmente o atributo de ImmutableId em um objeto de contato/grupo do AD do Azure existente para correspondência de disco rígida no momento não há suporte.

## <a name="custom-configuration"></a>Configuração personalizada
**P: Onde os cmdlets do PowerShell para o Azure AD Connect estão documentados?**  
Com exceção dos cmdlets documentados neste site, não há suporte para outros cmdlets do PowerShell encontrados no Azure AD Connect para uso do cliente.

**P: Posso usar a opção "Exportação do servidor/importação do servidor" encontrada no *Synchronization Service Manager* para mover a configuração entre servidores?**  
Nº Essa opção não irá recuperar todos os parâmetros de configuração e não deve ser usada. Em vez disso, você deve usar o assistente para criar a configuração básica no segundo servidor e usar o editor de regra de sincronização para gerar scripts do PowerShell para mover qualquer regra personalizada entre servidores. Confira [Migração Swing](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**P: As senhas podem ser armazenadas em cache para a página de entrada do Azure, e isso pode ser evitado, já que contém um elemento de entrada de senha com o atributo de preenchimento automático = "false"?**</br>
Atualmente, modificar os atributos HTML do campo de entrada de senha, incluindo a marca de preenchimento automático, não há suporte. Um recurso que permite o uso de Javascript personalizado, permitindo que você adicione qualquer atributo para o campo de senha está sendo realizado no momento.

**P: Na página de entrada do Azure, aparecem os nomes de usuários que se conectaram anteriormente com êxito.  Esse comportamento pode ser desativado?**</br>
Atualmente, modificar os atributos HTML do campo de entrada de senha, incluindo a marca de preenchimento automático, não há suporte. Um recurso que permite o uso de Javascript personalizado, permitindo que você adicione qualquer atributo para o campo de senha está sendo realizado no momento.

**P: Existe uma maneira de evitar sessões simultâneas?**</br>
Nº

## <a name="auto-upgrade"></a>Atualização automática

**P: quais são as vantagens e as consequências do uso da atualização automática?**</br>
Todos os clientes são aconselhados a habilitar a atualização automática para a instalação do Azure AD Connect. Os benefícios são que eles sempre receba os patches mais recentes, incluindo atualizações de segurança de vulnerabilidades que foram encontradas no Azure AD Connect. O processo de atualização é simples e ocorrerá automaticamente assim que uma nova versão estiver disponível. Muitos milhares de clientes do Azure AD Connect usam a atualização automática com cada nova versão.

O processo de atualização automática sempre primeiro estabelecerá se uma instalação está qualificada para atualização automática (esse processo inclui a busca de alterações personalizadas para regras, fatores ambientais específicos etc.) e, se assim, a atualização é executada e testada. Se os testes mostrarem que uma atualização não foi bem-sucedida, a versão anterior será restaurada automaticamente.

De acordo com o tamanho do ambiente, o processo poderá levar algumas horas. Além disso, durante a atualização, não haverá sincronização entre o Windows Server AD e o Azure AD.

**P: Recebi um email informando que a atualização de automática não funciona mais e preciso instalar a nova versão. Por que preciso fazer isso?**</br>
Ano passado, uma versão do Azure AD Connect que, em determinadas circunstâncias, pode ter desabilitado o recurso de atualização automática no servidor, foi liberado. Esse problema foi corrigido no Azure AD Connect versão 1.1.750.0. Os clientes que foram afetados por esse problema precisam executar um script do PowerShell para repará-lo ou atualizar manualmente a versão mais recente do Azure AD Connect para atenuar o problema. 

Para executar o script do PowerShell, baixe o script de [aqui](https://aka.ms/repairaadconnect) e execute o script em seu servidor do AADConnect em uma janela do PowerShell administrativa. [Este é um vídeo curto](https://aka.ms/repairaadcau) que explica detalhadamente como fazer isso.

Para atualizar manualmente, é necessário baixar e executar a versão mais recente do arquivo AADConnect.msi.
 
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

**P: Já atualizamos para a versão mais recente do AADConnect no ano passado, precisamos atualizar novamente?**</br> As equipes do Azure AD Connect faz frequentes atualizações para o serviço e é importante que seu servidor está atualizado com a versão mais recente para se beneficiar de correções de bugs e atualizações de segurança, bem como novos recursos. Se você habilitar a atualização automática, a versão do software será atualizada automaticamente. Para localizar o histórico de lançamento de versões do Azure AD Connect, siga este [link](active-directory-aadconnect-version-history.md).

**P: Quanto tempo levará para executar a atualização e o qual é o impacto sobre meus usuários?**</br>    
O tempo necessário para a atualização depende do tamanho do locatário e, para organizações maiores, talvez seja melhor fazê-la durante a noite ou no fim de semana. Durante a atualização nenhuma atividade de sincronização ocorre.

**P: Acho que atualizei para o AADConnect, mas o portal do Office ainda menciona o DirSync.  Por que é isso?**</br>    
A equipe do Office está trabalhando para que as atualizações do portal reflitam o nome atual do produto – ele não reflete a ferramenta de sincronização que você está usando.

**P: Verifiquei meu status de atualização automática, que diz "suspenso". Por que está suspenso? Devo habilitá-lo?**</br>     
Houve um bug em uma versão anterior que, em algumas situações, definia o status de atualização automática como "suspenso". Habilitá-lo manualmente é tecnicamente possível, mas exige seguir várias etapas complexas. Então, o melhor a fazer é instalar a versão mais recente do Azure AD Connect

**P: Minha empresa tem requisitos rigorosos de gerenciamento de alterações e quero controlá-los quando são descumpridos. É possível controlar quando a atualização automática é iniciada?**</br> Não, não há nenhum tal recurso hoje, esse recurso é algo que está sendo avaliação para uma versão futura.

**P: Receberei um email se a atualização automática falhar? Como saberei se ela foi bem-sucedida?**</br>     
Você não será notificado sobre o resultado da atualização, esse recurso é algo que está sendo avaliação para uma versão futura.

**P: Existe um cronograma de atualizações automáticas?**</br>    
A atualização automática é a primeira etapa no processo de lançamento de uma versão mais recente, então sempre que uma nova versão, não há atualizações automática serão enviadas. As versões mais recentes do Azure AD Connect são pré-anunciadas no [Roteiro do Azure AD](../../active-directory/whats-new.md).

**P: A atualização automática atualiza o AAD Connect Health?**</br>   Sim, atualização automática também atualiza o AAD Connect Health

**P: Os servidores do AAD Connect também são atualizados automaticamente no Modo de Preparo?**</br>   
Sim, você pode atualização automática um servidor de conexão do AD do Azure que está no modo de preparo.

**P: Se a atualização automática falhar e meu servidor AAD Connect não for iniciado, o que devo fazer?**</br>   
Em casos raros, o serviço Azure AD Connect não inicia após executar a atualização. Nesses casos, reinicialize o servidor, que geralmente corrige o problema. Se o serviço do Azure AD Connect não iniciar, abra um tíquete de suporte. Aqui está uma [link](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) que explica como fazer isso. 

**P: Não sei quais são os riscos ao atualizar para uma versão mais recente do Azure AD Connect. Vocês podem me ligar para ajudar com a atualização?**</br>
Se você precisar de ajuda a atualizar para uma versão mais recente do Azure AD Connect, abra um tíquete de suporte, aqui está uma [link](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) que mostra como fazer isso.

## <a name="troubleshooting"></a>solução de problemas
**P: Como posso obter ajuda com o Azure AD Connect?**

[Pesquise a Base de Dados de Conhecimento (KB) Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Pesquise a KB (Base de Dados de Conhecimento) da Microsoft para obter soluções técnicas de conserto de problemas comuns de suporte ao Azure AD Connect.

[Fóruns do Active Directory do Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Você pode pesquisar e procurar perguntas e respostas técnicas na comunidade ou fazer sua própria pergunta clicando [aqui](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Como obter suporte para o Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)



