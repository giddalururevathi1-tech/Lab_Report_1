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

## 📮 Postman Test Cases (Successful & Failed Requests)

### A. Successful Test Cases

#### 1. Default Books Retrieval (Default Sorting & Pagination)
- **Method:** `GET`
- **URL:** `http://localhost:3000/books`
- **Expected Status:** `200 OK`
- **Expected Output:** Array of books sorted by `id ASC` with default limit of 10.

#### 2. Search & Filter by Price Range
- **Method:** `GET`
- **URL:** `http://localhost:3000/books?search_q=The&min_price=200&max_price=400`
- **Expected Status:** `200 OK`
- **Expected Output:** Books containing "The" in title priced between ₹200 and ₹400.

#### 3. Sorting & Pagination
- **Method:** `GET`
- **URL:** `http://localhost:3000/books?order_by=price&order=DESC&limit=3&offset=0`
- **Expected Status:** `200 OK`
- **Expected Output:** Top 3 most expensive books.

#### 4. Successful Book Creation
- **Method:** `POST`
- **URL:** `http://localhost:3000/books`
- **Headers:** `Content-Type: application/json`
- **Body (raw JSON):**
  ```json
  {
    "title": "Brave New World",
    "author": "Aldous Huxley",
    "price": 320,
    "pages": 268
  }
  ```
- **Expected Status:** `201 Created`
- **Expected Response:**
  ```json
  {
    "message": "Book Added Successfully",
    "bookId": 11
  }
  ```

#### 5. Successful Book Details Update
- **Method:** `PUT`
- **URL:** `http://localhost:3000/books/2`
- **Headers:** `Content-Type: application/json`
- **Body (raw JSON):**
  ```json
  {
    "title": "1984 (Special Edition)",
    "author": "George Orwell",
    "price": 350,
    "pages": 340
  }
  ```
- **Expected Status:** `200 OK`
- **Expected Response:**
  ```json
  {
    "message": "Book Details Updated Successfully"
  }
  ```

#### 6. Successful Book Deletion
- **Method:** `DELETE`
- **URL:** `http://localhost:3000/books/2`
- **Expected Status:** `200 OK`
- **Expected Response:**
  ```json
  {
    "message": "Book Deleted Successfully"
  }
  ```

---

### B. Failed / Validation Error Test Cases

#### 1. Invalid `order_by` Column (SQL Injection Protection)
- **Method:** `GET`
- **URL:** `http://localhost:3000/books?order_by=password`
- **Expected Status:** `400 Bad Request`
- **Expected Response:**
  ```json
  {
    "error": "Invalid 'order_by' parameter. Allowed values: id, title, author, price, pages"
  }
  ```

#### 2. Invalid Price Range Logic
- **Method:** `GET`
- **URL:** `http://localhost:3000/books?min_price=500&max_price=200`
- **Expected Status:** `400 Bad Request`
- **Expected Response:**
  ```json
  {
    "error": "'min_price' cannot be greater than 'max_price'"
  }
  ```

#### 3. POST Missing Title
- **Method:** `POST`
- **URL:** `http://localhost:3000/books`
- **Headers:** `Content-Type: application/json`
- **Body (raw JSON):**
  ```json
  {
    "author": "Aldous Huxley",
    "price": 320,
    "pages": 268
  }
  ```
- **Expected Status:** `400 Bad Request`
- **Expected Response:**
  ```json
  {
    "error": "Title is required and must be a non-empty string"
  }
  ```

#### 4. POST Non-Numeric Price
- **Method:** `POST`
- **URL:** `http://localhost:3000/books`
- **Headers:** `Content-Type: application/json`
- **Body (raw JSON):**
  ```json
  {
    "title": "Brave New World",
    "author": "Aldous Huxley",
    "price": "three hundred twenty",
    "pages": 268
  }
  ```
- **Expected Status:** `400 Bad Request`
- **Expected Response:**
  ```json
  {
    "error": "Price is required and must be a valid number"
  }
  ```

#### 5. Non-Existent Book ID (404)
- **Method:** `GET`
- **URL:** `http://localhost:3000/books/9999`
- **Expected Status:** `404 Not Found`
- **Expected Response:**
  ```json
  {
    "message": "Book not found"
  }
  ```

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
