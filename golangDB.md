# Go, Fiber we `pgxpool` bilen PostgreSQL CRUD Operasiýalary (Ýerli Ulgam)

Bu resminama, Go programmirleme dilinde `fiber` framework-i we `pgxpool` kütüphanesi bilen ýerli PostgreSQL maglumat bazasynda CRUD (Ýazma, Okamak, Täzelemek, Öçürmek) operasiýalaryny we `GetByID` işlevini nädip ýerine ýetirilýändigini düşündirýär. REST API döretmek üçin `fiber` ulanylýar we ähli operasiýalar ýerli ulgamda (localhost) işleýär.

## 1. Giriş

`pgxpool`, PostgreSQL bilen ýokary öndürijilikli we köp ulanyjyly baglanyşyklar üçin optimizirlenen Go kütüphanesidir. `fiber`, Go üçin ýeňil we çalt REST API framework-idir. Bu resminamada, bir “ulanyjy” (user) tablisasy bilen işlemek üçin ýerli REST API dörediler, we aşakdaky operasiýalar ýerine ýetiriler:

* **POST**: Täze ulanyjy döretmek
* **GET**: Ähli ulanyjylary almak
* **GET (by ID)**: Belli bir ulanyjyny ID boýunça almak
* **PUT**: Ulanyjynyň maglumatlaryny täzelemek
* **DELETE**: Ulanyjyny öçürmek

## 2. Zerurlyklar

Başlamazdan öň aşakdaky gurallar we enjamlar ýerli ulgamda taýyn bolmaly:

* **Go**: 1.20 ýa-da ondan ýokary wersiýa
* **PostgreSQL**: Ýerli kompýuterde işjeň (mysal: `localhost:5432`)
* **Modullar**:
  * `github.com/jackc/pgx/v5/pgxpool` (PostgreSQL baglanyşygy üçin)
  * `github.com/gofiber/fiber/v2` (REST API üçin)
* **Maglumatlar**:
  * PostgreSQL maglumat bazasynyň ýerli baglanyşyk maglumatlary (mysal: `postgres://postgres:password@localhost:5432/mydb`)

## 3. Proýektiň Gurluşy

Proýektiň faýl gurluşy aşakdaky ýaly bolar:

```
projectNouman/
├── main.go
├── go.mod
├── handlers/
│   └── user.go
├── models/
│   └── user.go
└── db/
    └── db.go
```

### 3.1 Maglumat Bazasy Tablisasynyň Döredilmegi

Ýerli PostgreSQL-de aşakdaky SQL buýrugy bilen `users` tablisasyny dörediň:

```sql
CREATE DATABASE mydb;
\c mydb;
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL
);
```

**Not**: PostgreSQL-i ýerli kompýuterde işjeňleşdirmek üçin:

* Ubuntu üçin: `sudo apt install postgresql`
* Başlatmak üçin: `sudo service postgresql start`
* Ýerli ulanyjy we parol sazlamalaryny ýerine ýetiriň (mysal: `postgres` ulanyjysy we paroly).

## 4. Proýektiň Kodlary

### 4.1 Modullaryň Ýüklenmegi

Proýekti başlatmak üçin Go moduly dörediň we zerur kütüphanalary ýükläň:

```bash
go mod init example.com/projectNouman
go get github.com/jackc/pgx/v5/pgxpool
go get github.com/gofiber/fiber/v2
```

### 4.2 Maglumat Bazasy Baglanyşygy (`db/db.go`)

`pgxpool` bilen ýerli PostgreSQL-e baglanmak üçin:

```go
package db

import (
    "context"
    "log"
    "github.com/jackc/pgx/v5/pgxpool"
)

func ConnectDB() *pgxpool.Pool {
    dbURL := "postgres://postgres:password@localhost:5432/mydb?sslmode=disable"
    pool, err := pgxpool.New(context.Background(), dbURL)
    if err != nil {
        log.Fatalf("Maglumat bazasyna birigip bolmady: %v", err)
    }

    err = pool.Ping(context.Background())
    if err != nil {
        log.Fatalf("Maglumat bazasy bilen baglanyşyk ýalňyş: %v", err)
    }

    log.Println("Maglumat bazasyna birikdi!")
    return pool
}
```

**Not**: `dbURL` içindäki `postgres`, `password`, we `mydb` ýerine öz ýerli sazlamalaryňyzy ulanyň. `sslmode=disable` ýerli baglanyşyklar üçin SSL-i ýapýar.

### 4.3 Ulanyjy Modeli (`models/user.go`)

Ulanyjy maglumatlarynyň strukturasy:

```go
package models

type User struct {
    ID    int    `json:"id"`
    Name  string `json:"name"`
    Email string `json:"email"`
}
```

### 4.4 Ulanyjy Handler-lary (`handlers/user.go`)

Bu faýlda CRUD operasiýalaryny ýerine ýetirýän handler funksiýalary ýerleşýär:

```go
package handlers

import (
    "context"
    "strconv"
    "example.com/projectNouman/models"
    "github.com/gofiber/fiber/v2"
    "github.com/jackc/pgx/v5/pgxpool"
)

type UserHandler struct {
    db *pgxpool.Pool
}

func NewUserHandler(db *pgxpool.Pool) *UserHandler {
    return &UserHandler{db: db}
}

func (h *UserHandler) CreateUser(c *fiber.Ctx) error {
    var user models.User
    if err := c.BodyParser(&user); err != nil {
        return c.Status(fiber.StatusBadRequest).JSON(fiber.Map{"error": err.Error()})
    }

    query := "INSERT INTO users (name, email) VALUES ($1, $2) RETURNING id"
    err := h.db.QueryRow(context.Background(), query, user.Name, user.Email).Scan(&user.ID)
    if err != nil {
        return c.Status(fiber.StatusInternalServerError).JSON(fiber.Map{"error": "Ulanyjy döredip bolmady"})
    }

    return c.Status(fiber.StatusCreated).JSON(user)
}

func (h *UserHandler) GetUsers(c *fiber.Ctx) error {
    rows, err := h.db.Query(context.Background(), "SELECT id, name, email FROM users")
    if err != nil {
        return c.Status(fiber.StatusInternalServerError).JSON(fiber.Map{"error": "Ulanyjylary alyp bolmady"})
    }
    defer rows.Close()

    users := []models.User{}
    for rows.Next() {
        var user models.User
        if err := rows.Scan(&user.ID, &user.Name, &user.Email); err != nil {
            return c.Status(fiber.StatusInternalServerError).JSON(fiber.Map{"error": "Maglumatlary okap bolmady"})
        }
        users = append(users, user)
    }

    return c.Status(fiber.StatusOK).JSON(users)
}

func (h *UserHandler) GetUserByID(c *fiber.Ctx) error {
    id, err := strconv.Atoi(c.Params("id"))
    if err != nil {
        return c.Status(fiber.StatusBadRequest).JSON(fiber.Map{"error": "Nädogry ID"})
    }

    var user models.User
    query := "SELECT id, name, email FROM users WHERE id = $1"
    err = h.db.QueryRow(context.Background(), query, id).Scan(&user.ID, &user.Name, &user.Email)
    if err != nil {
        return c.Status(fiber.StatusNotFound).JSON(fiber.Map{"error": "Ulanyjy tapylmady"})
    }

    return c.Status(fiber.StatusOK).JSON(user)
}

func (h *UserHandler) UpdateUser(c *fiber.Ctx) error {
    id, err := strconv.Atoi(c.Params("id"))
    if err != nil {
        return c.Status(fiber.StatusBadRequest).JSON(fiber.Map{"error": "Nädogry ID"})
    }

    var user models.User
    if err := c.BodyParser(&user); err != nil {
        return c.Status(fiber.StatusBadRequest).JSON(fiber.Map{"error": err.Error()})
    }

    query := "UPDATE users SET name = $1, email = $2 WHERE id = $3"
    result, err := h.db.Exec(context.Background(), query, user.Name, user.Email, id)
    if err != nil || result.RowsAffected() == 0 {
        return c.Status(fiber.StatusNotFound).JSON(fiber.Map{"error": "Ulanyjy täzelep bolmady"})
    }

    user.ID = id
    return c.Status(fiber.StatusOK).JSON(user)
}

func (h *UserHandler) DeleteUser(c *fiber.Ctx) error {
    id, err := strconv.Atoi(c.Params("id"))
    if err != nil {
        return c.Status(fiber.StatusBadRequest).JSON(fiber.Map{"error": "Nädogry ID"})
    }

    query := "DELETE FROM users WHERE id = $1"
    result, err := h.db.Exec(context.Background(), query, id)
    if err != nil || result.RowsAffected() == 0 {
        return c.Status(fiber.StatusNotFound).JSON(fiber.Map{"error": "Ulanyjy öçürilip bolmady"})
    }

    return c.Status(fiber.StatusOK).JSON(fiber.Map{"message": "Ulanyjy öçürildi"})
}
```

### 4.5 Esasy Programm (`main.go`)

Ýerli REST API-ni işledýän esasy programm:

```go
package main

import (
    "log"
    "example.com/projectNouman/db"
    "example.com/projectNouman/handlers"
    "github.com/gofiber/fiber/v2"
)

func main() {
    pool := db.ConnectDB()
    defer pool.Close()

    userHandler := handlers.NewUserHandler(pool)

    app := fiber.New()

    // REST API endpoint-lary
    app.Post("/users", userHandler.CreateUser)
    app.Get("/users", userHandler.GetUsers)
    app.Get("/users/:id", userHandler.GetUserByID)
    app.Put("/users/:id", userHandler.UpdateUser)
    app.Delete("/users/:id", userHandler.DeleteUser)

    log.Fatal(app.Listen(":8080"))
}
```

## 5. Ýerli Synag

API-ni ýerli ulgamda synamak üçin `curl` ýa-da Postman ulanyp bilersiňiz. Mysallar:

1. **Täze Ulanyjy Döretmek (POST)**:
   ```bash
   curl -X POST http://localhost:8080/users -H "Content-Type: application/json" -d '{"name":"Ahmet","email":"ahmet@example.com"}'
   ```
2. **Ähli Ulanyjylary Almak (GET)**:
   ```bash
   curl http://localhost:8080/users
   ```
3. **Ulanyjyny ID boýunça Almak (GET)**:
   ```bash
   curl http://localhost:8080/users/1
   ```
4. **Ulanyjyny Täzelemek (PUT)**:
   ```bash
   curl -X PUT http://localhost:8080/users/1 -H "Content-Type: application/json" -d '{"name":"Ahmet Y","email":"ahmet.y@example.com"}'
   ```
5. **Ulanyjyny Öçürmek (DELETE)**:
   ```bash
   curl -X DELETE http://localhost:8080/users/1
   ```

### 5.1 Ýerli PostgreSQL-iň Işledilmegi

* PostgreSQL-iň ýerli kompýuterde işleýändigini barlaň: `psql -U postgres`
* Maglumat bazasyny dörediň we tablisany ýerleşdiriň (bölüm 3.1-e serediň).
* `main.go` faýlyny işledip API-ni başlatmak üçin:
  ```bash
  go run main.go
  ```

## 6. Howpsuzlyk we Öndürijilik Çäreleri

* **Ýerli Baglanyşyk**: `sslmode=disable` ýerli ulgamda howpsuzlyk talaplary pes bolany üçin ulanylýar. Uzak serwer üçin `sslmode=require` goşuň.
* **Parol Howpsuzlygy**: Ýerli ulgamda parol açyk ýazylandyr, ýöne hakyky proýektlerde `.env` faýly we `godotenv` kütüphanesi ulanyň.
* **Hatalaryň Dolandyrylmagy**: Kod, nädogry ID ýa-da dublikat e-poçta ýaly ýagdaýlary dolandyrýar.
* **Pool Sazlamalary**: `pgxpool` üçin maksimum baglanyşyk sanyny sazlaň (mysal: `pool.Config().MaxConns = 10`) ýerli ulgamyň çäklerini göz öňünde tutup.

## 7. Netije

Bu resminama, Go, `fiber` we `pgxpool` bilen ýerli PostgreSQL maglumat bazasynda CRUD operasiýalaryny ýerine ýetirmegiň we REST API döretmegiň ýoluny görkezýär. Kod mysallary, ýerli ulgamda IoT ýa-da başga proýektler üçin başlagyç nokady hödürleýär. Goşmaça sazlamalar ýa-da optimizasiýalar gerek bolsa, `pgx` we `fiber` resminamalaryna göz aýlap bilersiňiz.
