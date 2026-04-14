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
        <p>OpenClaw Extensions (for Discord) — By Warecario</p>
        <p>This is an extension manager for OpenClaw bots. Extensions add functionality to your bot.</p>
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
                <p style="padding:20px; color:#858585;">Searching for extensions...</p>
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
        <p>ARR</p>
        <hr style="border: 0; border-top: 1px solid #333; margin: 20px 0;">
        <ol>
            <p>These files can be used by everybody. But only Warecario may make, distribute, or sell these projects. The Template file is only by for Warecario's use. Anything against this is strictly prohibited.</p>
            <p>.cario2weak is not copyrighted, as it's a file extension. My only ask is you use it for plugin making, but dont use my Template for it.</p>
        </ol>
    </div>
</div>

<script>
// --- VERIFY THESE ARE CORRECT ---
const GITHUB_USER = "Warecario";
const GITHUB_REPO = "OpenClaw-Extensions";
const BRANCH = "main"; // Change to "master" if that is your main branch
// --------------------------------

let allExtensions = [];

async function loadData() {
    const listDiv = document.getElementById('extension-list');
    
    try {
        // 1. Get Repo Contents
        const res = await fetch(`https://api.github.com/repos/${GITHUB_USER}/${GITHUB_REPO}/contents`);
        if (!res.ok) throw new Error("Could not access repository. Is the name correct?");
        
        const files = await res.json();
        
        // Filter for your specific extensions
        allExtensions = files.filter(f => f.name.endsWith('.cario2weak') || f.name.endsWith('.octweak'))
                             .filter(f => f.name !== 'template.cario2weak');

        renderList(allExtensions);

        // 2. Get License Content
        const licRes = await fetch(`https://raw.githubusercontent.com/${GITHUB_USER}/${GITHUB_REPO}/${BRANCH}/License`);
        if (licRes.ok) {
            document.getElementById('license-text').innerText = await licRes.text();
        } else {
            document.getElementById('license-text').innerText = "License file not found in the root folder.";
        }

    } catch (err) {
        listDiv.innerHTML = `<p style="padding:20px; color:#ff6b6b;"><b>Error:</b> ${err.message}</p>`;
    }
}

function formatName(name) {
    return name.replace(/\.(cario2weak|octweak)$/, '')
               .replace(/[-_]/g, ' ')
               .replace(/RP/g, 'Roleplay').replace(/GB/g, 'GreenBrew').replace(/OC/g, 'OpenClaw')
               .toUpperCase();
}

function renderList(list) {
    const container = document.getElementById('extension-list');
    if (list.length === 0) {
        container.innerHTML = "<p style='padding:20px;'>No .cario2weak files found in this repo.</p>";
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
        <h2 style="margin-top:0;">${formatName(name)}</h2>
        <p style="color:#858585; margin-bottom: 30px;">File: <code>${name}</code></p>
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
