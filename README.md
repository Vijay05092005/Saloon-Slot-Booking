### Purpose
The Salon Booking & E-Commerce Web System is an integrated, dual-engine enterprise web platform designed to streamline salon operations. It merges customer appointment scheduling with an online store for beauty products and an administrative dashboard for staff and inventory management.

### Problem It Solves
1. **Inefficient Appointment Management:** Traditional manual salon booking leads to overlapping slots, double bookings, and phone call overhead.
2. **Fragmented Retail & Services:** Salon clients frequently purchase care products in-salon; unifying service appointments and product purchasing on one platform increases retail revenue and client convenience.
3. **Audit & Session Visibility:** Administrative teams lack tracking of staff details, active user login sessions, historical user connections, and real-time inventory updates.
4. **Instant Automated Support:** Customers require basic responses regarding business hours, service pricing, and salon locations outside working hours without human intervention.

### Target Users
* **Customers / Clients:** Browse salon retail products, maintain a virtual shopping cart, place product orders, select open appointment slots by date/time, make UPI payments, and query the automated salon assistant.
* **Salon Administrators:** Manage the product catalog (CRUD), manage salon staff members (CRUD), configure daily/weekly time slots, monitor customer appointments, inspect user sessions/cookie logs, and view XML order archives.

### Main Features
* **Hybrid Dual-Engine Architecture:** Java EE Servlets/JSP backend combined with PHP Web Scripts on Apache.
* **User Authentication & Session Audit:** Role-based access control (`admin`, `customer`), password strength validation, cookie logging (`user_cookies`), and live session tracking (`user_sessions`).
* **Interactive E-Commerce Store:** Session-backed shopping cart, dynamic quantity updates, address/payment collection (Cash / UPI), and XML-based order persistence (`data/orders.xml`).
* **Dynamic Slot Booking & UPI Payment:** Date-filtered slot grid, atomic MySQL transaction locking (`Available` $\rightarrow$ `Booked`), advance payment verification, and UPI QR Code interface.
* **Keyword-Based AI Chatbot Assistant:** MySQL keyword lookup engine matching client queries with pre-configured responses.
* **Staff & Product Administrative CRUD:** Management modules for product details, pricing, images, and staff employee records (salary, experience, role, status).

### High-Level Summary
The application is a hybrid Java web application (`war` packaging managed via Maven) and PHP web system interacting with a unified MySQL database (`salon_db`) and a DOM XML data store (`orders.xml`). Java handles session management, catalog browsing, cart operations, staff management, and order XML serialization. PHP manages slot scheduling, booking transactions, admin slot creation dashboards, and the keyword chatbot API.

---

## 2. Technology Stack

| Layer / Category | Technology Used | Version / Spec | Why It Is Used |
| :--- | :--- | :--- | :--- |
| **Primary Languages** | Java | JDK 8 / Java 11 | Strong typing, object-oriented servlets, enterprise reliability, and robust DOM XML processing. |
| | PHP | PHP 7.4+ / 8.x | Rapid scripting, lightweight database operations, and seamless execution on Apache HTTP Server for slot booking. |
| | SQL | MySQL Dialect 8.0 | Structured relational query language for user accounts, sessions, slots, staff, and chatbot data. |
| | HTML5 / CSS3 / JS | Standard Specifications | Native browser rendering, custom CSS variables, responsive grid layouts, and dynamic DOM manipulation. |
| **Backend Frameworks** | Java Servlet API | `javax.servlet-api` 4.0.1 | Request handling, session management, and MVC controller layer without full framework overhead. |
| | Native PHP (`mysqli`) | MySQLi Extension | Prepared statements, transactional control, and low-latency database queries in PHP scripts. |
| **Frontend Rendering** | JavaServer Pages (JSP) | JSP 2.3 / JSTL | Dynamic HTML template rendering on the server with Java session and request attribute integration. |
| **Database** | MySQL Server | 8.0.41 | High-performance relational database supporting foreign keys, unique indexes, and ACID transactions. |
| **Persistence / Storage**| DOM XML Engine | W3C DOM (`org.w3c.dom`) | File-based structured order persistence (`data/orders.xml`) decoupled from the primary SQL store. |
| **Build Tools** | Apache Maven | Maven 3.x | Dependency management, compile lifecycle control, and web application archive (`war`) packaging. |
| | Eclipse / VS Code | IDE Tooling | Development project structure (`.classpath`, `.project`, `.settings`). |
| **Web / Application Server**| Apache Tomcat | Tomcat 9.0 / 10.x | Servlet container executing Java web applications on port `8080`. |
| | Apache HTTP Server / XAMPP| Apache 2.4 | Web server executing PHP scripts in `SalonBookingPHP` on port `80`. |
| **Authentication** | HTTP Sessions & Cookies | Native Servlet Spec | In-memory session tracking (`HttpSession`), persistent client cookies, and audit log tables in MySQL. |
| **APIs / Data Exchange**| Chatbot Query API | Custom REST-like JSON/Text | CORS-enabled HTTP endpoints handling asynchronous client queries. |

---

## 3. System Architecture

### Architecture Style
The system implements a **Hybrid Layered MVC (Model-View-Controller) & Component Architecture**:
* **Presentation Layer (View):** JSP templates (`index.jsp`, `home.jsp`, `user/products.jsp`, `admin/*.jsp`) and PHP view scripts (`slot_booking.php`, `admin/dashboard.php`).
* **Control Layer (Controller):** Java Servlets (`LoginServlet`, `RegisterServlet`, `AddProductServlet`, etc.) handling HTTP requests, input validation, and flow redirects.
* **Service & Persistence Layer (Model):** Utility classes (`DBConnection`, `OrderXmlUtil`), MySQL tables via JDBC/MySQLi, and DOM XML document transformers.

### Component Map
1. **Client Browser:** Communicates over HTTP with both Tomcat (`8080`) and Apache (`80`).
2. **Java Servlet Container (Tomcat):** Serves e-commerce, authentication, staff administration, and XML order parsing.
3. **PHP Runtime (Apache):** Handles real-time appointment slot generation, customer booking transactions, and keyword bot lookups.
4. **MySQL Database (`salon_db`):** Shared single source of truth for users, sessions, cookies, staff, products, slots, bookings, and chatbot QA pairs.
5. **XML Data Store (`orders.xml`):** Standalone XML document storing detailed client purchase receipts and itemized orders.

### System Architecture Diagram (ASCII)

```
                                  +-----------------------+
                                  |    Client Browser     |
                                  +-----------+-----------+
                                              |
                     +------------------------+------------------------+
                     | HTTP (Port 8080)                                | HTTP (Port 80)
                     v                                                 v
    +---------------------------------+               +---------------------------------+
    |   Apache Tomcat Servlet Engine  |               |    Apache HTTP Server (PHP)     |
    |                                 |               |                                 |
    |  +---------------------------+  |               |  +---------------------------+  |
    |  | Views: JSP Files          |  |               |  | Views: PHP Scripts        |  |
    |  | (products.jsp, cart.jsp,  |  |               |  | (slot_booking.php,        |  |
    |  |  adminDashboard.jsp)      |  |               |  |  admin/dashboard.php)     |  |
    |  +-------------+-------------+  |               |  +-------------+-------------+  |
    |                |                |               |                |                |
    |  +-------------v-------------+  |               |  +-------------v-------------+  |
    |  | Controllers: Java Servlets|  |               |  | Logic: PHP Handlers         |  |
    |  | (Login, Register, Product,|  |               |  | (create_slot.php,           |  |
    |  |  Staff Servlets)          |  |               |  |  chatbot.php)               |  |
    |  +-------------+-------------+  |               |  +-------------+-------------+  |
    |                |                |               |                |                |
    |  +-------------v-------------+  |               |                |                |
    |  | Utility / Data Layer      |  |               |                |                |
    |  | (DBConnection,            |  |               |                |                |
    |  |  OrderXmlUtil)            |  |               |                |                |
    |  +------+--------------+-----+  |               |                |                |
    +---------|--------------|--------+               +----------------+----------------+
              |              |                                         |
              | JDBC         +---------------+                         | MySQLi
              v                              v                         v
    +-------------------+          +-------------------+    +-------------------+
    |    MySQL Database |          | XML File Store    |    |  MySQL Database   |
    |     (salon_db)    |<---------| (data/orders.xml) |    |    (salon_db)     |
    +-------------------+          +-------------------+    +-------------------+
```

---

## 4. Complete Folder Structure

```
SalonBooking/
├── .classpath                      # Eclipse IDE Java build path configuration
├── .project                        # Eclipse project metadata
├── .settings/                      # IDE environment settings
├── .vscode/                        # Visual Studio Code settings
├── pom.xml                         # Apache Maven build descriptor & dependency configuration
├── database.sql                    # SQL database schema dump & initial sample data
├── XmlRewrite.java                 # Standalone Java maintenance script for XML normalization
├── data/
│   ├── .gitkeep                    # Version control directory retention file
│   └── orders.xml                  # XML data store for e-commerce product orders
├── SalonBookingPHP/                # PHP Module Root (Runs on Apache / XAMPP)
│   ├── db.php                      # MySQL database connection helper for PHP
│   ├── chatbot.php                 # Keyword-based AI chatbot processing script
│   ├── slot_booking.php            # Customer slot booking interface & transaction handler
│   ├── admin/                      # Administrative PHP management scripts
│   │   ├── create_slot.php         # Bulk slot creation interface & insertion logic
│   │   ├── dashboard.php            # Admin metrics dashboard for slot bookings
│   │   ├── delete_slot.php         # Slot deletion handler
│   │   ├── edit_slot.php           # Slot updating interface & modification handler
│   │   ├── view_bookings.php       # Administrative list of customer slot bookings
│   │   └── view_slots.php          # Administrative overview & filterable slot table
│   └── assets/                     # PHP module static resources
│       ├── hl.jpg                  # Hero banner graphic asset
│       ├── qr.jpg                  # Payment UPI QR code graphic asset
│       └── styles.css              # Custom CSS stylesheet for PHP slot pages
└── src/                            # Java EE Source Root
    └── main/
        ├── java/                   # Java Source Code
        │   ├── servlets/           # HTTP Servlet Controller Layer
        │   │   ├── AddProductServlet.java    # Handles creation of new retail products
        │   │   ├── AddStaffServlet.java      # Handles registration of new staff members
        │   │   ├── DeleteProductServlet.java # Handles product deletion
        │   │   ├── DeleteStaffServlet.java   # Handles staff deletion
        │   │   ├── LoginServlet.java         # User login authentication & audit logging
        │   │   ├── LogoutServlet.java        # User session termination & status updates
        │   │   ├── RegisterServlet.java      # New user account registration
        │   │   ├── UpdateProductServlet.java # Updates existing product details
        │   │   ├── UpdateStaffServlet.java   # Updates existing staff details
        │   │   └── ViewStaffServlet.java     # Reserved staff view servlet
        │   └── util/               # Helper Utility Layer
        │       ├── DBConnection.java         # JDBC connection manager for MySQL
        │       └── OrderXmlUtil.java         # W3C DOM XML parser & serializer for orders
        └── webapp/                 # Web Application Resources (Deployed to Tomcat)
            ├── adminDashboard.jsp            # User session audit dashboard for admins
            ├── home.jsp                      # System landing page & navigation hub
            ├── index.jsp                     # Authentication page (Login & Register forms)
            ├── css/                          # Java web application stylesheet folder
            ├── images/                       # Product graphics folder
            ├── js/                           # Frontend JavaScript scripts folder
            ├── META-INF/                     # Manifest configuration directory
            ├── WEB-INF/
            │   ├── web.xml                   # Java Web Application Deployment Descriptor
            │   └── lib/                      # Additional web dependencies library folder
            ├── admin/                        # Administrative JSP View Pages
            │   ├── addProduct.jsp            # Add product form view
            │   ├── addStaff.jsp              # Add staff form view
            │   ├── editProduct.jsp           # Edit product form view
            │   ├── editStaff.jsp             # Edit staff form view
            │   ├── viewOrders.jsp            # XML orders view & breakdown
            │   ├── viewProducts.jsp          # Product catalog management view
            │   └── viewStaff.jsp             # Staff records management view
            └── user/                         # Customer JSP View Pages
                ├── addToCart.jsp             # Cart item addition controller snippet
                ├── cart.jsp                  # Shopping cart view & quantity controls
                ├── clearCart.jsp             # Cart reset action snippet
                ├── payment.jsp               # Checkout address & payment options page
                ├── processPayment.jsp        # Order execution & XML serialization handler
                ├── products.jsp              # E-commerce product catalog grid
                ├── removeFromCart.jsp        # Item removal action snippet
                ├── updateQty.jsp             # Item quantity adjustment handler snippet
                ├── css/                      # User sub-directory stylesheet folder
                └── images/                   # User sub-directory images folder
```

---

## 5. File-by-File Explanation

### Backend Utility & Helper Files

#### 1. `DBConnection.java` (`src/main/java/util/DBConnection.java`)
* **Purpose:** Provides a centralized database connection factory for Java servlets.
* **Responsibilities:** Loads the MySQL JDBC driver (`com.mysql.cj.jdbc.Driver`) and opens a `Connection` to `jdbc:mysql://localhost:3306/salon_db`.
* **Major Classes:** `util.DBConnection`.
* **Major Functions:** `public static Connection getConnection()`.
* **Dependencies:** `java.sql.Connection`, `java.sql.DriverManager`.
* **Inputs:** None (uses hardcoded database connection credentials).
* **Outputs:** Active JDBC `Connection` object, or `null` on exception.
* **Interaction:** Called by all servlets (`LoginServlet`, `RegisterServlet`, product & staff servlets) to obtain database connections.

#### 2. `OrderXmlUtil.java` (`src/main/java/util/OrderXmlUtil.java`)
* **Purpose:** Handles all XML parsing, creation, document appending, and serialization operations for orders stored in `data/orders.xml`.
* **Responsibilities:**
  * Defines data carrier inner classes: `OrderItem`, `OrderRecord`, `NewOrderData`.
  * Loads or initializes the XML file with a root `<orders>` element.
  * Formats document nodes, strips whitespace nodes, and appends new `<order>` elements.
  * Reads XML elements into structured Java lists for admin display.
* **Major Classes:** `OrderXmlUtil`, `OrderItem`, `OrderRecord`, `NewOrderData`.
* **Major Functions:**
  * `public static synchronized OrderRecord appendOrder(File xmlFile, NewOrderData orderData)`
  * `public static synchronized List<OrderRecord> readOrders(File xmlFile)`
  * `private static Document loadOrCreateDocument(File xmlFile)`
  * `private static void writeDocument(Document document, File xmlFile)`
  * `private static void removeWhitespaceNodes(Node node)`
* **Dependencies:** `org.w3c.dom.*`, `javax.xml.parsers.*`, `javax.xml.transform.*`, `java.util.UUID`, `java.time.LocalDateTime`.
* **Inputs:** `File` object pointing to `data/orders.xml`, `NewOrderData` objects.
* **Outputs:** Serialized XML elements written to file, and `List<OrderRecord>` populated from XML.
* **Interaction:** Invoked by `processPayment.jsp` during customer checkout and `admin/viewOrders.jsp` to display administrative order lists.

#### 3. `XmlRewrite.java` (`XmlRewrite.java`)
* **Purpose:** A standalone maintenance utility to test, format, and normalize the XML schema of `data/orders.xml`.
* **Responsibilities:** Uses Java reflection to invoke private XML document loading and writing methods in `OrderXmlUtil`.
* **Major Classes:** `XmlRewrite`.
* **Major Functions:** `public static void main(String[] args)`.
* **Dependencies:** `util.OrderXmlUtil`, `org.w3c.dom.Document`, reflection APIs.
* **Inputs:** File path `data/orders.xml`.
* **Outputs:** Reformatted `orders.xml` file.
* **Interaction:** Executed via command line for manual XML file structure repair or debugging.

---

### Java Servlet Controllers

#### 4. `LoginServlet.java` (`src/main/java/servlets/LoginServlet.java`)
* **Purpose:** Authenticates user credentials, handles role redirection, and records user audit entries.
* **Responsibilities:**
  * Validates email format (`regex`) and password complexity rules.
  * Queries the `users` table for matching `email`, `password`, and `role`.
  * Initializes HTTP session attributes (`userId`, `userName`, `role`).
  * Writes a persistent HTTP cookie (`userEmail`) and logs records into `user_cookies` and `user_sessions`.
* **Major Functions:** `doGet()`, `doPost()`.
* **Inputs:** Form parameters `loginEmail`, `loginpassword`, `loginType`.
* **Outputs:** HTTP redirect to `/index.jsp?error=...`, `/admin/viewProducts.jsp`, or `/user/products.jsp`.
* **Interaction:** Handles submitted credentials from `index.jsp`.

#### 5. `LogoutServlet.java` (`src/main/java/servlets/LogoutServlet.java`)
* **Purpose:** Terminates user session and records logout timestamps.
* **Responsibilities:** Obtains current `HttpSession`, updates the matching active record in `user_sessions` to `INACTIVE` with `logout_time = NOW()`, and invalidates the session.
* **Major Functions:** `doGet()`.
* **Inputs:** Session ID from HTTP request.
* **Outputs:** Redirect to `index.jsp`.
* **Interaction:** Triggered when users click Logout links across JSP headers.

#### 6. `RegisterServlet.java` (`src/main/java/servlets/RegisterServlet.java`)
* **Purpose:** Registers new user accounts in the database.
* **Responsibilities:**
  * Validates email syntax and password strength.
  * Inserts user records (`name`, `email`, `password`, `phone`, `role`) into the `users` table.
  * Handles duplicate email exceptions (`SQLIntegrityConstraintViolationException`).
* **Major Functions:** `doPost()`.
* **Inputs:** Form fields `regName`, `regEmail`, `regpassword`, `regPhone`, `regType`.
* **Outputs:** Redirect to `/index.jsp?msg=registered` or error query parameters.
* **Interaction:** Form endpoint for registration on `index.jsp`.

#### 7. `AddProductServlet.java` (`src/main/java/servlets/AddProductServlet.java`)
* **Purpose:** Admin controller for adding a product to the catalog.
* **Responsibilities:** Reads form parameters and executes an `INSERT INTO products` prepared statement.
* **Major Functions:** `doPost()`.
* **Inputs:** `name`, `price`, `image`, `active`.
* **Outputs:** Redirect to `/admin/viewProducts.jsp`.
* **Interaction:** Invoked by form submission in `/admin/addProduct.jsp`.

#### 8. `UpdateProductServlet.java` (`src/main/java/servlets/UpdateProductServlet.java`)
* **Purpose:** Admin controller for modifying existing product details.
* **Responsibilities:** Executes `UPDATE products SET name=?, price=?, image=?, active=? WHERE id=?`.
* **Major Functions:** `doPost()`.
* **Inputs:** Product `id`, `name`, `price`, `image`, `active`.
* **Outputs:** Redirect to `/admin/viewProducts.jsp`.
* **Interaction:** Invoked by form submission in `/admin/editProduct.jsp`.

#### 9. `DeleteProductServlet.java` (`src/main/java/servlets/DeleteProductServlet.java`)
* **Purpose:** Admin controller for removing a product from the database.
* **Responsibilities:** Executes `DELETE FROM products WHERE id=?`.
* **Major Functions:** `doGet()`.
* **Inputs:** Query parameter `id`.
* **Outputs:** Redirect to `/admin/viewProducts.jsp`.
* **Interaction:** Triggered by Delete links in `/admin/viewProducts.jsp`.

#### 10. `AddStaffServlet.java`, `UpdateStaffServlet.java`, `DeleteStaffServlet.java`
* **Purpose:** Admin controllers for managing salon employees.
* **Responsibilities:** Perform `INSERT`, `UPDATE`, and `DELETE` SQL operations on the `staff` table.
* **Inputs:** Staff attributes (`name`, `role`, `experience`, `phone`, `salary`, `id`).
* **Outputs:** Redirects to `/admin/viewStaff.jsp`.
* **Interaction:** Process form inputs from `/admin/addStaff.jsp` and `/admin/editStaff.jsp`.

---

### PHP Application Scripts (`SalonBookingPHP/`)

#### 11. `db.php` (`SalonBookingPHP/db.php`)
* **Purpose:** Database connection initialization for the PHP script engine.
* **Responsibilities:** Creates a new `$conn = new mysqli("localhost", "root", "12345", "salon_db")` connection instance and handles connection failures.
* **Inputs:** None.
* **Outputs:** Active `$conn` global object.
* **Interaction:** Included by all PHP scripts (`slot_booking.php`, `chatbot.php`, `admin/*.php`).

#### 12. `chatbot.php` (`SalonBookingPHP/chatbot.php`)
* **Purpose:** Serves automated response logic for customer chat queries.
* **Responsibilities:**
  * Configures CORS headers to allow cross-origin requests from Tomcat (`http://localhost:8080`).
  * Fetches keyword-answer mappings from the `chatbot` SQL table.
  * Performs string token search on input message to find matching keywords.
* **Inputs:** `POST` parameter `message`.
* **Outputs:** Text string response containing the answer.
* **Interaction:** Called via AJAX requests from customer pages or `ChatbotProxyServlet`.

#### 13. `slot_booking.php` (`SalonBookingPHP/slot_booking.php`)
* **Purpose:** Primary customer interface for date selection, slot browsing, and booking payments.
* **Responsibilities:**
  * Queries available slots for a selected date (`status = 'Available'`).
  * Executes atomic multi-query SQL transactions: marks slot as `Booked` and inserts a `bookings` record containing customer details and UPI transaction ID.
  * Displays advance payment QR code (`assets/qr.jpg`).
* **Inputs:** `GET`/`POST` parameter `slot_date`, `POST` fields `slot_id`, `name`, `email`, `payment_method`, `transaction_id`.
* **Outputs:** Rendered HTML view with success/error status alerts.

#### 14. `admin/create_slot.php` (`SalonBookingPHP/admin/create_slot.php`)
* **Purpose:** Admin interface for generating batch time slots for specific dates.
* **Responsibilities:** Accepts start time, end time, and interval duration; loops through time blocks to insert entries into `slots`.
* **Inputs:** Date, start time, end time, interval parameters.
* **Outputs:** Redirect or status output on slot creation.

#### 15. `admin/dashboard.php`, `view_slots.php`, `edit_slot.php`, `delete_slot.php`, `view_bookings.php`
* **Purpose:** Administrative slot management portal in PHP.
* **Responsibilities:** Summarize metrics (total slots, available vs booked, today's count), edit existing slot date/times, remove unused slots, and review customer bookings.

---

### JSP View Pages

#### 16. `index.jsp`
* **Purpose:** User authentication interface containing tabbed Login and Register forms.
* **Responsibilities:** Collects credentials, presents real-time validation feedback, and submits data to `LoginServlet` or `RegisterServlet`.

#### 17. `home.jsp`
* **Purpose:** Main portal landing page.
* **Responsibilities:** Displays feature cards linking users to retail shopping (`user/products.jsp`), appointment bookings (`slot_booking.php`), and administrative management tools.

#### 18. `adminDashboard.jsp`
* **Purpose:** Administrative user session tracking view.
* **Responsibilities:** Connects to MySQL to query and table-format active/historical records from `user_sessions` and `user_cookies`.

#### 19. `user/products.jsp` & `user/cart.jsp`
* **Purpose:** Retail product catalog view and session shopping cart interface.
* **Responsibilities:**
  * `products.jsp`: Queries `products` where `active = 1` and displays product cards with "Add to Cart" buttons.
  * `cart.jsp`: Inspects the `HttpSession` cart map, calculates line-item subtotals and grand totals, and renders checkout controls.

#### 20. `user/processPayment.jsp`
* **Purpose:** Order completion controller page.
* **Responsibilities:** Reads cart contents from `HttpSession`, builds a `NewOrderData` DTO, invokes `OrderXmlUtil.appendOrder()` to save the receipt into `data/orders.xml`, and clears the session cart.

---

## 6. Module Breakdown

```
+-----------------------------------------------------------------------------------+
|                                 SYSTEM MODULES                                    |
+---------------------+---------------------+-----------------+---------------------+
| User & Auth Module  | E-Commerce Module   | Slot Booking    | Admin Management    |
| (Java Servlets)     | (JSP / XML)         | Module (PHP)    | (Java / PHP)        |
+---------------------+---------------------+-----------------+---------------------+
```

### Module 1: User Management & Authentication
* **Purpose:** Secure identity management, role-based access control, and user audit tracking.
* **Files:** `LoginServlet.java`, `LogoutServlet.java`, `RegisterServlet.java`, `index.jsp`, `user_cookies`, `user_sessions`.
* **Responsibilities:** User registration, password policy verification, login credentials checking, HTTP session population, cookie setting, and session audit updates.
* **Internal Workflow:** `index.jsp` submission $\rightarrow$ Servlet validation $\rightarrow$ MySQL verification/insertion $\rightarrow$ Session creation & audit log write $\rightarrow$ Page redirection.
* **Dependencies:** JDBC driver, MySQL `users`, `user_cookies`, and `user_sessions` tables.

### Module 2: E-Commerce & Order Management
* **Purpose:** Retail product browsing, virtual shopping cart maintenance, and XML order receipt archiving.
* **Files:** `products.jsp`, `cart.jsp`, `addToCart.jsp`, `updateQty.jsp`, `removeFromCart.jsp`, `clearCart.jsp`, `payment.jsp`, `processPayment.jsp`, `viewOrders.jsp`, `OrderXmlUtil.java`, `orders.xml`.
* **Responsibilities:** Display product items, update session cart data in-memory, process user address details, append structural order nodes into XML storage, and display XML orders to administrators.
* **Internal Workflow:** Product catalog click $\rightarrow$ `HttpSession` map update $\rightarrow$ Checkout address entry $\rightarrow$ `OrderXmlUtil.appendOrder()` $\rightarrow$ `orders.xml` update $\rightarrow$ Cart clearance.

### Module 3: Slot Scheduling & Booking
* **Purpose:** Appointment time slot creation, customer date-filtered reservation, and UPI advance payment processing.
* **Files:** `slot_booking.php`, `db.php`, `admin/dashboard.php`, `admin/create_slot.php`, `admin/view_slots.php`, `admin/edit_slot.php`, `admin/delete_slot.php`, `admin/view_bookings.php`.
* **Responsibilities:** Date selection filtering, transaction slot status updating (`Available` $\rightarrow$ `Booked`), payment reference saving, and administrative time slot configuration.
* **Internal Workflow:** Select Date $\rightarrow$ Query open slots $\rightarrow$ Fill user details & UPI reference $\rightarrow$ MySQL transaction (`UPDATE slots` & `INSERT INTO bookings`) $\rightarrow$ Render booking receipt.

### Module 4: Keyword AI Chatbot
* **Purpose:** Automated response system answering frequent client questions.
* **Files:** `chatbot.php`, `chatbot` SQL table.
* **Responsibilities:** Parse incoming query text, split keyword strings, match query tokens against stored answers, and return response text.

---

## 7. Application Workflow

The diagram below details the operational workflow from application execution to client response:

```
[ Application Launch ]
         │
         ▼
[ Tomcat Server Starts (Port 8080) ]  ───►  [ Apache / XAMPP Server Starts (Port 80) ]
         │                                              │
         ▼                                              ▼
[ Load web.xml Configuration ]               [ Load db.php MySQL Connection ]
         │                                              │
         ▼                                              ▼
[ Test MySQL Connection (DBConnection) ]     [ Test MySQL Connection ($conn) ]
         │                                              │
         +-----------------------+----------------------+
                                 │
                                 ▼
                     [ User Opens Home Page ]
                                 │
         +-----------------------+-----------------------+
         │                                               │
         ▼                                               ▼
[ E-Commerce Workflow ]                      [ Appointment Workflow ]
         │                                               │
   User Logs In                                   Select Booking Date
         │                                               │
   Browse Products                                Choose Time Slot & Fill Form
         │                                               │
   Add Items to Cart (Session)                    Submit UPI Payment Reference
         │                                               │
   Proceed to Payment                            Atomic MySQL Transaction
         │                                               │
   Process Order Receipt                         Update Slot -> 'Booked'
         │                                               │
   Append to data/orders.xml                     Insert into 'bookings' Table
         │                                               │
   Render Order Confirmation                     Render Success Confirmation
```

---

## 8. Request Lifecycle

### Detailed Request Lifecycle: Product Purchase & XML Order Generation

```
User Action (Click "Place Order")
  │
  ▼
processPayment.jsp (Server Script Execution)
  │
  ├─► 1. session.getAttribute("cart") ──► Retrieve HashMap<Integer, Integer>
  ├─► 2. Read Form Parameters ──────────► Name, Phone, Address, Payment Method
  ├─► 3. Query `products` Table ─────────► Obtain Product Names and Prices
  ├─► 4. Instantiates DTO Classes ────────► OrderItem, NewOrderData
  │
  ▼
OrderXmlUtil.appendOrder(xmlFile, orderData)
  │
  ├─► 1. loadOrCreateDocument() ─────────► Parse or initialize data/orders.xml
  ├─► 2. generate Order ID ──────────────► ORD-UUID (e.g., ORD-A1B2C3D4)
  ├─► 3. document.createElement() ───────► Build <order>, <customer>, <payment>, <items>
  ├─► 4. appendChild() ──────────────────► Insert Node hierarchy under root <orders>
  ├─► 5. writeDocument() ────────────────► Transformer writes UTF-8 XML to Disk
  │
  ▼
Clear Session Cart
  │
  ▼
Render Order Success View to Client Browser
```

---

## 9. Database Design

### Database Engine
* **Engine:** MySQL 8.0 (`InnoDB` engine enforcing foreign keys and ACID transactions).
* **Character Set:** `utf8mb4` with collation `utf8mb4_0900_ai_ci`.

### Database Schema Table Definitions

#### 1. `users` Table
Stores registered customer and administrator accounts.
```sql
CREATE TABLE `users` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(100) DEFAULT NULL,
  `email` VARCHAR(100) DEFAULT NULL,
  `password` VARCHAR(100) DEFAULT NULL,
  `phone` VARCHAR(15) DEFAULT NULL,
  `role` VARCHAR(20) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `email` (`email`)
);
```

#### 2. `user_sessions` Table
Audits active and historical user login sessions.
```sql
CREATE TABLE `user_sessions` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `user_id` INT DEFAULT NULL,
  `session_id` VARCHAR(255) DEFAULT NULL,
  `login_time` DATETIME DEFAULT NULL,
  `logout_time` DATETIME DEFAULT NULL,
  `status` VARCHAR(20) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `user_id` (`user_id`),
  CONSTRAINT `user_sessions_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `users` (`id`)
);
```

#### 3. `user_cookies` Table
Logs cookie assignments issued to authenticated users.
```sql
CREATE TABLE `user_cookies` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `user_id` INT DEFAULT NULL,
  `cookie_name` VARCHAR(100) DEFAULT NULL,
  `cookie_value` VARCHAR(255) DEFAULT NULL,
  `created_at` TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  KEY `user_id` (`user_id`),
  CONSTRAINT `user_cookies_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `users` (`id`)
);
```

#### 4. `products` Table
Stores retail product details for the salon e-commerce store.
```sql
CREATE TABLE `products` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(150) NOT NULL,
  `price` INT NOT NULL,
  `image` VARCHAR(200) NOT NULL,
  `active` TINYINT(1) NOT NULL DEFAULT '1',
  PRIMARY KEY (`id`),
  UNIQUE KEY `name` (`name`)
);
```

#### 5. `staff` Table
Stores employee records for salon staff management.
```sql
CREATE TABLE `staff` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(100) DEFAULT NULL,
  `role` VARCHAR(100) DEFAULT NULL,
  `experience` INT DEFAULT NULL,
  `phone` VARCHAR(15) DEFAULT NULL,
  `salary` DOUBLE DEFAULT NULL,
  `status` VARCHAR(50) DEFAULT 'Active',
  PRIMARY KEY (`id`)
);
```

#### 6. `slots` Table
Defines available and booked salon service appointment times.
```sql
CREATE TABLE `slots` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `service_name` VARCHAR(100) DEFAULT NULL,
  `slot_date` DATE DEFAULT NULL,
  `slot_time` VARCHAR(20) DEFAULT NULL,
  `status` VARCHAR(20) DEFAULT 'Available',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uq_slot_date_time` (`slot_date`, `slot_time`)
);
```

#### 7. `bookings` Table
Stores confirmed customer appointment bookings linked to slots.
```sql
CREATE TABLE `bookings` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `slot_id` INT DEFAULT NULL,
  `customer_name` VARCHAR(100) DEFAULT NULL,
  `customer_email` VARCHAR(100) DEFAULT NULL,
  `booking_date` TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP,
  `payment_amount` DECIMAL(10,2) NOT NULL DEFAULT '0.00',
  `payment_method` VARCHAR(20) NOT NULL DEFAULT 'UPI',
  `payment_status` VARCHAR(20) NOT NULL DEFAULT 'Paid',
  `transaction_id` VARCHAR(100) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uq_booking_slot` (`slot_id`),
  CONSTRAINT `bookings_ibfk_1` FOREIGN KEY (`slot_id`) REFERENCES `slots` (`id`) ON DELETE CASCADE
);
```

#### 8. `chatbot` Table
Stores keyword patterns and answers for the automated query chatbot.
```sql
CREATE TABLE `chatbot` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `keywords` VARCHAR(255) DEFAULT NULL,
  `answer` TEXT,
  PRIMARY KEY (`id`)
);
```

### Entity Relationship Diagram (ASCII)

```
  +---------------+             1:N             +-------------------+
  |     users     |----------------------------<|   user_sessions   |
  +-------+-------+                             +-------------------+
          |
          | 1:N                                 +-------------------+
          +------------------------------------<|   user_cookies    |
                                                +-------------------+

  +---------------+             1:1             +-------------------+
  |     slots     |<---------------------------|     bookings      |
  +---------------+                             +-------------------+

  +---------------+     +---------------+       +-------------------+
  |   products    |     |     staff     |       |     chatbot       |
  +---------------+     +---------------+       +-------------------+
```

---

## 10. API Documentation

### Java Servlet Endpoints (`Tomcat: http://localhost:8080/SalonBooking/`)

#### 1. Login Endpoint
* **Method:** `POST`
* **URL:** `/LoginServlet`
* **Auth Required:** No
* **Request Parameters:** `loginEmail`, `loginpassword`, `loginType`
* **Response:** Redirect to `/admin/viewProducts.jsp` (if admin), `/user/products.jsp` (if customer), or `/index.jsp?error=...`

#### 2. Register Endpoint
* **Method:** `POST`
* **URL:** `/RegisterServlet`
* **Auth Required:** No
* **Request Parameters:** `regName`, `regEmail`, `regpassword`, `regPhone`, `regType`
* **Response:** Redirect to `/index.jsp?msg=registered` or error parameter.

#### 3. Add Product Endpoint
* **Method:** `POST`
* **URL:** `/AddProductServlet`
* **Auth Required:** Yes (`admin`)
* **Request Parameters:** `name`, `price`, `image`, `active`
* **Response:** Redirect to `/admin/viewProducts.jsp`

#### 4. Update Product Endpoint
* **Method:** `POST`
* **URL:** `/UpdateProductServlet`
* **Auth Required:** Yes (`admin`)
* **Request Parameters:** `id`, `name`, `price`, `image`, `active`
* **Response:** Redirect to `/admin/viewProducts.jsp`

#### 5. Delete Product Endpoint
* **Method:** `GET`
* **URL:** `/DeleteProductServlet?id={id}`
* **Auth Required:** Yes (`admin`)
* **Response:** Redirect to `/admin/viewProducts.jsp`

#### 6. Staff Endpoints (`AddStaffServlet`, `UpdateStaffServlet`, `DeleteStaffServlet`)
* **Methods:** `POST` / `GET`
* **URLs:** `/AddStaffServlet`, `/UpdateStaffServlet`, `/DeleteStaffServlet?id={id}`
* **Auth Required:** Yes (`admin`)
* **Response:** Redirect to `/admin/viewStaff.jsp`

---

### PHP Endpoints (`Apache: http://localhost/SalonBookingPHP/`)

#### 7. Chatbot Query Endpoint
* **Method:** `POST`
* **URL:** `/chatbot.php`
* **Auth Required:** No
* **Request Parameters:** `message` (e.g., `"what are your working hours"`)
* **Response:** Plain Text (e.g., `"We are open from 9 AM to 8 PM."`)

#### 8. Slot Booking Endpoint
* **Method:** `POST`
* **URL:** `/slot_booking.php`
* **Auth Required:** No
* **Request Parameters:** `slot_id`, `slot_date`, `name`, `email`, `payment_method`, `transaction_id`, `book`
* **Response:** HTML page containing success or failure status alert.

---

## 11. Authentication Flow

```
User Enters Credentials on index.jsp
                 │
                 ▼
Submits POST Request to /LoginServlet
                 │
                 ▼
Server Regex Validation:
  ├── Email format match (^[^@\s]+@[^@\s]+\.[^@\s]+$)
  └── Password strength check ((?=.*[a-z])(?=.*[A-Z])(?=.*\d)...)
                 │
                 ▼
Query Database: SELECT * FROM users WHERE email=? AND password=? AND role=?
                 │
        +--------+--------+
        |                 |
  Match Found       Invalid Credentials
        │                 │
        ▼                 ▼
Create HttpSession:  Redirect index.jsp?error=invalid
  ├── session.setAttribute("userId", rs.getInt("id"))
  ├── session.setAttribute("userName", rs.getString("name"))
  └── session.setAttribute("role", role)
        │
        ▼
Add Client Cookie & Write Audit Records:
  ├── Set HTTP Cookie `userEmail` (Max-Age: 3600s)
  ├── INSERT INTO user_cookies (user_id, cookie_name, cookie_value)
  └── INSERT INTO user_sessions (user_id, session_id, login_time, status='ACTIVE')
        │
        ▼
Redirect to User / Admin Dashboard Based on Role
```

---

## 12. Core Business Logic

### Key Business Rules & Algorithms
1. **Password Complexity Policy:** Passwords must contain a minimum of 8 characters, including at least one uppercase letter, one lowercase letter, one numeric digit, and one special character (`@$!%*?&`).
2. **Atomic Slot Reservation:** Slot bookings use database transactions. If two users attempt to book the same slot simultaneously, the first transaction updates `slots SET status='Booked' WHERE status='Available'`. If `affected_rows !== 1`, the transaction rolls back, preventing double bookings.
3. **XML Receipt Generation:** Orders are appended to `data/orders.xml` with a unique ID format (`ORD-` + 8-character UUID) and serialized timestamp.
4. **Session Shopping Cart Aggregation:** Cart items are aggregated in a session-bound `HashMap<Integer, Integer>` (Product ID $\rightarrow$ Quantity).
5. **Chatbot Keyword Matching Algorithm:** Explodes stored keyword strings by commas and performs case-insensitive substring checks against user message tokens.

---

## 13. Data Flow

```
  User Action (e.g., Book Appointment Slot)
                     │
                     ▼
             HTML / JSP / PHP Form
                     │
                     ▼
           Input Sanitization & Regex
                     │
                     ▼
         Controller / Handler Logic
                     │
         +-----------+-----------+
         │                       │
         ▼                       ▼
MySQL PreparedStatement     XML File Serialization
         │                       │
         ▼                       ▼
  Database Table           data/orders.xml
         │                       │
         +-----------+-----------+
                     │
                     ▼
        HTTP Response View Rendered
```

---

## 14. Error Handling

* **Validation Exceptions:** Input data failing regex filters (e.g., malformed email or weak password) triggers immediate redirects with descriptive query parameters (`?error=invalidemail` or `?error=weakpassword`).
* **Database Unique Constraint Violations:** Handles `SQLIntegrityConstraintViolationException` during registration to redirect with `?error=exists` if an email is already registered.
* **SQL Transaction Failures:** PHP booking handlers wrap operations in `$conn->begin_transaction()`. Failures execute `$conn->rollback()` and return user-friendly error messages.
* **XML Processing Exceptions:** `OrderXmlUtil` methods throw `Exception` up to calling scripts, which log stack traces to prevent corrupt XML file writes.

---

## 15. Configuration

### 1. Maven Project Object Model (`pom.xml`)
Defines Java version dependencies (Java 8/11 compiler plugin) and the `javax.servlet-api` requirement under `war` packaging.

### 2. Servlet Deployment Descriptor (`src/main/webapp/WEB-INF/web.xml`)
Maps servlets (`LoginServlet`, `RegisterServlet`, `AddProductServlet`, etc.) to URL patterns and defines `home.jsp` as the welcome file.

### 3. Java Database Connection Utility (`DBConnection.java`)
Configures JDBC credentials (`jdbc:mysql://localhost:3306/salon_db`, User: `root`, Password: `12345`).

### 4. PHP Database Connection Configuration (`SalonBookingPHP/db.php`)
Configures PHP `mysqli` database credentials for `salon_db`.

---

## 16. Dependencies

| Package / Library | Source / Scope | Purpose |
| :--- | :--- | :--- |
| `javax.servlet-api` (4.0.1) | Maven Dependency (`provided`) | Core Servlet interface specifications for request/response handling. |
| MySQL Connector/J | System Classpath / Server | JDBC driver translating Java SQL calls into MySQL network protocol commands. |
| W3C DOM API | Java SE Standard Library | XML document tree building, parsing, and serialization. |
| PHP `mysqli` Extension | PHP Core Extension | Native database driver for PHP to interact with MySQL. |

---

## 17. Security

* **SQL Injection Prevention:** All SQL queries involving dynamic parameters use parameterized `PreparedStatement` (Java) or `mysqli::prepare()` (PHP).
* **Password Validation:** Regex enforcement guarantees password complexity prior to database insertion.
* **Cross-Origin Resource Sharing (CORS):** `chatbot.php` explicitly restricts accepted origins using `Access-Control-Allow-Origin: http://localhost:8080`.
* **Role-Based Routing:** Servlets inspect session attributes (`role`) to separate administrative actions from customer actions.

---

## 18. Performance

* **Database Indexing:** Primary key auto-increment indexes on all tables, plus unique composite indexes on `slots(slot_date, slot_time)` and `bookings(slot_id)`.
* **Atomic Query Execution:** Single-row update checks prevent lock contention during peak booking times.
* **Efficient XML Parsing:** DOM XML document whitespace removal routines minimize XML document size on disk.

---

## 19. Deployment

### Local Environment Setup

1. **Install Prerequisites:**
   * JDK 8 or JDK 11
   * Apache Maven 3.x
   * MySQL Server 8.0
   * Apache Tomcat 9.x/10.x
   * XAMPP / WampServer (with PHP 7.4+ and Apache)

2. **Database Setup:**
   * Open MySQL terminal or phpMyAdmin.
   * Create database and import schema:
     ```bash
     mysql -u root -p -e "CREATE DATABASE salon_db;"
     mysql -u root -p salon_db < database.sql
     ```

3. **Deploy Java Application on Tomcat:**
   * Build WAR file using Maven: `mvn clean package`
   * Copy generated `SalonBooking.war` to Tomcat's `webapps/` folder.
   * Start Tomcat: `bin/startup.bat` (accessible at `http://localhost:8080/SalonBooking/`).

4. **Deploy PHP Application on Apache:**
   * Copy the `SalonBookingPHP/` folder to XAMPP's `htdocs/` directory (`C:\xampp\htdocs\SalonBookingPHP`).
   * Start Apache from XAMPP Control Panel (accessible at `http://localhost/SalonBookingPHP/`).

---

## 20. Project Workflow

```
Dev Writes Feature / Fix  ──►  Update DB Schema (database.sql)  ──►  Run Maven Package
                                                                          │
                                                                          ▼
Verify PHP Pages (Apache)  ◄───  Verify Java Pages (Tomcat)  ◄───  Deploy WAR to Server
```

---

## 21. Design Decisions

1. **Why a Hybrid Java & PHP Architecture?**
   * *Trade-off:* Hybrid architectures introduce dual web-server management (Tomcat and Apache).
   * *Rationale:* Java Servlets provide robust enterprise session control, strict DTO structures, and XML processing for e-commerce, while PHP provides rapid, lightweight scripting for dynamic appointment availability grids.
2. **Why XML for Orders & MySQL for Relational Data?**
   * *Trade-off:* XML file storage requires DOM parsing and lacks relational joins.
   * *Rationale:* Archiving completed orders in XML isolates historical sales receipts from relational database migrations and provides portable data backups.
3. **Why Native JDBC & Prepared Statements instead of an ORM?**
   * *Rationale:* Eliminates framework overhead (e.g., Hibernate/JPA), providing direct control over SQL query execution and database performance.

---

## 22. Important Classes

### 1. `util.DBConnection`
* **Responsibilities:** Manages JDBC driver initialization and opens MySQL database connections.
* **Methods:** `getConnection()`
* **Lifecycle:** Static factory utility class.

### 2. `util.OrderXmlUtil`
* **Responsibilities:** Performs DOM parsing, structural document generation, whitespace cleanup, and XML persistence for customer orders.
* **Inner Classes:** `OrderItem`, `OrderRecord`, `NewOrderData`.
* **Methods:** `appendOrder()`, `readOrders()`, `loadOrCreateDocument()`, `writeDocument()`, `removeWhitespaceNodes()`.
* **Lifecycle:** Utility class with static thread-safe (`synchronized`) methods.

### 3. `servlets.LoginServlet`
* **Responsibilities:** Authenticates login attempts, validates input syntax, manages user sessions/cookies, and updates audit tables.
* **Methods:** `doGet()`, `doPost()`.
* **Lifecycle:** Managed by the Servlet Container (Tomcat).

---

## 23. Important Functions

### 1. `OrderXmlUtil.appendOrder(File xmlFile, NewOrderData orderData)`
* **Inputs:** `File` (path to `orders.xml`), `NewOrderData` (DTO containing order parameters).
* **Outputs:** Synchronized `OrderRecord` instance containing the generated order ID and timestamp.
* **Purpose:** Thread-safe XML node creation and document writing for completed orders.
* **Caller:** `user/processPayment.jsp`.

### 2. `OrderXmlUtil.readOrders(File xmlFile)`
* **Inputs:** `File` object (`data/orders.xml`).
* **Outputs:** `List<OrderRecord>` containing order histories.
* **Purpose:** Parses structural XML nodes into Java DTO objects for admin view rendering.
* **Caller:** `admin/viewOrders.jsp`.

### 3. `slot_booking.php` Booking Transaction Handler
* **Inputs:** `POST` fields (`slot_id`, `slot_date`, `name`, `email`, `payment_method`, `transaction_id`).
* **Outputs:** HTML view with transaction confirmation or failure notice.
* **Purpose:** Executes atomic MySQL transactions (`UPDATE slots` and `INSERT INTO bookings`).

---

## 24. Sequence Diagrams (ASCII)

### Sequence Diagram 1: User Authentication Flow

```
User               index.jsp          LoginServlet           MySQL DB            Browser Cookies
 │                     │                   │                    │                       │
 │─── Enter Credentials ──►                │                    │                       │
 │    Submit Form      │                   │                    │                       │
 │                     │─── POST Request ─►│                    │                       │
 │                     │                   │── Regex Check ──┐  │                       │
 │                     │                   │                 │  │                       │
 │                     │                   │◄────────────────┘  │                       │
 │                     │                   │                    │                       │
 │                     │                   │── SELECT Users ───►│                       │
 │                     │                   │◄── Return Record ──│                       │
 │                     │                   │                    │                       │
 │                     │                   │── INSERT Session ─►│                       │
 │                     │                   │── INSERT Cookie ──►│                       │
 │                     │                   │                    │                       │
 │                     │                   │── Add Cookie ─────────────────────────────►│
 │                     │                   │                                            │
 │◄──────────────── Redirect to Dashboard ─│                                            │
```

### Sequence Diagram 2: Slot Booking Transaction Flow

```
Customer           slot_booking.php         MySQL Database ($conn)
   │                       │                          │
   │── Choose Date ───────►│                          │
   │                       │── SELECT Available ─────►│
   │                       │◄─ Return Open Slots ─────│
   │                       │                          │
   │── Submit Booking & ──►│                          │
   │   UPI Reference       │                          │
   │                       │── Begin Transaction ────►│
   │                       │── UPDATE slots ─────────►│ (Lock Slot status='Booked')
   │                       │── INSERT INTO bookings ─►│ (Save Payment Details)
   │                       │                          │
   │                       │── Commit / Rollback ────►│
   │◄── Render Receipt ────│                          │
```
