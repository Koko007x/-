# -<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>หน้าลงทะเบียน & เข้าสู่ระบบ</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; padding: 50px; }
        .hidden { display: none; }
        input { margin: 5px; padding: 8px; font-size: 18px; }
        button { padding: 10px 20px; font-size: 18px; cursor: pointer; }
        .bet-list { border: 1px solid #000; padding: 10px; margin-top: 20px; }
    </style>
</head>
<body>
    <div id="registerPage">
        <h2>ลงทะเบียน</h2>
        <input type="text" id="userId" placeholder="ID" required>
        <input type="password" id="password" placeholder="Password" required>
        <input type="text" id="telegram" placeholder="@ID Telegram" required>
        <button onclick="register()">เข้าสู่ระบบ</button>
        <br><br>
        <a href="admin.html">ไปยังหลังบ้าน (Admin)</a>
    </div>

    <div id="betPage" class="hidden">
        <h2>ส่งเดิมพัน</h2>
        <div>
            <input type="number" class="bet" maxlength="2" min="0" max="99">
            <input type="number" class="bet" maxlength="2" min="0" max="99">
            <input type="number" class="bet" maxlength="2" min="0" max="99">
            <input type="number" class="bet" maxlength="2" min="0" max="99">
        </div>
        <button onclick="submitBet()">ส่งเดิมพัน</button>
    </div>

    <!-- ส่วนแสดงข้อมูล Telegram และ เลขเดิมพัน -->
    <div class="bet-list">
        <h3>ข้อมูลการเดิมพันทั้งหมด</h3>
        <div id="betDataList"></div>
    </div>

    <script>
        // เก็บข้อมูลใน LocalStorage
        function register() {
            let userId = document.getElementById('userId').value;
            let password = document.getElementById('password').value;
            let telegram = document.getElementById('telegram').value;
            
            if (!userId || !password || !telegram) {
                alert("กรุณากรอกข้อมูลให้ครบถ้วน");
                return;
            }

            // ตรวจสอบข้อมูลที่ซ้ำกันใน LocalStorage
            let users = JSON.parse(localStorage.getItem("users")) || [];
            let isDuplicate = users.some(user => user.userId === userId || user.password === password || user.telegram === telegram);

            if (isDuplicate) {
                alert("ข้อมูลนี้ซ้ำกับผู้ใช้คนอื่น กรุณากรอกข้อมูลใหม่");
                return;
            }
            
            // หากไม่ซ้ำ ให้เก็บข้อมูลลงใน LocalStorage
            users.push({ userId, password, telegram });
            localStorage.setItem("users", JSON.stringify(users));
            
            document.getElementById("registerPage").classList.add("hidden");
            document.getElementById("betPage").classList.remove("hidden");
        }

        // ส่งเดิมพัน
        function submitBet() {
            let betNumbers = document.querySelectorAll('.bet');
            let betData = Array.from(betNumbers).map(input => input.value).join('-');
            
            if (betData.includes("--")) {
                alert("กรุณากรอกตัวเลขให้ครบ");
                return;
            }

            let telegram = document.getElementById('telegram').value;
            
            let bets = JSON.parse(localStorage.getItem("bets")) || [];
            bets.push({ telegram, betData });
            localStorage.setItem("bets", JSON.stringify(bets));

            // ลบแค่ข้อมูลการเดิมพัน แต่ไม่ลบข้อมูลผู้ใช้
            alert("ส่งเดิมพันเรียบร้อย!");
            window.location.href = "index.html"; // ไปหน้าลงทะเบียน
        }

        // ฟังก์ชันแสดงข้อมูลการเดิมพันทั้งหมด
        function displayBetData() {
            const betList = document.getElementById("betDataList");
            const bets = JSON.parse(localStorage.getItem("bets")) || [];
            
            if (bets.length === 0) {
                betList.innerHTML = "ยังไม่มีข้อมูลการเดิมพัน.";
            } else {
                bets.forEach((bet, index) => {
                    const betItem = document.createElement("p");
                    betItem.textContent = `#${index + 1} Telegram: ${bet.telegram} | เดิมพัน: ${bet.betData}`;
                    betList.appendChild(betItem);
                });
            }
        }

        // แสดงข้อมูลการเดิมพันทั้งหมดเมื่อโหลดหน้า
        window.onload = function() {
            displayBetData();
        }
    </script>
</body>
</html>
