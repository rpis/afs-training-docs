### Ćwiczenie 4 - użycie API-GW

1. Wybieramy utworzoną funkcję w portalu Azure i przechodzimy do menu "Api Management"
2. Możesz wybrać istniejący już komponent albo utworzyć nowy, utwórz nowy
   1. Wybierz region, nadajemy nazwe, ustalamy workspace nam na dowolną nazwę i wpisujemy email
   2. Wybierz (nie zmieniaj) Pricing tier. Powinien dla naszych celów być "Consumption" ![Create APIM](images/functions/ex4/create-apim.png)
   3. Przejdz dalej, odznacz Application Insights i potwierź "Review and create" i ponownie "Create"
   4. Po momencie zostanie założona instancja apim
   5. Na zakładce w API Management w twojej funkcji zostanie pokazana zakładka wyboru API, klikamy "Link API" ![Create API](images/functions/ex4/create-api.png)
   6. Zostanie przedstawiony wybór funcji do wystawienia jako api ![Func list](images/functions/ex4/choose-functions.png)
   7. Potwierdźć i zobaczysz widok ![Create from function APP](images/functions/ex4/create-from-function-app.png)
   8. Zatwierdź i zobaczysz utworzone api ![Created API](images/functions/ex4/created-api.png)
   9. Odznacz set subsription required na zakładce Settings  ![Setup](images/functions/ex4/set-subsription-required-false.png)
   10. Otwórz codespaces utworzony dla funcji i przetestuj działanie używając swojego adresu api managementu
   11. dodaj zabezpieczenia funcji (nie chcemy, żeby poza apim była wołana...)
       1.  Uruchom środowisko Codespace dla funkcji
       2.  Zmień aktualny authLevel z anonymous na function dla 3 utworzonych funkcji (pliki function.json)
       3.  Wdróż funkcję
       4.  Sprawdz, że wywołanioa którymi testowalismy bezpośrednio funkcję zwaracają błąd 401 (jak nie zwracają to coś jest nie tak)
       5.  Sprawdz wywołania przez apim, oczekujemy poprawnych wywołań
   

---


