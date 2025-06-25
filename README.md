<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Fraud Detection</title>
    <style>
        body { font-family: Arial; background: #f3f3f3; }
        .container { max-width: 400px; margin: 80px auto; padding: 30px; background: #fff; border-radius: 6px; }
        #result, #fraudResult { margin-top: 30px; text-align: center; font-size: 1.2em; }
    </style>
</head>
<body>
    <div class="container">
        <h2>Login</h2>
        <form id="loginForm">
            <input type="text" id="username" placeholder="Username" required />
            <input type="password" id="password" placeholder="Password" required />
            <button type="submit">Login</button>
        </form>
        <div id="result"></div>
        <div id="fraudSection" style="display:none;">
            <hr>
            <h3>Upload CSV for Fraud Check</h3>
            <input type="file" id="csvInput" accept=".csv" />
            <button id="detectBtn">Detect Fraud</button>
            <div id="fraudResult"></div>
        </div>
    </div>
    <script>
        document.getElementById("loginForm").onsubmit = async function(e) {
            e.preventDefault();
            const username = document.getElementById("username").value;
            const password = document.getElementById("password").value;
            const res = await fetch("/login", {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ username, password })
            });
            const data = await res.json();
            document.getElementById("result").innerHTML = data.message || "";
            if (data.success) {
                document.getElementById("fraudSection").style.display = "block";
            }
        };

        document.getElementById("detectBtn").onclick = async function() {
            const fileInput = document.getElementById("csvInput");
            if (!fileInput.files.length) {
                alert("Select a CSV file.");
                return;
            }
            const formData = new FormData();
            formData.append("file", fileInput.files[0]);
            const res = await fetch("/detect_fraud", {
                method: "POST",
                body: formData
            });
            const data = await res.json();
            document.getElementById("fraudResult").textContent = data.result;
        };
    </script>
</body>
</html>
