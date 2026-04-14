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
        <hr>
        <pre id="license-text" style="white-space: pre-wrap; color: #cccccc; font-family: 'Segoe UI', sans-serif;">Loading license...</pre>
    </div>
</div>

<script>
let allExtensions = [];

// --- CONFIGURATION ---
const username = "Warecario"; 
const repo = "OpenClaw-Extensions"; 
const branch = "main"; // <--- CHANGE TO "master" IF YOUR BRANCH IS MASTER
// ---------------------

async function loadData() {
    const listContainer = document.getElementById('extension-list');
    const licenseContainer = document.getElementById('license-text');

    try {
        // 1. Fetch Extensions
        const response = await fetch(`https://api.github.com/repos/${username}/${repo}/contents`);
        
        if (!response.ok) throw new Error(`GitHub API Error: ${response.statusText}`);
        
        const files = await response.json();
        
        allExtensions = files.filter(f => f.name.endsWith('.cario2weak') || f.name.endsWith('.octweak'))
                             .filter(f => f.name !== 'template.cario2weak');

        renderList(allExtensions);

        // 2. Fetch License (Checking for License, LICENSE, or License.txt)
        const possibleLicenses = ['License', 'LICENSE', 'license.txt', 'LICENSE.md'];
        let licenseData = "License file not found.";

        for (let name of possibleLicenses) {
            const licRes = await fetch(`https://raw.githubusercontent.com/${username}/${repo}/${branch}/${name}`);
            if (licRes.ok) {
                licenseData = await licRes.text();
                break;
            }
        }
        licenseContainer.innerText = licenseData;

    } catch (e) {
        console.error(e);
        listContainer.innerHTML = `<p style="padding:10px; color:#ff6b6b;">Error: ${e.message}. <br><br>Check if your repo name "${repo}" is correct.</p>`;
        licenseContainer.innerText = "Error loading license.";
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
        container.innerHTML = "<p style='padding:10px; color:#858585;'>No .cario2weak files found.</p>";
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
