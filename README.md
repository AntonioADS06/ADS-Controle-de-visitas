<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ADS Balanças — Controle de Visitas</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Big+Shoulders+Display:wght@600;700;800&family=IBM+Plex+Sans:wght@400;500;600&family=IBM+Plex+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#EFEDE6;
    --surface:#FFFFFF;
    --ink:#1C2026;
    --ink-muted:#5B6068;
    --border:#D8D5C8;
    --accent:#1D5C63;
    --accent-ink:#FFFFFF;
    --visited:#3F7D52;
    --visited-bg:#E7F0E8;
    --pending:#B5601E;
    --pending-bg:#F5E9DA;
    --danger:#A23B2E;
  }
  *{box-sizing:border-box;}
  body{
    margin:0;
    background:var(--bg);
    color:var(--ink);
    font-family:'IBM Plex Sans', sans-serif;
    -webkit-font-smoothing:antialiased;
  }
  .app{
    max-width:640px;
    margin:0 auto;
    min-height:100vh;
    background:var(--bg);
    padding-bottom:96px;
  }
  header.top{
    padding:28px 20px 20px;
    position:relative;
  }
  .brackets{
    position:relative;
    padding:16px 16px;
    border:1px solid var(--ink);
    background:var(--surface);
    display:flex;
    align-items:center;
    gap:14px;
  }
  .brand-logo{
    flex:none;
    height:52px;
    width:auto;
    display:block;
  }
  .brand-text{ min-width:0; }
  .brackets::before, .brackets::after{
    content:"";
    position:absolute;
    width:10px; height:10px;
    border-color:var(--accent);
  }
  .brackets::before{ top:-1px; left:-1px; border-top:2px solid var(--accent); border-left:2px solid var(--accent); }
  .brackets::after{ bottom:-1px; right:-1px; border-bottom:2px solid var(--accent); border-right:2px solid var(--accent); }
  .brand{
    font-family:'Big Shoulders Display', sans-serif;
    font-weight:800;
    font-size:34px;
    letter-spacing:0.5px;
    line-height:1;
    margin:0;
  }
  .brand-sub{
    margin:6px 0 0;
    font-size:13px;
    color:var(--ink-muted);
    font-weight:500;
  }
  .stats{
    display:flex;
    gap:1px;
    background:var(--border);
    margin:0 20px 18px;
    border:1px solid var(--border);
  }
  .stat{
    flex:1;
    background:var(--surface);
    padding:14px 12px;
    text-align:left;
  }
  .stat .num{
    font-family:'IBM Plex Mono', monospace;
    font-size:24px;
    font-weight:500;
    display:block;
  }
  .stat .label{
    font-size:11px;
    color:var(--ink-muted);
    margin-top:2px;
    display:block;
  }
  .stat.visited .num{ color:var(--visited); }
  .stat.pending .num{ color:var(--pending); }

  .toolbar{
    padding:0 20px 14px;
    display:flex;
    gap:8px;
  }
  .toolbar input[type=text]{
    flex:1;
    padding:10px 12px;
    border:1px solid var(--border);
    background:var(--surface);
    font-family:'IBM Plex Sans', sans-serif;
    font-size:14px;
    border-radius:2px;
  }
  .toolbar select{
    padding:10px 10px;
    border:1px solid var(--border);
    background:var(--surface);
    font-family:'IBM Plex Sans', sans-serif;
    font-size:14px;
    border-radius:2px;
  }
  .toolbar input:focus, .toolbar select:focus{ outline:2px solid var(--accent); outline-offset:1px; }

  .list{ padding:0 20px; display:flex; flex-direction:column; gap:8px; }
  .row{
    background:var(--surface);
    border:1px solid var(--border);
    display:flex;
    align-items:center;
    gap:12px;
    padding:12px;
    border-radius:2px;
    cursor:pointer;
  }
  .dial{
    flex:none;
    width:34px; height:34px;
    border-radius:50%;
    border:2px solid var(--pending);
    display:flex; align-items:center; justify-content:center;
    background:var(--pending-bg);
    transition:transform .25s ease, background .25s ease, border-color .25s ease;
  }
  .dial.done{
    border-color:var(--visited);
    background:var(--visited-bg);
    transform:rotate(360deg);
  }
  .dial svg{ width:16px; height:16px; }
  .row-main{ flex:1; min-width:0; }
  .row-name{ font-weight:600; font-size:15px; overflow:hidden; text-overflow:ellipsis; white-space:nowrap; }
  .row-meta{ font-size:12.5px; color:var(--ink-muted); margin-top:2px; overflow:hidden; text-overflow:ellipsis; white-space:nowrap; }
  .row-date{
    font-family:'IBM Plex Mono', monospace;
    font-size:12.5px;
    color:var(--ink-muted);
    flex:none;
    text-align:right;
  }
  .row-del{
    flex:none;
    background:none; border:none;
    color:var(--ink-muted);
    padding:6px;
    cursor:pointer;
    font-size:16px;
    line-height:1;
  }
  .row-del:hover{ color:var(--danger); }

  .empty{
    margin:40px 20px;
    text-align:center;
    color:var(--ink-muted);
    font-size:14px;
    line-height:1.6;
  }

  .fab{
    position:fixed;
    bottom:24px; left:50%;
    transform:translateX(-50%);
    max-width:600px;
    width:calc(100% - 40px);
    background:var(--accent);
    color:var(--accent-ink);
    border:none;
    padding:15px 18px;
    font-family:'IBM Plex Sans', sans-serif;
    font-weight:600;
    font-size:15px;
    border-radius:2px;
    cursor:pointer;
    box-shadow:0 2px 10px rgba(0,0,0,0.18);
  }
  .fab:active{ transform:translateX(-50%) scale(0.98); }

  .overlay{
    position:fixed; inset:0;
    background:rgba(20,22,25,0.5);
    display:flex; align-items:flex-end;
    z-index:50;
  }
  .sheet{
    background:var(--surface);
    width:100%;
    max-width:640px;
    margin:0 auto;
    border-radius:10px 10px 0 0;
    padding:20px 20px 24px;
    max-height:92vh;
    overflow-y:auto;
  }
  .sheet h2{
    font-family:'Big Shoulders Display', sans-serif;
    font-weight:700;
    font-size:22px;
    margin:0 0 16px;
  }
  .field{ margin-bottom:14px; }
  .field label{
    display:block;
    font-size:12.5px;
    color:var(--ink-muted);
    margin-bottom:5px;
    font-weight:500;
  }
  .field input, .field textarea{
    width:100%;
    padding:10px 11px;
    border:1px solid var(--border);
    border-radius:2px;
    font-family:'IBM Plex Sans', sans-serif;
    font-size:14.5px;
    background:var(--bg);
  }
  .field textarea{ resize:vertical; min-height:60px; }
  .field input:focus, .field textarea:focus{ outline:2px solid var(--accent); outline-offset:1px; }
  .sheet-actions{
    display:flex;
    gap:10px;
    margin-top:18px;
  }
  .btn{
    flex:1;
    padding:12px;
    border-radius:2px;
    font-family:'IBM Plex Sans', sans-serif;
    font-weight:600;
    font-size:14.5px;
    cursor:pointer;
    border:1px solid transparent;
  }
  .btn-primary{ background:var(--accent); color:#fff; border:none; }
  .btn-secondary{ background:none; border:1px solid var(--border); color:var(--ink); }
  .btn-danger-link{
    background:none; border:none; color:var(--danger);
    font-size:13px; text-decoration:underline; cursor:pointer;
    padding:8px 0 0; text-align:center; width:100%;
  }
  .toast{
    position:fixed; bottom:96px; left:50%; transform:translateX(-50%);
    background:var(--ink); color:#fff; font-size:13px;
    padding:9px 16px; border-radius:20px; opacity:0; pointer-events:none;
    transition:opacity .2s ease;
    z-index:60;
  }
  .toast.show{ opacity:1; }
</style>
</head>
<body>
<div class="app" id="app">
  <header class="top">
    <div class="brackets">
      <div class="brand-text">
        <p class="brand">ADS BALANÇAS</p>
        <p class="brand-sub">Controle de visitas a clientes</p>
      </div>
    </div>
  </header>

  <div class="stats">
    <div class="stat"><span class="num" id="statTotal">0</span><span class="label">clientes</span></div>
    <div class="stat visited"><span class="num" id="statVisited">0</span><span class="label">visitados</span></div>
    <div class="stat pending"><span class="num" id="statPending">0</span><span class="label">pendentes</span></div>
  </div>

  <div class="toolbar">
    <input type="text" id="search" placeholder="Buscar cliente ou responsável...">
    <select id="filter">
      <option value="todos">Todos</option>
      <option value="pendentes">Pendentes</option>
      <option value="visitados">Visitados</option>
    </select>
  </div>

  <div class="list" id="list"></div>
  <div class="empty" id="empty" style="display:none;"></div>

  <button class="fab" id="addBtn">+ Novo cliente</button>
</div>

<div class="toast" id="toast"></div>

<script>
const STORAGE_KEY = 'clientes';
let clientes = [];
let editingId = null;

const listEl = document.getElementById('list');
const emptyEl = document.getElementById('empty');
const searchEl = document.getElementById('search');
const filterEl = document.getElementById('filter');
const statTotal = document.getElementById('statTotal');
const statVisited = document.getElementById('statVisited');
const statPending = document.getElementById('statPending');
const toastEl = document.getElementById('toast');

function showToast(msg){
  toastEl.textContent = msg;
  toastEl.classList.add('show');
  setTimeout(()=>toastEl.classList.remove('show'), 2200);
}

function uid(){
  return Date.now().toString(36) + Math.random().toString(36).slice(2,7);
}

async function load(){
  try{
    const res = await window.storage.get(STORAGE_KEY, false);
    clientes = res && res.value ? JSON.parse(res.value) : [];
  }catch(e){
    clientes = [];
  }
  render();
}

async function persist(){
  try{
    const res = await window.storage.set(STORAGE_KEY, JSON.stringify(clientes), false);
    if(!res){ showToast('Não foi possível salvar. Tente novamente.'); }
  }catch(e){
    showToast('Não foi possível salvar. Tente novamente.');
  }
}

function fmtDate(iso){
  if(!iso) return '';
  const [y,m,d] = iso.split('-');
  return d+'/'+m+'/'+y;
}

function render(){
  const q = searchEl.value.trim().toLowerCase();
  const f = filterEl.value;

  let items = clientes.filter(c=>{
    const matchQ = !q || c.empresa.toLowerCase().includes(q) || (c.responsavel||'').toLowerCase().includes(q);
    const matchF = f==='todos' || (f==='visitados' && c.visitado) || (f==='pendentes' && !c.visitado);
    return matchQ && matchF;
  });

  items.sort((a,b)=>{
    if(a.visitado !== b.visitado) return a.visitado ? 1 : -1;
    return a.visitado
      ? (b.data||'').localeCompare(a.data||'')
      : (a.data||'').localeCompare(b.data||'');
  });

  statTotal.textContent = clientes.length;
  statVisited.textContent = clientes.filter(c=>c.visitado).length;
  statPending.textContent = clientes.filter(c=>!c.visitado).length;

  listEl.innerHTML = '';
  if(items.length === 0){
    emptyEl.style.display = 'block';
    emptyEl.textContent = clientes.length === 0
      ? 'Nenhum cliente cadastrado ainda. Toque em "Novo cliente" para começar a organizar suas visitas.'
      : 'Nenhum cliente encontrado com esse filtro.';
    return;
  }
  emptyEl.style.display = 'none';

  items.forEach(c=>{
    const row = document.createElement('div');
    row.className = 'row';
    row.innerHTML = `
      <div class="dial ${c.visitado ? 'done' : ''}" data-toggle="${c.id}" title="${c.visitado ? 'Marcar como pendente' : 'Marcar como visitado'}">
        ${c.visitado
          ? '<svg viewBox="0 0 24 24" fill="none" stroke="#3F7D52" stroke-width="3"><path d="M4 12l5 5L20 6"/></svg>'
          : '<svg viewBox="0 0 24 24" fill="none" stroke="#B5601E" stroke-width="3"><circle cx="12" cy="12" r="4"/></svg>'}
      </div>
      <div class="row-main" data-edit="${c.id}">
        <div class="row-name">${escapeHtml(c.empresa)}</div>
        <div class="row-meta">${escapeHtml(c.responsavel || 'Sem responsável definido')}</div>
      </div>
      <div class="row-date">${fmtDate(c.data)}</div>
      <button class="row-del" data-del="${c.id}" aria-label="Excluir">✕</button>
    `;
    listEl.appendChild(row);
  });
}

function escapeHtml(s){
  const d = document.createElement('div');
  d.textContent = s;
  return d.innerHTML;
}

listEl.addEventListener('click', (e)=>{
  const toggleId = e.target.closest('[data-toggle]')?.dataset.toggle;
  const delId = e.target.closest('[data-del]')?.dataset.del;
  const editId = e.target.closest('[data-edit]')?.dataset.edit;

  if(toggleId){
    const c = clientes.find(x=>x.id===toggleId);
    if(c){ c.visitado = !c.visitado; persist(); render(); showToast(c.visitado ? 'Marcado como visitado' : 'Marcado como pendente'); }
    return;
  }
  if(delId){
    if(confirm('Excluir este cliente da lista?')){
      clientes = clientes.filter(x=>x.id!==delId);
      persist(); render();
      showToast('Cliente removido');
    }
    return;
  }
  if(editId){
    openSheet(clientes.find(x=>x.id===editId));
  }
});

searchEl.addEventListener('input', render);
filterEl.addEventListener('change', render);

// ---- Sheet (add/edit) ----
document.getElementById('addBtn').addEventListener('click', ()=>openSheet(null));

function openSheet(client){
  editingId = client ? client.id : null;
  const overlay = document.createElement('div');
  overlay.className = 'overlay';
  overlay.innerHTML = `
    <div class="sheet">
      <h2>${client ? 'Editar cliente' : 'Novo cliente'}</h2>
      <div class="field">
        <label for="fEmpresa">Empresa</label>
        <input type="text" id="fEmpresa" placeholder="Ex: Padaria São José" value="${client ? escapeHtml(client.empresa) : ''}">
      </div>
      <div class="field">
        <label for="fResp">Responsável</label>
        <input type="text" id="fResp" placeholder="Nome de quem vai à visita" value="${client ? escapeHtml(client.responsavel||'') : ''}">
      </div>
      <div class="field">
        <label for="fData">Data da visita</label>
        <input type="date" id="fData" value="${client ? client.data||'' : new Date().toISOString().slice(0,10)}">
      </div>
      <div class="field">
        <label for="fObs">Observações (opcional)</label>
        <textarea id="fObs" placeholder="Endereço, telefone, detalhes da visita...">${client ? escapeHtml(client.observacoes||'') : ''}</textarea>
      </div>
      <div class="sheet-actions">
        <button class="btn btn-secondary" id="cancelBtn">Cancelar</button>
        <button class="btn btn-primary" id="saveBtn">Salvar</button>
      </div>
      ${client ? '<button class="btn-danger-link" id="deleteBtn">Excluir cliente</button>' : ''}
    </div>
  `;
  document.body.appendChild(overlay);

  overlay.querySelector('#cancelBtn').addEventListener('click', ()=>overlay.remove());
  overlay.addEventListener('click', (e)=>{ if(e.target === overlay) overlay.remove(); });

  overlay.querySelector('#saveBtn').addEventListener('click', ()=>{
    const empresa = overlay.querySelector('#fEmpresa').value.trim();
    const responsavel = overlay.querySelector('#fResp').value.trim();
    const data = overlay.querySelector('#fData').value;
    const observacoes = overlay.querySelector('#fObs').value.trim();

    if(!empresa){
      showToast('Informe o nome do cliente');
      return;
    }

    if(editingId){
      const c = clientes.find(x=>x.id===editingId);
      Object.assign(c, {empresa, responsavel, data, observacoes});
    }else{
      clientes.push({id: uid(), empresa, responsavel, data, observacoes, visitado:false});
    }
    persist();
    render();
    overlay.remove();
    showToast('Cliente salvo');
  });

  const delBtn = overlay.querySelector('#deleteBtn');
  if(delBtn){
    delBtn.addEventListener('click', ()=>{
      if(confirm('Excluir este cliente da lista?')){
        clientes = clientes.filter(x=>x.id!==editingId);
        persist(); render();
        overlay.remove();
        showToast('Cliente removido');
      }
    });
  }

  setTimeout(()=>overlay.querySelector('#fEmpresa').focus(), 50);
}

load();
</script>
</body>
</html>
