# Tasks

Laravel Backend demo alkalmazás Sanctum authentikációval és minimális jogosultság kezeléssel.

## Sanctum

<a href="https://laravel.com/docs/10.x/sanctum#main-content">https://laravel.com/docs/10.x/sanctum#main-content</a>

A Sanctum egy biztonsági kiegészítő a Laravel alkalmazásokhoz, amely lehetővé teszi a fejlesztők számára, hogy egyszerűen beállítsák a hitelesítési és autorizációs munkafolyamatokat a SPA (Single Page Application) és a mobilalkalmazások számára.

### 1. Projekt létrehozása

```
laravel new laravel_tasks
cd laravel_tasks
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

A ```php artisan vendor:publish``` parancs általánosan használatos a Laravel alkalmazásokban, hogy a fejlesztők közzétegyék a harmadik féltől származó komponensek (pl. biztonsági kiegészítők, nézetek, stb.) konfigurációs fájljait a projektben.

A ```php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"``` parancs a Laravel alkalmazásban használt Sanctum biztonsági kiegészítő konfigurációs fájljainak közzétételét jelenti.
A parancs a Sanctum konfigurációs fájljainak másolatát hozza létre a projekt config könyvtárában, amelyeket a fejlesztők személyre szabhatnak. Ha a konfigurációs fájlokat módosítja a fejlesztő, a módosításokat nem veszti el a későbbi frissítések során.

### 2. Adatbázis beállítások

- Adatbázis létrehozása: laravel_tasks
- Adatbázis elérés beállítása a .env fájlban.
- ```php artisan migrate``` parancs futtatása

### 3. Response-okat tartalmazó trait létrehozása

- App/Traits mappa létrehozása
- benne: HttpResponses.php

A PHP-ban a trait olyan eszköz, amely lehetővé teszi a programozó számára, hogy kódblokkokat (metódusokat és változókat) használjon több osztályban, anélkül, hogy öröklést kellene alkalmaznia.

A traitek használata a kód áttekinthetőségének és felhasználhatóságának javítását szolgálja. Használatával elkerülhetők a duplikált kódot, ami általában különböző osztályokban fordul elő, és helyettük egy általános trait-ot hozhatnak létre, amelyet több osztályban is felhasználhatnak.

Trait használata: az osztályon belül: ```use HttpResponses;``` 

### 4. Modell, controller, migration, factory létrehozása

```
php artisan make:controller AuthController
php artisan make:model -cfm --api Task
```

A --api használatával (szemben a -r kapcsolóval) api controller függvények jönnek látre, amelyek nem tartalmazzák a backend esetén szükségtelen edit és create metódusokat.

### 5. AuthController metódusok létrehozása (üresen vagy bennük csak egy return "Teszt szöveg"):
- register
- login
- logout

### 6. Route-ok definiálása
- routes/api.php
- ellenőrzés: php artisan route:list

### 7. ThunderClient (vagy Postman) útvonalak tesztelése
- POST register
- POST login
- POST logout

### 8. Saját StoreUserRequest  
- létrehozás: ```php artisan make:request StoreUserRequest```
- rules() metódus megírása
- authorize() metódusban return true; 

A Laravel `Request` egy egységes módja a kliens oldali adatok ellenőrzésének és feldolgozásának a Laravel alkalmazásban. A Laravel kérés létrehozása után a fejlesztők a kérés osztályában definiálhatják az adatok ellenőrzésének szabályait, például mezők jelenlétét, érvényességét, stb.

Az `authorize()` metódus a Laravel Request osztályában szolgál az adatok ellenőrzésére, mielőtt azok feldolgozásra kerülnének a vezérlőben vagy más alkalmazás komponensben.
A metódus a fejlesztők által meghatározott szabályok alapján ellenőrzi, hogy a kliens oldali adatok érvényesek-e, és hogy a felhasználó jogosult-e az adatok elküldésére. Ha a szabályok nem teljesülnek, akkor a authorize() metódus visszautasítja a kérést, és hibát jelz a felhasználónak.
Például, ha egy kérés az új blogbejegyzés tárolására szolgál, akkor a authorize() metódus ellenőrizheti, hogy a felhasználó be van-e jelentkezve, és hogy jogosult-e új bejegyzést létrehozni. Az authorize() metódus használata elősegíti a biztonságos és egységes adatkezelést és elősegíti a kliens oldali adatok ellenőrzésének centralizálását egy helyen. 

### 9. Regisztráció megvalósítása
- AuthControllerben `register()` metódus megírása
- ThunderClient register útvonal tesztelése
    - Header beállítása
    - Body JSON
- a user létrejön a users táblában
- a hozzá tartozó token a `personal_access_tokens` táblában

### 10. Login
- `php artisan make:request LoginUserRequest`
- `rules()` metódus megírása
- `authorize()` metódusban `return true;` 
- AuthControllerben `login()` metódus megírása
- ThunderClient login útvonal tesztelése
    - Header beállítása
    - Body JSON

### 11. Route-ok védelme a sanctummal a `api.php`-ben

- group middleware lérehozáse
- a registeren és a loginon kívül az összes többi route áthelyezése a middlewarebe
- tesztelés a `TaskController::index()` metódusával (adjon vissza csak egy "teszt" szöveget)
- ThunderClient GET:http://127.0.0.1:8000/api/tasks próbáljuk mehívni -> Status: 401
- Header beállítása itt is kell!!!
- ThunderClient-ben Auth/Bearer-be bemásolni a loginnál kapott token-t -> Status: 200

A **"Bearer"** az egyik leggyakrabban használt autentikációs típus a RESTful API-kban. A Bearer autentikáció olyan autentikációs folyamat, amely a kérésnek tartalmaz egy "Authorization" fejlécet, amely egy tokenet tartalmaz, amelyet a szerver használ a felhasználó azonosítására.

A **token** lehet bármilyen formátumú, amely megfelel a szerver által elfogadott biztonsági követelményeknek, és a szerver előre megadott eljárást használ a token érvényességének ellenőrzésére. Ha a token érvényes, akkor a szerver megengedi a kérés teljesítését. Ha a token nem érvényes, akkor a szerver elutasítja a kérés teljesítését.

A Bearer autentikáció használata megkönnyíti a fejlesztőknek a RESTful API-k biztonságának beállítását, mivel a token biztonságos adatforgalmat biztosít a kliens és a szerver között, és elkerüli a jelszavak átadását a kliens és a szerver között.

### 12. Tasks adatok generálása

- migration
    - `php artisan make:migration create_task_table` - **nem kell**, a modell lérehozásakor -m kapcsoló létrehzota
- `Task` modelben: `$fillable` és `user()` metódus
- factory
    - `php artisan make:factory TaskFactory` - **nem kell**, a modell lérehozásakor -f kapcsoló létrehzota
    - TaskFactory megírása
- `databaseSeeder.php`-ben `factory()`-k meghívása
- `php artisan migrate --seed`

### 13. TaskController CRUD megvalósítása

- `index()`
    - csak azokat a Taskokat listázzuk ki, amelyek az autentikált userhez tartoznak

    Megjegyzés:
    A visszadott collection automatikus json-ná alakítása helyett saját "szabványosabb" json szerkezet előállítása:
    
    - `php artisan make:resource TaskResource`
    - `App/Http/Resources/TaskResource`
    - `toArray()` metódus módosítása
    - `TaskController::index()` módosításban az új TaskResource használata

    A **Resource** osztályok a RESTful API adatok formázására szolgálnak. A Resource osztályok a model adatait átalakítják a kliens számára könnyen feldolgozható formátumba, például JSON vagy XML formátumba. A Resource osztályok segítenek a logikai elválasztásban a modell adatainak formázásától a vezérlő logikájától, amelyek a kérésre ad választ.
    
    A Resource osztályok használata javítja a RESTful API fejlesztési folyamatának szervezettségét és tisztaságát, és lehetővé teszi a könnyű adatfrissítést a model módosításakor.

- `create()` 
    - ez a metódus API eseetén nem kell, csak ha van űrlapunk is    
    - törölni

- `store()`
    - előtte validáció: `php artisan make:request StoreTaskRequest`
    - ThunderClient POST:http://127.0.0.1:8000/api/tasks
        - Header
        - Auth
        - Body -> Json

- `show(Task $task)`
    - Route Model Binding miatt a keresést a háttérben megoldja a Laravel
    - csak azokat a Taskokat engedjük amelyek az autentikált userhez tartoznak
    - `use HttpResponses;` trait kell az osztály elejére az `error()` message miatt

- `edit()` 
    - ez a metódus API eseetén nem kell, csak ha van frontend is    
    - törölni

- `update()`
    - PUT és PATCH metódushoz is jó
    
    A HTTP PATCH és a PUT metódus között a különbség abban rejlik, hogy miként javítják vagy frissítik a szerver által tárolt adatokat.
    A **PUT** metódus a teljes adatot helyettesíti a korábbi adatokkal, amikor frissíti azokat. Ez azt jelenti, hogy ha a kliensnek csak bizonyos adatokat kell frissítenie, akkor az összes többi adatot is el kell küldenie a szervernek a frissítéshez, még akkor is, ha azok nem változtak.
    A **PATCH** metódus csak az adatok frissítését teszi lehetővé, amelyek valóban változtak. Ez jelentősen kisebb adatforgalmat eredményezhet, mivel a kliensnek csak a változó adatokat kell elküldenie a szervernek.

    - csak akkor engedjük ha a Task az autentikált userhez tartoznak

- `delete()`
    - csak akkor engedjük ha a Task az autentikált userhez tartoznak

### 14. logout megvalósítása

- `currentAccessToken()`-t nem ismeri az intellisence, de működik
- tesztelésnél érdemes a personal_access_tokens tábla tartalmát figyelni

### 15. token atomatikus lejáratának beállítása

- sceduler beállítása
    - `App/Console/Kernel.php`
        - `schedule()` metódus megírása
    - `php artisan schedule:list`
    - `php artisan schedule:work` -> tesztelési céllal - 1 percenként lefut

### 16. Jogosultságok refaktorálása Policy használatával

<a href="https://laravel.com/docs/10.x/authorization#creating-policies">https://laravel.com/docs/10.x/authorization#creating-policies</a>

- is_admin mező hozzáadása a User modellhez
    
    ```
    php artisan make:migration add_is_admin_to_users_table
    php artisan migrate
    ```

- `TaskPolicy` osztály létrehozása

    `php artisan make:policy TaskPolicy --model=Task`

- `TaskPoilicy` metódusok szerkesztése

- `TaskController` osztályban a metódusok átdolgozása

- `TaskPolicy` osztályban a `before()` metódus hozzáadása

- Az admin user-nek mindenhez legyen joga: `before()` metódus hozzáadása a `TaskPolicy` osztályhoz.

    <a href="https://laravel.com/docs/10.x/authorization#policy-filters">https://laravel.com/docs/10.x/authorization#policy-filters</a>



## ThunderClient export
`/thunder-collection_Laravel Tasks API.json`

# Forrás

<a href="https://www.youtube.com/watch?v=TzAJfjCn7Ks">https://www.youtube.com/watch?v=TzAJfjCn7Ks</a>

<a href="https://github.com/codewithdary/laravel-sanctum-tutorial">https://github.com/codewithdary/laravel-sanctum-tutorial</a>
