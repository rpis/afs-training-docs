***Środowisko powinno zostac skonfigurowane dla każdej grupy ćwiczeń , gdy zaczynamy pracę***

---
## Założenie repozytorium w git
1. Otwórz konsole github i zaloguj się ([link](https://github.com))
2. Wybierz nowe repozytorium
3. Uzupełnij nazwę repozytorium
4. Wybierz czy ma być widoczne publicznie czy prywatnie. Uwaga domyślnie tworzone jest repozytorium publiczne
5. Zaznacz "Add README file" - jest to istotne ze względu na utworzenie automatycznie brancha main
6. Potwierdź utworzenie repozytorium
   

Tym sposobem masz przygotowane repozytorium
![Repo](images/environment/repo-after-creation.png)

---
## Uruchomienie codespaces dla utworzonego repo

1. Będąc w konsoli githuba wybierz "Codespaces" ![Repo](images/environment/codespaces-start.png)
2. Wybierz "New Codespaces"
3. Wskaż utworzone wczeniej repozytorium, wybieramy branch - domyslnie main, ustawiamy region jak najbliższy geograficznie oraz zmieniamy typ maszyny na "2-core"  ![Repo](images/environment/codespaces-machine.png)
4. Potwierdź wybór i po momencie środowsko jest dostępne

---
### Konfiguracja codespaces
Pracuj na otwartym w przeglądarce środowisku
1. Otwórz terminal (Menu kabab w lewym rogu ekranu -> Terminal -> New terminal
2. Wykonaj instalację podsatwowych rozszerzeń (można to zrobić również ręcznie) 
    ```
        code --install-extension ms-vscode.vscode-node-azure-pack
        code --install-extension humao.rest-client
    ```
3. Zainstaluj core-tools dla azure (niezbędne tylko dla środowiska dla funkcji)
    ```
        sudo apt-get update
        sudo apt-get install azure-functions-core-tools-4
    ```
4. [Opcjonalne gdy azure core tools nie obsługuje domyślnej wersji node] Zmień wersję node na 18 (lub inna akceptowaną przez azure core tools) (niezbędne tylko dla środowiska dla funkcji)
    ```
        nvm install 18
        nvm use 18
    ```
5. [Opcjonalne gdy azure core tools nie obsługuje domyślnej wersji node]Sprawdz wersję node komendą node --version, powinna zostać pokazana v18.x

Twoje środowisko jest gotowe do pracy

[Opcjonalne gdy azure core tools nie obsługuje domyślnej wersji node]  Uwaga : Po każdym uruchomieniu środowiska należy wybrać wersję Node instrukcją "nvm use 18"