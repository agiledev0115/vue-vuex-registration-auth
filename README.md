# vue-vuex-registration-login-example
Vue + Vuex - User Registration and Login Tutorial & Example

To see a demo and further details go to http://jasonwatmore.com/post/2018/07/14/vue-vuex-user-registration-and-login-tutorial-example

# fullstackwebapp
This is a full stack web app that consists of frontend (vue), backend API (.net core), and a SQL DB.

You can deploy this solution on Azure PaaS services. Follow the steps bellow:

1. Create a two Azure App Services that will be used for front and backend apps
2. Create an [Azure SQL DB](https://docs.microsoft.com/en-us/azure/azure-sql/database/single-database-create-quickstart?tabs=azure-portal)
3. Clone the repo locally by running the command:
```
git clone https://github.com/wshamroukh/vue-vuex-registration-login-example
git clone https://github.com/wshamroukh/aspnet-core-3-registration-login-api
```
4. In the Azure Portal go to the overview page for the SQL database created in the previous step.
5. Click "Show database connection strings" and copy the "ADO.NET (SQL authentication)" connection string.
5. Open the ASP.NET Core app settings file (`/appsettings.json`) in a text editor.
6. Replace the value of the WebApiDatabase connection string with the one you just copied, so it looks something like this:
```
"ConnectionStrings": {
  "WebApiDatabase": "Server=tcp:jw-sql-server.database.windows.net,1433;Initial Catalog=my-sql-db-2;Persist Security Info=False;User ID=jason;Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"
},
```
8. Inside the connection string replace {your_password} with the Azure SQL server admin password you created when setting up the database.
9. Open the ASP.NET Core app settings file (`/appsettings.json`) in a text editor.
10. Replace the value of the app settings Secret with your own random string, a quick and easy way is to generate a couple of GUIDs and join them together to make a long random string (e.g. from https://www.guidgenerator.com/), or you could use Powershell command: `New-Guid`
11. Build the API with the command:
```
dotnet publish --configuration Release
```
12. Copy the files from the `aspnet-core-3-registration-login-api\bin\Release\netcoreapp3.1\publish` to the backend app service using SFTP
13. Navigate into the cloned directory of the frontend (vue) application and install all required node packages with the command:
```
npm install
```
14. Open the main React index file (/src/index.jsx) in a text editor.
15. Delete the following lines from the file to remove the fake backend that the React app uses by default: <br />
```
// setup fake backend 
import { configureFakeBackend } from './_helpers';
configureFakeBackend();
```
16. Open the webpack config file `/webpack.config.js` in a text editor.
17. Change the `apiUrl` config property to to the URL of your backend Azure Web App for instance:`https://backend.azurewebsites.net`
18. Build the React app with the command:
```
npm run build
```
19. Copy the files from the `react-redux-registration-login-example\dist` directory to the frontend Azure Web App Service using SFTP.
20. Create a file named `web.config` and copy it to the frontend Azure Web App Service using SFTP.
```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <system.webServer>
        <rewrite>
            <rules>
                <rule name="React" stopProcessing="true">
                    <match url=".*" />
                    <conditions logicalGrouping="MatchAll">
                        <add input="{REQUEST_URI}" pattern="^/api/.*" negate="true" />
                        <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
                        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
                    </conditions>
                    <action type="Rewrite" url="/" />
                </rule>
            </rules>
        </rewrite>
    </system.webServer>
</configuration>
```
21. Test now by accessing the frontend Azure Web App Service URL.
