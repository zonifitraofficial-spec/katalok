<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Katalog Sales</title>
<style>
    * { margin: 0; padding: 0; box-sizing: border-box; font-family: system-ui, -apple-system, sans-serif; }
    body { background: #f5f5f7; color: #1d1d1f; }
    header { background: #007aff; color: white; padding: 16px; position: sticky; top: 0; z-index: 10; }
    header h1 { font-size: 18px; font-weight: 600; }
   .container { padding: 16px; padding-bottom: 80px; }
   .search { width: 100%; padding: 12px; border-radius: 12px; border: none; background: white; margin-bottom: 16px; font-size: 16px; }

   .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
   .card { background: white; border-radius: 16px; overflow: hidden; box-shadow: 0 2px 8px rgba(0,0,0,0.08); }
   .card img { width: 100%; height: 140px; object-fit: cover; background: #eee; }
   .card-body { padding: 12px; }
   .card-body h3 { font-size: 15px; margin-bottom: 4px; }
   .price { color: #007aff; font-weight: 600; font-size: 16px; }
   .desc { font-size: 13px; color: #666; margin-top: 4px; }
   .btn { padding: 8px 12px; background: #007aff; color: white; border: none; border-radius: 8px; width: 100%; margin-top: 8px; font-size: 14px; }

   .fab { position: fixed; bottom: 20px; right: 20px; width: 56px; height: 56px; border-radius: 50%; background: #007aff; color: white; border: none; font-size: 28px; box-shadow: 0 4px 12px rgba(0,0,0,0.2); }

   .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 20; }
   .modal.active { display: flex; align-items: center; justify-content: center; }
   .modal-content { background: white; width: 90%; max-width: 400px; border-radius: 16px; padding: 20px; }
   .modal-content input,.modal-content textarea { width: 100%; padding: 10px; margin-top: 8px; border: 1px solid #ddd; border-radius: 8px; }
   .modal-actions { display: flex; gap: 8px; margin-top: 16px; }
   .modal-actions button { flex: 1; padding: 12px; border: none; border-radius: 8px; font-size: 16px; }
   .btn-save { background: #007aff; color: white; }
   .btn-cancel { background: #e5e5ea; }

   .empty { text-align: center; padding: 40px 20px; color: #888; }
</style>
</head>
<body>

<header>
    <h1>📦 Katalog Sales</h1>
</header>

<div class="container">
    <input type="text" class="search" placeholder="Cari produk..." id="searchBox" onkeyup="render()">

    <div class="grid" id="productGrid"></div>

    <div class="empty" id="emptyState" style="display:none;">
        Belum ada produk. Klik tombol + untuk tambah.
    </div>
</div>

<button class="fab" onclick="openModal()">+</button>

<div class="modal" id="modal">
    <div class="modal-content">
        <h3>Tambah Produk</h3>
        <input type="text" id="pName" placeholder="Nama produk">
        <input type="number" id="pPrice" placeholder="Harga">
        <textarea id="pDesc" placeholder="Deskripsi singkat" rows="3"></textarea>
        <input type="url" id="pImg" placeholder="Link gambar">
        <div class="modal-actions">
            <button class="btn-cancel" onclick="closeModal()">Batal</button>
            <button class="btn-save" onclick="saveProduct()">Simpan</button>
        </div>
    </div>
</div>

<script>
let products = JSON.parse(localStorage.getItem('katalog')) || [];

function render() {
    const grid = document.getElementById('productGrid');
    const empty = document.getElementById('emptyState');
    const q = document.getElementById('searchBox').value.toLowerCase();

    const filtered = products.filter(p =>
        p.name.toLowerCase().includes(q) || p.desc.toLowerCase().includes(q)
    );

    if (filtered.length === 0) {
        grid.innerHTML = '';
        empty.style.display = 'block';
        return;
    }

    empty.style.display = 'none';
    grid.innerHTML = filtered.map((p, i) => `
        <div class="card">
            <img src="${p.img || 'https://via.placeholder.com/300x200?text=No+Image'}" alt="${p.name}">
            <div class="card-body">
                <h3>${p.name}</h3>
                <div class="price">Rp ${Number(p.price).toLocaleString('id-ID')}</div>
                <div class="desc">${p.desc}</div>
                <button class="btn" onclick="shareWA(${i})">Share ke WhatsApp</button>
            </div>
        </div>
    `).join('');
}

function openModal() {
    document.getElementById('modal').classList.add('active');
}

function closeModal() {
    document.getElementById('modal').classList.remove('active');
    document.getElementById('pName').value = '';
    document.getElementById('pPrice').value = '';
    document.getElementById('pDesc').value = '';
    document.getElementById('pImg').value = '';
}

function saveProduct() {
    const name = document.getElementById('pName').value;
    const price = document.getElementById('pPrice').value;
    const desc = document.getElementById('pDesc').value;
    const img = document.getElementById('pImg').value;

    if (!name ||!price) {
        alert('Nama dan harga wajib diisi');
        return;
    }

    products.unshift({ name, price, desc, img });
    localStorage.setItem('katalog', JSON.stringify(products));
    render();
    closeModal();
}

function shareWA(i) {
    const p = products[i];
    const text = `*${p.name}*%0AHarga: Rp ${Number(p.price).toLocaleString('id-ID')}%0A${p.desc}%0A${p.img}`;
    window.open(`https://wa.me/?text=${text}`, '_blank');
}

// Load awal
render();
</script>

</body>
</html>
