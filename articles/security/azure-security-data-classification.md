<properties
   pageTitle="Classificação de dados para o Azure | Microsoft Azure"
   description="Este artigo fornece uma introdução aos conceitos básicos da classificação de dados e realça seu valor, especificamente no contexto da computação em nuvem e usando o Microsoft Azure"
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/19/2016"
   ms.author="yurid"/>

# Classificação de dados para o Azure

Este artigo fornece uma introdução aos conceitos básicos da classificação de dados e realça seu valor, especificamente no contexto da computação em nuvem e usando o Microsoft Azure.

## Conceitos básicos de classificação de dados

A classificação de dados bem-sucedida em uma organização exige amplo conhecimento das necessidades da organização e uma compreensão completa de onde residem seus ativos de dados.
 
Os dados existem em um destes três estados básicos:

- Em repouso 
- Em andamento 
- Em trânsito 
 
Todos os três estados exigem soluções técnicas exclusivas para classificação de dados, mas os princípios aplicados da classificação de dados devem ser os mesmos para cada um deles. Os dados classificados como confidenciais precisam permanecer confidenciais quando em repouso, em andamento e em trânsito.
 
Os dados também podem ser estruturados ou não estruturados. Os processos de classificação típicos para os dados estruturados encontrados em planilhas e bancos de dados são menos complexos e demorados de gerenciar do que aqueles para dados não estruturados, como documentos, código-fonte e email.

> [AZURE.TIP] para saber mais sobre recursos do Azure e práticas recomendadas para criptografia de dados, leia [Práticas recomendadas de criptografia de dados do Azure](azure-security-data-encryption-best-practices.md)

Em geral, as organizações têm mais dados não estruturados do que dados estruturados. Independentemente de os dados serem estruturados ou não estruturados, é importante que você gerencie a confidencialidade dos dados. Quando implementada corretamente, a classificação de dados ajuda a garantir que ativos de dados sigilosos ou confidenciais sejam gerenciados com maior supervisão do que ativos de dados que são considerados públicos ou para livre distribuição.

### Controle do acesso a dados 

Autenticação e autorização são frequentemente confundidos um com o outro e suas funções são mal compreendidas. Na realidade, são bastante diferentes, como mostrado na figura a seguir.

![Controle e acesso a dados](./media/azure-security-data-classification/azure-security-data-classification-fig1.png)

### Autenticação 

A autenticação normalmente consiste em pelo menos duas partes: um nome de usuário ou ID de usuário para identificar um usuário e um token, como uma senha, para confirmar que a credencial de nome de usuário é válida. O processo não fornece ao usuário autenticado acesso a itens ou serviços; ele verifica se o usuário é quem diz ser.

> [AZURE.TIP] [Azure Active Directory](./active-directory/active-directory-whatis.md) fornece serviços de identidade baseados em nuvem que lhe permitem autenticar e autorizar usuários.

### Autorização
 
A autorização é o processo de fornecer a um usuário autenticado a capacidade de acessar um aplicativo, um conjunto de dados, um arquivo de dados ou algum outro objeto. Para atribuir aos usuários autenticados os direitos de usar, modificar ou excluir itens que eles podem acessar, é necessária atenção à classificação de dados.

A autorização bem-sucedida requer a implementação de um mecanismo para validar as necessidades de usuários individuais para acessar arquivos e informações com base em uma combinação de considerações de função, política de segurança e política de risco. Por exemplo, dados de aplicativos de LOB (linha de negócios) específicos talvez não precisem ser acessados por todos os funcionários, e apenas um pequeno subconjunto de funcionários provavelmente precisará acessar os arquivos de RH (recursos humanos). Porém, para que as organizações controlem quem pode acessar dados, assim como quando e como, deve ser adotado um sistema eficaz para autenticar os usuários.

> [AZURE.TIP] no Microsoft Azure, utilize o RBAC (Controle de Acesso Baseado em Função) do Azure para conceder somente a quantidade de acesso de que os usuários precisam para realizar seus trabalhos. Leia [Use as atribuições de função para gerenciar o acesso aos recursos do Azure Active Directory](../active-directory/role-based-access-control-configure.md) para saber mais.

### Funções e responsabilidades na computação em nuvem 

Embora os provedores de nuvem possam ajudar a gerenciar riscos, os clientes precisam garantir que o gerenciamento e a imposição da classificação de dados sejam implementados corretamente para fornecer o nível apropriado de serviços de gerenciamento de dados.
 
As responsabilidades de classificação de dados variam com base no modelo de serviço de nuvem usado, conforme mostrado na figura a seguir. Os três modelos de serviço de nuvem primários são IaaS (infraestrutura como serviço), PaaS (plataforma como serviço) e SaaS (software como serviço). A implementação de mecanismos de classificação de dados também varia com base na dependência e nas expectativas do provedor de nuvem.

![Funções](./media/azure-security-data-classification/azure-security-data-classification-fig2.png)

Embora você seja responsável por classificar seus dados, os provedores de nuvem devem assumir compromissos por escrito sobre como protegerão e manterão a privacidade dos dados do cliente armazenados em sua nuvem.

- Os requisitos dos **provedores de IaaS** são limitados a garantir que o ambiente virtual possa acomodar recursos de classificação de dados e requisitos de conformidade do cliente. Os provedores de IaaS têm uma função menor na classificação de dados, pois só precisam garantir que os dados do cliente atendam aos requisitos de conformidade. No entanto, os provedores ainda devem garantir que seus ambientes virtuais atendam a requisitos de classificação de dados, além de proteger seus datacenters.
- As responsabilidades dos **provedores de PaaS** podem ser combinadas, pois a plataforma pode ser usada em uma abordagem em camadas para fornecer segurança a uma ferramenta de classificação. Os provedores de PaaS podem ser responsáveis pela autenticação e, possivelmente, por algumas regras de autorização e devem fornecer recursos de segurança e classificação de dados para sua camada de aplicativo. De forma semelhante aos provedores de IaaS, os provedores de PaaS precisam garantir que sua plataforma esteja em conformidade com quaisquer requisitos de classificação de dados relevantes.
- Os **provedores de SaaS** frequentemente são considerados como parte de uma cadeia de autorização e precisam garantir que os dados armazenados no aplicativo SaaS possam ser controlados por tipo de classificação. Os aplicativos SaaS podem ser usados para aplicativos de LOB e, por sua própria natureza, precisam fornecer os meios para autenticar e autorizar os dados que são usados e armazenados. 

## Processo de classificação 

Muitas organizações que compreendem a necessidade de classificação de dados e desejam implementá-la enfrentam um desafio básico: por onde começar?

Uma maneira simples e eficiente de implementar a classificação de dados é usar o modelo PLANEJAR, FAZER, VERIFICAR, AGIR do [MOF](https://technet.microsoft.com/solutionaccelerators/dd320379.aspx). A figura a seguir mostra as tarefas que são necessárias para implementar com êxito a classificação de dados nesse modelo.

1. **PLANEJAR**. Identifique os ativos de dados e um custodiante de dados para implantar o programa de classificação e desenvolva perfis de proteção. 
2. **FAZER**. Depois que as políticas de classificação de dados forem acordadas, implante o programa e implemente tecnologias de imposição conforme necessário para dados confidenciais.  
3. **VERIFICAR**. Verifique e valide os relatórios para assegurar que as ferramentas e os métodos que estão sendo usados estão abordando com eficiência as políticas de classificação. 
4. **ACT**. Examine o status do acesso a dados e examine arquivos e dados que exigem revisão usando uma metodologia de revisão e reclassificação para adotar alterações e lidar com novos riscos.  

![Planejar, Fazer, Verificar, Agir](./media/azure-security-data-classification/azure-security-data-classification-fig3.png)
 
###Selecione um modelo de terminologia que atenda às suas necessidades
 
Existem vários tipos de processos de classificação de dados, incluindo processos manuais, processos baseados em local que classificam os dados com base no local do usuário ou do sistema, processos baseados em aplicativos, como classificação específica de banco de dados, e processos automatizados usados por várias tecnologias, alguns dos quais são descritos na seção "Proteção de dados confidenciais", mais adiante neste artigo.
 
Este artigo introduz dois modelos de terminologia generalizada com base em modelos bastante usados e respeitados pelo setor. Esses modelos de terminologia, que fornecem três níveis de confidencialidade de classificação, são mostrados na tabela a seguir.

> [AZURE.NOTE] ao classificar um arquivo ou recurso que combina dados que, normalmente, seriam classificados em níveis diferentes, o nível mais alto de classificação presente deve estabelecer a classificação geral. Por exemplo, um arquivo que contém dados confidenciais e restritos deve ser classificado como restrito.

| **Confidencialidade** | **Modelo de terminologia 1** | **Modelo de terminologia 2** |
|--------------------|---------------------------|-------------------------|
| Alto | Confidencial | Restrito |
| Média | Apenas para uso interno | Sigiloso |
| Baixo | Público | Irrestrito |

#### Confidencial (restrito) 

As informações que são classificadas como confidenciais ou restritas incluem dados que podem ser catastróficos para uma ou mais pessoas e/ou organizações caso sejam comprometidos ou perdidos. Com frequência, essas informações são fornecidas apenas para "divulgação restrita àqueles diretamente interessados" e podem incluir:

- Dados pessoais, incluindo informações de identificação pessoal, como números de Previdência Social ou de identificação nacional, números de passaporte, números de cartão de crédito, números de carteira de motorista, registros médicos e números de ID de apólice de seguros de saúde.  
- Registros financeiros, incluindo números de contas financeiras, como números de verificação ou de contas de investimentos. 
- Material de negócios, como documentos ou dados que são exclusivos ou propriedade intelectual específica.  
- Dados legais, incluindo material potencialmente privilegiado de advogados. 
- Dados de autenticação, incluindo chaves de criptografia particular, pares de nome de usuário e senha ou outras sequências de identificação, como arquivos de chave biométrica particulares. 

Frequentemente, os dados classificados como confidenciais têm requisitos regulatórios e de conformidade para manipulação de dados.

#### Apenas para uso interno (sigiloso)
 
Informações que são classificadas com um nível de sigilo médio incluem arquivos e dados que não causariam um impacto grave para um indivíduo e/ou uma organização caso perdidos ou destruídos. Essas informações podem incluir:

- Emails, a maioria dos quais pode ser excluída ou distribuída sem causar uma crise (excluindo caixas de correio ou email de pessoas que são identificadas na classificação confidencial).  
- Documentos e arquivos que não têm dados confidenciais.
 
Em geral, essa classificação inclui tudo o que não é confidencial. Essa classificação pode incluir a maioria dos dados de negócios, pois a maioria dos arquivos que são gerenciados ou usadas no dia a dia pode ser classificada como sigilosa. Com exceção de dados que são tornados feitos públicos ou confidenciais, todos os dados em uma organização de negócios podem ser classificados como sigilosos por padrão.

#### Público (irrestrito)
 
As informações que são classificadas como públicas incluem dados e arquivos que não são essenciais para as necessidades de negócios ou as operações. Essa classificação também pode incluir dados deliberadamente lançados para o uso do público, como material de marketing ou comunicados à imprensa. Além disso, essa classificação pode incluir dados como mensagens de email de spam armazenadas por um serviço de email.

### Definir a propriedade dos dados
 
É importante estabelecer uma cadeia de custódia clara de propriedade para todos os ativos de dados. A tabela a seguir identifica diferentes funções de propriedade de dados em iniciativas de classificação de dados e seus respectivos direitos.

| **Função** | **Criar** | **Modificar/excluir** | **Delegar** | **Ler** | **Arquivar/restaurar** |
|-----------------|---------------|---------------------|---------------|-------------|-----------------------|
| Proprietário | X | X | X | X | X |
| Custodiante | | | X | | |
| Administrador | | | | | X |
| Usuário* | | X | | X | |
****Os usuários podem receber direitos adicionais, como editar e excluir, de um custodiante*

> [AZURE.NOTE] esta tabela não fornece uma lista completa de funções e direitos, mas apenas uma amostra representativa.

O **proprietário do ativo de dados** é o criador original dos dados, que pode delegar a propriedade e atribuir um custodiante. Quando um arquivo é criado, o proprietário deve ser capaz de atribuir uma classificação, o que significa que ele tem a responsabilidade de compreender o que precisa ser classificado como confidencial com base nas políticas da organização. Todos os dados do proprietário do ativo de dados podem ser classificados automaticamente apenas para uso interno (sigilosos), a menos que ele seja responsável pela propriedade ou criação de tipos de dados confidenciais (restritos). Com frequência, a função do proprietário será alterada depois que os dados forem classificados. Por exemplo, o proprietário pode criar um banco de dados de informações confidenciais e renunciar seus direitos para o custodiante dos dados.

> [AZURE.NOTE] os proprietários de ativos de dados muitas vezes usam uma combinação de serviços, dispositivos e mídia, alguns dos quais são particulares e outros dos quais pertencem à organização. Uma política organizacional clara pode ajudar a garantir que o uso de dispositivos, como laptops e dispositivos inteligentes, esteja de acordo com as diretrizes de classificação de dados.

O **custodiante de ativos de dados** é atribuído pelo proprietário do ativo (ou seu representante) para gerenciar o ativo de acordo com contratos com o proprietário do ativo ou de acordo com os requisitos de política aplicáveis. Idealmente, a função de custodiante pode ser implementada em um sistema automatizado. Um custodiante de ativo garante que os controles de acesso necessários sejam fornecidos e é responsável por gerenciar e proteger ativos delegados aos seus cuidados. As responsabilidades do custodiante de ativos podem incluir:

- Proteger o ativo de acordo com a orientação do proprietário do ativo ou de acordo com o proprietário do ativo 
- Garantir que as políticas de classificação sejam cumpridas 
- Informar os proprietários dos ativos de quaisquer alterações feitas em controles aprovados e/ou procedimentos de proteção antes que essas alterações entrem em vigor 
- Relatar ao proprietário do ativo as alterações ou a remoção das responsabilidades do custodiante do ativo 
- Um **administrador** representa um usuário que é responsável por garantir que a integridade seja mantida, mas ele não é um proprietário de ativos de dados, um custodiante nem um usuário. De fato, muitas funções de administrador fornecem serviços de gerenciamento de contêiner de dados sem ter acesso aos dados. A função de administrador inclui backup e restauração dos dados, manutenção de registros de ativos e escolha, aquisição e operação dos dispositivos e do armazenamento que contêm os ativos. 
- O usuário do ativo inclui qualquer pessoa que receba acesso a dados ou a um arquivo. A atribuição de acesso muitas vezes é delegada pelo proprietário ao custodiante do ativo.  

### Implementação
  
Considerações de gerenciamento se aplicam a todas as metodologias de classificação. Essas considerações precisam incluir detalhes sobre quem, o que, onde, quando e por que um ativo de dados deve ser usado, acessado, alterado ou excluído. Todo o gerenciamento do ativo deve ser feito com uma compreensão de como uma organização encara seus riscos, mas pode ser aplicada uma metodologia simple, conforme definido no processo de classificação de dados. Considerações adicionais para a classificação de dados incluem a introdução de novos aplicativos e ferramentas e a alteração de gerenciamento após a implementação de um método de classificação.

### Reclassificação
 
A reclassificação ou alteração do estado de classificação de um ativo de dados precisa ser feita quando um usuário ou sistema determina que o perfil de risco ou a importância do ativo de dados foi alterado. Essa iniciativa é importante para garantir que o status de classificação continue sendo válido e atual. A maior parte do conteúdo que não é classificado manualmente pode ser classificado automaticamente ou com base no uso por um custodiante de dados ou pelo proprietário dos dados.

### Reclassificação de dados manual
 
Idealmente, essa iniciativa garantiria que os detalhes de uma alteração sejam capturados e auditados. O motivo mais provável para a reclassificação manual seria por questões de diferenciação ou para registros mantidos em formato de papel ou um requisito para examinar dados originalmente classificados incorretamente. Como este documento considera a classificação de dados e a movimentação de dados para a nuvem, as iniciativas de reclassificação manual exigem atenção caso a caso, e uma revisão de gerenciamento de risco seria o ideal para atender aos requisitos de classificação. Em geral, essa iniciativa considera a política da empresa sobre o que precisa ser classificado, o estado de classificação padrão (todos os dados e arquivos sigilosos, mas não confidenciais) e exceções para dados de alto risco.

### Reclassificação de dados automática
 
A reclassificação de dados automática usa a mesma regra geral que a classificação manual. A exceção é que soluções automatizadas podem garantir que as regras sejam seguidas e aplicadas conforme necessário. A classificação de dados pode ser feita como parte de uma política de imposição de classificação de dados, que pode ser imposta quando dados são armazenados em uso e em trânsito usando a tecnologia de autorização.

- Baseada em aplicativo. O uso de determinados aplicativos por padrão define um nível de classificação. Por exemplo, dados de software de CRM (gerenciamento de relacionamento com o cliente), RH e ferramentas de gerenciamento de registro de saúde são confidenciais por padrão. 
- Baseada em local. O local dos dados pode ajudar a identificar a confidencialidade dos dados. Por exemplo, é mais provável que os dados armazenados por um departamento financeiro ou de RH sejam de natureza confidencial.  
 
### Retenção, recuperação e descarte de dados 

A recuperação e o descarte de dados, assim como a reclassificação de dados, são um aspecto essencial do gerenciamento de ativos de dados. Os princípios para recuperação e descarte de dados seriam definidos por uma política de retenção de dados e impostos da mesma maneira que a reclassificação de dados. Essa iniciativa seria executada pelas funções de custodiante e de administrador como uma tarefa de colaboração.

Se não houver uma política de retenção de dados, isso poderá significar a perda de dados ou o descumprimento de requisitos de descoberta legais e regulatórios. A maioria das organizações sem uma política de retenção de dados claramente definida tende a usar uma política de retenção padrão de "manter tudo". No entanto, essa política de retenção tem riscos adicionais em cenários de serviços de nuvem.

Por exemplo, uma política de retenção de dados para provedores de serviços de nuvem pode ser considerada como sendo "pela duração da assinatura" (enquanto o serviço for pago, os dados serão mantidos). Esse contrato de pagamento para retenção talvez não lide com políticas de retenção corporativas ou regulatórias. A definição de uma política para dados confidenciais pode garantir que os dados sejam armazenados e removidos com base nas práticas recomendadas. Além disso, uma política de arquivamento pode ser criada para formalizar um entendimento sobre quais dados devem ser descartados e quando.

A política de retenção de dados deve tratar dos requisitos regulatórios e de conformidade, bem como dos requisitos de retenção corporativa legais. Os dados confidenciais podem provocar questões quanto à duração da retenção e às exceções para dados que foram armazenados com um provedor. Essas questões são mais prováveis para dados que não foram classificados corretamente.

> [AZURE.TIP] saiba mais sobre as políticas de Retenção de Dados do Azure e muito mais lendo o [Contrato de Assinatura do Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/)

## Proteção de dados confidenciais
  
Depois que os dados são classificados, a localização e a implementação de maneiras de proteger dados confidenciais passam a ser uma parte integrante de qualquer estratégia de implantação de proteção de dados. A proteção de dados confidenciais requer atenção extra em relação a como os dados são armazenados e transmitidos em arquiteturas convencionais, bem como na nuvem.

Esta seção fornece informações básicas sobre algumas tecnologias que podem automatizar as iniciativas de imposição para ajudar a proteger dados que foram classificados como confidenciais.
 
Como mostra a figura a seguir, essas tecnologias podem ser implantadas como soluções locais ou baseadas na nuvem ou de modo híbrido, com algumas delas implantadas no local e outras na nuvem. (Algumas tecnologias, como criptografia e gerenciamento de direitos, também se estendem aos dispositivos do usuário.)

![Tecnologias](./media/azure-security-data-classification/azure-security-data-classification-fig4.png)

### Software de gerenciamento de direitos  

Uma solução para impedir a perda de dados é o software de gerenciamento de direitos. Diferentemente das abordagens que tentam interromper o fluxo de informações em pontos de saída em uma organização, o software de gerenciamento de direitos funciona em níveis profundos nas tecnologias de armazenamento de dados. Os documentos são criptografados, e o controle sobre quem pode descriptografá-los usa controles de acesso que são definidos em uma solução de controle de autenticação, como um serviço de diretório.

> [AZURE.TIP] Você pode usar o Azure RMS (Azure Rights Management) como a solução de proteção de informações para proteger os dados em diferentes cenários. Leia [O que é o Azure Rights Management?](https://docs.microsoft.com/rights-management/understand-explore/what-is-azure-rms) para saber mais sobre essa solução do Azure.

Alguns dos benefícios do software de gerenciamento de direitos incluem:

- Informações confidenciais protegidas. Os usuários podem proteger seus dados diretamente usando aplicativos habilitados para gerenciamento de direitos. Não são necessárias etapas adicionais. A criação de documentos, o envio de email e a publicação de dados oferecem uma experiência de proteção de dados consistente. 
- A proteção acompanha os dados. Os clientes permanecem no controle de quem tem acesso aos dados, seja na nuvem, na infraestrutura de TI existente ou na área de trabalho do usuário. As organizações podem optar por criptografar os dados e restringir o acesso de acordo com seus requisitos de negócios. 
- Políticas de proteção de informações padrão. Os administradores e usuários podem usar políticas padrão para muitos cenários comerciais comuns, como "Confidencial da Empresa – Somente Leitura" e "Não Encaminhar". Há suporte a um conjunto avançado de direitos de uso, como ler, copiar, imprimir, salvar, editar e encaminhar, para permitir flexibilidade na definição de direitos de uso personalizados. 

> [AZURE.TIP] você pode proteger dados no Armazenamento do Azure usando a [Criptografia do Serviço de Armazenamento do Azure](../storage/storage-service-encryption.md) para Dados em Repouso. Você também pode usar a [Azure Disk Encryption](azure-security-disk-encryption.md) para proteger dados contidos em discos virtuais usados para Máquinas Virtuais do Azure.

### Gateways de criptografia

Os gateways de criptografia operam em suas próprias camadas para fornecer serviços de criptografia redirecionando todo o acesso a dados baseados na nuvem. Essa abordagem não deve ser confundida com a de uma VPN (rede virtual privada). Os gateways de criptografia são projetados para fornecer uma camada transparente às soluções baseadas em nuvem.

Os gateways de criptografia podem fornecer um meio de gerenciar e proteger dados que foram classificados como confidenciais, criptografando os dados em trânsito, bem como os dados em repouso.
 
Os gateways de criptografia são colocados no fluxo de dados entre dispositivos de usuário e datacenters de aplicativos para fornecer serviços de criptografia/descriptografia. Essas soluções, como VPNs, são predominantemente locais. Elas foram projetadas para fornecer a terceiros o controle sobre chaves de criptografia, o que ajuda a reduzir o risco de colocar os dados e a chave de gerenciamento em um só provedor. Assim como a criptografia, essas soluções são projetadas para funcionar de forma perfeita e transparente entre os usuários e o serviço.

> [AZURE.TIP] você pode usar a Rota Expressa do Azure para estender suas redes locais para a nuvem da Microsoft por meio de uma conexão privada dedicada. Leia [Visão geral técnica da Rota Expressa](../expressroute/expressroute-introduction.md) para saber mais sobre essa funcionalidade. Outras opções para conectividade entre locais entre sua rede local e o [Azure é uma VPN site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

### Prevenção de perda de dados 
A perda de dados (às vezes chamada de vazamento de dados) é uma consideração importante, e a prevenção de perda de dados externos causada por indivíduos privilegiados mal-intencionadas e acidentais é fundamental para muitas organizações.
 
As tecnologias de DLP (prevenção de perda de dados) podem ajudar a garantir que soluções como serviços de email não transmitam dados que foram classificados como confidenciais. As organizações podem aproveitar recursos de DLP em produtos existentes para ajudar a evitar a perda de dados. Esses recursos usam políticas que podem ser facilmente criadas do zero ou usando um modelo fornecido pelo provedor de software.
 
As tecnologias de DLP podem executar uma análise profunda de conteúdo por meio de correspondências de palavras-chave, correspondências de dicionário, avaliação de expressões regulares e exame de outro conteúdo para detectar conteúdo que viola as políticas de DLP organizacionais. Por exemplo, a DLP pode ajudar a evitar a perda dos seguintes tipos de dados:

- Números de Previdência Social e de identificação nacional 
- Informações bancárias 
- Números de cartão de crédito  
- Endereços IP 

Algumas tecnologias de DLP também fornecem a capacidade de substituir a configuração de DLP (por exemplo, se uma organização precisar transmitir informações de números do seguro social para um processador de folha de pagamento). Além disso, é possível configurar a DLP para que os usuários sejam notificados antes mesmo que tentem enviar informações confidenciais que não devam ser transmitidas.

> [AZURE.TIP] você pode usar os recursos de DLP do Office 365 para proteger seus documentos. Leia [Controles de conformidade do Office 365: prevenção de perda de dados](https://blogs.office.com/2013/10/28/office-365-compliance-controls-data-loss-prevention/) para saber mais.

## Consulte também

- [Práticas Recomendadas de Criptografia de Dados do Azure](azure-security-data-encryption-best-practices.md)
- [Práticas recomendadas de Gerenciamento de Identidade do Azure e segurança de controle de acesso](azure-security-identity-management-best-practices.md)
- [Blog da equipe de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/)
- [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)

<!---HONumber=AcomDC_0525_2016-->