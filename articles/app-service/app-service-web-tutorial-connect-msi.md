---
title: Proteger a conexão do Banco de Dados SQL do Azure no Serviço de Aplicativo usando a identidade de serviço gerenciada | Microsoft Docs
description: Saiba como proteger a conectividade do banco de dados usando uma identidade de serviço gerenciada e também como aplicá-la a outros serviços do Azure.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/17/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 173588c0200666c52f3ac0a5d2e70d667cfe3294
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445554"
---
# <a name="tutorial-secure-sql-database-connection-with-managed-service-identity"></a>Tutorial: proteger a conexão do Banco de Dados SQL com a identidade de serviço gerenciada

O [Serviço de Aplicativo](app-service-web-overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches no Azure. Ele também fornece uma [identidade de serviço gerenciada](app-service-managed-service-identity.md) para seu aplicativo, que é uma solução turnkey para proteger o acesso ao [Banco de Dados SQL do Azure](/azure/sql-database/) e a outros serviços do Azure. As identidades de serviço gerenciadas no Serviço de Aplicativo tornam seu aplicativo mais seguro, eliminando os segredos do aplicativo, como as credenciais nas cadeias de conexão. Neste tutorial, você adicionará a identidade de serviço gerenciada ao aplicativo Web ASP.NET de exemplo criado no [Tutorial: criar um aplicativo ASP.NET no Azure com o Banco de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md). Quando você terminar, seu aplicativo de exemplo se conectará ao Banco de Dados SQL com segurança sem a necessidade de nomes de usuário e senhas.

Você aprenderá a:

> [!div class="checklist"]
> * Habilitar a identidade de serviço gerenciada
> * Conceder ao Banco de Dados SQL acesso à identidade do serviço
> * Configurar o código do aplicativo para autenticar com o Banco de Dados SQL usando a autenticação do Azure Active Directory
> * Conceder privilégios mínimos para a identidade de serviço no Banco de Dados SQL

> [!NOTE]
> A autenticação do Azure Active Directory é _diferente_ da [autenticação Integrada do Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) no Active Directory (AD DS) local. O AD DS e o Azure Active Directory usam protocolos de autenticação completamente diferentes. Para saber mais, veja [A diferença entre o Windows Server AD DS e o Azure AD](../active-directory/fundamentals/understand-azure-identity-solutions.md#the-difference-between-windows-server-ad-ds-and-azure-ad).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo continua de onde parou no [Tutorial: criar um aplicativo ASP.NET no Azure com o Banco de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md). Se você ainda não fez isso, siga o tutorial primeiro. Como alternativa, você pode adaptar as etapas ao seu próprio aplicativo ASP.NET com o Banco de Dados SQL.

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-service-identity"></a>Habilitar a identidade de serviço gerenciada

Para habilitar uma identidade de serviço para o aplicativo do Azure, use o comando [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) no Cloud Shell. No comando a seguir, substitua *\<app name>*.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

Confira um exemplo da saída depois que a identidade é criada no Azure Active Directory:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

Você usará o valor de `principalId` na próxima etapa. Se você quiser ver os detalhes da nova identidade no Azure Active Directory, execute o seguinte comando opcional com o valor de `principalId`:

```azurecli-interactive
az ad sp show --id <principalid>
```

## <a name="grant-database-access-to-identity"></a>Conceder acesso de banco de dados à identidade

Em seguida, conceda acesso de banco de dados para a identidade do serviço do aplicativo usando o comando [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin_create) no Cloud Shell. No comando a seguir, substitua *\<server_name>* e <principalid_from_last_step>. Digite um nome de administrador para *\<admin_user>*.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

A identidade de serviço gerenciada agora tem acesso ao seu servidor do Banco de Dados SQL do Azure.

## <a name="modify-connection-string"></a>Modificar cadeia de conexão

Modifique a conexão que você definiu anteriormente para seu aplicativo usando o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell. No comando a seguir, substitua *\<app name>* pelo nome do seu aplicativo e substitua *\<server_name>* e *\<db_name>*  com aqueles do Banco de Dados SQL.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>Modificar código ASP.NET

Em seu projeto **DotNetAppSqlDb** no Visual Studio, abra _packages.config_ e adicione a linha a seguir na lista de pacotes.

```xml
<package id="Microsoft.Azure.Services.AppAuthentication" version="1.1.0-preview" targetFramework="net461" />
```

Abra _Models\MyDatabaseContext.cs_ e adicione as seguintes instruções `using` na parte superior do arquivo:

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

Na classe `MyDatabaseContext`, adicione o seguinte construtor:

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

Este construtor configura um objeto SqlConnection personalizado para usar um token de acesso para o Banco de Dados SQL do Azure a partir do Serviço de Aplicativo. Com o token de acesso, o aplicativo do Serviço de Aplicativo é autenticado com o Banco de Dados SQL do Azure com sua identidade de serviço gerenciada. Para saber mais, confira [Obtendo tokens para recursos do Azure](app-service-managed-service-identity.md#obtaining-tokens-for-azure-resources). A instrução `if` permite que você continue a testar seu aplicativo localmente com LocalDB.

> [!NOTE]
> `SqlConnection.AccessToken` tem suporte atualmente apenas no .NET Framework 4.6 e versões posteriores, não no [.NET Core](https://www.microsoft.com/net/learn/get-started/windows).
>

Para usar o novo construtor, abra `Controllers\TodosController.cs` e localize a linha `private MyDatabaseContext db = new MyDatabaseContext();`. O código existente usa o controlador `MyDatabaseContext` padrão para criar um banco de dados usando a cadeia de conexão padrão, que tinha o nome de usuário e a senha em texto não criptografado antes de [você alterar isso](#modify-connection-string).

Substitua a linha inteira pelo código a seguir:

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new SqlConnection());
```

### <a name="publish-your-changes"></a>Publicar suas alterações

Agora, só falta publicar suas alterações no Azure.

No **Gerenciador de Soluções**, clique com botão direito no projeto **DotNetAppSqlD** e selecione **Publicar**.

![Publicar no Gerenciador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na página de publicação, clique em **Publicar**. Quando a nova página da Web mostra a lista de tarefas pendentes, seu aplicativo se conecta ao banco de dados usando a identidade de serviço gerenciada.

![Aplicativo Web do Azure após Migração do Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Agora, você pode editar a lista de tarefas pendentes como fazia antes.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>Conceder privilégios mínimos à identidade

Durante as etapas anteriores, você deve ter notado que sua identidade de serviço gerenciada está conectada ao SQL Server como o administrador do Azure AD. Para conceder privilégios mínimos para sua identidade de serviço gerenciada, você precisa entrar no servidor do Banco de Dados SQL do Azure como o administrador do Azure AD e adicionar um grupo do Azure Active Directory que contém a identidade de serviço. 

### <a name="add-managed-service-identity-to-an-azure-active-directory-group"></a>Adicionar identidade de serviço gerenciada a um grupo do Azure Active Directory

No Cloud Shell, adicione a identidade de serviço gerenciada do aplicativo a um novo grupo do Azure Active Directory chamado _myAzureSQLDBAccessGroup_, conforme mostrado no script abaixo:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Se você quiser ver a saída JSON completa para cada comando, remova os parâmetros `--query objectId --output tsv`.

### <a name="reconfigure-azure-ad-administrator"></a>Reconfigurar o administrador do Azure AD

Anteriormente, você atribuía a identidade de serviço gerenciada como administrador do Azure AD para seu Banco de Dados SQL. Você não pode usar essa identidade para entrada interativa (adicionar usuários de banco de dados) e, portanto, precisa usar o usuário real do Azure AD. Para adicionar o usuário do Azure AD, siga as etapas em [Provisionar um administrador do Azure Active Directory para seu Servidor do Banco de Dados SQL do Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server). 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Conceder permissões ao grupo do Azure Active Directory

No Cloud Shell, entre no Banco de Dados SQL usando o comando SQLCMD. Substitua _\<servername >_ pelo nome do servidor do Banco de Dados SQL e substitua _\<AADusername >_ e _\<AADpassword >_ pelas credenciais do usuário do Azure AD.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

No prompt do SQL, execute os comandos a seguir, o que adicionará o grupo do Azure Active Directory criado anteriormente como um usuário.

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
GO
```

Digite `EXIT` para retornar ao prompt do Cloud Shell. Em seguida, execute novamente o SQLCMD, mas especifique o nome do banco de dados em _\<dbname >_.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

No prompt do SQL para o banco de dados desejado, execute os comandos a seguir para conceder permissões de leitura e gravação ao grupo do Azure Active Directory.

```sql
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> * Habilitar a identidade de serviço gerenciada
> * Conceder ao Banco de Dados SQL acesso à identidade do serviço
> * Configurar o código do aplicativo para autenticar com o Banco de Dados SQL usando a autenticação do Azure Active Directory
> * Conceder privilégios mínimos para a identidade de serviço no Banco de Dados SQL

Vá para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo Web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](app-service-web-tutorial-custom-domain.md)
