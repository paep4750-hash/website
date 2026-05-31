<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <title>Action RPG Battle</title>
    <style>
        body { font-family: sans-serif; text-align: center; background: #222; color: white; }
        #game-ui { border: 2px solid #fff; width: 80%; margin: 20px auto; padding: 20px; }
    </style>
</head>
<body>

<div id="game-ui">
    <h1>ด่าน: <span id="level">1</span>/10</h1>
    <h3>เงิน: <span id="money">0</span> | เลือด: <span id="hp">100</span> | พลังป้องกัน: <span id="def">0</span></h3>
    <button onclick="fight()">ต่อสู้ (เก็บเงิน)</button>
    
    <hr>
    <h2>ร้านค้า</h2>
    <div id="shop">
        <p>ชุดเกราะเหล็ก (Def +10): ราคา 500 | สต๊อก: <span id="stock-armor">5</span> 
           <button onclick="buyItem('armor')">ซื้อ</button></p>
    </div>
</div>

<script>
    let player = { money: 0, hp: 100, def: 0, level: 1 };
    let shop = { armor: { price: 500, stock: 5 } };

    function updateUI() {
        document.getElementById('level').innerText = player.level;
        document.getElementById('money').innerText = player.money;
        document.getElementById('hp').innerText = player.hp;
        document.getElementById('def').innerText = player.def;
        document.getElementById('stock-armor').innerText = shop.armor.stock;
    }

    function fight() {
        if (player.hp <= 0) { alert("ตายแล้ว! เริ่มใหม่"); player.hp = 100; return; }
        
        // ต่อสู้: ได้เงิน และโดนดาเมจ
        let damage = Math.max(0, 10 - player.def); 
        player.hp -= damage;
        player.money += 100;

        // ผ่านด่านทุกๆ 5 ครั้ง
        if (player.money % 500 === 0 && player.level < 10) {
            player.level++;
            alert("ผ่านด่าน! เติมสต๊อกร้านค้าให้แล้ว");
            restock();
        }
        updateUI();
    }

    function buyItem(item) {
        if (shop[item].stock > 0 && player.money >= shop[item].price) {
            player.money -= shop[item].price;
            shop[item].stock -= 1;
            player.def += 10;
            alert("ซื้อสำเร็จ!");
        } else {
            alert("สินค้าหมด หรือ เงินไม่พอ!");
        }
        updateUI();
    }

    function restock() {
        shop.armor.stock = 5;
        updateUI();
    }
</script>
</body>
</html>
