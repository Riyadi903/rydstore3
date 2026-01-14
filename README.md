<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>RYD STORE APK</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        /* Mencegah seleksi teks yang mengganggu klik di APK */
        * { -webkit-tap-highlight-color: transparent; user-select: none; }
        body { font-family: 'sans-serif'; background-color: #0b0e11; color: white; touch-action: manipulation; }
        .step-content { display: none; }
        .step-content.active { display: block; animation: fadeIn 0.2s ease-in; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        
        .card-op { background: #1c2127; padding: 15px; border: 2px solid #2d3748; border-radius: 12px; cursor: pointer; text-align: center; font-weight: bold; }
        .card-op.active { border-color: #f1c40f; color: #f1c40f; background: #242a31; }
        
        .product-item { background: #161a1e; border: 1px solid #2d3748; padding: 16px; border-radius: 12px; cursor: pointer; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; }
        
        /* Tombol melayang untuk kembali */
        .btn-nav { background: #1c2127; border: 1px solid #2d3748; border-radius: 50%; width: 40px; height: 40px; display: flex; align-items: center; justify-content: center; }
    </style>
</head>
<body class="pb-10">

    <nav class="bg-[#161a1e] p-4 sticky top-0 z-50 flex items-center gap-4 border-b border-gray-800">
        <div id="back-btn" onclick="goBack()" style="display:none;" class="btn-nav">
            <i class="fas fa-arrow-left text-yellow-500"></i>
        </div>
        <h1 class="font-bold text-lg uppercase tracking-tight">RYD <span class="text-yellow-500">STORE</span></h1>
    </nav>

    <div class="max-w-md mx-auto px-5 mt-6">

        <div id="step-1" class="step-content active">
            <div class="bg-[#161a1e] p-5 rounded-2xl border border-gray-800 mb-6">
                <label class="block text-[10px] text-gray-500 mb-2 font-bold uppercase">Nomor Tujuan</label>
                <input type="number" id="nomor_hp" inputmode="numeric" placeholder="08xxxxxxxxxx" class="w-full p-4 bg-gray-900 rounded-xl border border-gray-700 text-white font-bold outline-none focus:border-yellow-500 text-xl">
            </div>

            <p class="text-[10px] font-bold text-gray-500 mb-3 uppercase tracking-widest">Pilih Operator</p>
            <div class="grid grid-cols-2 gap-3 mb-8">
                <div onclick="setOp(this, 'TELKOMSEL')" class="card-op">TELKOMSEL</div>
                <div onclick="setOp(this, 'INDOSAT')" class="card-op">INDOSAT</div>
                <div onclick="setOp(this, 'XL')" class="card-op">XL AXIATA</div>
                <div onclick="setOp(this, 'THREE')" class="card-op">THREE (3)</div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="setKat('PULSA')" class="bg-gradient-to-br from-gray-800 to-gray-900 p-6 rounded-2xl border border-gray-700">
                    <i class="fas fa-mobile-alt text-yellow-500 text-3xl mb-2"></i>
                    <div class="font-bold text-sm uppercase">PULSA</div>
                </button>
                <button onclick="setKat('DATA')" class="bg-gradient-to-br from-gray-800 to-gray-900 p-6 rounded-2xl border border-gray-700">
                    <i class="fas fa-wifi text-blue-500 text-3xl mb-2"></i>
                    <div class="font-bold text-sm uppercase">DATA</div>
                </button>
            </div>
        </div>

        <div id="step-2" class="step-content">
            <div class="text-center mb-6">
                <h2 id="judul-kat" class="text-xl font-bold text-yellow-500 uppercase italic"></h2>
                <p class="text-[10px] text-gray-500 font-bold uppercase">Pilih Nominal</p>
            </div>
            <div id="list-container" class="space-y-2"></div>
        </div>

        <div id="step-3" class="step-content">
            <div class="bg-white text-black p-6 rounded-[2rem] shadow-2xl border-4 border-yellow-500">
                <div class="text-center mb-4">
                    <h2 class="font-black text-2xl italic uppercase leading-none">CHECKOUT</h2>
                    <p class="text-[9px] font-bold text-gray-400 mt-1 uppercase">Silakan Scan & Bayar</p>
                </div>
                
                <div class="bg-gray-100 p-2 rounded-xl mb-4 border border-gray-200">
                    <img src="https://lh3.googleusercontent.com/u/0/d/1DcaGaY-Xesvfzn9sgpDpcjOtELT0loar" class="w-full rounded-lg" alt="QRIS">
                </div>

                <div class="text-left space-y-2 border-t border-dashed border-gray-300 pt-4 text-xs font-bold uppercase">
                    <div class="flex justify-between"><span>No HP:</span><span id="f-no"></span></div>
                    <div class="flex justify-between"><span>Paket:</span><span id="f-item" class="text-yellow-600 text-right ml-4"></span></div>
                    <div class="flex justify-between text-lg pt-2 font-black border-t mt-2"><span>TOTAL:</span><span id="f-price" class="text-green-600 font-black"></span></div>
                </div>
            </div>
            <button onclick="konfirmasiWA()" class="w-full bg-yellow-500 text-black font-black py-5 rounded-2xl mt-6 shadow-xl uppercase tracking-tighter text-lg">KONFIRMASI VIA WHATSAPP</button>
        </div>

    </div>

    <script>
        let order = { hp: '', op: '', kat: '', item: '', price: '' };

        const db = {
            'TELKOMSEL': {
                'PULSA': [{n:'5.000', p:'7.500'}, {n:'10.000', p:'12.500'}, {n:'20.000', p:'22.500'}, {n:'50.000', p:'51.000'}, {n:'100.000', p:'99.000'}],
                'DATA': [{n:'OMG 1GB', p:'15.000'}, {n:'OMG 5GB', p:'42.000'}, {n:'OMG 10GB', p:'75.000'}]
            },
            'INDOSAT': {
                'PULSA': [{n:'5.000', p:'7.200'}, {n:'10.000', p:'12.200'}, {n:'20.000', p:'22.200'}, {n:'50.000', p:'51.000'}, {n:'100.000', p:'99.000'}],
                'DATA': [{n:'Freedom 2GB', p:'18.000'}, {n:'Freedom 8GB', p:'40.000'}, {n:'Freedom 12GB', p:'60.000'}]
            },
            'XL': {
                'PULSA': [{n:'5.000', p:'7.300'}, {n:'10.000', p:'12.300'}, {n:'20.000', p:'22.300'}, {n:'50.000', p:'51.200'}, {n:'100.000', p:'99.000'}],
                'DATA': [{n:'Xtra Combo 5GB', p:'35.000'}, {n:'Xtra Combo 10GB', p:'58.000'}, {n:'Xtra Combo 20GB', p:'89.000'}]
            },
            'THREE': {
                'PULSA': [{n:'5.000', p:'7.100'}, {n:'10.000', p:'12.100'}, {n:'20.000', p:'22.100'}, {n:'50.000', p:'50.800'}, {n:'100.000', p:'99.000'}],
                'DATA': [{n:'Happy 2GB', p:'12.000'}, {n:'Happy 5GB', p:'25.000'}, {n:'Happy 18GB', p:'55.000'}]
            }
        };

        function setOp(el, name) {
            document.querySelectorAll('.card-op').forEach(c => c.classList.remove('active'));
            el.classList.add('active');
            order.op = name;
        }

        function setKat(kat) {
            order.hp = document.getElementById('nomor_hp').value;
            if (!order.hp || !order.op) {
                alert("Nomor & Operator wajib diisi!");
                return;
            }
            order.kat = kat;
            document.getElementById('judul-kat').innerText = order.op + " " + kat;
            
            let html = "";
            db[order.op][kat].forEach(i => {
                html += `
                <div class="product-item" onclick="toCheckout('${i.n}', '${i.p}')">
                    <span class="font-bold uppercase text-sm">${order.op} ${i.n}</span>
                    <b class="text-yellow-500">Rp ${i.p}</b>
                </div>`;
            });
            document.getElementById('list-container').innerHTML = html;
            changePage(2);
        }

        function toCheckout(item, price) {
            order.item = item;
            order.price = price;
            document.getElementById('f-no').innerText = order.hp;
            document.getElementById('f-item').innerText = order.op + " " + item;
            document.getElementById('f-price').innerText = "Rp " + price;
            changePage(3);
        }

        function changePage(n) {
            document.querySelectorAll('.step-content').forEach(s => s.classList.remove('active'));
            document.getElementById('step-' + n).classList.add('active');
            document.getElementById('back-btn').style.display = (n > 1) ? 'flex' : 'none';
            window.scrollTo(0,0);
        }

        function goBack() {
            let now = 1;
            if (document.getElementById('step-2').classList.contains('active')) now = 1;
            if (document.getElementById('step-3').classList.contains('active')) now = 2;
            changePage(now);
        }

        function konfirmasiWA() {
            const wa = "6285881934739";
            const text = `*KONFIRMASI RYD STORE*\n------------------\n📱 No HP: ${order.hp}\n🏢 Operator: ${order.op}\n📦 Item: ${order.kat} ${order.item}\n💰 Total: Rp ${order.price}\n------------------\n*STATUS: SUDAH TRANSFER*`;
            window.open(`https://wa.me/${wa}?text=${encodeURIComponent(text)}`, '_blank');
        }
    </script>
</body>
</html>
