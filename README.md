<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Casa do Sabor - Sistema de Gestão</title>
<script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
<style>
*{box-sizing:border-box}
body{font-family:Arial, sans-serif;margin:0;background:#f5f1eb;color:#333}
header{background:#d35400;color:white;padding:15px 20px}
header h1{margin:0;font-size:22px}
nav{background:#e67e22;padding:10px;display:flex;gap:8px;flex-wrap:wrap}
nav button{background:white;border:none;padding:10px 16px;cursor:pointer;border-radius:20px;font-weight:bold}
nav button.active, nav button:hover{background:#2c3e50;color:white}
section{display:none;padding:20px;background:white;margin:15px;border-radius:12px;box-shadow:0 2px 8px rgba(0,0,0,0.1)}
section.active{display:block}
h2{color:#d35400;margin-top:0}
input, select{padding:10px;width:100%;margin:5px 0 10px 0;border:1px solid #ddd;border-radius:6px}
button.primario{background:#e67e22;color:white;border:none;padding:10px 16px;border-radius:6px;cursor:pointer;font-weight:bold}
button.primario:hover{background:#d35400}
table{width:100%;border-collapse:collapse;margin-top:15px}
th, td{border:1px solid #eee;padding:8px;text-align:left;font-size:14px}
th{background:#fdf0e6}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:10px}
@media(max-width:700px){.grid{grid-template-columns:1fr}}
.ocr-box{background:#fff8f0;border:1px dashed #e67e22;padding:12px;border-radius:8px;margin-top:10px;white-space:pre-wrap;min-height:60px}
.badge{background:#27ae60;color:white;padding:3px 8px;border-radius:10px;font-size:12px}
</style>
</head>
<body>
<header><h1>🍲 Casa do Sabor - Sistema de Gestão</h1></header>
<nav>
<button class="active" onclick="show('dashboard')">Dashboard</button>
<button onclick="show('checkin')">Check-in Funcionários</button>
<button onclick="show('funcionarios')">Funcionários</button>
<button onclick="show('contas')">Contas Bancárias</button>
<button onclick="show('cartoes')">Cartões</button>
<button onclick="show('mercadorias')">Mercadorias</button>
<button onclick="show('compras')">Compras</button>
<button onclick="show('vendas')">Vendas</button>
</nav>

<section id="dashboard" class="active">
<h2>Dashboard</h2>
<div class="grid">
<div><strong>Vendas hoje:</strong> <span id="dashVendas">R$ 0,00</span></div>
<div><strong>Funcionários presentes:</strong> <span id="dashPresentes">0</span></div>
<div><strong>Produtos em estoque baixo:</strong> <span id="dashBaixo">0</span></div>
<div><strong>Saldo em contas:</strong> <span id="dashSaldo">R$ 0,00</span></div>
</div>
</section>

<section id="checkin">
<h2>Check-in de Funcionários</h2>
<select id="checkFunc"></select>
<button class="primario" onclick="fazerCheckin()">Registrar Entrada / Saída</button>
<table id="tabelaCheckin"><tr><th>Funcionário</th><th>Horário</th><th>Tipo</th></tr></table>
</section>

<section id="funcionarios">
<h2>Cadastro de Funcionários</h2>
<div class="grid">
<input id="fNome" placeholder="Nome completo">
<input id="fCargo" placeholder="Cargo">
<input id="fSalario" type="number" placeholder="Salário">
<input id="fTelefone" placeholder="Telefone">
</div>
<button class="primario" onclick="salvarFuncionario()">Salvar Funcionário</button>
<table id="tabelaFunc"><tr><th>Nome</th><th>Cargo</th><th>Telefone</th><th>Ação</th></tr></table>
</section>

<section id="contas">
<h2>Contas Bancárias</h2>
<div class="grid">
<input id="cBanco" placeholder="Banco">
<input id="cAgencia" placeholder="Agência">
<input id="cConta" placeholder="Conta">
<input id="cSaldo" type="number" placeholder="Saldo atual">
</div>
<button class="primario" onclick="salvarConta()">Salvar Conta</button>
<table id="tabelaConta"><tr><th>Banco</th><th>Conta</th><th>Saldo</th></tr></table>
</section>

<section id="cartoes">
<h2>Cartões</h2>
<div class="grid">
<input id="cartNome" placeholder="Apelido do cartão">
<input id="cartBandeira" placeholder="Bandeira (Visa, Master...)">
<input id="cartLimite" type="number" placeholder="Limite">
<input id="cartVenc" placeholder="Vencimento">
</div>
<button class="primario" onclick="salvarCartao()">Salvar Cartão</button>
<table id="tabelaCartao"><tr><th>Cartão</th><th>Bandeira</th><th>Limite</th></tr></table>
</section>

<section id="mercadorias">
<h2>Mercadorias / Estoque</h2>
<div class="grid">
<input id="mNome" placeholder="Nome do produto">
<input id="mCategoria" placeholder="Categoria">
<input id="mQtd" type="number" placeholder="Quantidade">
<input id="mCusto" type="number" placeholder="Preço de custo">
<input id="mVenda" type="number" placeholder="Preço de venda">
<input id="mCodigo" placeholder="Código de barras">
</div>
<button class="primario" onclick="salvarMercadoria()">Salvar Produto</button>
<table id="tabelaMerc"><tr><th>Produto</th><th>Qtd</th><th>Custo</th><th>Venda</th></tr></table>
</section>

<section id="compras">
<h2>Compras - Entrada com Nota</h2>
<div class="grid">
<input id="compForn" placeholder="Fornecedor">
<input id="compProd" placeholder="Produto">
<input id="compQtd" type="number" placeholder="Quantidade">
<input id="compValor" type="number" placeholder="Valor total">
</div>
<p><strong>Escanear nota com câmera (OCR):</strong></p>
<input type="file" accept="image/*" capture="environment" id="fotoCompra">
<button class="primario" onclick="lerOCR('fotoCompra','resCompra')">📷 Ler Nota com Câmera</button>
<div id="resCompra" class="ocr-box">O texto da nota vai aparecer aqui...</div>
<button class="primario" onclick="salvarCompra()">Registrar Compra</button>
<table id="tabelaCompra"><tr><th>Fornecedor</th><th>Produto</th><th>Valor</th></tr></table>
</section>

<section id="vendas">
<h2>Vendas - PDV</h2>
<div class="grid">
<select id="vProd"></select>
<input id="vQtd" type="number" placeholder="Qtd" value="1">
</div>
<button class="primario" onclick="addVenda()">Adicionar ao Carrinho</button>
<p><strong>Ou escanear etiqueta / comprovante:</strong></p>
<input type="file" accept="image/*" capture="environment" id="fotoVenda">
<button class="primario" onclick="lerOCR('fotoVenda','resVenda')">📷 Escanear com Câmera</button>
<div id="resVenda" class="ocr-box">Texto escaneado...</div>
<table id="tabelaVenda"><tr><th>Produto</th><th>Qtd</th><th>Total</th></tr></table>
<h3>Total: <span id="totalVenda">R$ 0,00</span></h3>
<button class="primario" onclick="finalizarVenda()">Finalizar Venda</button>
</section>

<script>
let funcionarios = JSON.parse(localStorage.getItem('cs_func')||'[]');
let contas = JSON.parse(localStorage.getItem('cs_contas')||'[]');
let cartoes = JSON.parse(localStorage.getItem('cs_cartoes')||'[]');
let mercadorias = JSON.parse(localStorage.getItem('cs_merc')||'[]');
let checkins = JSON.parse(localStorage.getItem('cs_check')||'[]');
let compras = JSON.parse(localStorage.getItem('cs_compras')||'[]');
let carrinho = [];
let vendasTotal = JSON.parse(localStorage.getItem('cs_vendas')||'[]');

function show(id){
document.querySelectorAll('section').forEach(s=>s.classList.remove('active'));
document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
document.getElementById(id).classList.add('active');
event.target.classList.add('active');
atualizarTudo();
}

function salvarFuncionario(){
let f={nome:fNome.value, cargo:fCargo.value, salario:fSalario.value, tel:fTelefone.value};
if(!f.nome) return alert('Digite o nome');
funcionarios.push(f); localStorage.setItem('cs_func',JSON.stringify(funcionarios));
fNome.value=fCargo.value=fSalario.value=fTelefone.value=''; atualizarTudo();
}
function salvarConta(){
let c={banco:cBanco.value, agencia:cAgencia.value, conta:cConta.value, saldo:parseFloat(cSaldo.value)||0};
contas.push(c); localStorage.setItem('cs_contas',JSON.stringify(contas)); atualizarTudo();
}
function salvarCartao(){
let c={nome:cartNome.value, band:cartBandeira.value, limite:cartLimite.value, venc:cartVenc.value};
cartoes.push(c); localStorage.setItem('cs_cartoes',JSON.stringify(cartoes)); atualizarTudo();
}
function salvarMercadoria(){
let m={nome:mNome.value, cat:mCategoria.value, qtd:parseFloat(mQtd.value)||0, custo:parseFloat(mCusto.value)||0, venda:parseFloat(mVenda.value)||0, cod:mCodigo.value};
mercadorias.push(m); localStorage.setItem('cs_merc',JSON.stringify(mercadorias)); atualizarTudo();
}
function fazerCheckin(){
let nome=checkFunc.value; if(!nome) return;
let tipo = checkins.length>0 && checkins[checkins.length-1].nome==nome && checkins[checkins.length-1].tipo=='Entrada'? 'Saída' : 'Entrada';
checkins.push({nome, hora:new Date().toLocaleString(), tipo}); localStorage.setItem('cs_check',JSON.stringify(checkins)); atualizarTudo();
}
function salvarCompra(){
let c={forn:compForn.value, prod:compProd.value, qtd:compQtd.value, valor:compValor.value};
compras.push(c); localStorage.setItem('cs_compras',JSON.stringify(compras)); atualizarTudo();
}
function addVenda(){
let prod = mercadorias.find(x=>x.nome==vProd.value); if(!prod) return;
let qtd = parseFloat(vQtd.value)||1;
carrinho.push({nome:prod.nome, qtd, total:qtd*prod.venda}); renderVenda();
}
function finalizarVenda(){
if(carrinho.length==0) return alert('Carrinho vazio');
let total = carrinho.reduce((s,i)=>s+i.total,0);
vendasTotal.push({data:new Date().toLocaleString(), total}); localStorage.setItem('cs_vendas',JSON.stringify(vendasTotal));
carrinho=[]; renderVenda(); atualizarTudo(); alert('Venda finalizada!');
}
function renderVenda(){
let tb=document.getElementById('tabelaVenda'); tb.innerHTML='<tr><th>Produto</th><th>Qtd</th><th>Total</th></tr>';
let tot=0; carrinho.forEach(i=>{tot+=i.total; tb.innerHTML+=`<tr><td>${i.nome}</td><td>${i.qtd}</td><td>R$ ${i.total.toFixed(2)}</td></tr>`});
document.getElementById('totalVenda').innerText='R$ '+tot.toFixed(2);
}
async function lerOCR(inputId, outId){
let file=document.getElementById(inputId).files[0]; if(!file) return alert('Tire uma foto primeiro');
document.getElementById(outId).innerText='Lendo imagem... aguarde';
let {data:{text}} = await Tesseract.recognize(file,'por');
document.getElementById(outId).innerText=text;
}

function atualizarTudo(){
document.getElementById('tabelaFunc').innerHTML='<tr><th>Nome</th><th>Cargo</th><th>Telefone</th><th>Ação</th></tr>'+funcionarios.map((f,i)=>`<tr><td>${f.nome}</td><td>${f.cargo}</td><td>${f.tel}</td><td><button onclick="funcionarios.splice(${i},1);localStorage.setItem('cs_func',JSON.stringify(funcionarios));atualizarTudo()">X</button></td></tr>`).join('');
document.getElementById('checkFunc').innerHTML=funcionarios.map(f=>`<option>${f.nome}</option>`).join('');
document.getElementById('vProd').innerHTML=mercadorias.map(m=>`<option>${m.nome}</option>`).join('');
document.getElementById('tabelaConta').innerHTML='<tr><th>Banco</th><th>Conta</th><th>Saldo</th></tr>'+contas.map(c=>`<tr><td>${c.banco}</td><td>${c.conta}</td><td>R$ ${c.saldo}</td></tr>`).join('');
document.getElementById('tabelaCartao').innerHTML='<tr><th>Cartão</th><th>Bandeira</th><th>Limite</th></tr>'+cartoes.map(c=>`<tr><td>${c.nome}</td><td>${c.band}</td><td>R$ ${c.limite}</td></tr>`).join('');
document.getElementById('tabelaMerc').innerHTML='<tr><th>Produto</th><th>Qtd</th><th>Custo</th><th>Venda</th></tr>'+mercadorias.map(m=>`<tr><td>${m.nome}</td><td>${m.qtd}</td><td>R$ ${m.custo}</td><td>R$ ${m.venda}</td></tr>`).join('');
document.getElementById('tabelaCompra').innerHTML='<tr><th>Fornecedor</th><th>Produto</th><th>Valor</th></tr>'+compras.map(c=>`<tr><td>${c.forn}</td><td>${c.prod}</td><td>R$ ${c.valor}</td></tr>`).join('');
document.getElementById('tabelaCheckin').innerHTML='<tr><th>Funcionário</th><th>Horário</th><th>Tipo</th></tr>'+checkins.slice(-20).reverse().map(c=>`<tr><td>${c.nome}</td><td>${c.hora}</td><td><span class="badge">${c.tipo}</span></td></tr>`).join('');
let saldoTotal=contas.reduce((s,c)=>s+(parseFloat(c.saldo)||0),0);
let vendasHoje=vendasTotal.reduce((s,v)=>s+v.total,0);
document.getElementById('dashSaldo').innerText='R$ '+saldoTotal.toFixed(2);
document.getElementById('dashVendas').innerText='R$ '+vendasHoje.toFixed(2);
document.getElementById('dashBaixo').innerText=mercadorias.filter(m=>m.qtd<5).length;
document.getElementById('dashPresentes').innerText=checkins.filter(c=>c.tipo=='Entrada').length - checkins.filter(c=>c.tipo=='Saída').length;
}
atualizarTudo();
</script>
</body>
</html>