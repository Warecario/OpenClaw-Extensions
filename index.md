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
        <h3>License</h3>
        <pre id="license-text" style="white-space: pre-wrap; color: #cccccc; font-family: 'Segoe UI', sans-serif;">Loading license...</pre>
    </div>
</div>

<script>
let allExtensions = [];
// IMPORTANT: Change 'Warecario' to your actual GitHub username if different
const repoPath = 'Warecario/OpenClaw-Extensions'; 

async function loadData() {
    try {
        const response = await fetch(`https://api.github.com/repos/${repoPath}/contents`);
        const files = await response.json();
        
        allExtensions = files.filter(f => f.name.endsWith('.cario2weak') || f.name.endsWith('.octweak'))
                             .filter(f => f.name !== 'template.cario2weak');

        renderList(allExtensions);

        const licRes = await fetch(`https://raw.githubusercontent.com/${repoPath}/main/License`);
        if(licRes.ok) {
            document.getElementById('license-text').innerText = await licRes.text();
        } else {
            document.getElementById('license-text').innerText = "License file not found in repository.";
        }
    } catch (e) {
        document.getElementById('extension-list').innerHTML = "<p style='padding:10px; color:red;'>Error loading extensions. Check repo name.</p>";
    }
}

function formatName(filename) {
    return filename.replace('.cario2weak', '').replace('.octweak', '')
                   .replace(/-/g, ' ').replace(/_/g, ' ')
                   .replace(/RP/g, 'Roleplay').replace(/GB/g, 'GreenBrew')
                   .replace(/OC/g, 'OpenClaw').replace(/Term/g, 'Terminal')
                   .toUpperCase();
}

function renderList(list) {
    const container = document.getElementById('extension-list');
    if (list.length === 0) {
        container.innerHTML = "<p style='padding:10px; color:#858585;'>No extensions found.</p>";
        return;
    }
    container.innerHTML = list.map(ext => {
        return `<button class="ext-item" onclick="selectExtension('${ext.name}', '${ext.download_url}')">${formatName(ext.name)}</button>`;
    }).join('');
}

function filterExtensions() {
    const query = document.getElementById('search-bar').value.toLowerCase();
    const filtered = allExtensions.filter(ext => ext.name.toLowerCase().includes(query));
    renderList(filtered);
}

async function selectExtension(name, url) {
    const panel = document.getElementById('details-panel');
    panel.innerHTML = `
        <h2>${formatName(name)}</h2>
        <p style="color:#858585; margin-bottom: 20px;">Source: ${name}</p>
        <button class="copy-btn" onclick="copyPrompt('${url}', this)">Copy Install Prompt</button>
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

function showTab(tabId) {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-tab').forEach(t => t.classList.remove('active'));
    document.getElementById(tabId + '-view').classList.add('active');
    event.currentTarget.classList.add('active');
}

loadData();
</script>
