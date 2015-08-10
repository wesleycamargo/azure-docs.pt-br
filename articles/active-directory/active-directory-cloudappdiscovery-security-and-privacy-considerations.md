<properties 
	pageTitle="Considerações de privacidade e segurança no Cloud App Discovery" 
	description="Este tópico descreve as considerações de segurança e privacidade relacionadas ao Cloud App Discovery." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Considerações de privacidade e segurança no Cloud App Discovery

A Microsoft está comprometida em proteger a privacidade e segurança de seus dados, oferecendo softwares e serviços que ajudam a gerenciar a segurança de TI da sua organização. <br> Reconhecemos que quando você confia seus dados a outras pessoas, essa confiança exige investimentos rigorosos em engenharia de segurança e competências para dar suporte a isso. A Microsoft obedece a diretrizes rígidas de conformidade e segurança, desde práticas seguras do ciclo de vida de desenvolvimento do software até o funcionamento de um serviço. <br> Segurança e proteção de dados é uma prioridade principal da Microsoft.

Este tópico explica como os dados são coletados, processados e protegidos dentro do Cloud App Discovery do Active Directory do Azure




##Visão geral

Cloud App Discovery é um recurso do AD do Azure e é hospedado no Microsoft Azure. <br> Agentes de ponto de extremidade do Cloud App Discovery são usados para coletar dados de descoberta de aplicativos de computadores gerenciados por TI. <br> Os dados coletados são enviados com segurança por um canal criptografado para o serviço Cloud App Discovery do AD do Azure. <br> Os dados do Cloud App Discovery para uma organização ficam, então, visíveis no portal do Azure.


<center>![Como funciona o Cloud App Discovery](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center>


As seções a seguir seguem o fluxo de informações e descrevem como ele é protegido à medida que avança de sua organização para o serviço Cloud App Discovery e, finalmente, para o portal do Cloud App Discovery.



## Coletando dados de sua organização

Para usar o recurso Cloud App Discovery do Active Directory do Azure para obter insights sobre os aplicativos usados pelos funcionários da sua organização, você precisa primeiro implantar os agentes de ponto de extremidade do Cloud App Discovery do AD do Azure nos computadores de sua organização.

Os administradores do locatário do Active Directory do Azure (ou seu representante) podem baixar o pacote de instalação do agente no portal do Azure. Os agentes podem ser instalados manualmente em vários computadores da organização usando a política de grupo ou do SCCM.

Para obter mais informações sobre opções de implantação, consulte [Guia de implantação de política de grupo do Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).


### Dados coletados pelo agente

As informações descritas na lista a seguir são coletadas pelo agente quando são feitas conexões aos aplicativos Web. As informações são coletadas apenas para os aplicativos que o administrador configurou para descoberta de aplicativo. <br> A lista de aplicativos para a qual você escolhe coletar metadados é configurável no portal, na guia Configurações.



> [AZURE.NOTE]Para obter mais detalhes, consulte [Introdução ao Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 
**Categoria das informações**: informações do usuário <br>**Descrição**:<br> o nome de usuário do Windows do processo que fez uma solicitação ao aplicativo Web de destino (por exemplo: DOMAIN\\username), bem como SID (Identificador de Segurança do Windows) do usuário.


**Categoria das informações**: informações do processo <br> **Descrição**: <br> o nome do processo que fez a solicitação ao aplicativo Web de destino (por exemplo: “iexplore.exe”)

**Categoria das informações**: informações do computador <br>**Descrição**:<br> o nome NetBIOS do computador no qual o agente está instalado.

**Categoria das informações**: informações de tráfego do aplicativo <br> **Descrição**: <br>

As seguintes informações de conexão:

- Endereços IP de origem (computador local) e destino e números de porta 

- O endereço IP público da organização por meio do qual a solicitação sai.

- A hora da solicitação

- O volume de tráfego enviado e recebido

- A versão do IP (4 ou 6)

- Somente para conexões TLS: o nome do host de destino da extensão de Indicação de Nome de Servidor ou o certificado do servidor.

As seguintes informações de HTTP:

- Método (GET, POST, etc.) 

- Protocolo (HTTP/1.1, etc.)

- Cadeia de caracteres de agente do usuário

- Nome do host

- URI de destino (exceto a cadeia de caracteres de consulta)

- Informações de tipo de conteúdo

- Informações de referência de URL (exceto a cadeia de caracteres de consulta)



> [AZURE.NOTE]As informações de HTTP acima são coletadas para todas as conexões não criptografadas. Para conexões de TLS, essas informações só são capturadas quando a configuração “Inspeção profunda” é ativada no portal. A configuração é “ON” por padrão. Para obter mais detalhes, consulte [Introdução ao Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 

 
### Como funciona o agente

A instalação do agente inclui dois componentes:

- Um componente de modo de usuário

- Um componente de driver de modo kernel (driver da Plataforma de filtragem do Windows)



Quando o agente é instalado, ele armazena um certificado confiável específico de computador no computador que ele usa para estabelecer uma conexão segura com o serviço Cloud App Discovery. <br> O agente recupera periodicamente a configuração de política do serviço Cloud App Discovery nessa conexão segura. <br> A política inclui informações sobre quais aplicativos de nuvem monitorar e se a atualização automática deve ser habilitada, entre outras coisas.

À medida que o tráfego da Web é enviado e recebido no computador, o Cloud App Discovery analisa o tráfego e extrai os metadados relevantes (consulte a tabela acima). <br> A cada minuto, o agente carrega os metadados coletados no serviço Cloud App Discovery, em um canal criptografado.

Para conexões criptografadas, uma etapa adicional é executada em um esforço para melhorar a precisão dos dados coletados.<br> Isso é conhecido como uma inspeção profunda. <br> O componente de driver intercepta o tráfego criptografado e insere a si mesmo no fluxo criptografado. Ele faz isso criando um certificado raiz autoassinado no computador, fazendo com que o aplicativo cliente confie no agente Cloud App Discovery. Esse certificado raiz autoassinado é marcado como não exportável e atribuído à ACL (Lista de Controle de Acesso) dos administradores. Destina-se a nunca deixar o computador no qual ele foi criado.


### Respeitando a privacidade do usuário

Nossa meta é oferecer aos administradores as ferramentas para definir o equilíbrio entre ótica detalhada de uso do aplicativo e a privacidade do usuário conforme apropriado para sua organização. Para esse fim, nós fornecemos os seguintes botões na página de configurações no Portal:

- **Coleta de Dados**: os administradores podem optar por especificar em quais aplicativos ou categorias de aplicativos desejam obter dados de descoberta.


- **Inspeção Profunda**: os administradores podem escolher especificar se o agente coleta tráfego HTTP para conexões SSL/TLS.



O agente de ponto de extremidade do Cloud App Discovery coleta apenas as informações descritas na tabela acima.



> [AZURE.NOTE]Para obter mais detalhes, consulte [Introdução ao Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 



## Enviando dados para o Cloud App Discovery

Depois que os metadados foram coletados pelo agente, são armazenados em cache no computador por até um minuto ou até que os dados em cache alcancem um tamanho de 5 MB. Em seguida, são compactado e enviados por uma conexão segura ao serviço Cloud App Discovery.

Se o agente não consegue se comunicar com o serviço Cloud App Discovery por algum motivo, os metadados coletados são armazenados em um cache de arquivo local que só pode ser acessado por usuários com privilégios no computador (como o grupo de administradores). <br> O agente tenta automaticamente reenviar os metadados em cache até que sejam recebidos com êxito pelo serviço Cloud App Discovery.



## Recebendo os dados ao final do serviço

Os agentes se autenticam no serviço Cloud App Discovery usando o certificado de autenticação de cliente específico do computador mencionado acima e encaminha dados por um canal criptografado. <br> Os processos de pipeline analíticos do serviço Cloud App Discovery processa metadados para cada cliente separadamente, particionando-o logicamente em todos os estágios do pipeline de análise. Os metadados analisados conduzem os vários relatórios no portal.

Os metadados não processados e analisados metadados são armazenados por até 180 dias. Além disso, os clientes podem escolher capturar metadados analisados em uma conta de armazenamento de blob do Azure de sua escolha. Isso é útil para análise offline de metadados, bem como retenção mais longa dos dados.

## Acessando os dados usando o portal do Azure

Em um esforço para proteger os metadados coletados seguros, por padrão somente os administradores globais do locatário têm acesso ao recurso Cloud App Discovery no portal do Azure. <br> No entanto, os administradores podem optar por delegar esse acesso a outros usuários ou grupos.



> [AZURE.NOTE]Para obter mais detalhes, consulte [Introdução ao Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 


Qualquer usuário que acesse dados no portal deve ser licenciado com uma licença AD Premium do Azure.



**Recursos adicionais**


* [Como descobrir aplicativos na nuvem não aprovados, usados em minha organização](active-directory-cloudappdiscovery-whatis.md) 

<!---HONumber=July15_HO5-->