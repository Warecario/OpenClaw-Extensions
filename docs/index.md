---
layout: default
---

<link rel="stylesheet" href="{{ '/assets/css/style.scss' | relative_url }}">

<div id="nav-container">
    <button class="nav-tab active" onclick="showTab(this, 'home')">Home</button>
    <button class="nav-tab" onclick="showTab(this, 'extensions')">Extensions</button>
    <button class="nav-tab" onclick="showTab(this, 'scripts')">Scripts</button>
    <button class="nav-tab" onclick="showTab(this, 'packs')">Packs</button>
    <button class="nav-tab" onclick="showTab(this, 'license')">License</button>
</div>

<div id="home-view" class="view active">
    <div class="card">
        <h3>Read Before Use</h3>
        <p>OpenClaw Extensions (for Discord) — By Warecario</p>
        
        <div style="margin: 20px 0; padding: 15px; background: #1e1e1e; border-radius: 4px; border-left: 4px solid #007acc;">
            <a href="https://github.com/Warecario/OpenClaw-Extensions/releases/download/v1/OC-CarioPlugins-1-0.exe" style="color: #007acc !important; font-weight: bold; text-decoration: none;">
               App For Windows
            </a>
        </div>

        <hr>
        <h4>How to use:</h4>
        <ol>
            <li>Browse extensions in the <b>Extensions</b> tab.</li>
            <li>Browse scripts in the <b>Scripts</b> tab.</li>
            <li>Browse plugin packs in the <b>Packs</b> tab.</li>
            <li>Click an item to view details.</li>
            <li>Click the <b>Copy</b> button to get the install prompt.</li>
            <li>Paste the prompt to your bot to install.</li>
        </ol>
    </div>
</div>

<div id="extensions-view" class="view">
    <div class="app-container">
        <div class="sidebar">
            <input type="text" id="search-extensions" placeholder="Search extensions..." onkeyup="filterItems('extensions')">
            <div id="extension-list">
                <p>Loading Extensions...</p>
            </div>
        </div>
        <div class="details-panel" id="details-panel-extensions">
            <div class="placeholder-text">Select an extension from the list</div>
        </div>
    </div>
</div>

<div id="scripts-view" class="view">
    <div class="app-container">
        <div class="sidebar">
            <input type="text" id="search-scripts" placeholder="Search scripts..." onkeyup="filterItems('scripts')">
            <div id="script-list">
                <p>Loading Scripts...</p>
            </div>
        </div>
        <div class="details-panel" id="details-panel-scripts">
            <div class="placeholder-text">Select a script from the list</div>
        </div>
    </div>
</div>

<div id="packs-view" class="view">
    <div class="app-container">
        <div class="sidebar">
            <input type="text" id="search-packs" placeholder="Search packs..." onkeyup="filterItems('packs')">
            <div id="pack-list">
                <p>Loading Packs...</p>
            </div>
        </div>
        <div class="details-panel" id="details-panel-packs">
            <div class="placeholder-text">Select a pack from the list</div>
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

const categories = {
    extensions: {
        listId: 'extension-list',
        searchId: 'search-extensions',
        panelId: 'details-panel-extensions',
        placeholder: 'Select an extension from the list',
        loadingText: 'Loading Extensions...',
        patterns: ['.cario2weak', '.octweak'],
        exclude: ['template.cario2weak']
    },
    scripts: {
        listId: 'script-list',
        searchId: 'search-scripts',
        panelId: 'details-panel-scripts',
        placeholder: 'Select a script from the list',
        loadingText: 'Loading Scripts...',
        patterns: ['.cario5cript'],
        exclude: ['template.cario5cript']
    },
    packs: {
        listId: 'pack-list',
        searchId: 'search-packs',
        panelId: 'details-panel-packs',
        placeholder: 'Select a pack from the list',
        loadingText: 'Loading Packs...',
        patterns: ['.carioPack'],
        exclude: ['template.carioPack']
    }
};

const allItems = {
    extensions: [],
    scripts: [],
    packs: []
};

async function loadData() {
    try {
        const res = await fetch(`https://api.github.com/repos/${GITHUB_USER}/${GITHUB_REPO}/contents`);
        const files = await res.json();

        Object.keys(categories).forEach(category => {
            allItems[category] = files.filter(f => matchesCategory(f.name, categories[category].patterns, categories[category].exclude));
            const list = document.getElementById(categories[category].listId);
            if (list) {
                list.innerHTML = `<p>${categories[category].loadingText}</p>`;
            }
        });

        renderList(allItems.extensions, 'extensions');
    } catch (err) {
        Object.keys(categories).forEach(category => {
            const list = document.getElementById(categories[category].listId);
            if (list) {
                list.innerHTML = `<p>Check connection or repo settings.</p>`;
            }
        });
    }
}

function matchesCategory(name, patterns, exclude) {
    const lower = name.toLowerCase();
    return patterns.some(pattern => lower.endsWith(pattern.toLowerCase())) && !exclude.map(item => item.toLowerCase()).includes(lower);
}

function formatName(name) {
    return name.replace(/\.(cario2weak|octweak|cario5cript|carioPack)$/i, '').replace(/[-_]/g, ' ').toUpperCase();
}

function renderList(list, category) {
    const container = document.getElementById(categories[category].listId);
    if (!container) return;
    if (!list.length) {
        container.innerHTML = `<p>No items found.</p>`;
        return;
    }
    container.innerHTML = list.map(item => `
        <button class="ext-item" onclick="selectItem('${category}', '${item.name}', '${item.download_url}')">
            ${formatName(item.name)}
        </button>
    `).join('');
}

function filterItems(category) {
    const query = document.getElementById(categories[category].searchId).value.toLowerCase();
    const filtered = allItems[category].filter(item => item.name.toLowerCase().includes(query));
    renderList(filtered, category);
}

async function selectItem(category, name, url) {
    const panel = document.getElementById(categories[category].panelId);
    if (!panel) return;
    panel.innerHTML = `<div class="placeholder-text">Reading item data...</div>`;

    try {
        const res = await fetch(url);
        const content = await res.text();
        const descMatch = content.match(/DESCRIPTION:\s*([\s\S]*?)\s*~~~/i);
        let description = descMatch ? descMatch[1].trim() : 'No description provided.';
        if (description.length > 500) {
            description = description.substring(0, 500) + '...';
        }

        panel.innerHTML = `
            <h2>${formatName(name)}</h2>
            <div style="text-align: left; background: #252525; padding: 15px; border-radius: 5px; margin: 10px 0;">
                <p style="margin: 0; font-weight: bold; color: #007acc;">Description:</p>
                <p style="margin: 5px 0 0 0; color: #eee; line-height: 1.4;">${description}</p>
            </div>
            <p>File: <code>${name}</code></p>
            <button class="copy-btn" onclick="copyPrompt('${url}', this)">Copy Install Prompt</button>
        `;
    } catch (err) {
        panel.innerHTML = `<h2>${formatName(name)}</h2><p>Error loading description.</p>`;
    }
}

function copyPrompt(url, btn) {
    navigator.clipboard.writeText(`Read the file at this link: ${url} and apply what is said in the file.`);
    btn.innerText = 'Copied!';
    setTimeout(() => { btn.innerText = 'Copy Install Prompt'; }, 2000);
}

function showTab(btnElement, tabId) {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-tab').forEach(t => t.classList.remove('active'));
    document.getElementById(tabId + '-view').classList.add('active');
    btnElement.classList.add('active');
}

loadData();
</script>
