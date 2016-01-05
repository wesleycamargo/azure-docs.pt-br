<properties
	pageTitle="Considerações de privacidade e segurança no Cloud App Discovery | Microsoft Azure"
	description="Este tópico descreve as considerações de segurança e privacidade relacionadas ao Cloud App Discovery."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="markusvi"/>

# Considerações de privacidade e segurança no Cloud App Discovery

A Microsoft está comprometida em proteger a privacidade e segurança de seus dados, oferecendo softwares e serviços que ajudam a gerenciar a segurança de TI da sua organização. <br> Reconhecemos que quando você confia seus dados a outras pessoas, essa confiança exige investimentos rigorosos em engenharia de segurança e competências para dar suporte a isso. A Microsoft obedece a diretrizes rígidas de conformidade e segurança, desde práticas seguras do ciclo de vida de desenvolvimento do software até o funcionamento de um serviço. <br> Segurança e proteção de dados é uma prioridade principal da Microsoft.

Este tópico explica como os dados são coletados, processados e protegidos dentro do Cloud App Discovery do Active Directory do Azure




##Visão geral

Cloud App Discovery é um recurso do AD do Azure e é hospedado no Microsoft Azure. <br> O agente de ponto de extremidade do Cloud App Discovery é usado para coletar dados de descoberta de aplicativos de computadores gerenciados por TI. <br> Os dados coletados são enviados com segurança por um canal criptografado para o serviço Cloud App Discovery do AD do Azure. <br> Os dados de uma organização no Cloud App Discovery ficam visíveis no portal do Azure. <br>


<center>![Como o Cloud App Discovery funciona](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png) </center> <br>


As seções a seguir seguem o fluxo de informações e descrevem como ele é protegido à medida que avança de sua organização para o serviço Cloud App Discovery e, finalmente, para o portal do Cloud App Discovery.



## Coletando dados de sua organização

Para usar o recurso Cloud App Discovery do Active Directory do Azure para obter insights sobre os aplicativos usados pelos funcionários da sua organização, você precisa primeiro implantar o agente de ponto de extremidade do Cloud App Discovery do AD do Azure nos computadores de sua organização.

Os administradores do locatário do Active Directory do Azure (ou seu representante) podem baixar o pacote de instalação do agente no portal do Azure. O agente pode ser instalado manualmente em vários computadores da organização usando a política de grupo ou do SCCM.

Para obter mais instruções sobre opções de implantação, consulte [Guia de implantação da política de grupo do Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx). <br>

### Dados coletados pelo agente

As informações descritas na lista a seguir são coletadas pelo agente quando é feita uma conexão ao aplicativo Web. As informações são coletadas apenas para os aplicativos que o administrador configurou para descoberta. <br> Você pode editar a lista de aplicativos na nuvem que o agente monitora por meio da folha do Cloud App Discovery no portal do [Microsoft Azure](https://portal.azure.com), em **Configurações**->**Coleta de Dados**->**Lista de Coleção de Aplicativos**. Para obter mais detalhes, consulte [Introdução ao Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) <br> **Categoria das informações**: informações do usuário <br> **Descrição**: <br> o nome de usuário do Windows do processo que fez uma solicitação ao aplicativo Web de destino (por exemplo: DOMÍNIO\\nomedeusuário), bem como o SID (Identificador de Segurança) do Windows do usuário.


**Categoria das informações**: informações do processo <br> **Descrição**: <br> o nome do processo que fez a solicitação ao aplicativo Web de destino (por exemplo: "iexplore.exe")

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



> [AZURE.NOTE]As informações de HTTP acima são coletadas para todas as conexões não criptografadas. Para conexões de TLS, essas informações só são capturadas quando a configuração “Inspeção profunda” é ativada no portal. A configuração é “ON” por padrão. Para obter mais detalhes, consulte abaixo, e [Introdução ao Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)


<br><br>
### Como funciona o agente

A instalação do agente inclui dois componentes:

- Um componente de modo de usuário

- Um componente de driver no modo kernel (driver da Plataforma para Filtros do Windows)



Quando o agente é instalado, ele armazena um certificado confiável específico de computador no computador que ele usa para estabelecer uma conexão segura com o serviço Cloud App Discovery. <br> O agente recupera periodicamente a configuração de política do serviço Cloud App Discovery nessa conexão segura. <br> A política inclui informações sobre quais aplicativos de nuvem monitorar e se a atualização automática deve ser habilitada, entre outras coisas.

À medida que o tráfego da Web é enviado e recebido no computador do Internet Explorer e Chrome, o Cloud App Discovery analisa o tráfego e extrai os metadados relevantes (consulte a seção acima, **Dados coletados pelo agente**). <br> A cada minuto, o agente carrega os metadados coletados no serviço Cloud App Discovery por um canal criptografado.

O componente de driver intercepta o tráfego criptografado e insere a si mesmo no fluxo criptografado. Mais detalhes na seção abaixo, **Interceptando dados de conexões criptografadas (inspeção profunda)**.


### Respeitando a privacidade do usuário

Nossa meta é oferecer aos administradores as ferramentas para definir o equilíbrio entre ótica detalhada de uso do aplicativo e a privacidade do usuário conforme apropriado para sua organização. Para esse fim, nós fornecemos os seguintes botões na página de configurações no Portal:

- **Coleta de Dados**: os administradores podem optar por especificar em quais aplicativos ou categorias de aplicativos desejam obter dados de descoberta.

- **Inspeção Profunda**: os administradores podem optar por especificar se o agente coleta tráfego HTTP para conexões SSL/TLS (ou **'Inspeção Profunda'**). Veremos mais sobre isso na próxima seção.

- **Opções de Consentimento**: os administradores podem usar o portal do Cloud App Discovery para optar por notificar os usuários da coleta de dados pelo agente e se será necessário o consentimento do usuário para que o agente comece a coletar dados de usuário.

O Cloud App Discovery Endpoint Agent coleta apenas as informações descritas na seção acima, **Dados coletados pelo agente**.


### Interceptando dados de conexões criptografadas (inspeção profunda)
Como mencionado anteriormente, os administradores podem configurar o agente para monitorar dados de conexões criptografadas ("inspeção profunda"). O TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) é um dos protocolos mais comuns em uso na Internet hoje em dia. Ao criptografar a comunicação com o TLS, um cliente pode estabelecer um canal de comunicação seguro e privado com um servidor Web; o TLS fornece proteção essencial para transmitir credenciais de autenticação e evitar a divulgação de informações confidenciais.

Enquanto o canal criptografado seguro ponta a ponta fornecido pelo TLS permite importantes proteções de segurança e privacidade, o protocolo frequentemente é utilizado para fins mal-intencionados ou perigosos. Tanto que, de fato, esse TLS frequentemente é chamado de "protocolo de bypass de firewall universal". A raiz do problema é que a maioria dos firewalls não consegue inspecionar a comunicação TLS porque os dados da camada de aplicativo são criptografados com SSL. Sabendo disso, os invasores costumam aproveitar o TLS para transmitir ataques maliciosos ao usuário, certos de que mesmo os firewalls de camada de aplicativo mais inteligentes são completamente cegos ao TLS e precisam simplesmente retransmitir a comunicação TLS entre hosts. Os usuários finais frequentemente aproveitam o TLS para ignorar os controles de acesso impostos pelos firewalls corporativos e servidores proxy, usando-o para se conectar a proxies públicos e encapsular protocolos não TLS por meio do firewall que, de alguma forma, podem ser bloqueados pela política.

A inspeção profunda permite que o agente do Cloud App Discovery atue como um intermediário confiável. Quando é feita uma solicitação de cliente para acessar um recurso protegido por HTTPS, o driver do Endpoint Agent intercepta a conexão e estabelece uma nova com o servidor de destino a fim de recuperar seu certificado SSL em nome do cliente. O agente então confirma se o certificado pode ser confiável (verificando se ele não foi revogado e executando outras verificações pertinentes) e, se for aprovado, o Endpoint Agent copia as informações do certificado de servidor e cria o próprio certificado de servidor (conhecido como um certificado de interceptação) usando essas informações. O certificado de interceptação é assinado dinamicamente pelo Endpoint Agent com um certificado raiz, que é instalado no repositório de certificados confiáveis do Windows. Esse certificado raiz autoassinado é marcado como não exportável e atribuído à ACL (Lista de Controle de Acesso) dos administradores. Destina-se a nunca deixar o computador no qual ele foi criado. Quando o aplicativo cliente do usuário final recebe o certificado de interceptação, ele pode confiar nele porque este pode validar com êxito a cadeia de certificados por todo o caminho até o certificado raiz. Esse processo, na maioria das vezes, é transparente para o usuário final com algumas advertências, conforme descrito abaixo.

Ao habilitar a inspeção profunda, o Cloud App Discovery Endpoint Agent pode descriptografar e inspecionar comunicações criptografadas pelo TLS, permitindo que o serviço reduza o ruído e forneça informações sobre o uso dos aplicativos de nuvem criptografados.

#### Uma advertência
Antes de ativar a inspeção profunda, é altamente recomendável que você comunique suas intenções aos departamento jurídico e de RH e obtenha seu consentimento. Inspecionar a comunicação criptografada privada do usuário final pode ser um assunto confidencial, por motivos óbvios. Antes de uma implementação de produção da inspeção profunda, certifique-se de que as políticas de segurança e uso aceitável da empresa foram atualizadas para indicar que a comunicação criptografada será ser inspecionada. A notificação dos usuários e a isenção de sites considerados confidenciais (como sites de bancos e de atendimento médico) também podem ser necessárias se você configurar o Cloud App Discovery para monitorá-los. Conforme mencionado acima, os administradores podem usar o portal do Cloud App Discovery para optar por notificar os usuários da coleta de dados pelo agente e se será necessário o consentimento do usuário para que o agente comece a coletar dados de usuário.

### Problemas conhecidos e desvantagens
Há alguns casos em que a interceptação do TLS pode afetar a experiência do usuário final:

- Certificados de EV (Validação Estendida) renderizam a barra de endereços do navegador da Web em verde para indicar visualmente que você está visitando um site da Web confiável. A inspeção TLS não pode duplicar a EV no certificado que emite ao cliente, de modo que os sites da Web que usam certificados de EV funcionarão normalmente, mas a barra de endereços não será exibida em verde.  

- A anexação da chave pública (também conhecida como anexação de certificado) foi desenvolvida para ajudar a proteger os usuários contra ataques man-in-the-middle e autoridades de certificado invasoras. Quando o certificado raiz para um site anexado não corresponde a uma das autoridades de certificação conhecidas, o navegador rejeita a conexão com um erro. Como a intercepção de TLS é, na verdade, um intermediário, essas conexões falharão.

- Se os usuários clicarem no ícone de cadeado na barra de endereço do navegador para inspecionar as informações do site, eles não verão a cadeia que termina na autoridade de certificação usada para assinar o certificado do site, mas sim uma cadeia de certificados que termina com o repositório de certificados confiáveis do Windows.

Para reduzir as ocorrências desses problemas, acompanhamos os serviços de nuvem e os aplicativos cliente conhecidos para usar anexação de validação estendida ou chave pública e instruir o Endpoint Agent a evitar a interceptação de conexões afetadas. No entanto, mesmo nesses casos, ainda receberemos relatórios do uso desses aplicativos de nuvem e do volume de dados que está sendo transferido, mas uma vez que eles não são profundamente inspecionados, nenhum detalhe sobre como os aplicativos foram usados estará disponível.


## Enviando dados para o Cloud App Discovery

Depois que os metadados foram coletados pelo agente, são armazenados em cache no computador por até um minuto ou até que os dados em cache alcancem um tamanho de 5 MB. Em seguida, são compactado e enviados por uma conexão segura ao serviço Cloud App Discovery.

Se o agente não consegue se comunicar com o serviço Cloud App Discovery por algum motivo, os metadados coletados são armazenados em um cache de arquivo local que só pode ser acessado por usuários com privilégios no computador (como o grupo de administradores). <br> O agente tenta automaticamente reenviar os metadados em cache até que sejam recebidos com êxito pelo serviço Cloud App Discovery.



## Recebendo os dados ao final do serviço

Os agentes se autenticam no serviço Cloud App Discovery usando o certificado de autenticação de cliente específico do computador mencionado acima e encaminha dados por um canal criptografado. <br> Os processos de pipeline analítico do serviço Cloud App Discovery processam metadados para cada cliente separadamente, particionando-os logicamente em todos os estágios do pipeline analítico Os metadados analisados conduzem os vários relatórios no portal.

Os metadados não processados e analisados metadados são armazenados por até 180 dias. Além disso, os clientes podem escolher capturar metadados analisados em uma conta de armazenamento de blob do Azure de sua escolha. Isso é útil para análise offline de metadados, bem como retenção mais longa dos dados.

## Acessando os dados usando o portal do Azure

Em um esforço para proteger os metadados coletados seguros, por padrão somente os administradores globais do locatário têm acesso ao recurso Cloud App Discovery no portal do Azure. <br> No entanto, os administradores podem optar por delegar esse acesso a outros usuários ou grupos.



> [AZURE.NOTE]Para obter mais detalhes, consulte [Introdução ao Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

<br> Qualquer usuário que acesse dados no portal deve ser licenciado com uma licença do Azure AD Premium.



**Recursos adicionais**


* [Como descobrir aplicativos na nuvem não aprovados, usados em minha organização](active-directory-cloudappdiscovery-whatis.md)

<!---HONumber=AcomDC_1203_2015-->