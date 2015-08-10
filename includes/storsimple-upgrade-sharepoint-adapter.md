### Atualizar o SharePoint 2010 para SharePoint 2013 e instalar o Adaptador StorSimple para SharePoint

>[AZURE.IMPORTANT]Todos os arquivos que foram movidos anteriormente para o armazenamento externo via RBS não estarão disponíveis até que a atualização seja concluída e o recurso RBS esteja habilitado novamente. Para limitar o impacto do usuário, execute qualquer atualização ou reinstalação durante uma janela de manutenção planejada.

[AZURE.INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]
 
#### Para atualizar o SharePoint 2010 para SharePoint 2013 e instalar o adaptador

1. No farm do SharePoint 2010, observe o caminho do armazenamento de BLOB para os BLOBs externalizados e os bancos de dados de conteúdo para os quais o RBS está habilitado. 

2. Instale e configure o novo farm do SharePoint 2013.

3. Mova os bancos de dados, aplicativos e coleções de site do farm do SharePoint 2010 para o novo farm do SharePoint 2013. Para obter instruções, acesse a Visão geral do processo de atualização para o SharePoint 2013.

4. Instale o Adaptador StorSimple para SharePoint no novo farm. Para ver os procedimentos, acesse [Instalar o Adaptador StorSimple para SharePoint](#install-the-storsimple-adapter-for-sharepoint).

5. Usando as informações que você anotou na etapa 1, habilite o RBS para o mesmo conjunto de bancos de dados de conteúdo e forneça o mesmo caminho de armazenamento de BLOB que foi usado na instalação do SharePoint 2010. Para ver os procedimentos, acesse [Configurar RBS](#configure-rbs). Depois de concluir essa etapa, os arquivos externalizados anteriormente devem poder ser acessados no novo farm.

### Atualizar o Adaptador StorSimple para SharePoint

>[AZURE.IMPORTANT]Você deve agendar essa atualização para ocorrer durante uma janela de manutenção planejada pelos seguintes motivos:
>
>- O conteúdo externalizado anteriormente não estará disponível até que o adaptador seja reinstalado.
>
>- Qualquer conteúdo carregado no site depois da desinstalação da versão anterior do Adaptador StorSimple para SharePoint, mas antes da instalação da nova versão, será armazenado no banco de dados de conteúdo. Você precisará mover esse conteúdo para o dispositivo StorSimple depois de instalar o novo adaptador.


#### Para atualizar o Adaptador StorSimple para SharePoint 

1. Desinstale a versão anterior do Adaptador StorSimple para SharePoint.

    >[AZURE.NOTE]Isso desabilitará automaticamente o RBS nos bancos de dados de conteúdo. No entanto, os BLOBs existentes permanecerão no dispositivo StorSimple. Como o RBS está desabilitado e os BLOBs não migraram de volta para os bancos de dados de conteúdo, todas as solicitações para esses BLOBs falharão.
 
2. Instale o novo Adaptador StorSimple para SharePoint. O novo adaptador reconhecerá automaticamente os bancos de dados de conteúdo habilitados ou desabilitados para RBS anteriormente e usará as configurações anteriores.

<!---HONumber=July15_HO5-->