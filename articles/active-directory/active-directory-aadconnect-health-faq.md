<properties 
	pageTitle="Perguntas frequentes do Azure AD Connect Health" 
	description="Encontre respostas para perguntas frequentes sobre o Azure AD Connect Health. Estas perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015"
	ms.author="billmath"/>


# Perguntas frequentes (FAQ) do Azure AD Connect Health

Encontre respostas para perguntas frequentes sobre o Azure AD Connect Health. Estas perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte.

## Perguntas gerais



**P: Tenho vários locatários no Active Directory do Azure. Como alternar para aquele com o Azure Active Directory Premium?**

Você pode alternar o locatário do AD do Azure, selecionando "Início" na barra de navegação à esquerda, e selecionando o nome de usuário conectado no momento no canto superior direito e escolhendo a conta do locatário à direita. Se a conta do locatário não estiver listada aqui, selecione a saída e use as credenciais de administrador global do locatário do Azure Active Directory Premium para efetuar login.


## Perguntas sobre a instalação



**P: Qual é o impacto da instalação do agente do Azure AD Connect Health sobre servidores individuais?**

O impacto da instalação do agente de integridade de identidade Microsoft nos servidores ADFS é mínimo em relação à CPU, memória consumo de largura de banda e armazenamento.

Os números a seguir são uma aproximação.

- O consumo da CPU: ~ 1% de aumento
- Consumo de memória: 10% da memória total do sistema
- Uso de largura de banda de rede: ~ 1 MB / 1000 das solicitações de ADFS
>[AZURE.NOTE]

- Armazenamento de buffer local para o agente de integridade do AD: ~ 20 MB
- Armazenamento de dados necessário para o canal de auditoria


É recomendável que você forneça um espaço em disco de 1024 MB (1 GB) para o canal de auditoria do AD FS para agentes de integridade do AD processarem todos os dados.

**P: Será necessário reinicializar meus servidores durante a instalação dos agentes do Azure AD Connect Health?**

Não. A instalação dos agentes não exigirá a reinicialização do servidor. No entanto, a instalação de algumas das etapas de pré-requisito pode exigir uma reinicialização do servidor.

Por exemplo, no Windows Server 2008 R2, a instalação do .net Framework 4.5 requer a reinicialização do servidor.


**P: Os Serviços do Azure AD Connect Health funcionam por meio de um proxy http de passagem?**

Sim, o processo de registro e a operação normal podem funcionar através de um proxy explícito configurado para encaminhar solicitações http de saída. “Netsh WinHttp set Proxy” não funciona nesse caso pois o agente usa System.Net para fazer solicitações da web em vez do Serviços HTTP do Microsoft Windows.

Execute a qualquer momento antes de executar o Register-AdHealthAgent (a etapa final da instalação)


- Etapa 1 – Adicionar entrada no arquivo machine.config


Localize o arquivo machine.config. O arquivo está localizado em %windir%\\Microsoft.NET\\Framework64[version]\\config\\machine.config</li>

Adicione a seguinte entrada sob o elemento de <configuration></configuration> no arquivo machine.config.
 
		
	<system.net>  
			<defaultProxy useDefaultCredentials="true">
       		<proxy 
        usesystemdefault="true" 
        proxyaddress="http://YOUR.PROXY.HERE.com"  
        bypassonlocal="true"/>
		</defaultProxy>
	</system.net> 

 

Mais<defaultProxy>informações podem ser encontradas [aqui] (https://msdn.microsoft.com/library/kd3cf2ex(v=vs.110).aspx).

Essas definições configuram os aplicativos .NET em todo o sistema para usar o proxy definido explicitamente quando fizer solicitações http .NET. Não é recomendável modificar cada app.config individualmente porque a configuração será desfeita durante a atualização automática. Você somente precisará alterar um arquivo e ele será mantido pelas atualizações se você modificar apenas o machine.config.

- Etapa 2 - Configurar Proxy nas opções da Internet

Abra o Internet Explorer -> Configurações -> Opções de Internet -> Conexões -> Configurações da LAN.

Selecione Usar um Servidor Proxy para LAN

Selecionar Avançada, SE você tiver portas de proxy diferente de HTTP e HTTPS/Secure




**P: Os serviços do Azure AD Connect Health oferecem suporte à autenticação básica ao se conectar a Proxies Http?**

Não. Um mecanismo para especificar o nome de usuário/senha arbitrariamente para fins de autenticação básica não é suportado.


## Perguntas de operações



**P: Eu preciso habilitar a auditoria nos servidores de Proxy de aplicativo do AD FS ou meus servidores de Proxy de aplicativo da Web?**

Não, a auditoria não precisa estar habilitada em servidores de Proxy de aplicativo do AD FS ou servidores de Proxy de aplicativo da Web. Ela só precisa estar habilitada em servidores federados de AD FS.



**P: Como Alertas do Azure AD Connect Health são resolvidos?**

Alertas do Azure AD Connect Health são resolvidos em uma condição de êxito. Os agentes do Azure AD Connect Health detectam e relatam as condições de sucesso para o serviço periodicamente. Para alguns alertas, a supressão é periódica. Ou seja, se a mesma condição de erro não for observada no período de 48 horas a partir da geração do alerta, este será resolvido automaticamente.




**P: Quais portas de firewall eu preciso abrir para o agente do Azure AD Connect Health funcione?**

Você precisará ter as portas TCP/UDP 80 e 443 abertas para o agente do Azure AD Connect Health poder se comunicar com os pontos de extremidade de serviço de integridade do AD do Azure.

<!---HONumber=July15_HO3-->