<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Controle de Limpezas — Clínica OSE</title>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: system-ui, sans-serif; background: #f4f4f0; color: #1a1a18; min-height: 100vh; }
  header { background: #0F6E56; color: #fff; padding: 1rem 1.5rem; display: flex; align-items: center; justify-content: space-between; flex-wrap: wrap; gap: 8px; }
  header h1 { font-size: 18px; font-weight: 500; }
  header p { font-size: 13px; opacity: 0.8; margin-top: 2px; }
  .btn-relatorio { background: rgba(255,255,255,0.15); border: 1px solid rgba(255,255,255,0.4); color: #fff; padding: 6px 14px; border-radius: 8px; font-size: 13px; cursor: pointer; }
  .btn-relatorio:hover { background: rgba(255,255,255,0.25); }
  .container { max-width: 1050px; margin: 0 auto; padding: 1.25rem 1rem; }
  .top-bar { display: flex; gap: 8px; margin-bottom: 1rem; flex-wrap: wrap; }
  .top-bar input { flex: 1; min-width: 140px; font-size: 14px; padding: 7px 11px; border: 1px solid #ccc; border-radius: 8px; background: #fff; color: #1a1a18; }
  .btn { padding: 7px 16px; font-size: 13px; font-weight: 500; border: 1px solid #ccc; border-radius: 8px; background: transparent; color: #1a1a18; cursor: pointer; white-space: nowrap; }
  .btn:hover { background: #f0f0ec; }
  .btn-add { border-color: #0F6E56; color: #0F6E56; }
  .btn-add:hover { background: #E1F5EE; }
  .metrics { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; margin-bottom: 1rem; }
  .metric { background: #fff; border-radius: 8px; padding: 10px 14px; border: 1px solid #e5e5e0; }
  .metric .label { font-size: 11px; color: #888; margin-bottom: 4px; }
  .metric .val { font-size: 24px; font-weight: 500; }
  .val.verde { color: #0F6E56; }
  .val.amarelo { color: #854F0B; }
  .val.vermelho { color: #A32D2D; }
  .val.cinza { color: #5F5E5A; }
  .filters { display: flex; gap: 6px; margin-bottom: 12px; flex-wrap: wrap; }
  .pill { padding: 4px 13px; font-size: 12px; border-radius: 999px; border: 1px solid #ccc; cursor: pointer; background: #fff; color: #666; }
  .pill.active { background: #1D9E75; color: #fff; border-color: #1D9E75; }
  .pill.f-aguardando.active { background: #BA7517; border-color: #BA7517; }
  .pill.f-atrasado.active { background: #A32D2D; border-color: #A32D2D; }
  .card { background: #fff; border-radius: 12px; border: 1px solid #e5e5e0; overflow: auto; }
  table { width: 100%; border-collapse: collapse; font-size: 13px; min-width: 700px; }
  th { text-align: left; padding: 8px 12px; font-size: 11px; font-weight: 500; color: #888; border-bottom: 1px solid #eee; text-transform: uppercase; letter-spacing: 0.04em; background: #fafaf8; }
  td { padding: 10px 12px; border-bottom: 1px solid #f0f0ec; vertical-align: middle; }
  tr:last-child td { border-bottom: none; }
  tr:hover td { background: #fafaf8; }
  .badge { display: inline-block; padding: 3px 10px; border-radius: 999px; font-size: 11px; font-weight: 500; }
  .badge.liberada { background: #E1F5EE; color: #085041; }
  .badge.aguardando { background: #FAEEDA; color: #633806; }
  .badge.atrasado { background: #FCEBEB; color: #791F1F; }
  .action-btn { font-size: 11px; padding: 4px 10px; border-radius: 6px; border: 1px solid; cursor: pointer; background: none; font-weight: 500; }
  .action-btn.ok { border-color: #0F6E56; color: #0F6E56; }
  .action-btn.ok:hover { background: #E1F5EE; }
  .del-btn { background: none; border: none; cursor: pointer; color: #aaa; font-size: 14px; padding: 3px 7px; border-radius: 4px; }
  .del-btn:hover { background: #fef0f0; color: #A32D2D; }
  .empty { text-align: center; padding: 2.5rem; color: #aaa; font-size: 13px; }
  .modal-bg { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.35); align-items: center; justify-content: center; z-index: 10; }
  .modal-bg.open { display: flex; }
  .modal { background: #fff; border-radius: 14px; border: 1px solid #ddd; padding: 1.5rem; width: 360px; max-width: 95%; }
  .modal h3 { font-size: 15px; font-weight: 500; margin-bottom: 14px; }
  .modal label { font-size: 12px; color: #888; display: block; margin-bottom: 3px; margin-top: 10px; }
  .modal input { width: 100%; font-size: 13px; padding: 7px 11px; border: 1px solid #ccc; border-radius: 8px; background: #fff; color: #1a1a18; }
  .modal-btns { display: flex; gap: 8px; margin-top: 16px; justify-content: flex-end; }
  #print-area { display: none; }
  @media print {
    body * { visibility: hidden; }
    #print-area, #print-area * { visibility: visible; }
    #print-area { display: block !important; position: absolute; top: 0; left: 0; width: 100%; padding: 1.5rem; font-family: system-ui, sans-serif; font-size: 13px; color: #000; }
    .print-header { margin-bottom: 1.5rem; border-bottom: 2px solid #0F6E56; padding-bottom: 0.75rem; }
    .print-header h1 { font-size: 18px; font-weight: 600; color: #0F6E56; }
    .print-header p { font-size: 12px; color: #555; margin-top: 3px; }
    .print-section { margin-bottom: 1.25rem; page-break-inside: avoid; }
    .print-section h2 { font-size: 13px; font-weight: 600; text-transform: uppercase; letter-spacing: 0.05em; color: #333; border-bottom: 1px solid #ccc; padding-bottom: 4px; margin-bottom: 6px; }
    .print-row { display: flex; justify-content: space-between; padding: 5px 0; border-bottom: 1px solid #eee; font-size: 12px; }
    .print-row:last-child { border-bottom: none; }
    .print-row .pname { font-weight: 500; }
    .print-row .pdetail { color: #555; font-size: 11px; }
    .print-row .pphone { color: #0F6E56; }
    .print-empty { font-size: 12px; color: #aaa; padding: 6px 0; }
  }
  @media (max-width: 600px) { .metrics { grid-template-columns: repeat(2, 1fr); } }
</style>
</head>
<body>

<header>
  <div>
    <h1>Controle de Limpezas Odontológicas</h1>
    <p>Plano odontológico — liberação a cada 6 meses</p>
  </div>
  <button class="btn-relatorio" onclick="imprimirRelatorio()">📋 Imprimir relatório</button>
</header>

<div class="container">
  <div class="top-bar">
    <input id="search" type="text" placeholder="Buscar por nome ou celular..." oninput="render()" />
    <button class="btn btn-add" onclick="openModal()">+ Adicionar paciente</button>
  </div>
  <div class="metrics" id="metrics"></div>
  <div class="filters">
    <button class="pill active" data-f="todos" onclick="setFilter('todos',this)">Todos</button>
    <button class="pill f-liberada" data-f="liberada" onclick="setFilter('liberada',this)">Limpeza liberada</button>
    <button class="pill f-aguardando" data-f="aguardando" onclick="setFilter('aguardando',this)">Aguardando</button>
    <button class="pill f-atrasado" data-f="atrasado" onclick="setFilter('atrasado',this)">Sem retorno (+1 ano)</button>
  </div>
  <div class="card">
    <table>
      <thead>
        <tr>
          <th>Paciente</th>
          <th>Celular</th>
          <th>Última limpeza</th>
          <th>Próxima liberação</th>
          <th>Status</th>
          <th>Confirmar limpeza</th>
          <th></th>
        </tr>
      </thead>
      <tbody id="tbody"></tbody>
    </table>
    <div id="empty" class="empty" style="display:none">Nenhum paciente encontrado.</div>
  </div>
</div>

<div id="print-area"></div>

<div class="modal-bg" id="modal-bg" onclick="if(event.target===this)closeModal()">
  <div class="modal">
    <h3>Adicionar paciente</h3>
    <label>Nome completo *</label>
    <input id="m-name" type="text" placeholder="Ex: Maria da Silva" />
    <label>Celular</label>
    <input id="m-phone" type="tel" placeholder="Ex: (45) 99999-0000" />
    <label>Data da última limpeza *</label>
    <input id="m-date" type="date" />
    <div class="modal-btns">
      <button class="btn" onclick="closeModal()">Cancelar</button>
      <button class="btn btn-add" onclick="addPatient()">Salvar</button>
    </div>
  </div>
</div>

<script>
const DAY = 86400000;

// Carrega pacientes — compatível com chave antiga e nova
let patients = JSON.parse(localStorage.getItem('dental_v2') || localStorage.getItem('dental_patients') || 'null') || [];
let nextId = patients.length ? Math.max(...patients.map(p => p.id)) + 1 : 1;
let filter = 'todos';

function save() { localStorage.setItem('dental_v2', JSON.stringify(patients)); }

// Retorna a data da última limpeza independente do nome do campo
function getDate(p) {
  return p.lastClean || p.last_clean || '';
}

function diasParaLiberar(p) {
  const d = getDate(p);
  if (!d) return 9999;
  const next = new Date(d + 'T00:00:00').getTime() + 180 * DAY;
  return Math.ceil((next - Date.now()) / DAY);
}

function getStatus(p) {
  const d = getDate(p);
  if (!d) return 'atrasado';
  const diffDays = Math.floor((Date.now() - new Date(d + 'T00:00:00').getTime()) / DAY);
  if (diffDays >= 365) return 'atrasado';
  if (diasParaLiberar(p) <= 0) return 'liberada';
  return 'aguardando';
}

function fmtDate(str) {
  if (!str) return '-';
  const [y, m, d] = str.split('-');
  return d + '/' + m + '/' + y;
}

function nextReleaseLabel(p) {
  const d = getDate(p);
  if (!d) return 'Sem data';
  const diffDays = Math.floor((Date.now() - new Date(d + 'T00:00:00').getTime()) / DAY);
  if (diffDays >= 365) return 'Há mais de 1 ano';
  const dias = diasParaLiberar(p);
  if (dias <= 0) return '✅ Liberada';
  const nextDate = new Date(new Date(d + 'T00:00:00').getTime() + 180 * DAY);
  return 'Em ' + dias + ' dia' + (dias > 1 ? 's' : '') + ' (' + fmtDate(nextDate.toISOString().split('T')[0]) + ')';
}

function sortLista(lista) {
  return lista.slice().sort((a, b) => diasParaLiberar(a) - diasParaLiberar(b));
}

function setFilter(f, el) {
  filter = f;
  document.querySelectorAll('.pill').forEach(p => p.classList.remove('active'));
  el.classList.add('active');
  render();
}

function render() {
  const q = document.getElementById('search').value.toLowerCase();
  let list = patients.filter(p =>
    p.name.toLowerCase().includes(q) || (p.phone && p.phone.includes(q))
  );
  if (filter !== 'todos') list = list.filter(p => getStatus(p) === filter);
  list = sortLista(list);

  const counts = { liberada: 0, aguardando: 0, atrasado: 0 };
  patients.forEach(p => counts[getStatus(p)]++);

  document.getElementById('metrics').innerHTML =
    '<div class="metric"><div class="label">Total de pacientes</div><div class="val cinza">' + patients.length + '</div></div>' +
    '<div class="metric"><div class="label">Limpeza liberada</div><div class="val verde">' + counts.liberada + '</div></div>' +
    '<div class="metric"><div class="label">Aguardando 6 meses</div><div class="val amarelo">' + counts.aguardando + '</div></div>' +
    '<div class="metric"><div class="label">Sem retorno +1 ano</div><div class="val vermelho">' + counts.atrasado + '</div></div>';

  const labels = { liberada: 'Limpeza liberada', aguardando: 'Aguardando', atrasado: 'Sem retorno +1 ano' };
  const tbody = document.getElementById('tbody');
  tbody.innerHTML = list.map(p => {
    const st = getStatus(p);
    const showBtn = st === 'liberada' || st === 'atrasado';
    const d = getDate(p);
    return '<tr>' +
      '<td style="font-weight:500">' + p.name + '</td>' +
      '<td style="color:#0F6E56">' + (p.phone || '<span style="color:#ccc">—</span>') + '</td>' +
      '<td>' + fmtDate(d) + '</td>' +
      '<td style="font-size:12px;color:#888">' + nextReleaseLabel(p) + '</td>' +
      '<td><span class="badge ' + st + '">' + labels[st] + '</span></td>' +
      '<td>' + (showBtn ? '<button class="action-btn ok" onclick="confirmarLimpeza(' + p.id + ')">✓ Realizada hoje</button>' : '<span style="font-size:12px;color:#ccc">—</span>') + '</td>' +
      '<td><button class="del-btn" onclick="del(' + p.id + ')">✕</button></td>' +
      '</tr>';
  }).join('');

  document.getElementById('empty').style.display = list.length ? 'none' : 'block';
}

function confirmarLimpeza(id) {
  const p = patients.find(x => x.id === id);
  if (!p) return;
  const hoje = new Date().toISOString().split('T')[0];
  if (!confirm('Confirmar que ' + p.name + ' realizou a limpeza hoje (' + fmtDate(hoje) + ')?\n\nA contagem de 6 meses será reiniciada.')) return;
  p.lastClean = hoje;
  p.last_clean = hoje;
  save(); render();
}

function del(id) {
  const p = patients.find(x => x.id === id);
  if (!confirm('Remover ' + p.name + ' do sistema?')) return;
  patients = patients.filter(x => x.id !== id);
  save(); render();
}

function openModal() {
  document.getElementById('m-name').value = '';
  document.getElementById('m-phone').value = '';
  document.getElementById('m-date').value = new Date().toISOString().split('T')[0];
  document.getElementById('modal-bg').classList.add('open');
  setTimeout(() => document.getElementById('m-name').focus(), 100);
}

function closeModal() { document.getElementById('modal-bg').classList.remove('open'); }

function addPatient() {
  const name = document.getElementById('m-name').value.trim();
  const phone = document.getElementById('m-phone').value.trim();
  const date = document.getElementById('m-date').value;
  if (!name || !date) { alert('Preencha o nome e a data.'); return; }
  patients.push({ id: nextId++, name, phone, lastClean: date });
  save(); closeModal(); render();
}

function imprimirRelatorio() {
  const now = new Date();
  const dataGeracao = 'Gerado em ' + now.toLocaleDateString('pt-BR') + ' às ' + now.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' });
  const liberados  = sortLista(patients.filter(p => getStatus(p) === 'liberada'));
  const aguardando = sortLista(patients.filter(p => getStatus(p) === 'aguardando'));
  const atrasados  = sortLista(patients.filter(p => getStatus(p) === 'atrasado'));

  function secao(titulo, lista, detalhe) {
    let html = '<div class="print-section"><h2>' + titulo + ' (' + lista.length + ')</h2>';
    if (!lista.length) html += '<p class="print-empty">Nenhum paciente nesta categoria.</p>';
    else lista.forEach(p => {
      html += '<div class="print-row"><div><div class="pname">' + p.name + '</div><div class="pdetail">' + detalhe(p) + '</div></div><div class="pphone">' + (p.phone || '—') + '</div></div>';
    });
    return html + '</div>';
  }

  let html = '<div class="print-header"><h1>Relatório de Limpezas — Clínica OSE</h1><p>' + dataGeracao + ' &nbsp;|&nbsp; Total: ' + patients.length + ' pacientes</p></div>';
  html += secao('✅ Limpeza liberada', liberados, p => 'Última limpeza: ' + fmtDate(getDate(p)));
  html += secao('⏳ Aguardando liberação', aguardando, p => nextReleaseLabel(p));
  html += secao('🔴 Sem retorno há mais de 1 ano', atrasados, p => 'Última limpeza: ' + fmtDate(getDate(p)));

  document.getElementById('print-area').innerHTML = html;
  window.print();
}

setInterval(render, 3600000);
render();
</script>
</body>
</html>
