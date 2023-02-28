### Ćwiczenie 3 - funkcje i bezpieczeństwo wewnątrz infrastruktury

1. Stwórzmy tożsamość developerską, aby móc wykorzytać "System Identity" w naszym środowisku
   1. W konsoli Azure znajdz Azure Active Directory i otwórz
   2. Wybierz "App registrations", zostanie przedstawiona lista zedfiniowanych aplikacji
   3. Wybierz "New registration", nadaj swoja nazwę aplikacji i zarejestruj, nie musisz uzupełniać "Redirect uri" i zmieniać "Supported account type".Po założeniu zobaczysz: ![New application AD](images/functions/ex3/new-application.png) Zapamietaj "Application (client) ID" oraz "Directory (tenant) ID"
   4. Wybierz "Add a certyficate or secret" i na liście "Client secrets" wybierz "New client secret"
   5. Uzupełnij nazwę secreta, możesz wybrać okres ważności i dodać secrert
   6. Zapamietaj zaprezentowane Value dla utworzonego secreta
   7. Ustaw zmienne środowiskowe
   
    ```
        export AZURE_CLIENT_ID=YOUR_CLIENT_ID
        export AZURE_TENANT_ID=YOUR_TENANT_ID
        export AZURE_CLIENT_SECRET=YOUR_CLIENT_SECRET

    ```

    na przykład:
       
     ```
        export AZURE_CLIENT_ID=b99abf34-615a-4ua5-b156-157cd5c72e1a
        export AZURE_TENANT_ID=c2eab347-eub5-431e-a1c0-761d5be089f0
        export AZURE_CLIENT_SECRET=2nq8Q~OrWqSEk-Nmo_huuylMrrBOpa2RWQtb4cp3

     ```

    8. Dodajmy bibliteke do obsługi uwierzytelnienia, wykonaj w terminalu:
        ```
            npm install @azure/identity
        ```
    9.  Zmień linie tworzenia Table clienta we wszytskich funkcjach na poniższą wraz z dodaniem importu :
        ```
            const { DefaultAzureCredential } = require("@azure/identity");
            const tableClient = new TableClient(process.env.TABLE_CONNECTION_STRING, "users",
                new DefaultAzureCredential());
        ```
   8.  Ustaw zmienna srodowiskowa TABLE_CONNECTION_STRING, na sam url bez uwierzytelnienia w stosunku do SAS np
       
       ```
            export TABLE_CONNECTION_STRING="https://afssimplefunction.table.core.windows.net/"
        ```

    9.  Uruchom i sprawdz jaki komunikat błedu otrzymasz, Powinien być taki:

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

    10. Wrócmy do portalu Azure i nadajmy uprawnienia dla naszej tożsamości developerskiej
        1.  Znajdz wykorzytywany storage account i wejdz na zakładkę "Access Control"
        2.  Przejdz na zakładę "Role Assignment"
        3.  Kliknij "Add", wybierając "Add role assignment"
        4.  Znajdz prawo "Storage Account Contributor", zaznacz na liscie i przejdz dalej ![cChoose role](images/functions/ex3/add-role-name.png)
        5.  Kliknij "Select member"i odfiltruj po nazwie zdefiniowanej w Ad aplikacji ![Select members](images/functions/ex3/select-members.png), zaznaczamy i potwierdzmy wybór
        6.  Potwierdzamy za pomocą "Review + assign", ponownie potwierdzamy
        7.  Po chwili zobaczymy na liście utorzone powiązanie roli
        8.  Powtarzamy czynności dodając role "Storage Table Data Contributor"
        9.  Powtarzamy czynności dodając role "Storage Blob Data Owner", w tym wypadku potwierdzamy również condition nie zmieniając nic
    11. Uruchamiamy funkcję i potwierdzamy działanie lokalne
    12. Przygotowanie funcji w Azurze do użycia "system identity"
        1.  W portalu azure znajdujemy naszą funkcję, wybieramy "Identity" i na zakładce "System assigned" ustawiamy na ON
        2. Zapisujemy zmiany
    13. Dodajemy role ustawiane w pkt.12 ale dla tożsamości naszej funckji (wyszukamy po początku nazwy)
    14. Zmianiamy zmienna środowiskową TABLE_CONNECTION_STRING na sam adres usługi
    15. Sprawdzamy działanie w środowisku Azure (czasami należy restartować funkcję, zwłaszcza jak za szybko zmian dokonujemy)

---
