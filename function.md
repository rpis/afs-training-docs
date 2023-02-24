***Cwiczenie składa się z kilku bloków***

### Cwicenie 1 - uruchomienie prostej funkcji
1. Utworz repozytorium oraz środowisko Codespaces do ćwiczeń związanych z funkcjami ([patrz](environment.md))
2. Majc uruchomione środowisko utworzymy w ramach naszego worskapce projekt funkcji
   1. Użyj skrótu ctrl-shipt-P, zostanie wyświetlona paleta ![Palette](images/environment/new-function-project.png)
   2. Odfiltruj zadania dotyczące funcjji wpiskując func itp
   3. Wybierz "Azure Function: create function"
   4. Wybierz aktualny katalog jako katalog tworzoenje funkcji ![Folder](images/functions/create-project.png)
   5. Wybierz język JavaSript
   6. Wybierz Http triger jako template dla pierwszej tworzoenj funkcji
   7. Nazwij funkcje get-all (lub jak chcesz)
   8. Ustaw poziom autoryzacji jako Anonymous
   9. Po wykonaniu powyższych czynności zostanie usupełniona zawartość projektu ![Workspace Content](images/functions/create-workspace-content.png)

3. Otwórz plik index.js z katalogu get-all (lub innego odpwiadającego nazwie Twojej funkcji). Została stworzona podstawowa funkcja
   ```
        module.exports = async function (context, req) {
            context.log('JavaScript HTTP trigger function processed a request.');

            const name = (req.query.name || (req.body && req.body.name));
            const responseMessage = name
                ? "Hello, " + name + ". This HTTP triggered function executed successfully."
                : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

            context.res = {
                // status: 200, /* Defaults to 200 */
                body: responseMessage
            };
    }
   ```
        Nie robi ona za wiele, ale posłuży nam do uruchomienia lokalnie

4. Uruchomienie lokalne (w rodowisku codespaces) funkcji:
    1. Otwórz terminal
    2. Wydaj polecenie:
 
        ``` 
            func start
        ``` 

    3. Po uruchomieniu powinien zostać wyświetlony komunikat ![Start funkcji](images/functions/func-start.png)
    4. Zostaniemy również poinformowani o wystawieniu nowego portu: ![Port open](images/functions/port-open.png)
    5. Możemy zobaczyć adresy i porty na których działa nasz aplikacja na zakładce "Porty" ![Ports](images/functions/ports.png)
    6. Na powyższej zakładce, gdy najedziemy na adres lokalny możemy uruchomić przweglądarkę w środowisku (symbol otwarcia w edytorze), lub w osobnej zakładce (symbol weba - globus)
    7. Po otwarciu w edytorze zobaczymy komunikat domyslny o uruchomionej funkcji ![Running](images/functions/running-function.png)
    8. Dodaj w adresie przeglądarki na koncu /api/get-all (mój adres ma postać : https://rpis-animated-space-sniffle-x5qg6qq4qw73xg-7071.preview.app.github.dev/api/get-all
    9. Po wywoałaniu widzimy odpwoiedz wykonanej fukkcji ![Answer](images/functions/function-simple-answer.png)
    10. Możemy również wywołać tak https://rpis-animated-space-sniffle-x5qg6qq4qw73xg-7071.preview.app.github.dev/api/get-all?name=Anna, zobacz samodzielnie co się stanie
    11. Użycie rest-client - aby sprawdzać działanie api w środowisku dostępny jest rozszerzenie do tego, użyjemy jego
        1.  Utwórz w glównym katalogu plik req.http (ustaw sie gdzies w glownym katalogu i wybierz dodaj plik)
        2.  Otwórz plik
        3.  Wklej do pliku wywołanmie własnej funkcji, w moim przypadku to:
            ```
                get http://localhost:7071/api/get-all?name=Anna
            ```
        4. Pojawił się również "Send request" ponad naszym get ...
        5. Kliknij "Send request"
        6. Powinno się pojawić dodatkowe okno z wynikiem wywołanmia podobnym do tego co uzyskaliśmy w przeglądarce
        7. Hint: możesz podać bazowy url i nie kopiować w następnych wywolaniach:
            ```

                @baseUrl = http://localhost:7071/api

                get {{baseUrl}}/get-all?name=Anna

            ```
    12. Deployment na azure
        1.  W menu kliknij na dodatku Azure (takie A...)
        2.  Wybierz "Sign in to Azure" i postępuj zgodnie ze wskazówkami (potwierdz otwórz najpierw i zaloguj się na swoje konto azurowe)
        3.  Rozwiń gałąż "Function App", powinna być pusta jeżeli nie masz swoim funkcji
        4.  Kliknij "+" pokazany przy "Resources", stworzymy funkcję ze środowsika
            1.  Wybierz "Create Function app ..." ![Func App](images/functions/component-choose.png)
            2.  Nazwij swoją funkcję - nazwa musi być globalnie unikalna
            3.  Wybierz środowisko Node.js 18 LTS
            4.  Wybierz lokalizację, gdzie będzie funkcja uruchamiana i nastąpi tworzenie funkcji
            5.  Po utworzeniu funcja jest widoczna w przglądarce ![Created Function](images/functions/visible-created-function.png)
        5. Kliknij na swoje funkcji i z menu wybierz "Deploy to function App", potwierdz deployment i w terminalu obserwuj działanie. Oczekuij na komunikat o skończonym deploymencie
        6. Znajdz adres swojej funckji i korzytając z rest client wywołaj swoją funckję z azure, możesz zmienić swój plik req.http według wzoru, podając znaleziony adres (nie zapomnij o api na koncu adresu)
            ```
                #@baseUrl = http://localhost:7071/api
                @baseUrl = https://afs-simple-function.azurewebsites.net/api

                get {{baseUrl}}/get-all?name=Anna

            ```
    13. Zmienimy kod dla get-all ...
        1.  Zmień kod w pliku index.js na:
            ```
                const response = [{
                    "id": 1,
                    "name": "John Doe",
                    "email": "john.doe@wp.pl"
                },{
                    "id": 2,
                    "name": "Katerina Pavelkova",
                    "email": "k.pav@nevi.cz"
                }]

                module.exports = async function (context, req) {
                    context.log('JavaScript HTTP trigger function processed a request.');
                    context.log('Response: ' + response);
                    context.res = {
                        status: 200, /* Defaults to 200 */
                        body: response
                    }; 
                }
            ```
        2. Uruchom funkcję lokalnie i sprawdz działanie
        3. Uruchom funkcję w Azure
        4. Wlacz logowanie na konsole z Azure i zobacz czy widzisz komunikat który masz w kodzie. Logowanie wlaczysz wybierając swoją funkcje i menu...
   
---
### Cwicenie 2 - funkcja i prosta baza danych

1. Utworzenie storage account
   1. Otwórz rozszerzenie dla azure
   2. Kliknij na "Storage accounts" i wybierz "Crezte new storege account"
   3. Wprowadz nazwę wybranaą przez Ciebie, pamiętaj że możesz używać tylko małych liter i numerów - nazwa musi być unikalna globalnie wiec możesz dodać swój prefiks (inicjały?)
   4. Wybierz lokalizację dla storage account i zatwierdz
   5. Po chwili zasób zostanie załozony i będzie widoczny na liście zasobów ![Lista zasobów](images/functions/ex2/create-storage.png)
2. Zmiany w kodach
   1. Dodanie biblioteki - wykonaj instrukcję
   
    ```
     npm install @azure/data-tables
    ```
   2. Zmodyfikujmy kod

    ```
        const { TableClient } = require("@azure/data-tables");

        const tableClient = new TableClient("YOUR_SAS_KEY!!!", "users");

        module.exports = async function (context, req) {
            context.log('JavaScript HTTP trigger function processed a request.');

            const entities = await tableClient.listEntities();
            var response = [];
            for await (const ent of entities) {
                response.push(ent);
            }
            context.res = {
                status: 200, /* Defaults to 200 */
                body: response
            }; 
        }
    ```
   3. Uzyskajmy SAS Key
       1. Logujemy się/ przechodzimy do Azure
       2. Znajdujmey założony staorage account
       3. W menu "Security + networking" otwieramy "Shared access signature"
       4. Zostawiamy proponowane ustawienia zmieniając jedynie:
          1.  dodajemy wszystkie w "Allowed resource types"
          2.  ustawiamy daty od do tak, żeby zawieraly następne parę godzin/dni/lat ... (zależy kto co liczy)
          3.  klikamy Generate
          4.  Kopiujemy z zapmaiętujemy sobie "Table service SAS URL"
   4. Wklejmy uzyskany klucz do kodu
   5. Uruchommy naszą funcję wywołując "func start"
   6. Spróbujmy wywołać funkcję
   7. Po wywołaniu w konsoli zobaczymy błąd

    ```
        [2023-02-19T21:11:59.600Z] Executed 'Functions.get-all' (Failed, Id=9a6a63a9-bd6e-4e75-88a3-f6804b237761, Duration=45ms)
        [2023-02-19T21:11:59.600Z] System.Private.CoreLib: Exception while executing function: Functions.get-all. System.Private.CoreLib: Result: Failure
        [2023-02-19T21:11:59.600Z] Exception: {"odata.error":{"code":"TableNotFound","message":{"lang":"en-US","value":"The table specified does not exist.\nRequestId:067148cb-e002-0014-6ca6-44a341000000\nTime:2023-02-19T21:11:59.5763885Z"}}}
        [2023-02-19T21:11:59.600Z] Stack: RestError: {"odata.error":{"code":"TableNotFound","message":{"lang":"en-US","value":"The table specified does not exist.\nRequestId:067148cb-e002-0014-6ca6-44a341000000\nTime:2023-02-19T21:11:59.5763885Z"}}}
        [2023-02-19T21:11:59.600Z]     at handleErrorResponse (/workspaces/afs-functions/node_modules/@azure/core-client/dist/index.js:1306:19)
        [2023-02-19T21:11:59.600Z]     at deserializeResponseBody (/workspaces/afs-functions/node_modules/@azure/core-client/dist/index.js:1241:45)
        [2023-02-19T21:11:59.600Z]     at process.processTicksAndRejections (node:internal/process/task_queues:95:5).
    ```
   8. Nie mamy założonej tablicy, wiec załóżmy z przykładową zawartości
      1. Przejdzmy do załozonego storage account
      2. Wybieramy "Storage browser", nastepnie "Tables", uzyskujemy widok ![Empty list](images/functions/ex2/empty-list-storage-browser.png)
      3. Wybieramy Add table, nazywamy "users" i potwierdzmy, Zostanie ona przedstawiona na liście
      4. Wybieramy naszą tablice i widzimy pustą zawartość
      5. Wybieramy "Add entity" i uzupełniamy podobnie jak poniżej ![Add Entity](images/functions/ex2/add-entity.png) i zatwierdzamy. Rekord zostanie przedstawiony na liście
   9. Wracamy do Codespaces i sprawdzamy ponownie działanie funkcji. Powinnośmy uzyskać w odpowiedzi dane wprowadzone do bazy
   10. Wdrażamy na Azura i ponownie sprawdxzamy
   11. Podawanie connection stringa w kodzie nie jest najlepszym pomysłem, zatem wynieśmy go do zmiennych środowiskowych
       1.  Zmieńmy kod w podanym fragmencie

        ```
        ...
            const tableClient = new TableClient(process.env.TABLE_CONNECTION_STRING, "users");
        ...

        ```
    12. Ustawz zmienna środowiskowa w terminalu
        ```
            export TABLE_CONNECTION_STRING="YOUR_SAS_KEY"
        ```
    13. Sprawdz czy nie zapsules
    14. Wdrożenie na Azure musimy zacząc od ustwienia zmiennej środowiskowej w funkcji
        1.  Znajdz swoją funnkcję w zasobach prezentowanych w rozszerzeniu do Azure
        2.  Rozwin i wybierz "Application Settings"
        3.  Dodaj nową zmienna o nazwie TABLE_CONNECTION_STRING, zawierającą klucz SAS (tym razem bez "")
        4.  Wdróż i sprawdz działanie
    15. Stwórzmy funckję obsługującą tworzenie nowego użytkownika
        1. Utwórz palate i znajdz "Create Function" -> poszukaj po słowie kluczowym i wybierz tę funkcję
        2. Wybierz "Http triger", nazwij "post-one" i wybierz uwierzytlenianie anonimowe. Zostanie utworzona nowa funkcja
        3. Zmień kod w pliku index.js (dla utorzonej funkcji) na poniższy, zapewniający zapis nowego rekordu do bazy
            
            ```
                const { TableClient } = require("@azure/data-tables");

                const tableClient = new TableClient(process.env.TABLE_CONNECTION_STRING, "users");

                module.exports = async function (context, req) {
                    context.log('JavaScript HTTP trigger function processed a request.');

                    context.log(req.body);
                    try {
                        const entity = await tableClient.createEntity({
                            partitionKey: req.body.id,
                            rowKey: req.body.id,
                            name: req.body.name,
                            email: req.body.email,
                            status: "INACTIVE"
                        });
                        context.res = {
                            status: 201,
                            body: entity
                        };
                    } catch (e) {
                        context.res = {
                            status: 400,
                            body: e
                        };

                    }
                }
            ```
        4. Zmieńmy ustawienia funcji aby akceptowała tylko post (w pliku function.json):
        ```
                {
                "bindings": [
                    {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req",
                    "methods": [
                        "post"
                    ]
                    },
                    {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                    }
                ]
                }
        ```

        5. Uruchom loklnie projekt i uzywjąc poniższego wywołania (plik req.http) sprawdz działanie
            ```
                ###
                post {{baseUrl}}/post-one

                {
                    "id": "2",
                    "name": "Irina Pavelkowa",
                    "email": "ip@cz.cz"
                }
            ```
    16. Uapiujmy naszw endpoiny, definiując route:
        1.  Dla funkcji get-all, jak poniżej:
            ```
                {
                "bindings": [
                    {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req",
                    "route": "user",
                    "methods": [
                        "get"
                    ]
                    },
                    {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                    }
                ]
                }
            ```
        2. Dla funkcji post-one, jakej:
            ```
                {
                "bindings": [
                    {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req",
                    "route": "user",
                    "methods": [
                        "post"
                    ]
                    },
                    {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                    }
                ]
                }
            ```
        3. Zmienily się nam automatycznie uri naszych funkcji, teraz w req.http inaczej je zdefiniujemy
            ```

            @baseUrl = http://localhost:7071/api
            #@baseUrl = https://afs-simple-function.azurewebsites.net/api

            get {{baseUrl}}/user


            ###
            post {{baseUrl}}/user

            {
                "id": "2",
                "name": "Irina Pavelkowa",
                "email": "ip@cz.cz"
            }
            ```

    16. [Opcjonalne] Dodajmy funkcje pobierającą pojeyńczy rekokord z bazy po id
        1.  Utwórz nową funkcję nazywając ja "get-one"
        2.  Dodej kod:
            ```
                const { TableClient } = require("@azure/data-tables");

                const tableClient = new TableClient(process.env.TABLE_CONNECTION_STRING, "users");

                module.exports = async function (context, req) {
                    context.log('JavaScript HTTP trigger function processed a request.');

                    const entity = await tableClient.getEntity(req.params.id, req.params.id);
                    context.res = {
                        // status: 200, /* Defaults to 200 */
                        body: entity
                    };
                }
            ```
        3. Ogranicz działałanie jedynie do get, oraz zdefiniuj routing jak poniżej:
           ```
              "route": "user/{id}",
            ```
        4. Sprawdz działąnie wyKorzytując wywołanie:
            ```
                ###
                get {{baseUrl}}/user/1
            ```
        5. Wdróż na azure i sprawdz działanie.
    

---
### Cwicenie 3 - funkcje i bezpieczeństwo wewnątrz infrastruktury

1. Stwórzmy tożsamość developerską, aby móc wykorzytać "System Identity" w naszym środowisku
   1. W konsoli Azure znajdz Azure Active Directory i otwórz
   2. Wybierz "App registratios", zostanie przedstawiona lista zedfiniowanych aplikacji
   3. Wybierz "New registration", nadaj swoja nazwę aplikacji i zarejestruj, nie musisz uzupełniać "Redirect uri" i zmieniać "Supported account type".Po założeniu zobaczysz: ![New application AD](images/functions/ex3/new-application.png). Zapamietaj "Application (client) ID" oraz "Directory (tenant) ID"
   4. Wybierz "Add a certyficate or secret" i na liście "Client secrets" wybierz "New client secret"
   5. Uzupełnij nazwę secreta, możesz wybrać okres ważności i dodać secert
   6. Zapamietaj zaprezentowane Value dla utworzonego secreta
   7. Ustaw zmienne środowiskowe
    ```
        export AZURE_CLIENT_ID=YOUR_CLIENT_ID
        export AZURE_TENANT_ID=YOUR_TENANT_ID
        export AZURE_CLIENT_SECRET=YOUR_CLIENT_SECRET

    ```
    na przykład:
        ```
            export AZURE_CLIENT_ID=b99abf34-615a-41a5-b156-157cd5c72e1a
            export AZURE_TENANT_ID=c2eab347-e7b5-431e-a1c0-761d5be089f0
            export AZURE_CLIENT_SECRET=2nq8Q~OrWqSEk-Nmo_hWqylMrrBOpa2RWQtb4cp3

        ```
    8. Dodajmy bibliteke do obsługi uwierzytelnienia, wykonaj w terminalu:
        ```
            npm install @azure/identity
        ```
    9. Zmień linie tworzenia Table clienta we wszytskich funkcjach na poniższą wraz z dodaniem importu :
        ```
            const { DefaultAzureCredential } = require("@azure/identity");
            const tableClient = new TableClient(process.env.TABLE_CONNECTION_STRING, "users",
                new DefaultAzureCredential());
        ```
   10. Ustaw zmienna srodowiskowa TABLE_CONNECTION_STRING, na sam url bez uwierztyleniania w stosunku do SAS np
        ```
            export TABLE_CONNECTION_STRING="https://afssimplefunction.table.core.windows.net/"
        ```
    11. Uruchom i sprawdz jaki komunikat błedu otrzymasz, Powinien być taki:

    ```
        [2023-02-20T13:01:15.315Z] JavaScript HTTP trigger function processed a request.
        [2023-02-20T13:01:15.684Z] Executed 'Functions.get-all' (Failed, Id=8ddb9275-ab1f-4c3c-8312-e3331e95e91a, Duration=496ms)
        [2023-02-20T13:01:15.684Z] System.Private.CoreLib: Exception while executing function: Functions.get-all. System.Private.CoreLib: Result: Failure
        [2023-02-20T13:01:15.684Z] Exception: {"odata.error":{"code":"AuthorizationPermissionMismatch","message":{"lang":"en-US","value":"This request is not authorized to perform this operation using this permission.\nRequestId:47027879-6002-00a4-672b-455ac1000000\nTime:2023-02-20T13:01:15.6394009Z"}}}
        [2023-02-20T13:01:15.685Z] Stack: RestError: {"odata.error":{"code":"AuthorizationPermissionMismatch","message":{"lang":"en-US","value":"This request is not authorized to perform this operation using this permission.\nRequestId:47027879-6002-00a4-672b-455ac1000000\nTime:2023-02-20T13:01:15.6394009Z"}}}
        [2023-02-20T13:01:15.685Z]     at handleErrorResponse (/workspaces/afs-functions/node_modules/@azure/core-client/dist/index.js:1306:19)
        [2023-02-20T13:01:15.685Z]     at deserializeResponseBody (/workspaces/afs-functions/node_modules/@azure/core-client/dist/index.js:1241:45)
        [2023-02-20T13:01:15.685Z]     at process.processTicksAndRejections (node:internal/process/task_queues:95:5).
    ```
    12. Wrócmy do portalu Azure i nadajmy uprawnienia dla naszej tożsamości developerskiej
        1.  Znajdz wykorzytywany storage account i wejdz na zakładkę "Access Control"
        2.  Przejdz na zakładę "Role Assignment"
        3.  Kliknij "Add", wybierając "Add role assignment"
        4.  Znajdz prawo "Storage Account Contributor", zaznacz na liscie i przejdz dalej ![cChoose role](images/functions/ex3/add-role-name.png)
        5.  Kliknij "Select member"i odfiltruj po nazwie zdefiniowanej w Ad aplikacji ![Select members](images/functions/ex3/select-members.png), zaznaczamy i potierdzmy wybór
        6.  Potwierdzamy za pomocą "Review + assign", ponownie potwierdzamy
        7.  Po chwili zobaczymy na liście utorzone powiązanie roli
        8.  Powtarzamy czynnoci dodając role "Storage Table Data Contributor"
        9.  Powtarzamy czynnoci dodając role "Storage Blob Data Owner", w tym wypadku potwierdzamy również condition nie zmieniając nic
    13. Uruchamiamy funkcję i potwierdzamy działanie lokalne
    14. Przygotowanie funcji w Azurze do użycia "system identity"
        1.  W portalu azure znajdujemy naszą funkcję, wybieramy "Identity" i na zakładce "System assigned" ustawiamy na ON
        2. Zapisujemy zmiany
    15. Dodajemy role ustawiane w pkt.12 ale dla tożsamości naszej funckji (wyszukamy po początku nazwy)
    16. Zmianiamy zmienna środowiskową TABLE_CONNECTION_STRING na sam adres usługi
    17. Sprawdzamy działanie w środowisku Azure (czasami należy restartować funkcję, zwłaszcza jak za szybko zmian dokonujemy)

---
### Cwicenie 4 - użycie API-GW

1. Wybieramy utworzoną funkcję w portalu Azure i przechodzimy do mneu "Api Management"
2. Możemy wybrać isniejący już komponent albo utworzyć nowy, tworzymy nowy
   1. Wybieramy region, nadajemy nazwe, ustalamy workspace nam na dowolną nazwę i wpisujemy email
   2. Wybieramy (nie zmieniamy) Pricing tier. Powinien dla naszych celów być "Consumption" ![Create APIM](images/functions/ex4/create-apim.png)
   3. Przechodzimy dalej, odznaczamy Application Insights i potwerdzmy "Review and create" i ponownie "Create"
   4. Po momencie zostanie założona instancjia apim
   5. Na zakładce w API Management w funckji zostanie pokazana zakładka wyboru API, klikamy "Link API" ![Create API](images/functions/ex4/create-api.png)
   6. Zostanie przedstawiony wybór funcji do wystawienia jako api ![Func list](images/functions/ex4/choose-functions.png)
   7. Potwierdamy i zobaczymy widok ![Create from function APP](images/functions/ex4/create-from-function-app.png)
   8. Zatwierdzamy i zobaczymy utworzone api ![Created API](images/functions/ex4/created-api.png)
   9. Odznacz set subsription required na zakładce Settings  ![Setup](images/functions/ex4/set-subsription-required-false.png)
   10. Otwórz codespaces utworzony dla funcji i przetestuj działanie używając swojego adresu api managementu
   11. Dodanie zabezpieczenia funcji (nie chcemy, żeby poza apim była wołana...)
       1.  Uruchom środowisko Codespace dla funkcji
       2.  Zmień aktulany authLevel z anonymous na function dla 3 utworzonych funkcji (pliki function.json)
       3.  Wdróż funkcję
       4.  Sprawdz, że wywołanioa którymi testowalismy poprzednio funkcję zwaracają błąd 401 (jak nie zwracają to coś jest nie tak)
       5.  Sprawdz wywołania przez apim, oczekujemy poprawnych wywołań
   

---
### Cwicenie 5 - Durable functions

---
### Cwicenie 6 - stoerage queue


