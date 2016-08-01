<properties
	pageTitle="Perguntas frequentes do Azure AD Connect Health"
	description="Encontre respostas para perguntas frequentes sobre o Azure AD Connect Health. Estas perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte."
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
	ms.date="07/14/2016"
	ms.author="vakarand"/>


# Perguntas frequentes (FAQ) do Azure AD Connect Health

Encontre respostas para perguntas frequentes sobre o Azure AD Connect Health. Estas perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte.

## Perguntas gerais



**P: Eu gerencio vários diretórios do Azure AD. Como alternar para aquele com o Azure Active Directory Premium?**

Você pode alternar entre diferentes diretórios do Azure AD selecionando o Nome de usuário conectado atualmente no canto superior direito e escolhendo a conta apropriada. Se a conta não estiver listada, selecione Sair e use as credenciais de administrador global do Diretório que tiver o Azure Active Directory Premium para entrar.

## Perguntas sobre a instalação



**P: Qual é o impacto da instalação do agente do Azure AD Connect Health sobre servidores individuais?**

O impacto da instalação do agente de integridade de identidade Microsoft nos controladores de domínio ou servidores ADFS é mínimo em relação à CPU, armazenamento e largura de banda de rede de consumo de memória.

Os números a seguir são uma aproximação.

- O consumo da CPU: ~ 1% de aumento
- Consumo de memória: 10% da memória total do sistema
- Uso de largura de banda de rede: ~ 1 MB / 1000 das solicitações de ADFS

>[AZURE.NOTE]Caso o agente não possa se comunicar com o Azure, ele armazenará os dados localmente, até o limite máximo definido. Quando o agente atingir o limite, se ele não puder carregar os dados no serviço, as novas transações do ADFS vão substituir quaisquer transações "em cache" em uma base "menos atendido recentemente".

- Armazenamento de buffer local para o agente de integridade do AD: ~ 20 MB
- Armazenamento de dados necessário para o canal de auditoria


É recomendável que você forneça um espaço em disco de 1024 MB (1 GB) para o canal de auditoria do AD FS para agentes de integridade do AD processarem todos os dados.

**P: Será necessário reinicializar meus servidores durante a instalação dos agentes do Azure AD Connect Health?**

Não. A instalação dos agentes não exigirá a reinicialização do servidor. No entanto, a instalação de algumas das etapas de pré-requisito pode exigir uma reinicialização do servidor.

Por exemplo, no Windows Server 2008 R2, a instalação do .net Framework 4.5 requer a reinicialização do servidor.


**P: Os Serviços do Azure AD Connect Health funcionam por meio de um proxy http de passagem?**

Sim. Para operações em andamento, você pode configurar o Agente de integridade para encaminhar solicitações http de saída usando um HTTP Proxy. Consulte [Configurar agentes do Azure AD Connect Health para usar HTTP Proxy](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy) para saber mais.

Se precisar configurar um proxy durante o registro do Agente, você precisa modificar as configurações de Proxy do Internet Explorer. <br> Abra o Internet Explorer -> Configurações -> Opções de Internet -> Conexões -> Configurações da LAN.<br> Selecione Usar um Servidor Proxy para LAN.<br> Selecione Avançado SE você tiver portas de proxy diferente de HTTP e HTTPS/Secure.<br>


**P: Os serviços do Azure AD Connect Health oferecem suporte à autenticação básica ao se conectar a Proxies Http?**

Não. Um mecanismo para especificar o nome de usuário/senha arbitrariamente para fins de autenticação básica não é suportado.


**P: quais versões do AD DS são compatíveis com o Azure AD Connect Health para AD DS?**

O monitoramento do AD DS tem suporte enquanto instalado nas seguintes versões de sistema operacional:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

## Perguntas de operações



**P: Eu preciso habilitar a auditoria nos servidores de Proxy de aplicativo do AD FS ou meus servidores de Proxy de aplicativo da Web?**

Não, a auditoria não precisa estar habilitada em servidores de Proxy de aplicativo do AD FS ou servidores de Proxy de aplicativo da Web. Ela só precisa estar habilitada em servidores federados de AD FS.



**P: Como Alertas do Azure AD Connect Health são resolvidos?**

Alertas do Azure AD Connect Health são resolvidos em uma condição de êxito. Os agentes do Azure AD Connect Health detectam e relatam as condições de sucesso para o serviço periodicamente. Para alguns alertas, a supressão é periódica. Ou seja, se a mesma condição de erro não for observada no período de 72 horas a partir da geração do alerta, este será resolvido automaticamente.




**P: Quais portas de firewall eu preciso abrir para o agente do Azure AD Connect Health funcione?**

Você precisará ter as portas TCP/UDP 80, 443 e 5671 abertas para o agente do Azure AD Connect Health poder se comunicar com os pontos de extremidade de serviço de integridade do Azure AD.


**P: por que vejo dois servidores com o mesmo nome no Portal do Azure AD Connect Health?**

Quando você remove um agente de um servidor, o servidor não é automaticamente removido do Portal do Azure AD Connect automaticamente. Portanto, se você removeu um agente manualmente de um servidor ou o próprio servidor, precisará excluir manualmente a entrada do servidor no portal do Azure AD Connect Health. Para saber mais, confira [Excluir uma instância de serviço ou servidor.](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance) Além disso, se você tiver criado um servidor com uma nova imagem ou um novo servidor com os mesmos detalhes (como o nome do computador), mas não tiver removido o servidor no portal do Azure AD Connect Health e instalado o agente no novo servidor, poderá ver duas entradas para o servidor. Nesse caso, você deve excluir a entrada que pertence ao servidor mais antigo manualmente. Os dados nessa entrada geralmente estarão desatualizados.

## Links relacionados

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Usar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [Histórico de versão do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0720_2016-->