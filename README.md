```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Relatório de Manutenção</title>

<style>
body{
    font-family: Arial, sans-serif;
    background:#f1f5f9;
    margin:0;
    padding:20px;
}

.container{
    max-width:700px;
    margin:auto;
    background:#fff;
    padding:20px;
    border-radius:15px;
    box-shadow:0 2px 10px rgba(0,0,0,.15);
}

h2{
    text-align:center;
    color:#1f2937;
}

h3{
    margin-top:20px;
}

label{
    display:block;
    margin-top:12px;
    font-weight:bold;
}

input,
select,
textarea{
    width:100%;
    box-sizing:border-box;
    padding:10px;
    border:1px solid #ccc;
    border-radius:8px;
    margin-top:5px;
}

textarea{
    min-height:80px;
}

button{
    width:100%;
    padding:12px;
    border:none;
    border-radius:10px;
    color:#fff;
    cursor:pointer;
    margin-top:12px;
    font-size:15px;
}

.primary{
    background:#2563eb;
}

.secondary{
    background:#0ea5e9;
}

.danger{
    background:#dc2626;
}

.equipamento{
    background:#f8fafc;
    border:1px solid #e5e7eb;
    border-radius:12px;
    padding:15px;
    margin-top:15px;
}

.hidden{
    display:none;
}

#resultado{
    white-space:pre-wrap;
    background:#f9fafb;
    border-radius:10px;
    padding:15px;
    margin-top:20px;
    border:1px solid #e5e7eb;
}
</style>
</head>

<body>

<div class="container">

<h2>📋 RELATÓRIO DE MANUTENÇÃO</h2>

<label>📅 Data</label>
<input type="date" id="data">

<label>🕓 Turno</label>
<select id="turno">
    <option>1º Turno</option>
    <option>2º Turno</option>
    <option>3º Turno</option>
</select>

<label>👷 Técnico Responsável</label>
<input type="text" id="tecnico">

<h3>⚙️ Eventos de Manutenção</h3>

<div id="eventos"></div>

<button class="primary" onclick="adicionarEvento()">
+ Adicionar Equipamento
</button>

<label><b>🛠️ Necessita Plano Preventivo?</b></label>
<select id="preventivo" onchange="mostrarCamposPlano()">
    <option value="Não">Não</option>
    <option value="Sim">Sim</option>
</select>

<div id="planoExtras" class="hidden">

    <label>📌 Equipamento do Plano</label>
    <select id="planoEquipSelect"></select>

    <label>📝 Descrição do Plano</label>
    <textarea id="planoDesc"></textarea>

</div>

<button class="primary" onclick="gerarRelatorio()">
Gerar Relatório
</button>

<button class="secondary" onclick="enviarWhatsApp()">
Enviar via WhatsApp
</button>

<button class="secondary" onclick="limparCampos()">
Limpar
</button>

<div id="resultado"></div>

</div>

<script>

(function(){

    const hoje = new Date();

    const yyyy = hoje.getFullYear();
    const mm = String(hoje.getMonth()+1).padStart(2,"0");
    const dd = String(hoje.getDate()).padStart(2,"0");

    document.getElementById("data").value =
        `${yyyy}-${mm}-${dd}`;

})();

let contador = 0;

function atualizarListaEquipPlano(){

    const select =
        document.getElementById("planoEquipSelect");

    select.innerHTML = "";

    document
        .querySelectorAll(".equip")
        .forEach(eq => {

            const nome = eq.value.trim();

            if(nome){

                const option =
                    document.createElement("option");

                option.value = nome;
                option.textContent = nome;

                select.appendChild(option);
            }

        });
}

function adicionarEvento(){

    contador++;

    const div =
        document.createElement("div");

    div.className = "equipamento";
    div.id = `evento_${contador}`;

    div.innerHTML = `
        <label>⚙️ Equipamento</label>
        <input
            type="text"
            class="equip"
            oninput="atualizarListaEquipPlano()">

        <label>❗ Problema</label>
        <textarea class="prob"></textarea>

        <label>🔧 Ação Executada</label>
        <textarea class="acao"></textarea>

        <button
            class="danger"
            onclick="removerEvento(${contador})">
            Remover Equipamento
        </button>
    `;

    document
        .getElementById("eventos")
        .appendChild(div);

    atualizarListaEquipPlano();
}

function removerEvento(id){

    const box =
        document.getElementById(`evento_${id}`);

    if(box){
        box.remove();
    }

    atualizarListaEquipPlano();
}

function mostrarCamposPlano(){

    const preventivo =
        document.getElementById("preventivo").value;

    const extras =
        document.getElementById("planoExtras");

    if(preventivo === "Sim"){

        extras.classList.remove("hidden");
        atualizarListaEquipPlano();

    }else{

        extras.classList.add("hidden");
        document.getElementById("planoDesc").value = "";
    }
}

function formatDateBR(data){

    const p = data.split("-");

    return `${p[2]}/${p[1]}/${p[0]}`;
}

function gerarRelatorio(){

    const data =
        document.getElementById("data").value;

    const turno =
        document.getElementById("turno").value;

    const tecnico =
        document.getElementById("tecnico")
        .value
        .trim();

    const preventivo =
        document.getElementById("preventivo").value;

    const planoEquip =
        document.getElementById("planoEquipSelect").value;

    const planoDesc =
        document.getElementById("planoDesc")
        .value
        .trim();

    const eventos =
        document.querySelectorAll(".equipamento");

    if(!data || !tecnico){

        alert("Preencha Data e Técnico.");
        return;
    }

    if(eventos.length === 0){

        alert("Adicione pelo menos um equipamento.");
        return;
    }

    if(preventivo === "Sim"){

        if(!planoEquip || !planoDesc){

            alert(
                "Selecione o equipamento e preencha a descrição do plano."
            );

            return;
        }
    }

    let msg =
`📋 RELATÓRIO DE MANUTENÇÃO

📅 Data: ${formatDateBR(data)}
🕓 Turno: ${turno}
👷 Técnico Responsável: ${tecnico}

`;

    eventos.forEach(ev => {

        const eq =
            ev.querySelector(".equip")
            .value
            .trim();

        const pr =
            ev.querySelector(".prob")
            .value
            .trim();

        const ac =
            ev.querySelector(".acao")
            .value
            .trim();

        msg += `⚙️ Equipamento: ${eq}\n\n`;

        if(pr){
            msg += `❗ Problema:\n${pr}\n\n`;
        }

        if(ac){
            msg += `🔧 Ação Executada:\n${ac}\n\n`;
        }

    });

    msg += `🛠️ Necessita Plano Preventivo?\n${preventivo}`;

    if(preventivo === "Sim"){

        msg += `

📌 Equipamento do Plano:
${planoEquip}

📝 Descrição do Plano:
${planoDesc}`;
    }

    document.getElementById("resultado")
        .textContent = msg;
}

function enviarWhatsApp(){

    const texto =
        document.getElementById("resultado")
        .textContent;

    if(!texto){

        alert("Gere o relatório antes de enviar.");
        return;
    }

    window.open(
        "https://wa.me/?text=" +
        encodeURIComponent(texto),
        "_blank"
    );
}

function limparCampos(){

    document.getElementById("tecnico").value = "";
    document.getElementById("eventos").innerHTML = "";
    document.getElementById("resultado").textContent = "";

    document
        .getElementById("planoExtras")
        .classList.add("hidden");

    document.getElementById("planoDesc").value = "";

    atualizarListaEquipPlano();
}

</script>

</body>
</html>
```
