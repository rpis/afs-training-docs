### Ćwiczenie 2 - wykorzytanie bazy Cosmos DB

1. Załóż bazę danych cosmodb
   1. W eksporatorze Azure znajdź "Azure Cosmos DB" i wybierz "Create Server" i w kolejnych krokach
   2. Wybierz Core(SQL)
   3. Nazwij swoją bazę danych
   4. Wybierz Serverless
   5. Wybierz Resource Group lub utwórz nową
   6. Wybierz lokalizację dla tworzonej bazy
   7. Zobacz utworzoną nazwę w eksploratorze oraz w portalu
 
2. Zainstaluj bibliotekę do cosmos db

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
4. Ustaw zmienna środowiskowa COSMOSDB_CONNECTION_STRING na wartość pobraną z Cosmos DB w explorerze (wybierz swoją baze i wybierz z menu Copy Connection String)
5. Uruchom lokalnie i sprawdz działanie - powinno zwrócić pustą tablicę
6. Dodaj do bazy danych ręcznie rekord, w tym celu:
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
            "status": "ACTIVE"
        }
    ```
7. Uruchoma kod lokalnie i zobacz działanie z bazą
8. Dodaj do "Application Settings" nową wartość COSMOSDB_CONNECTION_STRING ustawioną na connection string do cosmos db
9. Wdróż kod i sprawdz działanie
   
---