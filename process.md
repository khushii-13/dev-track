-----------------------------------
DEV-TRACK: STEP-BY-STEP GUIDE
-----------------------------------

TOOLS REQUIRED:
- XAMPP (Apache, MySQL)
- PHP
- MySQL
- HTML/CSS/JS
- AJAX

-----------------------------------
1. INSTALL & SETUP XAMPP
-----------------------------------
- Download XAMPP: https://www.apachefriends.org/index.html
- Install it
- Start Apache and MySQL from the XAMPP Control Panel

-----------------------------------
2. PROJECT SETUP
-----------------------------------
- Go to C:\xampp\htdocs
- Create folder: dev-track

-----------------------------------
3. CREATE DATABASE
-----------------------------------
- Open: http://localhost/phpmyadmin
- Create database: devtrack

- Run these SQL queries:

-- USERS TABLE
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL
);

-- LOGS TABLE
CREATE TABLE logs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    log_date DATE NOT NULL,
    title VARCHAR(255),
    notes TEXT,
    links TEXT,
    credentials TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

-----------------------------------
4. PROJECT STRUCTURE
-----------------------------------
dev-track/
├── db.php
├── auth.php
├── index.php           --> landing / welcome page
├── register.php        --> registration page
├── login.php           --> login page
├── logout.php          --> logout script
├── dashboard.php       --> shows list of logs
├── add_log.php         --> form to add new log
├── view_log.php        --> view log details
├── /ajax/
│   └── save_log.php    --> saves log via AJAX
├── /css/
│   └── styles.css
├── /js/
│   └── scripts.js

-----------------------------------
5. FILE: db.php
-----------------------------------
<?php
$conn = new mysqli("localhost", "root", "", "devtrack");
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
?>

-----------------------------------
6. FILE: auth.php (for session check)
-----------------------------------
<?php
session_start();
if (!isset($_SESSION['user_id'])) {
    header("Location: login.php");
    exit;
}
?>

-----------------------------------
7. FILE: register.php
-----------------------------------
- HTML form (username, password)
- PHP: Insert into `users` table after hashing password using `password_hash`

-----------------------------------
8. FILE: login.php
-----------------------------------
- HTML form (username, password)
- PHP: Verify credentials using `password_verify`, then set session

-----------------------------------
9. FILE: logout.php
-----------------------------------
<?php
session_start();
session_destroy();
header("Location: login.php");
exit;
?>

-----------------------------------
10. FILE: dashboard.php
-----------------------------------
- Include auth.php
- Fetch and display all logs by current user (with log_date and title)
- Each item links to view_log.php?id=...

-----------------------------------
11. FILE: add_log.php
-----------------------------------
- HTML form with:
  - log_date
  - title
  - notes
  - links
  - credentials
- JS: Submit via AJAX to ajax/save_log.php

-----------------------------------
12. FILE: /ajax/save_log.php
-----------------------------------
<?php
session_start();
require '../db.php';

if (!isset($_SESSION['user_id'])) {
    echo "Unauthorized.";
    exit;
}

$user_id = $_SESSION['user_id'];
$log_date = $_POST['log_date'];
$title = $_POST['title'];
$notes = $_POST['notes'];
$links = $_POST['links'];
$credentials = $_POST['credentials'];

$sql = "INSERT INTO logs (user_id, log_date, title, notes, links, credentials) VALUES (?, ?, ?, ?, ?, ?)";
$stmt = $conn->prepare($sql);
$stmt->bind_param("isssss", $user_id, $log_date, $title, $notes, $links, $credentials);

if ($stmt->execute()) {
    echo "Log saved successfully.";
} else {
    echo "Error: " . $conn->error;
}
?>

-----------------------------------
13. FILE: view_log.php
-----------------------------------
- Get log id from URL
- Fetch log from DB where id = ? and user_id = session user
- Display full details: title, notes, links, credentials

-----------------------------------
14. FILE: js/scripts.js
-----------------------------------
document.getElementById('logForm').addEventListener('submit', function(e) {
    e.preventDefault();
    const formData = new FormData(this);
    fetch('ajax/save_log.php', {
        method: 'POST',
        body: formData
    }).then(res => res.text()).then(data => {
        alert(data);
    });
});

-----------------------------------
15. FILE: css/styles.css
-----------------------------------
- Add styles for form, dashboard, log view
- Optional: responsive layout, dark mode toggle

-----------------------------------
16. TESTING THE PROJECT
-----------------------------------
- Visit http://localhost/dev-track
- Register a user
- Login
- Add logs
- View logs

-----------------------------------
17. OPTIONAL ADDITIONS
-----------------------------------
- Edit/Delete log
- Export to PDF
- Markdown editor for notes
- Search/filter logs
- Dark mode
- Notification system
