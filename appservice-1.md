### Ćwiczenie 1 - uruchomienie prostego App Service
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
5. Uruchom server lokalnie komenda:
    ```
    node index.js
    ```

6. Poprawne uruchomienie powinno skutkować uruchomieniem servera i komunikatem "Server is running on http://localhost:8080"
7. Utwórz plik req.http i spróbuj wywołac usłgę servera (na "\"). Pomocne może być:
    ```
        @baseUrl = http://localhost:8080/

        get { {baseUrl} } <- wąsy powinny być razem bez spacji 

    ```
8. Utwórz app service korzystając z palety.
   1. Otwórz palete i wybierz Create New Web App
   2. Zaloguje się do azure 
   3. Wybierz subskrypcję
   4. Nadaj unikalna nazwę tworzonemu app service
   5. Wybierz runtime - NodeJS 18 LTS
   6. Wybierz pricing tier Free(F1)
   7. Poczekaj na utworzenie

       Uwaga: Jeżeli chcesz możesz parametryzować wiecej wybierając Create New Web App Advanced
9. Zanim przystąpisz do wdrażania musisz w pliku package.json dodać domyślny skrypt start, plik powinien mieć taką zawartość (bez niepotrzebnych w tym momencie danych danych)

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
    

10. Wdróż aplikację na utworzony app service
    1.  Otwórz rozszerzenie dla Azure (sponsorem jest literka A)
    2.  Znajdz w zasobach swój app service
    3.  Z menu na nim wybierz "Deploy to the Web App", możesz obserwować proces wybierając zakładkę dane wyjściowe
    4.  Znajdz adres swojego app service i sprawdz działanie aplikacji

11. Zmień swój kod na:
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