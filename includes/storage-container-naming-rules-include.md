Todos os blobs no armazenamento do Azure devem residir em um contêiner. O contêiner faz parte do nome do blob. Por exemplo, `mycontainer` é o nome do contêiner nesses URIs do blob de exemplo:

	https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
	https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
 
> [AZURE.IMPORTANT]Observe que o nome de um contêiner deve sempre estar em minúsculas. Se você incluir uma letra maiúscula em um nome de contêiner ou de alguma forma violar as regras de nomenclatura do contêiner, você receberá um erro 400 (solicitação incorreta). Para conhecer as regras sobre como nomear contêineres, consulte [Nomenclatura e referência de contêineres, blobs e metadados](https://msdn.microsoft.com/library/azure/dd135715.aspx).

<!---HONumber=July15_HO2-->