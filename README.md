# 10
 Follow the simple step-by-step instructions, and watch as the ingredients transform into a delectable masterpiece that will tantalize your taste buds and leave you craving for more. Perfect for gatherings, family meals, or a cozy night in, this recipe is bound to become a favorite in your repertoire."
database.php

<?php
$host = "localhost"; 
$username = "root";
$password = "";
$database = "student";

$conn = new mysqli($host, $username, $password, $database);

if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
?>

login.html

<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
</head>
<body>
    <h2>Login</h2>
    <form action="login.php" method="post">
        <label for="username">Username:</label>
        <input type="text" id="username" name="username" required><br><br>
        <label for="password">Password:</label>
        <input type="password" id="password" name="password" required><br><br>
        <input type="submit" value="Login">
    </form>
</body>
</html> 

login.php
<?php
session_start();
require_once("database.php");

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = $_POST["username"];
    $password = $_POST["password"];

    $stmt = $conn->prepare("SELECT  username, password FROM users WHERE username = ?");
    $stmt->bind_param("s", $username);
    $stmt->execute();
    $result = $stmt->get_result();

    if ($result->num_rows == 1) {
        $row = $result->fetch_assoc();

        if (password_verify($password, $row["password"])) {
            $_SESSION["username"] = $row["username"];
            header("Location: welcome.php");
        } else {
            echo "Login failed. Please try again.";
        }
    } else {
        echo "Login failed. Please try again.";
    }

    $stmt->close();
}

$conn->close();
?>

register.html
<!DOCTYPE html>
<html>
<head>
    <title>Register</title>
</head>
<body>
    <h2>Register</h2>
    <form action="register.php" method="post">
        <label for="username">Username:</label>
        <input type="text" id="username" name="username" required><br><br>
        <label for="password">Password:</label>
        <input type="password" id="password" name="password" required><br><br>
        <input type="submit" value="Register">
    </form>
</body>
</html>


register.php
<?php
require_once("database.php");

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = $_POST["username"];
    $password = password_hash($_POST["password"], PASSWORD_DEFAULT);

    $stmt = $conn->prepare("INSERT INTO users (username, password) VALUES (?, ?)");
    $stmt->bind_param("ss", $username, $password);

    if ($stmt->execute()) {
        echo "Registration successful. <a href='login.html'>Login</a>";
    } else {
        echo "Registration failed. Please try again.";
    }

    $stmt->close();
}

$conn->close();
?>

welcome.php
<?php
session_start();

if (!isset($_SESSION["username"])) {
    header("Location: login.html");
}

echo "Welcome, " . $_SESSION["username"] . "! You are successfully logged in.";
?>

