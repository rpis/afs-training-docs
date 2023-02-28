***Środowisko powinno zostac skonfigurowane dla każdej grupy ćwiczeń , gdy zaczynamy pracę***

---
## Założenie repozytorium w git
1. Otwieramy konsole github i logujemy się ([link](https://github.com))
2. Wybieram nowe repozytorium
3. Uzupełniamy nazwę reppozytorium
4. Wybieramy czy ma być widoczne publicznie czy prywatnie. Uwaga domyślnie tworzone jest repozytorium publiczne
5. Zaznaczamy "Add README file" - jest to istotne ze względu na utworzenie automatycznie brancha main
6. Potwierdzmny utworzenie repozytorium
   

Tym sposobem mamy przygotowane repozytorium
![Repo](images/environment/repo-after-creation.png)

---
## Uruchomienie codespaces dla utworzionego repo

1. Będąc w konsoli githuba wybieramy "Codespaces" ![Repo](images/environment/codespaces-start.png)
2. Wybieramy "New Codespaces"
3. Wskazujemy utworzone wczeniej repozytorium, wybieramy branch - domyslnie main, ustawiamy region jak najbliższy geograficznie oraz zmieniamy typ maszyny na "2-core"  ![Repo](images/environment/codespaces-machine.png)
4. Potwierdzamy nasz wybór i po momencie nasze środowsko jest dostępne

---
### Konfiguracja codespaces dla 
Pracujemy na otwartym w przeglądarce środowisku
1. Otwieramy terminal (Menu kabab w lewym rogu ekranu -> Terminal -> New terminal
2. Wykonujemy instalację podsatwowych rozszerzeń (można to zrobić również ręcznie) 
    ```
        code --install-extension ms-vscode.vscode-node-azure-pack
        code --install-extension humao.rest-client
    ```
3. Instalujemy core-tools dla azure
    ```
        sudo apt-get update
        sudo apt-get install azure-functions-core-tools-4
    ```
4. Zmieniamy wersję node na 18 (azure core tools w tym momencie nie pracują na 19)
    ```
        nvm install 18
        nvm use 18
    ```
5. sprawdzamy wersję node komendą node --version, powinna zostać pokazana v18.x

Nasze środowisko jest gotowe do pracy

Uwaga : po każdym uruchomieniu środowiska należy wybrać wersję Node instrukcją "nvm use 18"