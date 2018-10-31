---
title: Dispositivo Coletor nas Migrações para Azure | Microsoft Docs
description: Fornece informações sobre o dispositivo Coletor nas Migrações para Azure.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/24/2018
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 006a246323e9f82ea9c9a6a2940ed624d7e44e13
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986762"
---
# <a name="about-the-collector-appliance"></a>Sobre o dispositivo Coletor

 Este artigo fornece informações sobre o Coletor de Migrações para Azure.

O Coletor de Migrações para Azure é um dispositivo leve que pode ser usado para descobrir um ambiente do vCenter local para fins de avaliação com o serviço [Migrações para Azure](migrate-overview.md), antes da migração para o Azure.  


## <a name="deploying-the-collector"></a>Implantando o Coletor

Você pode implantar o dispositivo Coletor usando um modelo OVF:

- Baixe o modelo OVF de um projeto das Migrações para Azure no portal do Azure. Importe o arquivo baixado para o vCenter Server para configurar a VM do dispositivo Coletor.
- No OVF, o VMware define uma VM com 4 núcleos, 8 GB de RAM e um disco de 80 GB. O sistema operacional é o Windows Server 2012 R2 (64 bits).
- Quando você executa o Coletor, são executadas várias verificações de pré-requisitos para garantir que o Coletor possa se conectar às Migrações para Azure.

- [Saiba mais](tutorial-assessment-vmware.md#create-the-collector-vm) sobre como criar o Coletor.


## <a name="collector-prerequisites"></a>Pré-requisitos do coletor

O Coletor precisa passar por algumas verificações de pré-requisitos para conferir se ele pode se conectar ao serviço de Migrações para Azure na Internet e fazer upload dos dados descobertos.

- **Verificar a conexão com a Internet**: o coletor pode conectar-se à Internet diretamente ou por meio de um proxy.
    - A verificação de pré-requisitos confere a conectividade com as [URLs obrigatórias e opcionais](#connect-to-urls).
    - Se houver uma conexão direta com a Internet, não será necessária nenhuma ação específica, além de conferir se o coletor pode acessar as URLs necessárias.
    - Se você estiver conectando por meio de um proxy, observe os [requisitos abaixo](#connect-via-a-proxy).
- **Verificar a sincronização de horário**: o Coletor deve ser sincronizado com o servidor de horário da Internet para garantir que as solicitações ao serviço sejam autenticadas.
    - A URL portal.azure.com deverá estar acessível no Coletor para que o horário possa ser validado.
    - Se o computador não estiver sincronizado, será necessário alterar o horário do relógio na VM do Coletor para corresponder ao horário atual. Para fazer isso, abra um prompt de administrador na VM e execute **w32tm /tz** para verificar o fuso horário. Execute **w32tm /resync** para sincronizar o horário.
- **Verificar se o serviço do coletor está em execução**: o serviço do Coletor de Migrações para Azure deve estar em execução na VM do Coletor.
    - Esse serviço é iniciado automaticamente quando o computador é inicializado.
    - Se o serviço não estiver em execução, inicie-o usando o Painel de Controle.
    - O serviço do coletor conecta-se ao vCenter Server, coleta os dados de desempenho e os metadados da VM e envia para o serviço de Migrações para Azure.
- **Verificar se o VMware PowerCLI 6.5 está instalado**: o módulo PowerShell VMware PowerCLI 6.5 precisa estar instalado na VM do Coletor, para que ele possa se comunicar com o vCenter Server.
    - Se o Coletor puder acessar as URLs necessárias para instalar o módulo, ele será instalado automaticamente durante a implantação do Coletor.
    - Se o Coletor não puder instalar o módulo durante a implantação, você precisará [instalá-lo manualmente](#install-vwware-powercli-module-manually).
- **Verifique a conexão com o vCenter Server**: o Coletor precisa ser capaz de conectar-se ao vCenter Server e consultar as VMs, seus metadados e os contadores de desempenho. [Verificar pré-requisitos](#connect-to-vcenter-server) para conectar-se.


### <a name="connect-to-the-internet-via-a-proxy"></a>Conectar-se à Internet por meio de um proxy

- Se o servidor proxy exigir autenticação, você poderá especificar o nome de usuário e a senha ao configurar o Coletor.
- O endereço IP/FQDN do servidor proxy deve ser especificado como *http://IPaddress* ou *http://FQDN*.
- Há suporte apenas para o proxy HTTP. Os servidores proxy baseados em HTTPS não têm suporte do Coletor.
- Se o servidor proxy for um proxy de interceptação, importe o certificado de proxy para a VM do Coletor.
    1. Na VM do coletor, acesse **Menu Iniciar** > **Gerenciar certificados do computador**.
    2. Na ferramenta Certificados, em **Certificados – Computador Local**, localize **Editores Confiáveis** > **Certificados**.

        ![Ferramenta de Certificados](./media/concepts-intercepting-proxy/certificates-tool.png)

    3. Copie o certificado de proxy para a VM do Coletor. Você precisará obtê-lo do seu administrador de rede.
    4. Clique duas vezes para abrir o certificado e, em seguida, clique em **Instalar Certificado**.
    5. No Assistente para Importação de Certificados > Localização do Repositório, escolha **Computador Local**.

    ![Local do repositório de certificados](./media/concepts-intercepting-proxy/certificate-store-location.png)

    6. Selecione **Colocar todos os certificados no repositório a seguir** > **Procurar** > **Editores Confiáveis**. Clique em **Concluir** para importar o certificado.

    ![Repositório de certificados](./media/concepts-intercepting-proxy/certificate-store.png)

    7. Verifique se o certificado foi importado conforme o esperado e se a verificação de pré-requisitos de conectividade com a Internet funciona conforme o esperado.




### <a name="connect-to-urls"></a>Conectar-se às URLs

A verificação de conectividade é validada conectando-se a uma lista de URLs.

**URL** | **Detalhes**  | **Verificação de pré-requisitos**
--- | --- | ---
*. portal.azure.com | Verifica a conectividade com o serviço do Azure e sincronização de horário. | Acesso à URL necessário.<br/><br/> A verificação de pré-requisitos falhará se não houver nenhuma conectividade.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Usado para baixar o módulo do PowerShell vCenter PowerCLI. | Acesso a URLs opcionais.<br/><br/> A verificação de pré-requisitos não falhará.<br/><br/> A instalação automática do módulo na VM do Coletor falhará. Você precisará instalar o módulo manualmente.


### <a name="install-vmware-powercli-module-manually"></a>Instalar manualmente o módulo VMware PowerCLI

1. Instale o módulo usando [estas etapas](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). Estas etapas descrevem a instalação online e offline.
2. Se a VM do Coletor estiver offline e instalar o módulo em um computador diferente com acesso à Internet, você precisará copiar os arquivos VMware.* desse computador para a VM do Coletor.
3. Após a instalação, você poderá reiniciar as verificações de pré-requisitos para confirmar se o PowerCLI está instalado.

### <a name="connect-to-vcenter-server"></a>Conectar-se ao vCenter Server

O Coletor conecta-se ao vCenter Server e consulta os metadados e os contadores de desempenho da VM. Aqui está o que você precisa para a conexão.

- Somente há suporte para o vCenter Server versões 5.5, 6.0 e 6.5.
- Você precisa de uma conta somente leitura com as permissões resumidas abaixo para descoberta. Somente data centers acessíveis com a conta podem ser acessados para descoberta.
- Por padrão você se conecta ao vCenter Server com um endereço IP ou FQDN. Se o vCenter Server escutar em uma porta diferente, conecte-se a ele usando o formato *IPAddress:Port_Number* ou *FQDN:Port_Number*.
- Para coletar dados de desempenho para armazenamento e rede, as configurações de estatísticas do vCenter Server precisam ser definidas como o nível três.
- Se o nível for inferior a três, a descoberta funcionará, mas os dados de desempenho não serão coletados. Alguns contadores poderão ser coletados, mas outros serão definidos como zero.
- Se os dados de desempenho de armazenamento e rede não forem coletados, as recomendações de tamanho de avaliação serão baseadas nos dados de desempenho de CPU e memória e nos dados de configuração de adaptadores de rede e disco.
- O Coletor deve ter uma linha de visão de rede para o vCenter Server.

#### <a name="account-permissions"></a>Permissões da conta

**Conta** | **Permissões**
--- | ---
Pelo menos uma conta de usuário somente leitura | Objeto de data center –> Propagar para o objeto filho, função = somente leitura   


## <a name="collector-communications"></a>Comunicações do Coletor

O Coletor comunica-se de acordo com o resumo no diagrama e na tabela a seguir.

![Diagrama de comunicação do coletor](./media/tutorial-assessment-vmware/portdiagram.PNG)


**O Coletor comunica-se com** | **Porta** | **Detalhes**
--- | --- | ---
Serviço Migrações para Azure | TCP 443 | O Coletor comunica-se com o serviço de Migrações para Azure por SSL 443.
vCenter Server | TCP 443 | O Coletor precisa ser capaz de comunicar-se com o vCenter Server.<br/><br/> Por padrão, ele se conecta ao vCenter na 443.<br/><br/> Se o vCenter Server escuta em uma porta diferente, essa porta precisa estar disponível como a porta de saída no Coletor.
RDP | TCP 3389 |


## <a name="securing-the-collector-appliance"></a>Protegendo o dispositivo Coletor

Recomendamos as seguintes etapas para proteger o dispositivo Coletor:

- Não compartilhe nem deixe as senhas de administrador com partes não autorizadas.
- Desligue o dispositivo quando não estiver em uso.
- Coloque o dispositivo em uma rede segura.
- Após a conclusão da migração, exclua a instância do dispositivo.
- Além disso, após a migração, exclua também os arquivos de backup de disco (VMDKs), pois os discos podem ter as credenciais do vCenter armazenadas em cache.

## <a name="os-license-in-the-collector-vm"></a>Licença de sistema operacional na VM do Coletor

O Coletor é fornecido com uma licença de avaliação do Windows Server 2012 R2 que é válida por 180 dias. Se o período de avaliação da VM do Coletor estiver expirando, baixe um novo OVA e crie outro dispositivo.

## <a name="updating-the-os-of-the-collector-vm"></a>Atualizando o sistema operacional da VM do Coletor

Embora o dispositivo Coletor tenha uma licença de avaliação de 180 dias, você precisará atualizar continuamente o sistema operacional no dispositivo para evitar o desligamento automático do dispositivo.

- Se o Coletor não for atualizado por 60 dias, ele iniciará o desligamento automático da computador.
- Se houver uma descoberta em execução, o computador não será desligado, mesmo após 60 dias. O computador será desligado depois que a descoberta for concluída.
- Se você já usou o Coletor por mais de 60 dias, é recomendado manter o computador sempre atualizado executando o Windows Update.

## <a name="upgrading-the-collector-appliance-version"></a>Atualizando a versão do dispositivo Coletor

É possível atualizar o Coletor para a última versão sem baixar o OVA novamente.

1. Baixe o [pacote de atualização mais recente listado](concepts-collector-upgrade.md)
2. Para garantir que o hotfix baixado é seguro, abra a janela de comando do Administrador e execute o comando a seguir para gerar o hash do arquivo ZIP. O hash gerado deve corresponder ao hash mencionado em relação à versão específica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (exemplo de uso C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Copie o arquivo zip para a máquina virtual do Coletor de Migrações para Azure (dispositivo do coletor).
4. Clique com o botão direito do mouse no arquivo zip e selecione Extrair Tudo.
5. Clique com o botão direito do mouse em Setup.ps1, selecione Executar com o PowerShell e siga as instruções na tela para instalar a atualização.


## <a name="discovery-methods"></a>Métodos de descoberta

Há dois métodos que o dispositivo Coletor pode usar para a descoberta, a descoberta avulsa ou a descoberta contínua.


### <a name="one-time-discovery"></a>Descoberta avulsa

O Coletor comunica-se uma única vez com o vCenter Server para reunir metadados sobre as VMs. Usando esse método:

- O dispositivo não fica conectado continuamente ao projeto de Migrações para Azure.
- As alterações no ambiente local não são refletidas nas Migrações para Azure após a conclusão da descoberta. Para refletir as alterações, você precisa descobrir o mesmo ambiente no mesmo projeto novamente.
- Para esse método de descoberta, você precisa definir as configurações de estatística no vCenter Server para o nível três.
- Depois de definir o nível para três, levará até um dia para que os contadores de desempenho sejam gerados. Portanto, recomendamos que você execute a descoberta após um dia.
- Durante a coleta de dados de desempenho para uma VM, o dispositivo baseia-se nos dados de desempenho históricos armazenados no vCenter Server. Ele coleta o histórico de desempenho do mês passado.
- O Azure Migrate coleta contadores médios (em vez de contador de pico) para cada métrica, o que pode resultar em subdimensionamento.

### <a name="continuous-discovery"></a>Descoberta contínua

O appliance Collector é continuamente conectado ao projeto de Migração do Azure e coleta continuamente os dados de desempenho das VMs.

- O Coletor cria perfis continuamente do ambiente local para coletar dados de utilização em tempo real a cada 20 segundos.
- Esse modelo não depende das configurações de estatísticas do vCenter Server para coletar dados de desempenho.
- O dispositivo acumula as amostras de 20 segundos e cria um único ponto de dados a cada 15 minutos.
- Para criar o ponto de dados, o dispositivo seleciona o valor de pico das amostras de 20 segundos e envia-o para o Azure.
- Você pode parar a criação de perfil contínua a qualquer momento do Coletor.

Observe que o appliance coleta apenas dados de desempenho continuamente, não detecta nenhuma alteração de configuração no ambiente local (ou seja, adição de VM, exclusão, adição de disco, etc.). Se houver uma alteração de configuração no ambiente local, você poderá fazer o seguinte para refletir as alterações no portal:

1. Adição de itens (VMs, discos, núcleos etc.): Para refletir essas alterações no portal do Azure, você pode interromper a descoberta do appliance e iniciá-lo novamente. Isso garantirá que as alterações sejam atualizadas no projeto de Migração do Azure.

2. Exclusão de VMs: devido à maneira como o appliance é projetado, a exclusão de VMs não é refletida, mesmo se você parar e iniciar a descoberta. Isso ocorre porque os dados das descobertas subsequentes são anexados a descobertas antigas e não substituídos. Nesse caso, você pode simplesmente ignorar a VM no portal, removendo-a do grupo e recalculando a avaliação.

> [!NOTE]
> A funcionalidade de descoberta contínua está na versão prévia. Recomendamos que você use esse método, pois esse método coleta dados de desempenho granulares e resulta em um dimensionamento correto e preciso.


## <a name="discovery-process"></a>Processo de descoberta

Depois que o dispositivo estiver configurado, você poderá executar a descoberta. Isso funciona assim:

- Você executa uma descoberta pelo escopo. Todas as VMs no caminho de inventário do vCenter especificado serão descobertas.
    - Você pode definir um escopo de cada vez.
    - O escopo pode incluir 1500 VMs ou menos.
    - O escopo pode ser um datacenter, uma pasta ou um host ESXi.
- Depois de conectar-se ao vCenter Server, conecte-se especificando um projeto de migração para a coleção.
- As VMs são descobertas e seus metadados e dados de desempenho são enviados para o Azure. Essas ações fazem parte de um trabalho de coleta.
    - O dispositivo Coletor recebe uma ID do Coletor específica que persiste em um determinado computador entre as descobertas.
    - Um trabalho de coleta em execução recebe uma ID de sessão específica. A ID é alterada para cada trabalho de coleta e pode ser usada para a solução de problemas.

### <a name="collected-metadata"></a>Metadados coletados

O dispositivo Coletor descobre os seguintes metadados estáticos das VMs:

- Nome de exibição da VM (no vCenter Server)
- Caminho de inventário da VM (o host ou a pasta no vCenter Server)
- Endereço IP
- Endereço MAC
- Sistema operacional
- Número de núcleos, discos, NICs
- Tamanho da memória, tamanhos de disco
- Contadores de desempenho da VM, do disco e da rede.

#### <a name="performance-counters"></a>contadores de desempenho

- **Descoberta avulsa**: quando os contadores são coletados para uma única descoberta. Observe o seguinte:

    - Pode levar até 15 minutos para a coleta e o envio dos metadados de configuração ao projeto.
    - Depois que os dados de configuração forem coletados, poderá demorar até uma hora para que os dados de desempenho fiquem disponíveis no portal.
    - Quando os metadados estiverem disponíveis no portal, a lista de VMs será exibida e você poderá começar a criar grupos para avaliação.
- **Descoberta contínua**: para a descoberta contínua, observe o seguinte:
    - Os dados de configuração da VM ficam disponíveis uma hora após o início da descoberta
    - Os dados de desempenho começam ficar disponíveis após duas horas.
    - Depois de iniciar a descoberta, aguarde pelo menos um dia para que o dispositivo crie o perfil do ambiente, antes de criar avaliações.

**Contador** | **Level** | **Nível por dispositivo** | **Impacto na avaliação**
--- | --- | --- | ---
cpu.usage.average | 1 | ND | Tamanho de VM recomendado e custo  
mem.usage.average | 1 | ND | Tamanho de VM recomendado e custo  
virtualDisk.read.average | 2 | 2 | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.write.average | 2 | 2  | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.numberReadAveraged.average | 1 | 3 |  Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.numberWriteAveraged.average | 1 | 3 |   Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
net.received.average | 2 | 3 |  Calcula o tamanho da VM                          |
net.transmitted.average | 2 | 3 | Calcula o tamanho da VM     

## <a name="next-steps"></a>Próximas etapas

[Configurar uma avaliação para VMs do VMware locais](tutorial-assessment-vmware.md)
