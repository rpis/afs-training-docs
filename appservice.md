***Cwiczenie składa się z kilku bloków***

### Cwiczenie 1 - uruchomienie prostego app service
1. Utwórz nowe repozytorium oraz środowisko codespaces [jak?](environment.md)
2. Uruchom środowisko
3.  Zainicjuj projekt poniższą komendą, potwierdzając ustawienia jako domyslne enterem lub zmieniając, finalnie potwierdz yes

    ```
        npm init 

    ```
4. Utwórz plik index.js z najprostszym serverem:
    ```
        const http = require("http");
        const port = 8080;

        const requestListener = function (req, res) {
            res.setHeader("Content-Type", "application/json");
            res.writeHead(200);
            res.end(`{"message": "Hello from app service"}`);
        };

        const server = http.createServer(requestListener);
        server.listen(port, () => {
            console.log(`Server is running on http://localhost:${port}`);
        });

    ```
5. Uruchoma server lokalnie komenda:
    ```
    node index.js
    ```

6. Poprawne uruchomienie powinno skutkować uruchomieniem servera i komunikatem "Server is running on http://localhost:8080"
7. Utwórz plik req.http i spróbuj wywołac usłgę servera (na "\"). Pomocne może być:
    ```
        @baseUrl = http://localhost:8080/

        get {{baseUrl}}

    ```
8. Utwórzmy app service korzysystając z palety.
   1. Otwórz palete i wybierz Create New Web App
   2.  Zaloguje się do azure 
   3.  Wybierz subskrypcję
   4.  Nadaj unikalna nazwę tworzonemu app service
   5.  Wybierz runtime - NodeJS 18 LTS
   6.  Wybierz pricing tier Free(F1)
   7.  Poczekaj na utworzenie

       Uwaga: Jeżeli chceszz możesz parmetrzyować wiecej wybierając Create New Web App Advanced
9. Zanim przystapimy do wdrażania musimy w pliku package.json dodać domyślny skrypt start, plik powinien mieć taką zawartość (bez niepotrzebnych w tym momencie danych danych)

    ```
        {
            "name": "afs-app-service",
            "version": "1.0.0",
            "description": "",
            "main": "index.js",
            "scripts": {
                "test": "echo \"Error: no test specified\" && exit 1",
                "start": "pm2 start index.js --no-daemon"
            }
        }
    ```
    

10. Wdróż apliokację na utworzony app service
    1.  Otwórz rozszerzenie dla Azure (sponsorem jest literka A)
    2.  Znajdz w zasobach swój app service
    3.  Z menu na nim wybierz "Deploy to the Web App", możesz obserwować proces wybierając zakładkę dane wyjściowe
    4.  Znajdz adres swojego app service i sprawdz działanie aplikacji

11. Zmienimy nasz kod na:
    ```
        const http = require("http");
        const port = process.env.PORT || 3000

        const response = [{
            "id": 1,
            "name": "John Doe",
            "email": "john.doe@wp.pl"
        }, {
            "id": 2,
            "name": "Katerina Pavelkova",
            "email": "k.pav@nevi.cz"
        }];

        const requestListener = function (req, res) {
            if (req.method === "GET" && req.url === "/") {
                res.setHeader("Content-Type", "application/json");
                res.writeHead(200);
                res.end(JSON.stringify(response));
            } else {
                res.setHeader("Content-Type", "application/json");
                res.writeHead(200);
                res.end(`{ "message": "Not found"}`);
            }
        };

        const server = http.createServer(requestListener);
        server.listen(port, () => {
            console.log(`Server is running on http://localhost:${port}`);
        });
    ```
12. Sprawdz dzialanie kodu lokalnie
13. Wdróż kod po zmianach na Azure
14. Zobacz jak wyglądają logi i sprawdz czy znajdziesz w nich logowane komunikaty

--

1. Założenie bazy danych cosmodb
   1. W eksporatorze Azure znajdz "Azure Cosmos DB" i wybierz "Create Server" i wokoeljych krokach
   2. Wybiezr Core(SQL)
   3. Nazwij swoją bazę danych
   4. Wybierz Serverless
   5. Wybierz Resource Group lub utwórz nową
   6. Wybierz lokalizację dla tworzonej bazy
   7. Zobacz utworzoną nazę w eksploratorze oraz w portalu
 
2. Zainstalug bibliotekę do cosmos db

    ```
        npm install @azure/cosmos
    ```
3. Zmień kod dla index.js na:
   
   ```
        const { CosmosClient } = require("@azure/cosmos");
        const http = require("http");
        const port = process.env.PORT || 3000

        let container = null;
        const cosmosClient = new CosmosClient(
            process.env.COSMOSDB_CONNECTION_STRING,
        );

        const requestListener = async function (req, res) {
            console.log("Call service :", req.method, req.url);
            if (req.method === "GET" && req.url === "/") {
                var users = (await container.items.readAll().fetchAll()).resources
                res.setHeader("Content-Type", "application/json");
                res.writeHead(200);
                res.end(JSON.stringify(users));
            } else if (req.method === "GET" && req.url.startsWith("/") && req.url.length > 1) {
                var id = req.url.substring(1);
                var user = (await container.item(id, id).read()).resource
                res.setHeader("Content-Type", "application/json");
                res.writeHead(200);
                res.end(JSON.stringify(user));
            } else if (req.method === "POST" && req.url === "/") {
                var body = '';
                req.on('data', function (data) {
                    body += data;
                });
                req.on('end', async function () {
                    try {
                        var b = JSON.parse(body);
                        var user = {
                            id: b.id,
                            partitionKey: b.id,
                            name: b.name,
                            email: b.email,
                            status: "INACTIVE",
                        };
                        var ret = (await container.items.create(user)).resource;
                        res.setHeader("Content-Type", "application/json");
                        res.writeHead(200);
                        res.end(JSON.stringify(ret));
                    } catch (err) {
                        console.log(err);
                        res.setHeader("Content-Type", "application/json");
                        res.writeHead(500);
                        res.end(`{ "message": "Internal server error"}`);
                    }
                });

            } else {
                res.setHeader("Content-Type", "application/json");
                res.writeHead(200);
                res.end(`{ "message": "Not found"}`);
            }
        };

        const server = http.createServer(requestListener);
        server.listen(port, async () => {
            const { database } = await cosmosClient.databases.createIfNotExists({
                id: 'users',
            });
            container = (await database.containers.createIfNotExists({
                id: 'users',
                partitionKey: '/partitionKey',
            })).container;
            console.log(`Server is running on http://localhost:${port}`);
        });
   ```

4. Dodaj do bazy danyc ręcznie rekord, w tym celu:
   1. Znajdz w portalu Azure swoja bazę cosmodb
   2. Wejdz do "Data Explorera"
   3. Wybierz w bazie users, strukture users oraz Items
   4. Dodaj "New Item"
    ```
        {
            "id": "1",
            "partitionKey": "1",
            "name": "John Doe",
            "email": "john.doe@wp.pl",
            "status": "ACTIVE",
        }
    ```
5. Ustaw zmienna srodowiskowa COSMOSDB_CONNECTION_STRING na wartość pobraną z Cosmos sb w explorerze (wybierz swoją baze i wybierz z menu Copy Connection String)
6. Uruchoma kod lokalnie i zobacz działanie z bazą
7. Dodaj do "Application Settings" nową wartość COSMOSDB_CONNECTION_STRING ustawioną na connection string do cosmos db
8. Wdróż kod i sprawdz działanie
   
---

### Cwicenie 3 - funkApp Service i bezpieczeństwo wewnątrz infrastruktury

1. Zainstalujmy azure cli, bedzie potrzny w dalszej czesci
    ```
        curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
    ```
    Potwierdz instalacje!
2. W ćwiczeniu wykorzytamy tożaszmośc developerską stworzoną w cwiczeniu 3 dla funkcji, zatem ustaw zmienne środowiskowe
    ```
        export AZURE_CLIENT_ID=YOUR_CLIENT_ID
        export AZURE_TENANT_ID=YOUR_TENANT_ID
        export AZURE_CLIENT_SECRET=YOUR_CLIENT_SECRET

    ```
3. ustaw również zmienną COSMOSDB_CONNECTION_STRING
4. Ustawmy prawa do korzystania z cosmosdb przez tożsamość developerską za pomoca azure cli
   1. Dodajmy plik z definicją roli w pliku role.json jak poniżej:
        ```
            {
                "RoleName": "Training Role",
                "Type": "CustomRole",
                "AssignableScopes": ["/"],
                "Permissions": [{
                    "DataActions": [
                        "Microsoft.DocumentDB/databaseAccounts/readMetadata",
                        "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
                        "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
                    ]
                }]
            }
        ```
    3. Logujemy się do azure cli za pomocą komendy "az login --use-device-code", po zalogowaniu (w przeglądarce) uzyskujemy:
        ```
            [
            {
                "cloudName": "AzureCloud",
                "homeTenantId": "c2eab347-e7b5-421e-a1c0-761d5be089f0",
                "id": "f3cf9995-c12c-4a30-ab32-76e33e05b921",
                "isDefault": true,
                "managedByTenants": [],
                "name": "Azure subscription 1",
                "state": "Enabled",
                "tenantId": "c2eab347-e7b5-421e-a1c0-761d5be089f0",
                "user": {
                "name": "biuro@xiteo.pl",
                "type": "user"
                }
            }
            ]
        ```
   4. Ustawiamy zmienne jak poniżej i wykonujemy w kolejnosci instrukcje
   
       1. ustawiamy nazwe naszej resource grypy w której utworzyliśmy cosmos db
   
            ```
                resourceGroupName=afs-app-service
            ```

       2. ustawiamy nazwe naszego cosmos db
   
        ```
            accountName=afs-app-service
        ```

       3. Tworzymy role za pomocą:
   
        ```
            az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role.json

        ```

        Powinniśmy uzyskać dopowiedz zbliżenje zawartości:

        ```
            {
            "assignableScopes": [
                "/subscriptions/f3cf9995-c12c-4a30-ab32-76e33e05b921/resourceGroups/afs-app-service/providers/Microsoft.DocumentDB/databaseAccounts/afs-app-service"
            ],
            "id": "/subscriptions/f3cf9995-c12c-4a30-ab32-76e33e05b921/resourceGroups/afs-app-service/providers/Microsoft.DocumentDB/databaseAccounts/afs-app-service/sqlRoleDefinitions/0c5f9175-0b18-4c09-98da-a4f914c6777f",
            "name": "0c5f9175-0b18-4c09-98da-a4f914c6777f",
            "permissions": [
                {
                "dataActions": [
                    "Microsoft.DocumentDB/databaseAccounts/readMetadata",
                    "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
                    "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
                ],
                "notDataActions": []
                }
            ],
            "resourceGroup": "afs-app-service",
            "roleName": "Training Role",
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions",
            "typePropertiesType": "CustomRole"
            }
        ```

     4. Ustawiamy zmienna okresjąca roleId, na podstawie pola name z poprzedniej odpowiedzi
   
        ```
            roleId=0c5f9175-0b18-4c09-98da-a4f914c6777f
        ```

     5. Ustawiamy identyfikator konta, dla którego będziemy dodawać role:
        
        ```
            principalId=91a730b1-1c7e-4f40-956b-9bd7108e1bf1
        ```

    6. Dodajemy role do zdefiniwanego konta
        
        ```
            az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $roleId 
        ```

        W odpowiedzi powinnośmy otrzymać:
        
        ```
            {
            "id": "/subscriptions/f3cf9995-c12c-4a30-ab32-76e33e05b921/resourceGroups/afs-app-service/providers/Microsoft.DocumentDB/databaseAccounts/afs-app-service/sqlRoleAssignments/52ad0185-32ac-4670-bc29-494e46d1b542",
            "name": "52ad0185-32ac-4670-bc29-494e46d1b542",
            "principalId": "91a730b1-1c7e-4f40-956b-9bd7108e1bf1",
            "resourceGroup": "afs-app-service",
            "roleDefinitionId": "/subscriptions/f3cf9995-c12c-4a30-ab32-76e33e05b921/resourceGroups/afs-app-service/providers/Microsoft.DocumentDB/databaseAccounts/afs-app-service/sqlRoleDefinitions/0c5f9175-0b18-4c09-98da-a4f914c6777f",
            "scope": "/subscriptions/f3cf9995-c12c-4a30-ab32-76e33e05b921/resourceGroups/afs-app-service/providers/Microsoft.DocumentDB/databaseAccounts/afs-app-service",
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments"
            }
        ```

5. Dodajmy bibliteke do obsługi uwierzytelnienia, wykonaj w terminalu:
       
    ```
       npm install @azure/identity
    ```

6. Zmień kod na podany poniżej obsługujący "System Identity":
   
   ```
        const { CosmosClient } = require("@azure/cosmos");
        const { DefaultAzureCredential } = require("@azure/identity");
        const http = require("http");
        const port = process.env.PORT || 3000

        let container = null;
        const cosmosClient = new CosmosClient({
            endpoint: process.env.COSMOSDB_CONNECTION_STRING,
            aadCredentials: new DefaultAzureCredential()
        });

        const requestListener = async function (req, res) {
            console.log("Call service :", req.method, req.url);
            if (req.method === "GET" && req.url === "/") {
                var users = (await container.items.readAll().fetchAll()).resources
                res.setHeader("Content-Type", "application/json");
                res.writeHead(200);
                res.end(JSON.stringify(users));
            } else if (req.method === "GET" && req.url.startsWith("/") && req.url.length > 1) {
                var id = req.url.substring(1);
                var user = (await container.item(id, id).read()).resource
                res.setHeader("Content-Type", "application/json");
                res.writeHead(200);
                res.end(JSON.stringify(user));
            } else if (req.method === "POST" && req.url === "/") {
                var body = '';
                req.on('data', function (data) {
                    body += data;
                });
                req.on('end', async function () {
                    try {
                        var b = JSON.parse(body);
                        var user = {
                            id: b.id,
                            partitionKey: b.id,
                            name: b.name,
                            email: b.email,
                            status: "INACTIVE",
                        };
                        var ret = (await container.items.create(user)).resource;
                        res.setHeader("Content-Type", "application/json");
                        res.writeHead(200);
                        res.end(JSON.stringify(ret));
                    } catch (err) {
                        console.log(err);
                        res.setHeader("Content-Type", "application/json");
                        res.writeHead(500);
                        res.end(`{ "message": "Internal server error"}`);
                    }
                });

            } else {
                res.setHeader("Content-Type", "application/json");
                res.writeHead(200);
                res.end(`{ "message": "Not found"}`);
            }
        };

        const server = http.createServer(requestListener);
        server.listen(port, async () => {
            const { database } = await cosmosClient.databases.createIfNotExists({
                id: 'users',
            });
            container = (await database.containers.createIfNotExists({
                id: 'users',
                partitionKey: '/partitionKey',
            })).container;
            console.log(`Server is running on http://localhost:${port}`);
        });
   ```

7. Uruchamiamy kod lokalnie i sprawdzamy poprawność działania
8.  Wdróż na Azure i sprawdz czy działa i jaki uzyskamy komunikat błędu.
9.  Uaktywnijmy System assigned w swoim App Service (ustaw na ON i zapisz). Po zapisie zobaczysz Principal ID
10. Dodaj utworzoną grupe do powyższego principala, jeżeli nie zamykałeś codespace to możesz ustawić principalId=[YOUR PRINCIPAL ID] i wykonać:
       
        ```
            az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $roleId 
        ```

11. Zmien COSMOSDB_CONNECTION_STRING w "Application Settings"
12. Wykonaj restart App Service i sprawdz działanie