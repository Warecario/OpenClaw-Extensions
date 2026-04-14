---
layout: default
---

<div id="nav-container">
    <button class="nav-tab active" onclick="showTab('home')">Home</button>
    <button class="nav-tab" onclick="showTab('extensions')">Extensions</button>
    <button class="nav-tab" onclick="showTab('license')">License</button>
</div>

<div id="home-view" class="view active">
    <div class="card">
        <h3>Read Before Use</h3>
        <p>OpenClaw Extensions (for Discord)<br>By Warecario</p>
        <p>This is an extension manager for OpenClaw bots. Extensions add functionality to your bot.</p>
        <hr>
        <h4>How to use:</h4>
        <ol>
            <li>Browse extensions in the Extensions tab</li>
            <li>Click an extension to view details</li>
            <li>Click the Copy button to get the install prompt</li>
            <li>Paste the prompt to your bot to install</li>
        </ol>
    </div>
</div>

<div id="extensions-view" class="view">
    <div class="app-container">
        <div class="sidebar">
            <input type="text" id="search-bar" placeholder="Search extensions..." onkeyup="filterExtensions()">
            <div id="extension-list">
                <p style="padding:10px; color:#858585;">Loading extensions...</p>
            </div>
        </div>

        <div class="details-panel" id="details-panel">
            <div class="placeholder-text">Click an extension to view details</div>
        </div>
    </div>
</div>

<div id="license-view" class="view">
    <div class="card">
        <pre id="license-text">Loading license...</pre>
    </div>
</div>

<script>
let allExtensions = [];

// 1. Fetch data from your GitHub Repo
async function loadData() {
    try {
        // Fetch Extensions
        const response = await fetch('https://api.github.com/repos/Warecario/OpenClaw-Extensions/contents');
        const files = await response.json();
        
        allExtensions = files.filter(f => f.name.endsWith('.cario2weak') || f.name.endsWith('.octweak'))
                             .filter(f => f.name !== 'template.cario2weak');

        renderList(allExtensions);

        // Fetch License
        const licRes = await fetch('https://raw.githubusercontent.com/Warecario/OpenClaw-Extensions/main/License');
        document.getElementById('license-text').innerText = await licRes.text();
    } catch (e) {
        console.error("Failed to load:", e);
    }
}

// 2. Render the list in the sidebar
function renderList(list) {
    const container = document.getElementById('extension-list');
    container.innerHTML = list.map(ext => {
        const displayName = ext.name.replace('.cario2weak', '').replace(/-/g, ' ').toUpperCase();
        return `<button class="ext-item" onclick="selectExtension('${ext.name}', '${ext.download_url}')">${displayName}</button>`;
    }).join('');
}

// 3. Search logic
function filterExtensions() {
    const query = document.getElementById('search-bar').value.toLowerCase();
    const filtered = allExtensions.filter(ext => ext.name.toLowerCase().includes(query));
    renderList(filtered);
}

// 4. Show details and Copy button
async function selectExtension(name, url) {
    const panel = document.getElementById('details-panel');
    const displayName = name.replace('.cario2weak', '').replace(/-/g, ' ').toUpperCase();
    
    panel.innerHTML = `
        <h2>${displayName}</h2>
        <p style="color:#858585">Source: ${name}</p>
        <div class="actions">
            <button class="copy-btn" onclick="copyPrompt('${url}', this)">Copy Install Prompt</button>
        </div>
    `;
}

function copyPrompt(url, btn) {
    const prompt = `Read the file at this link: ${url} and apply what is said in the file.`;
    navigator.clipboard.writeText(prompt);
    btn.innerText = "Copied!";
    btn.style.background = "#007acc";
    setTimeout(() => { 
        btn.innerText = "Copy Install Prompt"; 
        btn.style.background = "#16825d";
    }, 2000);
}

// Tab switcher
function showTab(tabId) {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-tab').forEach(t => t.classList.remove('active'));
    document.getElementById(tabId + '-view').classList.add('active');
    event.currentTarget.classList.add('active');
}

loadData();
</script>
