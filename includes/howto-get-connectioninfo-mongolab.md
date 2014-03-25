Quando você configurar um banco de dados de MongoLab, o MongoLab transmite uma conexão URI para Windows Azure no formato de cadeia de conexão padrão do MongoDB. Esse valor é usado para iniciar uma conexão por meio de sua escolha em MongoDB do MongoDB driver. Para obter mais informações sobre cadeias de conexão, consulte [conexões de](http://www.mongodb.org/display/DOCS/Connections) em mongodb.org.

**Esse URI contém seu nome de usuário do banco de dados e senha.  Tratá-lo como informações confidenciais e não a compartilhe.**

Você pode recuperar esse URI no Portal do Windows Azure usando as seguintes etapas:

1. Selecionar **Complementos**.  
![AddonsButton][button-addons]
1. Localize o serviço MongoLab na sua lista de complementos.  
![MongolabEntry][entry-mongolabaddon]
1. Clique no nome do seu complemento para chegar à página de complemento.
1. Clique em **Informações de Conexão**.  
![ConnectionInfoButton][button-connectioninfo] 
exibe o URI MongoLab: 
![ConnectionInfoScreen][screen-connectioninfo] 
1.  Clique no botão área de transferência para a direita do valor MONGOLAB_URI para copiar o valor total para a área de transferência.

[entry-mongolabaddon]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
[button-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
[button-addons]: ./media/howto-get-connectioninfo-mongolab/button-addons.png

