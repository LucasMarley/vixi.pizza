<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Vixi Pizzaria</title>
<style>
body{font-family:Arial,sans-serif;background:#f4f4f4;margin:0;padding-bottom:50px;color:#333}
header{background:#d32f2f;color:#fff;text-align:center;padding:20px}
.barra-massas{background:#222;color:#fbc02d;text-align:center;padding:12px;font-weight:bold;font-size:18px}
.barra-promo{background:#fbc02d;color:#000;text-align:center;padding:10px;font-weight:bold;display:none}
.container{max-width:750px;margin:20px auto;padding:0 15px}
.card{background:#fff;padding:15px;margin-bottom:12px;border-radius:8px;display:flex;justify-content:space-between;align-items:center;box-shadow:0 2px 4px rgba(0,0,0,.1);gap:10px}
.card h3{margin:0 0 5px 0;color:#d32f2f}
.card p{margin:0;font-size:14px;color:#666}
.direita{text-align:right;min-width:155px}
.preco{font-size:15px;font-weight:bold;color:#2e7d32;margin-bottom:8px;line-height:1.4}
.preco-velho{text-decoration:line-through;color:#999;font-size:12px;font-weight:normal}
button{background:#d32f2f;color:#fff;border:none;padding:10px 14px;border-radius:5px;font-weight:bold;cursor:pointer}
button:disabled{background:#ccc;cursor:not-allowed}
.modal{position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,.85);display:none;justify-content:center;align-items:center;z-index:999;padding:10px;box-sizing:border-box}
.caixa{background:#fff;padding:20px;border-radius:10px;width:100%;max-width:400px;box-sizing:border-box;max-height:92vh;overflow-y:auto}
.caixa h2{margin:0 0 12px 0;color:#d32f2f;font-size:20px}
.campo{margin-bottom:12px}
.campo label{display:block;font-weight:bold;margin-bottom:4px;font-size:14px}
.campo input[type=number],.campo input[type=text],.campo select{width:100%;padding:9px;font-size:15px;border:1px solid #ccc;border-radius:5px;box-sizing:border-box}
.grupo-opcoes{display:flex;gap:8px;margin-bottom:12px}
.grupo-opcoes label{flex:1;padding:9px;border:1px solid #ddd;border-radius:5px;text-align:center;cursor:pointer;font-size:14px;background:#fafafa}
.opcao-pgto{display:block;padding:9px;border:1px solid #ddd;border-radius:5px;margin-bottom:6px;cursor:pointer;font-size:14px}
.resumo{background:#f4f4f4;padding:10px;border-radius:5px;font-size:14px;margin-bottom:12px;border-left:4px solid #2e7d32;line-height:1.5}
.botoes-pgto{display:flex;gap:10px;margin-top:15px}
.btn-reset{background:#555;font-size:12px;padding:6px 10px;margin-top:8px}
</style>
</head>
<body>
<header>
  <h1>🍕 Vixi Pizzaria</h1>
  <p>Cardápio Digital</p>
  <button type="button" id="btn-reconfigurar" class="btn-reset">⚙️ Configurar Massas/Promo</button>
</header>

<div id="aviso-massas" class="barra-massas">Aguardando configuração...</div>
<div id="aviso-promo" class="barra-promo"></div>
<div class="container"><h2>Sabores Disponíveis</h2><div id="cardapio"></div></div>

<div id="tela-inicial" class="modal" style="display:flex">
<div class="caixa">
<h2>Controle de Massas</h2>
<div class="campo"><label>Quantas massas disponíveis hoje?</label><input type="number" id="qtd-massas" value="20" min="0"></div>
<div class="campo"><label><input type="checkbox" id="ativa-promo"> Ativar Promoção?</label></div>
<div id="box-promo" style="display:none;background:#fff8e1;padding:10px;border-radius:5px;margin-bottom:12px">
<div class="campo"><label>Nome da Promoção:</label><input type="text" id="nome-promo" placeholder="Ex: Promoção de Hoje"></div>
<div class="campo" style="margin-bottom:0"><label>Desconto (%):</label><input type="number" id="desc-promo" placeholder="Ex: 10" min="1" max="90"></div>
</div>
<button type="button" id="btn-confirmar-config" style="width:100%;padding:12px;font-size:16px">Confirmar</button>
</div>
</div>

<div id="tela-pagamento" class="modal">
<div class="caixa">
<h2>Monte sua Pizza</h2>
<div class="campo"><label>1. Escolha o Tamanho:</label>
<div class="grupo-opcoes">
<label><input type="radio" name="tamanho" value="M" checked> Tamanho M</label>
<label><input type="radio" name="tamanho" value="G"> Tamanho G</label>
</div></div>
<div class="campo"><label>2. Inteira ou Meio a Meio?</label>
<div class="grupo-opcoes">
<label><input type="radio" name="tipo_pizza" value="inteira" checked> Inteira</label>
<label><input type="radio" name="tipo_pizza" value="meia"> Meio a Meio</label>
</div></div>
<div id="box-segundo-sabor" class="campo" style="display:none"><label>Escolha o 2º Sabor (Metade):</label><select id="select-segundo-sabor"></select></div>
<div id="resumo-pedido" class="resumo"></div>

<div class="campo">
  <label>3. Seu Nome:</label>
  <input type="text" id="cliente-nome" placeholder="Digite seu nome">
</div>
<div class="campo">
  <label>4. Endereço de Entrega (ou Retirada):</label>
  <input type="text" id="cliente-endereco" placeholder="Rua, número, bairro ou 'Vou retirar'">
</div>

<div class="campo" style="margin-bottom:6px"><label>5. Forma de Pagamento:</label></div>
<label class="opcao-pgto"><input type="radio" name="pgto" value="Pix" checked> 💠 Pix</label>
<label class="opcao-pgto"><input type="radio" name="pgto" value="Cartão de Débito"> 💳 Cartão de Débito</label>
<label class="opcao-pgto"><input type="radio" name="pgto" value="Cartão de Crédito"> 💳 Cartão de Crédito</label>
<label class="opcao-pgto"><input type="radio" name="pgto" value="Dinheiro"> 💵 Dinheiro</label>
<div id="box-troco" class="campo" style="display:none;margin-top:8px"><label>Troco para quanto? (Opcional)</label><input type="text" id="valor-troco" placeholder="Ex: Troco para R$ 100"></div>
<div class="botoes-pgto">
<button type="button" id="btn-voltar-pgto" style="background:#757575;flex:1">Voltar</button>
<button type="button" id="btn-finalizar-pedido" style="background:#25D366;flex:2">📲 Enviar no WhatsApp</button>
</div></div></div>
<script>
// TROQUE O NÚMERO ABAIXO PELO WHATSAPP DA PIZZARIA (55 + DDD + NÚMERO)
var TELEFONE_WHATSAPP = "5511981903472";

var massas = 0, temPromo = false, tituloPromo = "", porcentagem = 0, pizzaSelecionada = -1;

var pizzas = [
  {nome:"Calabresa", desc:"Mussarela, calabresa fatiada, cebola e orégano.", precoM:35, precoG:45},
  {nome:"Marguerita", desc:"Mussarela, tomate, manjericão e parmesão.", precoM:32, precoG:42},
  {nome:"Frango com Catupiry", desc:"Frango desfiado temperado com Catupiry.", precoM:38, precoG:48},
  {nome:"Portuguesa", desc:"Presunto, mussarela, ovo, cebola, ervilha e azeitona.", precoM:40, precoG:50},
  {nome:"Quatro Queijos", desc:"Mussarela, provolone, parmesão e gorgonzola.", precoM:42, precoG:52}
];

function salvar(k,v){try{localStorage.setItem(k,v)}catch(e){}}
function ler(k){try{return localStorage.getItem(k)}catch(e){return null}}
function fmt(v){return "R$ " + v.toFixed(2).replace(".",",")}
function calcPreco(val){return temPromo && porcentagem > 0 ? val - (val * (porcentagem / 100)) : val}

window.addEventListener("DOMContentLoaded", function(){
  if(ler("pz3_ok") === "1"){
    massas = parseInt(ler("pz3_m"), 10) || 0;
    temPromo = ler("pz3_p") === "1";
    tituloPromo = ler("pz3_t") || "Promoção Especial";
    porcentagem = parseFloat(ler("pz3_d")) || 0;
    document.getElementById("tela-inicial").style.display = "none";
    atualizarTela();
  }
});

document.getElementById("btn-reconfigurar").addEventListener("click", function(){
  document.getElementById("qtd-massas").value = massas;
  document.getElementById("ativa-promo").checked = temPromo;
  document.getElementById("box-promo").style.display = temPromo ? "block" : "none";
  document.getElementById("nome-promo").value = tituloPromo;
  document.getElementById("desc-promo").value = porcentagem || "";
  document.getElementById("tela-inicial").style.display = "flex";
});

document.getElementById("ativa-promo").addEventListener("change", function(){
  document.getElementById("box-promo").style.display = this.checked ? "block" : "none";
});

document.getElementById("btn-confirmar-config").addEventListener("click", function(){
  var v = parseInt(document.getElementById("qtd-massas").value, 10);
  massas = isNaN(v) || v < 0 ? 0 : v;
  temPromo = document.getElementById("ativa-promo").checked;
  tituloPromo = document.getElementById("nome-promo").value || "Promoção Especial";
  porcentagem = parseFloat(document.getElementById("desc-promo").value) || 0;
  
  salvar("pz3_ok","1");
  salvar("pz3_m", massas);
  salvar("pz3_p", temPromo ? "1" : "0");
  salvar("pz3_t", tituloPromo);
  salvar("pz3_d", porcentagem);
  
  document.getElementById("tela-inicial").style.display = "none";
  atualizarTela();
});

function atualizarTela(){
  var barraMassas = document.getElementById("aviso-massas");
  if(massas > 0){
    barraMassas.textContent = "🔥 Massas disponíveis hoje: " + massas;
    barraMassas.style.color = "#fbc02d";
  } else {
    barraMassas.textContent = "❌ Massas esgotadas por hoje!";
    barraMassas.style.color = "#ff5252";
  }

  var barraPromo = document.getElementById("aviso-promo");
  if(temPromo && porcentagem > 0){
    barraPromo.style.display = "block";
    barraPromo.textContent = "🎉 " + tituloPromo + " - " + porcentagem + "% DE DESCONTO!";
  } else {
    barraPromo.style.display = "none";
  }

  var html = "";
  for(var i = 0; i < pizzas.length; i++){
    var p = pizzas[i];
    var pm = calcPreco(p.precoM);
    var pg = calcPreco(p.precoG);
    var precoHtml = "";
    if(temPromo && porcentagem > 0){
      precoHtml = "M: <span class='preco-velho'>" + fmt(p.precoM) + "</span> " + fmt(pm) + "<br>" +
                  "G: <span class='preco-velho'>" + fmt(p.precoG) + "</span> " + fmt(pg);
    } else {
      precoHtml = "M: " + fmt(p.precoM) + "<br>G: " + fmt(p.precoG);
    }
    html += '<div class="card">' +
      '<div><h3>' + p.nome + '</h3><p>' + p.desc + '</p></div>' +
      '<div class="direita"><div class="preco">' + precoHtml + '</div>' +
      '<button type="button" onclick="abrirPedido(' + i + ')" ' + (massas <= 0 ? 'disabled' : '') + '>' +
      (massas <= 0 ? 'Esgotado' : 'Pedir') + '</button></div></div>';
  }
  document.getElementById("cardapio").innerHTML = html;
}

function abrirPedido(idx){
  if(massas <= 0) return;
  pizzaSelecionada = idx;
  var sel = document.getElementById("select-segundo-sabor");
  sel.innerHTML = "";
  for(var i = 0; i < pizzas.length; i++){
    if(i !== idx){
      var opt = document.createElement("option");
      opt.value = i;
      opt.textContent = pizzas[i].nome;
      sel.appendChild(opt);
    }
  }
  document.querySelector('input[name="tamanho"][value="M"]').checked = true;
  document.querySelector('input[name="tipo_pizza"][value="inteira"]').checked = true;
  document.getElementById("box-segundo-sabor").style.display = "none";
  atualizarResumo();
  document.getElementById("tela-pagamento").style.display = "flex";
}

function calcularPedidoAtual(){
  var p1 = pizzas[pizzaSelecionada];
  var tam = document.querySelector('input[name="tamanho"]:checked').value;
  var tipo = document.querySelector('input[name="tipo_pizza"]:checked').value;
  var precoBase = tam === "M" ? p1.precoM : p1.precoG;
  var descricaoPizza = "Pizza " + tam + " - " + p1.nome;

  if(tipo === "meia"){
    var idx2 = parseInt(document.getElementById("select-segundo-sabor").value, 10);
    var p2 = pizzas[idx2];
    var preco2 = tam === "M" ? p2.precoM : p2.precoG;
    if(preco2 > precoBase) precoBase = preco2;
    descricaoPizza = "Pizza " + tam + " (Meio a Meio) - 1/2 " + p1.nome + " + 1/2 " + p2.nome;
  }

  var total = calcPreco(precoBase);
  return { descricao: descricaoPizza, total: total, tam: tam };
}

function atualizarResumo(){
  var dados = calcularPedidoAtual();
  document.getElementById("resumo-pedido").innerHTML =
    "<strong>Resumo do Pedido:</strong><br>" +
    dados.descricao + "<br>" +
    "<strong>Total a pagar: " + fmt(dados.total) + "</strong>";
}

document.querySelectorAll('input[name="tamanho"], input[name="tipo_pizza"]').forEach(function(el){
  el.addEventListener("change", function(){
    var tipo = document.querySelector('input[name="tipo_pizza"]:checked').value;
    document.getElementById("box-segundo-sabor").style.display = tipo === "meia" ? "block" : "none";
    atualizarResumo();
  });
});

document.getElementById("select-segundo-sabor").addEventListener("change", atualizarResumo);

document.querySelectorAll('input[name="pgto"]').forEach(function(el){
  el.addEventListener("change", function(){
    document.getElementById("box-troco").style.display = this.value === "Dinheiro" ? "block" : "none";
  });
});

document.getElementById("btn-voltar-pgto").addEventListener("click", function(){
  document.getElementById("tela-pagamento").style.display = "none";
});

document.getElementById("btn-finalizar-pedido").addEventListener("click", function(){
  var nome = document.getElementById("cliente-nome").value.trim();
  var endereco = document.getElementById("cliente-endereco").value.trim();
  
  if(!nome){
    alert("Por favor, digite seu nome.");
    document.getElementById("cliente-nome").focus();
    return;
  }
  if(!endereco){
    alert("Por favor, informe o endereço de entrega ou digite 'Vou retirar'.");
    document.getElementById("cliente-endereco").focus();
    return;
  }

  var dados = calcularPedidoAtual();
  var pgto = document.querySelector('input[name="pgto"]:checked').value;
  var troco = document.getElementById("valor-troco").value.trim();

  var msg = "🍕 *NOVO PEDIDO - VIXI PIZZARIA* 🍕\n\n";
  msg += "*Cliente:* " + nome + "\n";
  msg += "*Entrega:* " + endereco + "\n\n";
  msg += "*Item:* " + dados.descricao + "\n";
  msg += "*Total:* " + fmt(dados.total) + "\n";
  msg += "*Pagamento:* " + pgto;
  if(pgto === "Dinheiro" && troco){
    msg += " (" + troco + ")";
  }

  massas--;
  salvar("pz3_m", massas);
  atualizarTela();
  document.getElementById("tela-pagamento").style.display = "none";

  var url = "https://wa.me" + TELEFONE_WHATSAPP + "?text=" + encodeURIComponent(msg);
  window.open(url, "_blank");
});
</script>
</body>
</html>
