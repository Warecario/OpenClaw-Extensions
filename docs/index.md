---
layout: default
---

<div id="nav-container">
    <button class="nav-tab active" onclick="showTab(this, 'home')">Home</button>
    <button class="nav-tab" onclick="showTab(this, 'extensions')">Extensions</button>
    <button class="nav-tab" onclick="showTab(this, 'license')">License</button>
</div>

<div id="home-view" class="view active">
    <div class="card">
        <h3>Read Before Use</h3>
        <p>OpenClaw Extensions (for Discord) — By Warecario</p>
        
        <div style="margin: 20px 0; padding: 15px; background: #1e1e1e; border-radius: 4px; border-left: 4px solid #007acc;">
            <a href="https://download937.mediafire.com/..." style="color: #007acc !important; font-weight: bold; text-decoration: none;">
               App For Windows
            </a>
        </div>

        <hr>
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
                <p>Loading Extensions...</p>
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
        <hr>
        <p>These files can be used by everybody. But only Warecario may make, distribute, or sell these projects.</p>
    </div>
</div>

<script>
const GITHUB_USER = "Warecario";
const GITHUB_REPO = "OpenClaw-Extensions";
let allExtensions = [];

async function loadData() {
    const listDiv = document.getElementById('extension-list');
    try {
        const res = await fetch(`https://api.github.com/repos/${GITHUB_USER}/${GITHUB_REPO}/contents`);
        const files = await res.json();
        allExtensions = files.filter(f => {
            const n = f.name.toLowerCase();
            return (n.endsWith('.cario2weak') || n.endsWith('.octweak')) && n !== 'template.cario2weak';
        });
        renderList(allExtensions);
    } catch (err) {
        listDiv.innerHTML = `<p>Check connection or repo settings.</p>`;
    }
}

function formatName(name) { return name.replace(/\.(cario2weak|octweak)$/, '').replace(/[-_]/g, ' ').toUpperCase(); }

function renderList(list) {
    const container = document.getElementById('extension-list');
    if (!container) return;
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
        <h2>${formatName(name)}</h2>
        <p>File: <code>${name}</code></p>
        <button class="copy-btn" onclick="copyPrompt('${url}', this)">Copy Install Prompt</button>
    `;
}

function copyPrompt(url, btn) {
    navigator.clipboard.writeText(`Read the file at this link: ${url} and apply what is said in the file.`);
    btn.innerText = "Copied!";
    setTimeout(() => { btn.innerText = "Copy Install Prompt"; }, 2000);
}

function showTab(btnElement, tabId) {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-tab').forEach(t => t.classList.remove('active'));
    document.getElementById(tabId + '-view').classList.add('active');
    btnElement.classList.add('active');
}

loadData();
</script>
