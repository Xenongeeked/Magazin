<!DOCTYPE html>
<html lang="ro">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mini Magazin Online</title>
<style>
body{font-family:Arial;margin:0;background:#f2f2f2}
header{background:#222;color:white;padding:20px;text-align:center}
.container{padding:20px;max-width:1100px;margin:auto}
form{background:white;padding:15px;border-radius:12px;margin-bottom:15px;display:flex;flex-wrap:wrap;gap:10px}
input{padding:10px;border-radius:6px;border:1px solid #ccc;flex:1}
button{padding:10px 15px;background:#222;color:white;border:none;border-radius:6px;cursor:pointer}
button:hover{background:#444}
.search{margin-bottom:15px;width:100%;padding:10px;border-radius:6px;border:1px solid #ccc}
.products{display:grid;grid-template-columns:repeat(auto-fit,minmax(230px,1fr));gap:15px}
.card{background:white;border-radius:12px;overflow:hidden;box-shadow:0 3px 8px rgba(0,0,0,0.1);position:relative}
.card img{width:100%;height:160px;object-fit:cover}
.card-content{padding:10px}
.price{font-weight:bold;color:green}
.stock{font-size:14px;color:#555}
.actions{display:flex;gap:5px;margin-top:8px;flex-wrap:wrap}
.delete{background:red}
.edit{background:orange}
.buy{background:green}
.cart{background:white;padding:15px;border-radius:12px;margin-top:20px}
.cart h2{margin-top:0}
.admin{background:#fff3cd;border:1px solid #ffeeba;padding:10px;border-radius:8px;margin-bottom:10px}
</style>
</head>
<body>

<header>
<h1>Mini Magazin Online</h1>
<p>Admin: adaugă produse | Client: cumpără</p>
</header>

<div class="container">

<div class="admin">
<b>Panou Admin</b> (parola: 1234)
</div>

<input type="password" id="adminPass" placeholder="Introdu parola admin">

<form id="productForm" style="display:none">
<input type="text" id="name" placeholder="Nume produs" required>
<input type="number" id="price" placeholder="Preț" required>
<input type="number" id="stock" placeholder="Stoc" required>
<input type="text" id="image" placeholder="Link poză produs">
<button type="submit">Salvează</button>
</form>

<input type="text" id="search" class="search" placeholder="Caută produs...">

<div class="products" id="products"></div>

<div class="cart">
<h2>Coș de cumpărături</h2>
<div id="cartItems"></div>
<p><b>Total: <span id="total">0</span> RON</b></p>
</div>

</div>

<script>
const form = document.getElementById('productForm')
const productsDiv = document.getElementById('products')
const searchInput = document.getElementById('search')
const cartDiv = document.getElementById('cartItems')
const totalSpan = document.getElementById('total')
const adminPass = document.getElementById('adminPass')

let products = JSON.parse(localStorage.getItem('products')) || []
let cart = JSON.parse(localStorage.getItem('cart')) || []
let editIndex = -1

adminPass.addEventListener('input',()=>{
if(adminPass.value === "1234"){
form.style.display = "flex"
}
})

function saveProducts(){
localStorage.setItem('products', JSON.stringify(products))
}

function saveCart(){
localStorage.setItem('cart', JSON.stringify(cart))
}

function renderProducts(filter=""){
productsDiv.innerHTML = ''
products
.filter(p=>p.name.toLowerCase().includes(filter.toLowerCase()))
.forEach((p,index)=>{
const card = document.createElement('div')
card.className='card'
card.innerHTML = `
${p.image ? `<img src="${p.image}">` : ''}
<div class="card-content">
<h3>${p.name}</h3>
<p class="price">${p.price} RON</p>
<p class="stock">Stoc: ${p.stock}</p>
<div class="actions">
<button class="buy" onclick="buyProduct(${index})">Cumpără</button>
<button class="edit" onclick="editProduct(${index})">Edit</button>
<button class="delete" onclick="deleteProduct(${index})">Șterge</button>
</div>
</div>
`
productsDiv.appendChild(card)
})
}

function renderCart(){
cartDiv.innerHTML=""
let total=0
cart.forEach(item=>{
const p=document.createElement("p")
p.textContent=`${item.name} - ${item.price} RON`
cartDiv.appendChild(p)
total+=Number(item.price)
})

totalSpan.textContent=total
}

function buyProduct(index){
if(products[index].stock<=0){
alert("Nu mai este stoc")
return
}

products[index].stock--
cart.push(products[index])

saveProducts()
saveCart()

renderProducts(searchInput.value)
renderCart()
}

function deleteProduct(index){
products.splice(index,1)
saveProducts()
renderProducts(searchInput.value)
}

function editProduct(index){
const p = products[index]
document.getElementById('name').value = p.name
 document.getElementById('price').value = p.price
 document.getElementById('stock').value = p.stock
 document.getElementById('image').value = p.image
 editIndex = index
}

form.addEventListener('submit', function(e){
 e.preventDefault()
 const name = document.getElementById('name').value
 const price = document.getElementById('price').value
 const stock = document.getElementById('stock').value
 const image = document.getElementById('image').value

 if(editIndex >= 0){
 products[editIndex] = {name,price,stock,image}
 editIndex = -1
 }else{
 products.push({name,price,stock,image})
 }

 saveProducts()
 renderProducts(searchInput.value)
 form.reset()
})

searchInput.addEventListener('input',()=>{
renderProducts(searchInput.value)
})

renderProducts()
renderCart()
</script>

</body>
</html>

