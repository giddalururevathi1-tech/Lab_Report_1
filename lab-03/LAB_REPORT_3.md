# LAB REPORT 3
## COURSE: BACKEND DEVELOPMENT FUNDAMENTALS
### Lab Title: REST API, Query Parameters, Validation and Debugging

---

### **Student Information**
- **Student Name:** Giddaluru Revathi
- **Section:** CSE AIML-2B
- **Registration Number:** 252U1R6077
- **Academic Term:** Term 2
- **Course:** Backend Development Fundamentals
- **GitHub Repository (Lab 3):** [https://github.com/giddalururevathi1-tech/Lab_Report_1/tree/lab-3/lab-03](https://github.com/giddalururevathi1-tech/Lab_Report_1/tree/lab-3/lab-03)

---

## 1. Objectives & Learning Outcomes

1. **RESTful Architecture:** Design and implement RESTful API endpoints adhering to standard HTTP methods (`GET`, `POST`, `PUT`, `DELETE`) and uniform interface conventions.
2. **Query Parameters:** Implement query string processing in Express (`req.query`) to support filtering, searching, sorting, and pagination.
3. **Data Validation:** Implement defensive validation for:
   - Query parameters (type checks, value allowlisting, range checks).
   - Route parameters (`:bookId` positive integer check).
   - Request bodies (required field verification, string trimming, numeric checks).
4. **SQL Injection Prevention:** Use parameterized SQLite queries (`?` placeholders) and column allowlists for safe dynamic query generation.
5. **Debugging & Centralized Error Handling:** Implement request logging middleware to track incoming traffic and centralized Express error-handling middleware returning standard HTTP status codes (`200`, `201`, `400`, `404`, `500`).

---

## 2. Tools & Environment

| Component | Tool / Environment |
| :--- | :--- |
| **Operating System** | Windows 11 / Windows Server |
| **Runtime Environment** | Node.js (v24.x) |
| **Web Framework** | Express.js (v5.x) |
| **Database** | SQLite3 via `sqlite` & `sqlite3` drivers |
| **Development Tool** | Nodemon (auto-reloading) |
| **Testing Client** | Postman / Automated Node test suite |

---

## 3. Database Schema & Architecture

- **Database File:** `database/library.db`
- **Table Name:** `book`

### Database Schema Table

| Column Name | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `id` | `INTEGER` | `PRIMARY KEY` (AUTOINCREMENT) | Unique identifier for each book |
| `title` | `TEXT` | `NOT NULL` | Title of the book |
| `author` | `TEXT` | `NOT NULL` | Author of the book |
| `price` | `INTEGER` | `NOT NULL` | Price of the book |
| `pages` | `INTEGER` | | Total number of pages |

### Sample Data Records

| id | Title | Author | Price (₹) | Pages |
| :-: | :--- | :--- | :-: | :-: |
| 1 | To Kill a Mockingbird | Harper Lee | 399 | 281 |
| 2 | 1984 | George Orwell | 299 | 328 |
| 3 | The Great Gatsby | F. Scott Fitzgerald | 250 | 180 |
| 4 | The Catcher in the Rye | J.D. Salinger | 350 | 234 |
| 5 | Pride and Prejudice | Jane Austen | 220 | 279 |
| 6 | The Hobbit | J.R.R. Tolkien | 450 | 310 |
| 7 | Harry Potter and the Philosopher's Stone | J.K. Rowling | 499 | 223 |
| 8 | The Alchemist | Paulo Coelho | 299 | 208 |
| 9 | Crime and Punishment | Fyodor Dostoevsky | 420 | 430 |
| 10 | The Da Vinci Code | Dan Brown | 380 | 454 |

---

## 4. API Specification & Query Parameters

### 4.1. Core CRUD Routes

| HTTP Method | Route Endpoint | Description | Status Codes |
| :--- | :--- | :--- | :--- |
| **GET** | `/` | Root route displaying Lab 3 status message | `200 OK` |
| **GET** | `/books` | Retrieve books with optional query filtering | `200 OK`, `400 Bad Request` |
| **GET** | `/books/:bookId` | Retrieve a single book record by ID | `200 OK`, `400 Bad Request`, `404 Not Found` |
| **POST** | `/books` | Add a new book with validated payload | `201 Created`, `400 Bad Request` |
| **PUT** | `/books/:bookId` | Update an existing book's details by ID | `200 OK`, `400 Bad Request`, `404 Not Found` |
| **DELETE** | `/books/:bookId` | Delete a book record by ID | `200 OK`, `400 Bad Request`, `404 Not Found` |

---

### 4.2. Supported Query Parameters (`GET /books`)

| Parameter | Type | Default | Validation Criteria | Description |
| :--- | :--- | :---: | :--- | :--- |
| `search_q` | String | `""` | Any string | Partial case-insensitive title search via `LIKE ?` |
| `order_by` | String | `"id"` | Must be in `['id', 'title', 'author', 'price', 'pages']` | Column name to sort by |
| `order` | String | `"ASC"` | Must be `'ASC'` or `'DESC'` (case-insensitive) | Sort direction |
| `limit` | Integer | `10` | Must be an integer $> 0$ | Maximum number of records to return |
| `offset` | Integer | `0` | Must be an integer $\ge 0$ | Number of records to skip |
| `min_price` | Number | None | Must be a number $\ge 0$ | Minimum price threshold (`price >= ?`) |
| `max_price` | Number | None | Must be a number $\ge 0$, and $\ge$ `min_price` | Maximum price threshold (`price <= ?`) |

---

## 5. Key Implementation Code Snippets

### 5.1. Request Logging Middleware (Debugging)
```javascript
app.use((req, res, next) => {
  const timestamp = new Date().toISOString();
  console.log(`[${timestamp}] ${req.method} ${req.originalUrl}`);
  if (req.query && Object.keys(req.query).length > 0) {
    console.log("  Query Params:", JSON.stringify(req.query));
  }
  if (req.body && Object.keys(req.body).length > 0) {
    console.log("  Request Body:", JSON.stringify(req.body));
  }
  next();
});
```

### 5.2. Validation Helpers
```javascript
// Validate route parameter :bookId
const validateBookId = (bookId) => {
  const id = Number(bookId);
  if (!Number.isInteger(id) || id <= 0) {
    return { valid: false, error: "Invalid book ID: must be a positive integer" };
  }
  return { valid: true, id };
};

// Validate body payload for POST and PUT
const validateBookPayload = (body) => {
  if (!body || typeof body !== "object" || Object.keys(body).length === 0) {
    return { valid: false, error: "Request body cannot be empty" };
  }

  const { title, author, price, pages } = body;

  if (title === undefined || title === null || typeof title !== "string" || title.trim() === "") {
    return { valid: false, error: "Title is required and must be a non-empty string" };
  }

  if (author === undefined || author === null || typeof author !== "string" || author.trim() === "") {
    return { valid: false, error: "Author is required and must be a non-empty string" };
  }

  if (price === undefined || price === null || price === "" || typeof price === "boolean" || isNaN(Number(price))) {
    return { valid: false, error: "Price is required and must be a valid number" };
  }

  const numPrice = Number(price);
  if (numPrice < 0) {
    return { valid: false, error: "Price must be a non-negative number" };
  }

  let numPages = null;
  if (pages !== undefined && pages !== null && pages !== "") {
    numPages = Number(pages);
    if (!Number.isInteger(numPages) || numPages <= 0) {
      return { valid: false, error: "Pages must be a positive integer" };
    }
  }

  return {
    valid: true,
    data: { title: title.trim(), author: author.trim(), price: numPrice, pages: numPages },
  };
};
```

### 5.3. Query Parameters & Safe Parameterized SQL Query Builder (`GET /books`)
```javascript
app.get(["/books", "/books/"], async (req, res) => {
  try {
    const {
      search_q = "",
      order_by = "id",
      order = "ASC",
      limit = 10,
      offset = 0,
      min_price,
      max_price,
    } = req.query;

    const normalizedOrderBy = String(order_by).toLowerCase();
    if (!ALLOWED_ORDER_BY_COLUMNS.includes(normalizedOrderBy)) {
      return res.status(400).json({
        error: `Invalid 'order_by' parameter. Allowed values: ${ALLOWED_ORDER_BY_COLUMNS.join(", ")}`,
      });
    }

    const normalizedOrder = String(order).toUpperCase();
    if (!["ASC", "DESC"].includes(normalizedOrder)) {
      return res.status(400).json({
        error: "Invalid 'order' parameter. Allowed values: ASC, DESC",
      });
    }

    const limitNum = Number(limit);
    if (!Number.isInteger(limitNum) || limitNum <= 0) {
      return res.status(400).json({ error: "Invalid 'limit' parameter: must be a positive integer" });
    }

    const offsetNum = Number(offset);
    if (!Number.isInteger(offsetNum) || offsetNum < 0) {
      return res.status(400).json({ error: "Invalid 'offset' parameter: must be a non-negative integer" });
    }

    const conditions = [];
    const params = [];

    if (search_q !== undefined && String(search_q).trim() !== "") {
      conditions.push("title LIKE ?");
      params.push(`%${String(search_q).trim()}%`);
    }

    if (min_price !== undefined && min_price !== "") {
      const minPriceNum = Number(min_price);
      if (isNaN(minPriceNum) || minPriceNum < 0) {
        return res.status(400).json({ error: "Invalid 'min_price' parameter: must be a non-negative number" });
      }
      conditions.push("price >= ?");
      params.push(minPriceNum);
    }

    if (max_price !== undefined && max_price !== "") {
      const maxPriceNum = Number(max_price);
      if (isNaN(maxPriceNum) || maxPriceNum < 0) {
        return res.status(400).json({ error: "Invalid 'max_price' parameter: must be a non-negative number" });
      }
      conditions.push("price <= ?");
      params.push(maxPriceNum);
    }

    if (min_price !== undefined && min_price !== "" && max_price !== undefined && max_price !== "") {
      if (Number(min_price) > Number(max_price)) {
        return res.status(400).json({ error: "'min_price' cannot be greater than 'max_price'" });
      }
    }

    let query = `SELECT * FROM book`;
    if (conditions.length > 0) {
      query += ` WHERE ${conditions.join(" AND ")}`;
    }

    query += ` ORDER BY ${normalizedOrderBy} ${normalizedOrder} LIMIT ? OFFSET ?;`;
    params.push(limitNum, offsetNum);

    const books = await db.all(query, params);
    res.json(books);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

---

## 6. Test Cases & Verification Outputs

### 6.1. Automated Test Suite Execution (`npm test`)

```text
> rest-api-query-parameters-validation-and-debugging@1.0.0 test
> node test.js

==================================================
 Lab 3: REST API, Query Parameters & Validation   
==================================================
Server running at http://localhost:3000/
[2026-11-06T13:24:24.688Z] GET /
✅ [PASS] Root route returns Lab 3 title
[2026-11-06T13:24:24.692Z] GET /books
✅ [PASS] Default GET /books returns books sorted by id ASC
[2026-11-06T13:24:24.695Z] GET /books?search_q=Harry
✅ [PASS] Query param 'search_q' performs partial title search
[2026-11-06T13:24:24.697Z] GET /books?order_by=price&order=DESC&limit=3
✅ [PASS] Query params 'order_by=price' and 'order=DESC' sort correctly
[2026-11-06T13:24:24.700Z] GET /books?limit=3&offset=2
✅ [PASS] Query params 'limit=3&offset=2' paginates correctly
[2026-11-06T13:24:24.701Z] GET /books?min_price=300&max_price=400
✅ [PASS] Query params 'min_price' and 'max_price' filter prices correctly
[2026-11-06T13:24:24.702Z] GET /books?order_by=password
✅ [PASS] Validation rejects invalid 'order_by' column with HTTP 400
[2026-11-06T13:24:24.702Z] GET /books?order=DIAGONAL
✅ [PASS] Validation rejects invalid 'order' with HTTP 400
[2026-11-06T13:24:24.703Z] GET /books?limit=-2
✅ [PASS] Validation rejects negative 'limit' with HTTP 400
[2026-11-06T13:24:24.703Z] GET /books?min_price=600&max_price=200
✅ [PASS] Validation rejects min_price > max_price with HTTP 400
[2026-11-06T13:24:24.703Z] GET /books/abc
✅ [PASS] Validation rejects non-integer :bookId with HTTP 400
[2026-11-06T13:24:24.703Z] GET /books/9999
✅ [PASS] Returns 404 for non-existent book ID
[2026-11-06T13:24:24.704Z] GET /api/undefined-route
✅ [PASS] Catch-all middleware returns 404 for undefined endpoints
[2026-11-06T13:24:24.704Z] POST /books
✅ [PASS] POST /books rejects request with missing title (HTTP 400)
[2026-11-06T13:24:24.704Z] POST /books
✅ [PASS] POST /books rejects request with missing author (HTTP 400)
[2026-11-06T13:24:24.705Z] POST /books
✅ [PASS] POST /books rejects request with non-numeric price (HTTP 400)
[2026-11-06T13:24:24.705Z] POST /books
✅ [PASS] POST /books creates book successfully and returns HTTP 201
--------------------------------------------------
Results: All tests passed.
==================================================
```

---

### 6.2. Postman Verification Test Cases Table

| Test Case | Method | Request URL / Body | Expected Status | Result & Verified Screenshot |
| :--- | :---: | :--- | :---: | :--- |
| **1. Root Route** | `GET` | `http://localhost:3000/` | `200 OK` | Returns Lab 3 status message |
| **2. Default Books** | `GET` | `http://localhost:3000/books` | `200 OK` | Returns books sorted by `id ASC` — `screenshots/Screenshot (361).png` |
| **3. Read Single** | `GET` | `http://localhost:3000/books/2` | `200 OK` | Single book by ID (*1984*) — `screenshots/Screenshot (364).png` |
| **4. Partial Search** | `GET` | `http://localhost:3000/books?search_q=1984` | `200 OK` | Matches *1984* — `screenshots/Screenshot (365).png` |
| **5. Partial Search** | `GET` | `http://localhost:3000/books?search_q=Harry` | `200 OK` | Matches Harry Potter books — `screenshots/Screenshot (378).png` |
| **6. Sorting** | `GET` | `http://localhost:3000/books?order_by=price&order=DESC` | `200 OK` | Ordered from highest to lowest price — `screenshots/Screenshot (366).png` |
| **7. Price Filtering** | `GET` | `http://localhost:3000/books?min_price=100&max_price=500` | `200 OK` | Books priced between ₹100 and ₹500 — `screenshots/Screenshot (367).png` |
| **8. Pagination** | `GET` | `http://localhost:3000/books?limit=5&offset=0` | `200 OK` | Returns first 5 records — `screenshots/Screenshot (368).png` |
| **9. Create Success** | `POST` | `http://localhost:3000/books`<br>`{ "title": "Harry Potter", "author": "J.K. Rowling", "price": 450, "pages": 400 }` | `201 Created` | `{ "message": "Book Added Successfully", "bookId": 31 }` — `screenshots/Screenshot (369).png` |
| **10. Missing Title** | `POST` | `http://localhost:3000/books`<br>`{ "author": "J.K. Rowling", "price": 450, "pages": 400 }` | `400 Bad Request` | `{ "error": "Title is required and must be a non-empty string" }` — `screenshots/Screenshot (375).png` |
| **11. Missing Author** | `POST` | `http://localhost:3000/books`<br>`{ "title": "Harry Potter", "price": 450, "pages": 400 }` | `400 Bad Request` | `{ "error": "Author is required and must be a non-empty string" }` — `screenshots/Screenshot (377).png` |
| **12. Read Missing** | `GET` | `http://localhost:3000/books/999` | `404 Not Found` | `{ "message": "Book not found" }` |
| **13. Delete Missing**| `DELETE`| `http://localhost:3000/books/3` | `404 Not Found` | `{ "message": "Book not found" }` — `screenshots/Screenshot (374).png` |

---

## 7. Conclusion

In this lab, the backend application was upgraded from standard CRUD routes to a robust RESTful service incorporating:
1. **Flexible Query Processing:** Clients can filter, sort, and paginate through records seamlessly using expressive URL query parameters.
2. **Defensive Programming & Input Validation:** All route parameters, body payloads, and query parameters are strictly validated prior to database queries, preventing application crashes, data corruption, and SQL injection vulnerabilities.
3. **HTTP Status Code Conformity:** Proper semantic HTTP status codes (`200`, `201`, `400`, `404`, `500`) and structured JSON error responses provide predictable API contracts for client applications.
4. **Debugging and Observability:** Centralized error handling and request logging ensure ease of debugging and operational visibility.

