## <a name="what-is"></a>O que são Serviços Móveis?

Os Serviços Móveis são uma oferta de serviço do Azure desenvolvida para facilitar a criação de aplicativos móveis altamente funcionais usando o Azure. Os Serviços Móveis reúnem um conjunto de serviços do Azure que habilitam recursos de back-end para seus aplicativos. Os Serviços Móveis fornecem os seguintes recursos de back-end no Azure para oferecer suporte a seus aplicativos: 

+ Provisionamento e gerenciamento simples de tabelas para armazenar dados de aplicativo. 
+ Integração com os serviços de notificação para enviar notificações por push para seu aplicativo.
+ Integração com provedores de identidade conhecidos para autenticação.
+ Controle granular para autorizar o acesso às tabelas.
+ Lógica de negócios personalizada no servidor.
+ Integração com outros serviços de nuvem..
+ Suporta a capacidade de dimensionar uma instância de serviço móvel.
+ Monitoramento e registro de serviços.

Os Serviços Móveis fornecem uma biblioteca de cliente para cada plataforma suportada, o que torna ainda mais fácil de desenvolver aplicativos em sua plataforma que consome Serviços Móveis.

## <a name="concepts"> </a>Conceitos

A seguir, encontram-se importantes recursos e conceitos em Serviços Móveis:

<!--![1][1]-->

+ **Chave do aplicativo:** um valor exclusivo que é gerado por Serviços Móveis distribuídos com o seu aplicativo e apresentado em solicitações geradas pelo cliente. Embora seja útil para limitar o acesso ao serviço móvel de clientes aleatórios, esta chave não é segura e não deve ser usada para autenticar os usuários de seu aplicativo.    

+ **Token de autenticação:** o token de acesso que é gerado pelos Serviços Móveis depois que um usuário é autenticado.

+ **Esquema dinâmico:** a capacidade de Serviços Móveis para adicionar automaticamente uma coluna a uma tabela com base nos campos do objeto JSON enviados em uma solicitação de inserção ou atualização. O esquema dinâmico deve ser desabilitado quando seu aplicativo entrar em produção. 

+ **Provedor de identidade:** serviço externo, como o Facebook, Twitter, Google ou Conta da Microsoft, que é usado para autenticar os usuários de serviços móveis.

+ **Permissão:** o nível de autenticação mínima necessário para chamar uma operação de tabela.  

+ **Enviar notificação:** mensagem iniciada pelo serviço que é enviada para um usuário ou dispositivo registrado.

+ **Escala:** a capacidade de adicionar, com um custo adicional, mais poder de processamento, desempenho e armazenamento, conforme seu aplicativo se torna mais popular.

+ **Trabalho agendado:** código de script de servidor que é executado em uma programação predeterminada ou sob demanda.

+ **Um script de servidor:** código de JavaScript para lógica comercial personalizada que é armazenada no servidor. Esse código é registrado em uma operação de tabela (ler, inserir, atualizar, excluir) ou em um trabalho agendado. 

+ **Tabela:** os dados de usuário são armazenados em tabelas em um banco de dados SQL. Você pode criar essas tabelas no Portal de Gerenciamento.

+ **Operação da tabela:** uma solicitação de um HTTP recebida do cliente para ler, inserir, atualizar ou excluir dados da tabela.


<!-- Images. -->


  

