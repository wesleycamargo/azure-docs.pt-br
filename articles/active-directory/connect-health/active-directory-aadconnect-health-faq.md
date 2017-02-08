---
title: Perguntas frequentes do Azure AD Connect Health
description: "Encontre respostas para perguntas frequentes sobre o Azure AD Connect Health. Estas perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte."
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 2f990f4c888da91422a6a6e9873307cbb483e782
ms.openlocfilehash: b6687bc0ccde0e257338a939f39e343f75a6f5df


---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ) do Azure AD Connect Health
Encontre respostas para perguntas frequentes sobre o Azure AD Connect Health. Estas perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte.

## <a name="general-questions"></a>Perguntas gerais
**P: Eu gerencio vários diretórios do Azure AD. Como alternar para aquele com o Azure Active Directory Premium?**

Você pode alternar entre diferentes locatários do Azure AD selecionando o Nome de usuário conectado atualmente no canto superior direito e escolhendo a conta apropriada. Se a conta não estiver listada, selecione Sair e use as credenciais de administrador global do Diretório que tiver o Azure Active Directory Premium para entrar.

## <a name="installation-questions"></a>Perguntas sobre a instalação
**P: Qual é o impacto da instalação do agente do Azure AD Connect Health sobre servidores individuais?**

O impacto da instalação do ADFS de Agentes do Microsoft Azure AD Connect Health, de servidores de Proxy de Aplicativo Web, de servidores do Azure AD Connect (sincronização), de Controladores de Domínio é mínimo em relação à CPU, ao consumo de memória, à largura de banda de rede e ao armazenamento.

Os números a seguir são uma aproximação.

* O consumo da CPU: ~ 1% de aumento
* Consumo de memória: 10% da memória total do sistema

> [!NOTE]
> Caso o agente não possa se comunicar com o Azure, ele armazenará os dados localmente, até o limite máximo definido. O agente substitui os dados em "cache" de forma “atendido menos recentemente”.
> 
> 

* Armazenamento em buffer local para Agentes do Azure AD Connect Health: ~20 MB
* Para servidores do AD FS, é recomendável que você provisione um espaço em disco de 1024 MB (1 GB) para o Canal de Auditoria do AD FS para Agentes do Azure AD Connect Health para processar todos os dados de auditoria antes que eles sejam substituídos.

**P: Será necessário reinicializar meus servidores durante a instalação dos agentes do Azure AD Connect Health?**

Não. A instalação dos agentes não exigirá a reinicialização do servidor. No entanto, a instalação de algumas das etapas de pré-requisito pode exigir uma reinicialização do servidor.

Por exemplo, no Windows Server 2008 R2, a instalação do .net Framework 4.5 requer a reinicialização do servidor.

**P: Os Serviços do Azure AD Connect Health funcionam por meio de um proxy http de passagem?**

Sim.  Para operações em andamento, você pode configurar o Agente de integridade para encaminhar solicitações http de saída usando um HTTP Proxy.

Se precisar configurar um proxy durante o registro do Agente, talvez seja necessário modificar as configurações de Proxy do Internet Explorer com antecedência.

1. Abra o Internet Explorer -> Configurações -> Opções de Internet -> Conexões -> Configurações da LAN.
2. Selecione Usar um Servidor de Proxy para a LAN.
3. Selecione Avançado SE você tiver portas de proxy diferente de HTTP e HTTPS/Secure.

**P: Os serviços do Azure AD Connect Health oferecem suporte à autenticação básica ao se conectar a Proxies Http?**

Não. Um mecanismo para especificar o nome de usuário/senha arbitrariamente para fins de autenticação básica não é suportado.

**P: quais versões do AD DS são compatíveis com o Azure AD Connect Health para AD DS?**

O monitoramento do AD DS tem suporte enquanto instalado nas seguintes versões de sistema operacional:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

## <a name="operations-questions"></a>Perguntas de operações
**P: Eu preciso habilitar a auditoria nos servidores de Proxy de aplicativo do AD FS ou meus servidores de Proxy de aplicativo da Web?**

Não, a auditoria não precisa estar habilitada nos Servidores de Proxy de Aplicativo da Web (WAP). Habilite-a nos servidores do AD FS.

**P: Como Alertas do Azure AD Connect Health são resolvidos?**

Alertas do Azure AD Connect Health são resolvidos em uma condição de êxito. Os agentes do Azure AD Connect Health detectam e relatam as condições de sucesso para o serviço periodicamente. Para alguns alertas, a supressão é periódica. Em outras palavras, se a mesma condição de erro não for observada no período de 72 horas a partir da geração do alerta, este será resolvido automaticamente.

**P: Quais portas de firewall eu preciso abrir para o agente do Azure AD Connect Health funcione?**

Você precisará ter as portas TCP/UDP 443 e 5671 abertas para o agente do Azure AD Connect Health poder se comunicar com os pontos de extremidade de serviço Azure AD Health.

**P: por que vejo dois servidores com o mesmo nome no Portal do Azure AD Connect Health?**

Quando você remove um agente de um servidor, o servidor não é automaticamente removido do Portal do Azure AD Connect.  Se você tiver removido um agente manualmente de um servidor ou o próprio servidor, precisará excluir manualmente a entrada do servidor no portal do Azure AD Connect Health. 

Se você tiver criado um servidor com uma nova imagem ou um novo servidor com os mesmos detalhes (como o nome do computador), e se não tiver removido o servidor já registrado do portal do Azure AD Connect Health e instalado o agente no novo servidor, poderá ver duas entradas com o mesmo nome.  
Nesse caso, você deve excluir a entrada que pertence ao servidor mais antigo manualmente. Os dados para esse servidor devem estar desatualizados.

## <a name="related-links"></a>Links relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Usar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [Histórico de versão do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Jan17_HO3-->


