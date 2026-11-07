# Backend Development Fundamentals - Lab 3
## Project: REST API, Query Parameters, Validation and Debugging

This repository contains the complete implementation for **Lab 3: REST API, Query Parameters, Validation and Debugging**. Built using **Node.js**, **Express.js**, and **SQLite**, it provides a robust, production-grade RESTful API for managing a library book catalog. 

The project extends basic CRUD capabilities with advanced query parameter filtering, SQL-injection-safe dynamic sorting, pagination, multi-layer defensive validation for route and body parameters, and centralized error logging and debugging middleware.

---

## 👤 Author Information

- **Name:** Giddaluru Revathi
- **Registration Number:** 252U1R6077
- **Section:** CSE AIML-2B

---

## 🔗 Project Links

- 📂 **GitHub Repository:** [https://github.com/giddalururevathi1-tech/Lab_Report_1/tree/lab-3/lab-03](https://github.com/giddalururevathi1-tech/Lab_Report_1/tree/lab-3/lab-03)
- 🚀 **Live Deployment URL:** `[Insert Deployment Link (e.g., Vercel / Render) if deployed]`
- 💻 **Localhost Server:** [http://localhost:3000/](http://localhost:3000/)
- 📚 **Localhost Books API:** [http://localhost:3000/books](http://localhost:3000/books)

> [!NOTE]
> If the project has not been deployed to a remote cloud host, it can be run and tested completely in your local environment using the installation and run commands below.

---

## 🛠️ Technologies Used

- **Node.js:** Server-side JavaScript runtime environment for executing the application and test suites.
- **Express.js:** Minimalist, flexible web application framework providing routing, body-parsing, and middleware pipelines.
- **SQLite (sqlite3 & sqlite):** Lightweight, serverless relational database engine utilizing parameterized SQL queries for persistent data storage.
- **Nodemon:** Development utility providing automatic server restarts upon file changes.

---

## 🚀 Installation & Run Commands

### 1. Clone & Navigate to Project Directory
```bash
cd "Lab_Report 3"
```

### 2. Install Project Dependencies
```bash
npm install
```

### 3. Start Development Server (with Auto-Reload)
```bash
npm run dev
```

### 4. Start Production Server
```bash
npm start
```
*The Express server listens on `http://localhost:3000/`.*

### 5. Execute Automated Test Suite
```bash
npm test
```
*Runs the built-in automated test suite verifying all 17 test cases covering endpoints, query parameters, validation rules, and error handling.*

---

## 🌐 API Endpoints Table

| HTTP Method | Route Endpoint | Description | Expected Status Codes |
| :--- | :--- | :--- | :--- |
| **GET** | `/` | Root route returning project status message | `200 OK` |
| **GET** | `/books` | Retrieve books with optional filtering, sorting & pagination | `200 OK`, `400 Bad Request` |
| **GET** | `/books/:bookId` | Retrieve a single book record by ID | `200 OK`, `400 Bad Request`, `404 Not Found` |
| **POST** | `/books` | Create a new book record with validated payload | `201 Created`, `400 Bad Request` |
| **PUT** | `/books/:bookId` | Update an existing book's details by ID | `200 OK`, `400 Bad Request`, `404 Not Found` |
| **DELETE** | `/books/:bookId` | Delete a book record by ID | `200 OK`, `400 Bad Request`, `404 Not Found` |

---

## 🔍 `GET /books` Query Parameters

The `GET /books` endpoint supports 7 query parameters that can be used independently or combined:

| Parameter | Type | Default Value | Description | Example Query URL |
| :--- | :--- | :---: | :--- | :--- |
| `search_q` | String | `""` | Case-insensitive partial title search using SQL `LIKE ?` | `/books?search_q=Harry` |
| `order_by` | String | `"id"` | Column to sort by (`id`, `title`, `author`, `price`, `pages`) | `/books?order_by=price` |
| `order` | String | `"ASC"` | Sort direction: `ASC` or `DESC` (case-insensitive) | `/books?order=DESC` |
| `limit` | Integer | `10` | Maximum number of records to return (must be $> 0$) | `/books?limit=5` |
| `offset` | Integer | `0` | Number of records to skip for pagination (must be $\ge 0$) | `/books?offset=3` |
| `min_price` | Number | None | Filter books with price greater than or equal to value | `/books?min_price=300` |
| `max_price` | Number | None | Filter books with price less than or equal to value | `/books?max_price=450` |

---

## 🛡️ Validation Rules & HTTP Status Codes

### 1. Validation Rules
- **Query Parameter Validation (`GET /books`):**
  - `order_by`: Must match one of the allowed columns: `['id', 'title', 'author', 'price', 'pages']`. Disallowed columns return HTTP `400 Bad Request`.
  - `order`: Must be either `'ASC'` or `'DESC'` (case-insensitive). Invalid values return HTTP `400 Bad Request`.
  - `limit`: Must be a positive integer ($> 0$). Strings or values $\le 0$ return HTTP `400 Bad Request`.
  - `offset`: Must be a non-negative integer ($\ge 0$). Invalid values return HTTP `400 Bad Request`.
  - `min_price` / `max_price`: Must be non-negative numeric values. If both are passed, `min_price` cannot exceed `max_price`.
- **Route Parameter Validation (`:bookId`):**
  - Must be a valid positive integer. Non-integer or negative values on `GET`, `PUT`, and `DELETE` return HTTP `400 Bad Request`.
- **Request Body Validation (`POST` & `PUT`):**
  - `title`: Required non-empty string.
  - `author`: Required non-empty string.
  - `price`: Required non-negative number.
  - `pages`: If provided, must be a positive integer.

### 2. Expected HTTP Status Codes

| HTTP Status Code | Meaning | Typical Usage |
| :--- | :--- | :--- |
| **`200 OK`** | Success | Returned for successful `GET`, `PUT`, and `DELETE` requests. |
| **`201 Created`** | Created | Returned when a new book is successfully added via `POST /books`. |
| **`400 Bad Request`** | Validation Error | Returned when query parameters, route parameters, or request body fail validation checks. |
| **`404 Not Found`** | Resource Missing | Returned when accessing a non-existent `bookId` or undefined endpoint. |
| **`500 Internal Server Error`** | Server Exception | Returned by the centralized error handler in case of unexpected runtime failures. |

---

## 📸 Lab Execution & Verification Screenshots

The following verification table highlights the Postman test runs and terminal logs validating the query parameters, validation error handling, and CRUD features implemented for Lab 3:

| Execution Screenshot | HTTP Method & Endpoint | Test Scenario & Verification | Expected Status |
| :---: | :--- | :--- | :---: |
| <img src="Screenshot (361).png" alt="Default Books List" width="400" /> | `GET /books` | Default retrieval of books sorted by `id ASC` with default pagination (`limit=10`, `offset=0`) | **`200 OK`** |
| <img src="Screenshot (364).png" alt="Single Book by ID" width="400" /> | `GET /books/2` | Retrieve single book record by ID (`1984` by George Orwell) | **`200 OK`** |
| <img src="Screenshot (365).png" alt="Search Title 1984" width="400" /> | `GET /books?search_q=1984` | Partial title search using SQL `LIKE ?` returning "1984" | **`200 OK`** |
| <img src="Screenshot (378).png" alt="Search Title Harry" width="400" /> | `GET /books?search_q=Harry` | Partial title search returning "Harry Potter and the Philosopher's Stone" | **`200 OK`** |
| <img src="Screenshot (366).png" alt="Sort by Price Descending" width="400" /> | `GET /books?order_by=price&order=DESC` | Safe dynamic sorting by price in descending order | **`200 OK`** |
| <img src="Screenshot (379).png" alt="Sort by Price Headers View" width="400" /> | `GET /books?order_by=price&order=DESC` | Sorting verification with Postman response headers tab visible | **`200 OK`** |
| <img src="Screenshot (367).png" alt="Price Range Filter 100 to 500" width="400" /> | `GET /books?min_price=100&max_price=500` | Price range filtering using `price >= ? AND price <= ?` | **`200 OK`** |
| <img src="Screenshot (380).png" alt="Price Range Scrolled View" width="400" /> | `GET /books?min_price=100&max_price=500` | Scrolled response confirming all filtered books are within ₹100 - ₹500 | **`200 OK`** |
| <img src="Screenshot (368).png" alt="Pagination Limit 5 Offset 0" width="400" /> | `GET /books?limit=5&offset=0` | Pagination query returning the first 5 records using SQL `LIMIT 5 OFFSET 0` | **`200 OK`** |
| <img src="Screenshot (369).png" alt="POST Create Book Success" width="400" /> | `POST /books` | Successful creation of a new book record returning auto-generated ID | **`201 Created`** |
| <img src="Screenshot (375).png" alt="Validation Missing Title" width="400" /> | `POST /books` | Input validation rejection when `title` field is missing from payload | **`400 Bad Request`** |
| <img src="Screenshot (377).png" alt="Validation Missing Author" width="400" /> | `POST /books` | Input validation rejection when `author` field is missing from payload | **`400 Bad Request`** |
| <img src="Screenshot (374).png" alt="DELETE Missing Book 404" width="400" /> | `DELETE /books/3` | Route handling returning structured 404 when target book ID does not exist | **`404 Not Found`** |

---

## 📮 Postman Test Cases & Detailed Verification

### A. Successful Requests & Query Parameter Scenarios

#### 1. Retrieve All Books (Default Sorting `id ASC` & Default Limit `10`)
- **HTTP Method:** `GET`
- **Request URL:** `http://localhost:3000/books`
- **Status Code:** `200 OK`
- **Description:** Returns the complete list of books sorted by `id ASC` using default pagination parameters (`limit=10`, `offset=0`).

<p align="center">
  <img src="Screenshot (361).png" alt="Default Books List" width="850" />
</p>

---

#### 2. Retrieve a Single Book by ID
- **HTTP Method:** `GET`
- **Request URL:** `http://localhost:3000/books/2`
- **Status Code:** `200 OK`
- **Description:** Retrieves the single book record for ID `2` (*1984* by George Orwell).

<p align="center">
  <img src="Screenshot (364).png" alt="Single Book by ID" width="850" />
</p>

---

#### 3. Search Books by Title (`search_q=1984`)
- **HTTP Method:** `GET`
- **Request URL:** `http://localhost:3000/books?search_q=1984`
- **Status Code:** `200 OK`
- **Description:** Performs partial title search using SQL `LIKE ?` and returns matching book records.

<p align="center">
  <img src="Screenshot (365).png" alt="Search Title 1984" width="850" />
</p>

---

#### 4. Search Books by Partial Title (`search_q=Harry`)
- **HTTP Method:** `GET`
- **Request URL:** `http://localhost:3000/books?search_q=Harry`
- **Status Code:** `200 OK`
- **Description:** Returns all books containing "Harry" in the title.

<p align="center">
  <img src="Screenshot (378).png" alt="Search Title Harry" width="850" />
</p>

---

#### 5. Sorting Books by Price Descending (`order_by=price&order=DESC`)
- **HTTP Method:** `GET`
- **Request URL:** `http://localhost:3000/books?order_by=price&order=DESC`
- **Status Code:** `200 OK`
- **Description:** Validates allowed column allowlist and sorts records from highest to lowest price.

<p align="center">
  <img src="Screenshot (366).png" alt="Sort by Price Descending" width="850" />
</p>

*(Additional verification view showing Postman response headers tab):*
<p align="center">
  <img src="Screenshot (379).png" alt="Sort by Price Descending - Alternate View" width="850" />
</p>

---

#### 6. Filter Books by Price Range (`min_price=100&max_price=500`)
- **HTTP Method:** `GET`
- **Request URL:** `http://localhost:3000/books?min_price=100&max_price=500`
- **Status Code:** `200 OK`
- **Description:** Applies parameterized SQL `price >= ? AND price <= ?` to filter books priced between ₹100 and ₹500.

<p align="center">
  <img src="Screenshot (367).png" alt="Price Range Filter" width="850" />
</p>

*(Additional scrolled view confirming all items match range filter criteria):*
<p align="center">
  <img src="Screenshot (380).png" alt="Price Range Filter - Scrolled View" width="850" />
</p>

---

#### 7. Pagination with Limit and Offset (`limit=5&offset=0`)
- **HTTP Method:** `GET`
- **Request URL:** `http://localhost:3000/books?limit=5&offset=0`
- **Status Code:** `200 OK`
- **Description:** Returns the first 5 records using SQL `LIMIT 5 OFFSET 0`.

<p align="center">
  <img src="Screenshot (368).png" alt="Pagination Limit 5 Offset 0" width="850" />
</p>

---

#### 8. Create a New Book (`POST /books`)
- **HTTP Method:** `POST`
- **Request URL:** `http://localhost:3000/books`
- **Headers:** `Content-Type: application/json`
- **Status Code:** `201 Created`
- **Request Body (JSON):**
  ```json
  {
    "title": "Harry Potter",
    "author": "J.K. Rowling",
    "price": 450,
    "pages": 400
  }
  ```
- **Response Body:**
  ```json
  {
    "message": "Book Added Successfully",
    "bookId": 31
  }
  ```

<p align="center">
  <img src="Screenshot (369).png" alt="Successful POST Book" width="850" />
</p>

---

### B. Validation Failures & Error Handling Scenarios

#### 9. POST Validation Error: Missing Title
- **HTTP Method:** `POST`
- **Request URL:** `http://localhost:3000/books`
- **Headers:** `Content-Type: application/json`
- **Status Code:** `400 Bad Request`
- **Request Body (Missing `title`):**
  ```json
  {
    "author": "J.K. Rowling",
    "price": 450,
    "pages": 400
  }
  ```
- **Response Body:**
  ```json
  {
    "error": "Title is required and must be a non-empty string"
  }
  ```

<p align="center">
  <img src="Screenshot (375).png" alt="POST Missing Title Validation" width="850" />
</p>

---

#### 10. POST Validation Error: Missing Author
- **HTTP Method:** `POST`
- **Request URL:** `http://localhost:3000/books`
- **Headers:** `Content-Type: application/json`
- **Status Code:** `400 Bad Request`
- **Request Body (Missing `author`):**
  ```json
  {
    "title": "Harry Potter",
    "price": 450,
    "pages": 400
  }
  ```
- **Response Body:**
  ```json
  {
    "error": "Author is required and must be a non-empty string"
  }
  ```

<p align="center">
  <img src="Screenshot (377).png" alt="POST Missing Author Validation" width="850" />
</p>

---

#### 11. Resource Not Found Error (`DELETE /books/3`)
- **HTTP Method:** `DELETE`
- **Request URL:** `http://localhost:3000/books/3`
- **Status Code:** `404 Not Found`
- **Description:** Returns standard 404 error when attempting to delete a book ID that does not exist in the database.
- **Response Body:**
  ```json
  {
    "message": "Book not found"
  }
  ```

<p align="center">
  <img src="Screenshot (374).png" alt="DELETE Not Found 404" width="850" />
</p>

---

## 🔧 Debugging Issues & Solutions

During development and testing of Lab 3, several architectural and behavioral challenges were diagnosed and resolved:

### 1. SQL Injection Risk in Dynamic `ORDER BY` Clauses
- **Issue:** SQLite prepared statements (`?` placeholders) only parameterize literal values (such as search strings or numbers). Column identifiers in `ORDER BY ?` cannot be parameterized directly with `?`. Directly concatenating `req.query.order_by` into the SQL query created a severe SQL Injection vulnerability.
- **Solution:** Implemented strict allowlist validation using `ALLOWED_ORDER_BY_COLUMNS = ['id', 'title', 'author', 'price', 'pages']` and allowed sort directions `['ASC', 'DESC']`. Any parameter outside this allowlist is rejected immediately with HTTP `400 Bad Request` before the SQL query is constructed.

### 2. Flaky Automated Tests Due to Hardcoded ID Assertions
- **Issue:** Automated tests for default listing and pagination initially failed when manual CRUD operations (`DELETE` or `POST`) were executed in Postman, because tests hardcoded static assumptions (e.g., `res.data[0].id === 1` and `res.data[0].id === 3` for `offset=2`).
- **Solution:** Refactored the test suite in `test.js` to validate relative criteria rather than hardcoded IDs:
  - Validated that every item's `id` is strictly ascending (`res.data[i].id < res.data[i+1].id`).
  - Validated pagination by fetching the base set and confirming that the items at `offset=2` precisely matched indices `[2]`, `[3]`, and `[4]` of the unshifted query result.
  - Added auto-cleanup logic to delete test-created records after verification.

### 3. Type Coercion & Query Parameter Sanitation
- **Issue:** Values parsed from `req.query` are delivered as strings (e.g., `"300"` or `"invalid"`). Passing raw strings directly to SQLite pagination (`LIMIT "abc"`) caused unpredictable query behavior or uncaught exceptions.
- **Solution:** Implemented explicit numeric parsing (`Number()`) paired with `Number.isInteger()` checks. Defined safe fallback defaults (`limit = 10`, `offset = 0`, `order_by = 'id'`, `order = 'ASC'`) and returned standard HTTP `400` errors for malformed numeric inputs.

### 4. Database Race Conditions on Initial Server Startup
- **Issue:** Incoming HTTP requests arriving immediately upon server startup occasionally failed because asynchronous SQLite table creation and seeding were still in progress.
- **Solution:** Wrapped the asynchronous database setup in a promise (`dbPromise`) and attached a prerequisite middleware:
  ```javascript
  app.use(async (req, res, next) => {
    if (!db) await dbPromise;
    next();
  });
  ```
  This ensures the database connection and schema are fully initialized before any route executes.

### 5. Granular Field Validation in Request Bodies
- **Issue:** General validation checks returned vague errors when request payloads had empty strings (`""`) or missing keys, making client debugging difficult.
- **Solution:** Created a modular `validateBookPayload()` helper function that validates `title`, `author`, `price`, and `pages` individually in priority order, returning precise, user-friendly JSON error messages.

---

## 📋 Automated Test Suite Verification

All 17 automated integration tests pass successfully covering query parameters, edge-case validations, error responses, and CRUD lifecycle:

```bash
npm test
```

```text
==================================================
 Lab 3: REST API, Query Parameters & Validation   
==================================================
Server running at http://localhost:3000/
[GET]  /                                              ✅ [PASS] Root route returns Lab 3 title
[GET]  /books                                         ✅ [PASS] Default GET /books returns books sorted by id ASC
[GET]  /books?search_q=Harry                          ✅ [PASS] Query param 'search_q' performs partial title search
[GET]  /books?order_by=price&order=DESC&limit=3       ✅ [PASS] Query params 'order_by=price' and 'order=DESC' sort correctly
[GET]  /books?limit=3&offset=2                        ✅ [PASS] Query params 'limit=3&offset=2' paginates correctly
[GET]  /books?min_price=300&max_price=400             ✅ [PASS] Query params 'min_price' and 'max_price' filter prices correctly
[GET]  /books?order_by=password                       ✅ [PASS] Validation rejects invalid 'order_by' column with HTTP 400
[GET]  /books?order=DIAGONAL                          ✅ [PASS] Validation rejects invalid 'order' with HTTP 400
[GET]  /books?limit=-2                                ✅ [PASS] Validation rejects negative 'limit' with HTTP 400
[GET]  /books?min_price=600&max_price=200             ✅ [PASS] Validation rejects min_price > max_price with HTTP 400
[GET]  /books/abc                                     ✅ [PASS] Validation rejects non-integer :bookId with HTTP 400
[GET]  /books/9999                                    ✅ [PASS] Returns 404 for non-existent book ID
[GET]  /api/undefined-route                           ✅ [PASS] Catch-all middleware returns 404 for undefined endpoints
[POST] /books (missing title)                         ✅ [PASS] POST /books rejects request with missing title (HTTP 400)
[POST] /books (missing author)                        ✅ [PASS] POST /books rejects request with missing author (HTTP 400)
[POST] /books (non-numeric price)                     ✅ [PASS] POST /books rejects request with non-numeric price (HTTP 400)
[POST] /books (valid book payload)                    ✅ [PASS] POST /books creates book successfully and returns HTTP 201
--------------------------------------------------
Results: 17/17 tests passed.
==================================================
```

