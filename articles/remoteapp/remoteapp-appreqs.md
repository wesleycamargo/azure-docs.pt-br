
---
title: Requisitos de aplicativo para o RemoteApp do Azure | Microsoft Docs
description: Saiba mais sobre os requisitos para aplicativos que você deseja usar no RemoteApp do Azure
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Requisitos de aplicativo
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

O RemoteApp do Azure dá suporte a streaming de aplicativos baseados em Windows 32 bits ou 64 bits de uma imagem do Windows Server 2012 R2. A maioria dos aplicativos baseados em Windows de 32 bits ou 64 bits é executada "como está" no ambiente RemoteApp do Azure (Serviços de Área de Trabalho Remota, anteriormente conhecido como Serviços de Terminal). No entanto, há uma diferença entre executar e executar bem – alguns aplicativos funcionam corretamente e executam bem, enquanto outros, não. As informações a seguir fornecem diretrizes para o desenvolvimento de aplicativos em um ambiente de Serviços de Área de Trabalho Remota e os testes para garantir a compatibilidade.

Dica: estamos trabalhando na criação de alguns exemplos práticos dos aplicativos para você. Você verá os novos tópicos que abordam o uso do Microsoft Access, do QuickBooks e do App-V no RemoteApp.

## Requisitos
Esses três requisitos, se seguidos, ajudam o aplicativo a ser executado corretamente no RemoteApp:

1. Aplicativos que atenderem a todos os [Requisitos de certificação para aplicativos de área de trabalho do Windows](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) e cumprirem as [Diretrizes de programação dos Serviços de Área de Trabalho Remota](https://msdn.microsoft.com/library/aa383490.aspx) terão total compatibilidade com o RemoteApp.
2. Os aplicativos nunca devem armazenar dados localmente na imagem ou em instâncias do RemoteApp que possam ser perdidas. Depois de criar uma coleção do RemoteApp, as instâncias são clonadas e sem monitoração de estado, devendo conter apenas aplicativos. Armazenar dados em uma fonte externa ou no perfil do usuário.
3. Imagens personalizadas nunca devem conter dados que possam ser perdidos.

## Testando seus aplicativos
Siga estas etapas para testar aplicativos:

1. Instale o Windows Server 2012 R2 e o seu aplicativo
2. Habilite a Área de Trabalho Remota
3. Crie duas contas de usuário, UserA e UserB, adicionando ambas ao grupo de segurança da Área de Trabalho Remota.
4. Verifique a compatibilidade multissessões estabelecendo duas sessões RDS simultâneas para o PC durante a inicialização do aplicativo.
5. Validar o comportamento do aplicativo

## Diretrizes para desenvolvimento de aplicativos
Siga as diretrizes apresentadas para desenvolver aplicativos para o RemoteApp.

### Vários usuários
* Instalar um [aplicativo para um único usuário ](https://msdn.microsoft.com/library/aa380661.aspx)pode criar problemas em um ambiente multiusuário.
* Aplicativos devem [armazenar informações específicas do usuário](https://msdn.microsoft.com/library/aa383452.aspx) em locais específicos do usuário, separadamente de informações globais que se aplicam a todos os usuários.
* O RemoteApp usa vários [namespaces para objetos kernel](https://msdn.microsoft.com/library/aa382954.aspx); um namespace global é usado principalmente por serviços de aplicativos cliente/servidor.
* Não é seguro pressupor que o nome do computador ou o [endereço IP](https://msdn.microsoft.com/library/aa382942.aspx) atribuído ao computador está associados um único usuário, pois vários usuários podem ser conectados simultaneamente a um servidor de Host da Sessão da Área de Trabalho Remota (Host da Sessão RD).

### Desempenho
* Desabilite [efeitos gráficos](https://msdn.microsoft.com/library/aa380822.aspx) antes de adicionar seu aplicativo para o RemoteApp.
* Para maximizar a disponibilidade de CPU para todos os usuários, desabilite [tarefas em segundo plano](https://msdn.microsoft.com/library/aa380665.aspx) ou crie tarefas em segundo plano eficientes que não façam uso intenso de recursos.
* Você deve ajustar e balancear o [uso de thread](https://msdn.microsoft.com/library/aa383520.aspx) do aplicativo para um ambiente multiusuário com vários processadores.
* Para otimizar o desempenho, é recomendável que os aplicativos [detectem](https://msdn.microsoft.com/library/aa380798.aspx) se estão sendo executados em uma sessão de cliente.

<!---HONumber=AcomDC_0817_2016-->