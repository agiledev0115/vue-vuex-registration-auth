# fullstackwebapp
This is a full stack web app that consists of frontend (vue), backend API (.net core), and a SQL DB
1. Create a Windows VM on Azure and install IIS role on it
2. Download and install the .NET Core 3.1 Hosting Bundle from https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer
3. Restart IIS with the command net stop was /y && net start w3svc
4. Download and install the IIS URL Rewrite module from https://www.iis.net/downloads/microsoft/url-rewrite
5. Create an Azure SQL DB and take a note of the connection string, we will use it later on
6. Clone the repo by running the command: `git clone https://github.com/wshamroukh/fullstackwebapp`
7. In the Azure Portal go to the overview page for the SQL database created in the previous step.
8. Click "Show database connection strings" and copy the "ADO.NET (SQL authentication)" connection string.
9. Open the ASP.NET Core app settings file (/appsettings.json) in a text editor.
10. Replace the value of the WebApiDatabase connection string with the one you just copied, so it looks something like this:
`"ConnectionStrings": {
  "WebApiDatabase": "Server=tcp:jw-sql-server.database.windows.net,1433;Initial Catalog=my-sql-db-2;Persist Security Info=False;User ID=jason;Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"
},`
11. Inside the connection string replace {your_password} with the Azure SQL server admin password you created when setting up the database.
12. Open the ASP.NET Core app settings file (/appsettings.json) in a text editor.
13. Replace the value of the app settings Secret with your own random string, a quick and easy way is to generate a couple of GUIDs and join them together to make a long random string (e.g. from https://www.guidgenerator.com/).
14. Build the API with the command `dotnet publish --configuration Release`
15. Copy the files from the `aspnet-core-3-registration-login-api\bin\Release\netcoreapp3.1\publish` directory to the `C:\Projects\back-end` directory on the server via remote desktop.
16. Navigate into the cloned directory and install all required node packages with the command `npm install`
17. Open the main React index file (/src/index.jsx) in a text editor.
18. Delete the following lines from the file to remove the fake backend that the React app uses by default: <br />
`// setup fake backend 
import { configureFakeBackend } from './_helpers';
configureFakeBackend();`
19. Open the webpack config file `/webpack.config.js` in a text editor.
20. Change the `apiUrl` config property to `/api`
21. Build the React app with the command `npm run build`
22. Copy the files from the `react-redux-registration-login-example\dist` directory to the `C:\Projects\front-end` directory on the server via remote desktop.
23. Open IIS on the server.
24. Remove the "Default Web Site" in Sites and "DefaultAppPool" in Application Pools.
25. Right click the Sites folder and select "Add website".
26. Enter site name (e.g. my-app).
27. Set the physical path to the React app directory (C:\Projects\front-end).
28. Leave the host name empty and click "OK".
29. Right click the new site and select "Add Application".
30. Enter the alias "api" (without quotes).
31. Set the physical path to the ASP.NET Core api directory (C:\Projects\back-end).
32. Click "OK".
33. In Application Pools, right click the app pool for the new site and select "Basic Settings". Change the .NET CLR version to 'No Managed Code', the .NET CLR is not required by .NET Core applications.
34. Create a file named `web.config` inside the React front-end folder (C:\Projects\front-end) and add the below configuration into it
````<?xml version="1.0" encoding="utf-8"?>
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


35. Test now. Enter the public IP address of your Azure Windows Server in a browser to access and test your new React + ASP.NET Core + Azure SQL Server full stack application.


