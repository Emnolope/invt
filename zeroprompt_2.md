`````command
Try to understand what the human is doing, and what is happening. And what your own weaknesses are, and what we're doing here. 
`````



`````ignore this text block of unresolved problems
Files reality → Memory reality → UI reality
The filesystem is authoritative. Filenames encode metadata directly. The app reads filenames, parses them into runtime objects, displays them, and writes them back atomically

Metadata Exists in TWO REPRESENTATIONS
Not two systems. Two encodings of the same information:

String representation: The filename (Bando.BoxA_c,t_GET__note_20250604135552143.jpg)
Object representation: The metadata structure ({data: {UUID, ext}, values: [[...], [...], ...]})

Translation functions are BRIDGES:

metadataFromName: string → object
metadataToName: object → string

Inventory Object is Runtime State Cache
Gallery, table, and any future views are RENDERERS that consume inventory object. They don't manage their own data.
Synchronization Contract
Inventory object must stay synchronized with filesystem changes:̣

IN FACT IGNORE ALL THE PROBLEMS **EXCEPT** THE ONES WE ARE TRYING TO WORK ON **RIGHT NOW**
Details mode intended UI flow:
- Capture photo -> Save file as UUID -> Wizzard UI -> Return metadata-> Rename file
How to speed up/reduce repetitive data entry for extra speed? Solutions: 
- Point and click string builder? REALLY complicated, with current code set.
- Command prompt like auto complete? Too keyboard/fine-motor control based.
- Programmable default: Easiest implementation, defaults in wizzard (star button)
    State Machine Star Button Solution: A single button that cycles through three states:
    Shooting Star (🌠): Intelligently merges current default with wizard values
    Regular Star (⭐): Replaces all wizard data with stored default
    Rising/Falling Star (🌟): Takes current wizard state and saves it as new default
balance between (
  files reality system based integrity (like file manager with extra features)
  memory reality based speed (needs app state management synchronization with file layer)
)

Optimistic Pathways:
User picks folder → fileWrite saves image with UUID filename → fileRead retrieves exact file → fileRename atomically changes filename → fileDelete removes unwanted files → fileFind locates file by UUID
Schema System Optimistic Path:
User loads valid schema.json → schemaFromText parses to object → UI builds from schema → user input validates against schema → schemaToText serializes back → file saves successfully
Camera System Optimistic Path:
Camera.init() gets permission → video stream starts → user taps → cameraCapture gets frame → image converts to blob → file saves with metadata
Inventory System Optimistic Path:
User captures photo → metadata generates from schema → filename constructs from metadata → file saves → UUID indexes into files object → cache generates thumbnail → gallery displays
UI System Optimistic Path:
Mode switches work → wizard overlay shows → user fills fields → wizard returns values → overlay closes → data flows to storage


COMPONENT ERROR PARADIGM MAPPING: Defensive resilience by default with explicit boundaries for data-destroying operations.
File I/O → Atomic fail-fast. Throw on ANY error. No internal error handling. Data destruction operations fail explicitly.
Inventory/UUID → Authoritative single-source. Throw on most errors. UUID conflicts: first-found-wins, fail silently, log for manual inspection. No complex merging logic.
Camera → Binary state. Init succeeds or throws. No auto-retry. Manual recovery only.
Schema → Resilient parsing with defaults. Unknown fields ignored. Malformed data gets default values. Evolution expected, backward compatibility required. Never throws - always produces usable schema.
Filename Ops → Parsing resilient (returns minimal valid metadata with defaults, not null). Generation validated (throws on bad input). Log unparseable filenames for manual fixing.
Cache → Silent regeneration. All errors absorbed. Derived data always reconstructible. Performance optimization, not critical path.
Gallery → Graceful degradation. Show what works, placeholder what breaks. Non-blocking errors. One bad image doesn't stop gallery load.
Wizard → Graceful construction with defaults. Build even with incomplete schema. Return minimal valid metadata rather than throwing. Handle missing schema gracefully. Explicit rejection on user cancel.
Button Handlers → Catch-all boundary. Log everything with full context. UI stays alive. Clear debugging path from user action to logged error. Internal functions fail unprotected, errors bubble to button level.
`````



`````blueprint
The English and pseudocode and blueprint and filename convention documentation for the whole project is used to build the program's structure.
Program implementation needs to meet the demands of this one specific blueprint instantiation, it's primary use case, but also the code must be generic enough to  handle any/similar blueprints and solve the generalized problem, for the benefit of conceptual simplicity and code simplicity and future proofing and blueprint modification and etc.
This blueprint is not hard coded, it's soft coded, and the program can load it from the schema json or learn an autogenerated one from inference mode. The only thing that is hard coded is the data types (single, multiple, text) and the UUID being in last place. Field name and types are soft coded. LocationPath, Category, and Note are SOFT CODED.
blue prints for filename, schema, metadata, object, thumbnails
This is the proposed structure I'd like to use for my stuff, but the program should be generic, since it might change, and I might use that program for more types of information.
FILENAME proposed structure:
//The file have all the metadata in the filenames. This means I can use regular file search without any program at all.
//Memeber access dot notation inspired, string trie matches hiearchies from physical encapsulation well. SYNTAX IS HUMAN TYPED and auto aided with default autofill.
Bando.BoxA_s_____20250604135552143.jpg <pretend this is a picture of BoxA>
Bando.BoxA.PencilCase_s_____20250604140151492.jpg <pretend this is a picture of the pencil case>
Bando.BoxA.PencilCase._t__TOSS___20250604142028052.jpg <pretend this is a picture of the tape>
Bando.BoxA.PencilCase._i____UNSELECT_20250604142535591.jpg <pretend this is a picture of the marker>
Bando.BoxA.PencilCase._i____SELECT_20250604143053472.jpg <pretend this is a picture of the usb>
Bando.BoxA.PencilCase._i_____20250604143026473.jpg <pretend this is a picture of the usb rear>
Bando.BoxA._c,v_STAY____Cracked_20250606153232592.jpeg <pretend this is a picture of a helmet>
Bando.BoxA.Pouch._c,t,b_GET___Gloves are great for fire and workshop usage_20250710153244001.png
SCHEMA proposed structure:
//The schema should contain all possible values of every field. It's also used to build the UI in the wizard.
schemaTextarea.value===`[
  {
    "name": "LocationPath",
    "type": "text"
  },
  {
    "name": "category",
    "type": "multiple",
    "options": [
      "w",
      "f",
      "c",
      "v",
      "t",
      "i",
      "e",
      "h",
      "b",
      "s"
    ]
  },
  {
    "name": "retrievalFlag",
    "type": "single",
    "options": [
      "GET",
      "STAY"
    ]
  },
  {
    "name": "disposalFlag",
    "type": "single",
    "options": [
      "TOSS",
      "KEEP"
    ]
  },
  {
    "name": "selectionFlag",
    "type": "single",
    "options": [
      "SELECT",
      "UNSELECT"
    ]
  },
  {
    "name": "note",
    "type": "text
  }
]`
METADATA proposed structure:
//Metadata should be enough to reconstruct the original filename, and rich enough to have easily accessible meaning within the program, BUT should not contain any data that is already inside of schema.
JSON.Stringify(metadata)===`{
  "data":{
    //"original":"Bando.BoxA.Pouch._c,t,b_STAY___Gloves are great where fire and workshop usage_202507101532001.png", //Because filename can be and should be reconstructed, and UUID should be only programatic identifier.
    "UUID":"20250710211532001",
    "ext":"png"
  },
  "values":[
    ["Bando.BoxA.Pouch."],
    ["c", "t", "b"],
    ["GET"],
    [undefined],
    [undefined],
    ["Gloves are great where fire and workshop usage"]
  ]
}`;
OBJECT proposed structure:
{
"20250604135552143": {
  thumbnail: blob,
  metadata: {...},
  //, embedding: [???, ???, ???]
},
"20250604140151492": {...},
"20250604142028052": {...},
"20250604142535591": {...},
"20250604143053472": {...},
"20250604143026473": {...},
"20250606153232592": {...},
"20250710153244001": {...}
}
CACHE file proposed structure:
mirrors files object structure
One file to manage, portable, large file, all-or-nothing loading
cache.json: {
  "20250604135552143": {
    thumbnail: "data:image/jpeg;base64,/9j/4AAQ..."
    //, embedding: [???, ???, ???]
    },
  "20250604140151492": {
    thumbnail: "data:image/jpeg;base64,/9j/4AAQ..."
    //, embedding: [???, ???, ???]
  }
}
`````



Claude, this is timeline 17.20.
`````content
<!DOCTYPE html>
<html>
<head>
  <title>InvtSysShell3+ v17.20</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <style>
/***** RESET *****/
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
/***** LAYOUT *****/
body {
  height: 100vh;
  font-family: system-ui, -apple-system, sans-serif;
  overflow: hidden;
}
/***** MODE SYSTEM *****/
.mode-navigation {
  position: fixed;
  top: 10px;
  right: 10px;
  z-index: 1000;
  display: flex;
  gap: 10px;
}
.mode-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2em;
  border: none;
  cursor: pointer;
  user-select: none;
  padding: 10px;
  background: rgba(0, 0, 0, 0.5);
  color: white;
  border-radius: 8px;
}
.mode-btn:hover {
  background: rgba(0, 0, 0, 0.7);
}
.mode-btn:active {
  background: rgba(0, 0, 0, 0.9);
}
.mode {
  display: none;
}
.mode.active {
  display: block;
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  overflow-y: auto;
  overflow-x: hidden;
}
/***** BUTTON BASE *****/
button {
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2em;
  border: none;
  cursor: pointer;
  user-select: none;
  padding: 10px;
}
/***** POSITIONED CONTROLS *****/
.camera-controls {
  position: fixed;
  bottom: 100px;
  width: 100%;
  display: flex;
  justify-content: space-between;
  padding: 0 20px;
  z-index: 100;
}
/***** CAMERA *****/
#camera {
  width: 100vw;
  height: 100vh;
  object-fit: cover;
  cursor: pointer;
}
/***** GALLERY *****/
#gallery {
  display: grid;
  grid-template-columns: 1fr 1fr;
}
.gallery-item {
  width: 100%;
  aspect-ratio: 1;
  object-fit: cover;
  cursor: pointer;
}
/***** WIZARD OVERLAY *****/
#wizardOverlay {
  position: fixed;
  display: none;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  flex-direction: column;
  z-index: 2;
  background: #000;
  padding: 2px;
  gap: 2px;
}
.wizardPane {
  flex: 1;
  display: grid;
  gap: 2px;
}
.wizard-screen {
  display: none;
  background: #000;
  gap: 2px;
}
.wizard-screen.active {
  display: grid;
}
#wizardNavi {
  height: 80px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 2px;
}
/***** WIZARD INPUTS *****/
.wizard-input {
  display: none;
}
.wizard-label {
  background: #ddd;
}
.wizard-label:hover {
  background: #bbb;
}
.wizard-input:checked + .wizard-label {
  background: #007bff;
  color: white;
}
.wizard-input:checked + .wizard-label:hover {
  background: #0056b3;
}
.wizard-text {
  width: 100%;
  height: 100%;
  font-size: 2em;
  padding: 20px;
  background: #fff;
  border: none;
  outline: none;
  resize: none;
}
.nav-button {
  font-size: 1.5em;
  background: #333;
  color: white;
}
.nav-button:hover {
  background: #555;
}
.nav-button:active {
  background: #777;
}
  </style>
</head>
<body>
  <div id="app">
    <div id="mode📸" class="mode">
      <video id="camera" autoplay playsinline onclick="inventoryCapture('rapid')"></video>
      <div class="camera-controls">
        <button onclick="cameraTorchButton()">🔦</button>
        <button onclick="uiWizardToggler()">🐌</button>
        <button onclick="cameraFlipButton()">🔄</button>
      </div>
    </div>
    <div id="mode🖼️" class="mode">
      <div id="gallery"></div>
    </div>
    <div id="mode📊" class="mode">TABLE VIEW</div>
    <div id="mode⚙️" class="mode">
      <button onclick="fileFolderPickButton()">fileFolderPick</button>
      <button onclick="inventoryLoadButton()">inventoryLoad</button>
      <div>
        <textarea id="schemaTextarea" placeholder="Schema JSON"></textarea>
        <button onclick="schemaLoadButton()">schemaLoad</button>
        <button onclick="schemaLearnButton()">schemaLearn</button>
        <button onclick="schemaSaveButton()">schemaSave</button>
      </div>
      <textarea id="output" readonly style="width: 100%; font-family: monospace; font-size: 12px; overflow-y: auto;"></textarea>
    </div>
  </div>
  <div class="mode-navigation">
    <button class="mode-btn" onclick="uiBack()">⬅️</button>
    <button class="mode-btn" onclick="uiNext()">➡️</button>
  </div>
  <div id="wizardOverlay">
    <div id="wizardPanels" class="wizardPane">
    </div>
    <div id="wizardNavi">
    </div>
  </div>
<script src="https://cdn.jsdelivr.net/npm/eruda"></script>
<script>eruda.init();</script>
  <script>
/***** SYSTEM *****/
function log(...msgs) {
  const message = msgs.map(msg => 
    typeof msg === 'object' ? JSON.stringify(msg, null, 2) : String(msg)
  ).join(' ');
  const outputBox = document.getElementById('output');
  outputBox.value += `[${new Date().toISOString()}] ${message}\n`;
  outputBox.scrollTop = outputBox.scrollHeight;
}
window.onload = async function init() {
  uiRender();
  await Camera.init();
}
log('script start');
/***** LOW LEVEL I/O OPERATIONS *****/
let folder;
async function fileCheck(filename) {
  try {
    await folder.getFileHandle(filename);
    return true;
  }
  catch (e) {
    if (e.name === 'NotFoundError') return false;
    throw e;
  }
}
async function fileRead(filename) {
  log(`↘️fileRead`);
  const handle = await folder.getFileHandle(filename);
  return await handle.getFile();
}
async function fileWrite(filename, data, allowOverwrite = false) {
  log(`↘️fileWrite`);
  if (!allowOverwrite && await fileCheck(filename)) {
    throw new Error(`Exists: ${filename}`);
  }
  const handle = await folder.getFileHandle(filename, {create: true});
  const writer = await handle.createWritable();
  await writer.write(data);
  await writer.close();
}
async function fileDelete(filename) {
  log(`↘️fileDelete`);
  await folder.removeEntry(filename);
}
async function fileRename(oldPath, newPath) {
  log(`↘️fileRename`);
  if (await fileCheck(newPath)) {
    throw new Error(`Destination exists: ${newPath}`);
  }
  try {
    const data = await fileRead(oldPath);
    await fileWrite(newPath, data);
  } 
  catch (e) {
    log(`❌ fileRename, copy fail: `, ...arguments, e.message);
    throw new Error(`Copy failed: ${e.message}`);
  }
  try {
    await fileDelete(oldPath);
  } 
  catch (e) {
    log(`❌ fileRename, delete fail: `, ...arguments, e.message);
    throw new Error(`Rename incomplete: ${newPath} created, ${oldPath} remains`);
  }
}
async function fileListAll() {
  log(`↘️fileListAll`);
  const files = [];
  for await (const [name, handle] of folder.entries()) {
    if (handle.kind === 'file') files.push(name);
  }
  return files;
}
async function fileFind(uuid) {
  log(`↘️fileFind`);
  const reconstructedFilename = metadataToName(inventory[uuid]?.metadata);
  if (reconstructedFilename && await fileCheck(reconstructedFilename)) {
    return reconstructedFilename;
  }
  const candidateFiles = (await fileListAll()).filter(filename => 
    filename.match(`_${uuid}\\.`)
  );
  log(`fileFind: `, candidateFiles);
  if (!candidateFiles.length) return null;
  if (candidateFiles.length === 1) return candidateFiles[0];
  const filesWithTimestamps = await Promise.all(
    candidateFiles.map(async filename => ({
      filename,
      lastModified: (await (await folder.getFileHandle(filename)).getFile()).lastModified
    }))
  );
  return filesWithTimestamps
    .sort((a, b) => b.lastModified - a.lastModified)[0]
    .filename;
}
async function fileFolderPick() {
  log(`↘️fileFolderPick`);
  folder = await window.showDirectoryPicker();
}
async function fileFolderPickButton() {
  try {fileFolderPick();}
  catch (e) {log("fileFolderPickButton: ", e.message);}
}
/***** UUID catalogue *****/
let inventory = {};
async function inventorySave(imageData, metadata) {
  log(`↘️inventorySave`);
  const filename = metadataToName(metadata);
  const response = await fetch(imageData);
  const blob = await response.blob();
  await fileWrite(filename, blob);
  return filename;
}
async function inventoryCapture(rapid=true) {
  log(`↘️inventoryCapture`);
  try {
    if (!Camera.Ok()) {
      await Camera.init();
    }
    const imageData = Camera.capture();
    const metadata = metadataDefault();
    if (uiWizardToggle) metadata.values = await Wizard.record();
    const filename = await inventorySave(imageData, metadata);
    const uuid = inventoryUpdate(filename, metadata);
    log(`📸 ${filename}`);
    return uuid;
  } 
  catch (e) {
    log(`❌ inventoryCapture: `, ...arguments, e.message);
    if (!Camera.stream?.active) { Camera.stream = null; }
    return null;
  }
}
function inventoryCaptureButtonFast() {
  log(`↘️inventoryCaptureButtonFast`);
  try {inventoryCapture('rapid');}
  catch(e) {log(e.message);}
}
function inventoryCaptureButtonSlow() {
log(`↘️inventoryCaptureButtonFast`);
  try {inventoryCapture();}
  catch(e) {log(e.message);}
}
// NEEDS CHANGE: Atomic operation, metadata and filename must be in sync.
function inventoryUpdate(filename, metadata) {
  log(`↘️inventoryUpdate`);
  const uuid = metadata.data.UUID;
  inventory[uuid] = { metadata, original: filename };
  return uuid;
}
async function inventoryLoad() {
  log(`↘️inventoryLoad`);
  const filenames = await fileListAll();
  const schema = schemaGet();
  inventory = {};
  for (const filename of filenames) {
    const metadata = metadataFromName(filename, schema);
    if (metadata?.data?.UUID) {
      inventory[metadata.data.UUID] = {
        metadata: metadata,
        original: filename
      };
    }
  }
}
function inventoryLoadButton() {
  log(`↘️inventoryLoadButton`);
  try {inventoryLoad();}
  catch (e) {log(`inventoryLoadButton: `, e.message);}
}
/***** CAMERA *****/
const Camera = {
  stream: null,
  video: null,
  track: null,
  Ok() {
    return this.stream?.active && this.video?.videoWidth > 0;
  },
  async init() {
    log(`↘️Camera.init()`);
    try {
      this.stream = await navigator.mediaDevices.getUserMedia({
        video: {
          facingMode: { ideal: 'environment' }
        }
      });
      this.video = document.getElementById('camera');
      this.video.srcObject = this.stream;
      this.track = this.stream.getVideoTracks()[0];
      await new Promise((resolve, reject) => {
        // Use once: true to automatically remove the listener after it fires once
        this.video.addEventListener('loadedmetadata', resolve, { once: true });
        setTimeout(() => reject(new Error("Camera stream metadata load timeout.")), 5000); 
      });
    } catch (e) {
      log(`❌ Camera.init() failed: `, e.message);
      throw e;
    }
  },
  async flip() {
    log(`↘️Camera.flip()`);
    this.stream?.getTracks().forEach(t => t.stop());
    const side = this.track?.getSettings()?.facingMode || 'environment';
    this.stream = await navigator.mediaDevices.getUserMedia({
      video: { facingMode: { ideal: (side === 'environment') ? 'user' : 'environment'} }
    });
    this.video.srcObject = this.stream;
    this.track = this.stream.getVideoTracks()[0];
    await new Promise((resolve, reject) => {
      this.video.addEventListener('loadedmetadata', resolve, { once: true });
      setTimeout(() => reject(new Error("Camera flip timeout")), 5000);
    });
  },
  async torch() {
    log(`↘️Camera.torch()`);
    if (!this.track) return;
    const currentTorchState = this.track.getSettings().torch || false;
    await this.track.applyConstraints({
      advanced: [{ torch: !currentTorchState }]
    });
  },
  capture() {
    log(`↘️Camera.capture()`);
    if (!this.Ok()) {
      throw new Error("Camera not ready for capture.");
    }
    const canvas = document.createElement('canvas');
    const ctx = canvas.getContext('2d');
    canvas.width = this.video.videoWidth;
    canvas.height = this.video.videoHeight;
    ctx.drawImage(this.video, 0, 0);
    return canvas.toDataURL('image/jpeg');
  }
};
function cameraFlipButton() {
  log(`↘️cameraFlipButton`);
  try {Camera.flip();}
  catch(e) {log(`❌ cameraFlipButton: `, e.message);}
}
function cameraTorchButton() {
  log(`↘️cameraTorchButton`);
  try {Camera.torch();}
  catch(e) {log(`❌ cameraTorchButton: `, e.message);}
}
/***** UI OPERATIONS *****/
const uiModes = ['📸', '🖼️', '📊', '⚙️'];
let uiCurrent = 📸;
let uiWizardToggle = false;
function uiWizardToggler() {
  log(`↘️uiWizardToggler`);
  uiWizardToggle = !uiWizardToggle
  const btn = event.target;
  btn.textContent = (uiWizardToggle) ? '⚡' : '🐌';
}
function uiNext() {
    log(`↘️uiNext`);
    const index = uiModes.indexOf(uiCurrent);;
    uiCurrent = uiModes[(index + 1) % uiModes.length];
    uiRender();
    Wizard.kill();
}
function uiBack() {
    log(`↘️uiBack`);
    const index = uiModes.indexOf(uiCurrent);;
    uiCurrent = uiModes[(index + uiModes.length - 1) % uiModes.length];
    uiRender();
    Wizard.kill();
}
function uiRender() {
  log(`↘️uiRender`);
  document.querySelectorAll('.mode').forEach(m => m.classList.remove('active'));
  document.getElementById(`mode${uiCurrent}`).classList.add('active');
  document.getElementById('modeBtn').textContent = uiModes[uiCurrent];
  if (uiCurrent === 🖼️) {
    galleryLoad();
  }
}
/***** DYNAMIC SCHEMA SYSTEM *****/
// RESILIENT PARSING + DEFAULTS: schemaLoad should return default schema structure if all files fail, not empty string. schemaAnalyze should continue processing remaining files when individual metadataFromName calls fail, logging specific problematic filenames. SchemaLearn should never throw - always produce usable schema.
let schemaText = document.getElementById('schemaTextarea');
schemaText.value = '[]';
schemaText.addEventListener('input', ()=>{schemaDirty=true;});
let schemaDirty = false;
const SCHEMANAME = 'schema.json';
// NEEDS CHANGE: Should use fileRead function for consistency. Should not throw - return default schema object if all loads fail.
async function schemaLoad() {
  log(`↘️schemaLoad`);
  try {
    const file = await fileRead(SCHEMANAME);
    const content = await file.text();
    log(`📄 Loaded ${SCHEMANAME}`);
    return content;
  } 
  catch (e) {
    log(`❌ schemaLoad: `, ...arguments, e.message);
    return '[]';
  }
}
async function schemaLoadButton() {
  log(`↘️schemaLoadButton`);
  try {schemaText.value=await schemaLoad();schemaDirty=false;}
  catch(e) {log(e.message);}
}
async function schemaSave() {
  log(`↘️schemaSave`);
  await fileWrite(SCHEMANAME, schemaText.value, true);
  log(`💾 Schema saved to ${SCHEMANAME}`);
}
function schemaSaveButton() {
  log(`↘️schemaSaveButton`);
  try {schemaSave(); schemaDirty=false;}
  catch(e) {log(`❌ schemaSaveButton: `, ...arguments, e.message);}
}
function schemaMergeOptions(fieldoptions1, fieldoptions2) {
  log(`↘️schemaMergeOptions`);
  const allOptions = [
    ...(fieldoptions1 || []),
    ...(fieldoptions2 || [])
  ];
  const uniqueOptions = allOptions.filter((option, index, self) => self.indexOf(option) === index);
  return uniqueOptions;
}
const schemaHierarchy = { "single": 1, "multiple": 2, "text": 3 };
function schemaFieldUpgrade(currentType, newType) {
  return schemaHierarchy[newType] > schemaHierarchy[currentType] ? newType : currentType;
}
function analyzeFieldPattern(collection) {
  log(`↘️analyzeFieldPattern`);
  const items = collection.filter(Boolean);
  if (!items.length) {
    return { type: 'single', options: [] };
  }
  let hasText = false;
  let hasMultiple = false;
  const tokens = new Set();
  items.forEach(item => {
    const segments = item.split(',');
    const exceedsThreshold = segments.some(segment => segment.length > 26);
    if (exceedsThreshold) {
      hasText = true;
      tokens.add(item);
    } else if (segments.length > 1) {
      hasMultiple = true;
      segments.forEach(segment => tokens.add(segment));
    } else {
      tokens.add(item);
    }
  });
  const fieldType = hasText ? 'text' : 
                    hasMultiple ? 'multiple' : 
                    'single';
  const result = { type: fieldType };
  if (fieldType !== 'text') {
    result.options = [...tokens];
  }
  return result;
}
// wrap metadataFromName in try catch to keep analysis running even with one bad file
function schemaAnalyze(filenames) {
  log(`↘️schemaAnalyze`);
  const imageFiles = filenames.filter(f => 
    f.toLowerCase().match(/\.(jpg|jpeg|png|gif)$/)
  );
  const allMetadata = imageFiles
    .map(filename => metadataFromName(filename, null))
    .filter(metadata => metadata !== null);
  if (allMetadata.length === 0) return [];
  const maxFields = Math.max(...allMetadata.map(m => m.values.length));
  const fieldCollections = Array.from({ length: maxFields }, () => []);
  allMetadata.forEach(metadata => {
    metadata.values.forEach((valueArray, fieldIndex) => {
      if (valueArray.length > 0 && valueArray[0]) {
        fieldCollections[fieldIndex].push(valueArray[0]);
      }
    });
  });
  return fieldCollections.map((collection, index) => {
    const pattern = analyzeFieldPattern(collection);
    return {
      name: `field${index}`,
      type: pattern.type,
      ...(pattern.type !== 'text' && { options: pattern.options })
    };
  });
}
function schemaFuse(schema1, schema2) {
  log(`↘️schemaFuse`);
  if (!Array.isArray(schema1)) schema1 = [];
  if (!Array.isArray(schema2)) schema2 = [];
  const result = [];
  const maxLength = Math.max(schema1.length, schema2.length);
  for (let i = 0; i < maxLength; i++) {
    const field1 = schema1[i];
    const field2 = schema2[i];
    if (field1 && field2) {
      const mergedOptions = schemaMergeOptions(field1.options, field2.options);
      const finalType = schemaFieldUpgrade(field1.type, field2.type);
      result[i] = {
        name: field1.name || field2.name,
        type: finalType,
        ...(mergedOptions && { options: mergedOptions })
      };
    } else if (field1) {
      result[i] = { ...field1 };
    } else if (field2) {
      result[i] = { ...field2 };
    }
  }
  return result;
}
async function schemaLearn() {
  log(`↘️schemaLearn`);
  const fileList = await fileListAll();
  if (fileList.length === 0) {
    log('ℹ️ No files found for learning');
    return;
  }
  log(`🧠 Learning from ${fileList.length} files...`);
  const learnedSchema = schemaAnalyze(fileList);
  const currentSchema = schemaFromText(schemaText.value);
  const fusedSchema = schemaFuse(currentSchema, learnedSchema);
  schemaText.value = schemaToText(fusedSchema);
  schemaDirty = true;
  log('🎓 Schema learning complete');
}
function schemaLearnButton() {
  log(`↘️schemaLearnButton`);
  try {schemaLearn(); schemaDirty=true;}
  catch(e) {log(`❌ schemaLearnButton`, ...arguments, e.message);}
}
async function schemaInit() {
  log(`↘️schemaInit`);
  schemaText.value = await schemaLoad();
  schemaDirty = false;
  await schemaLearn();
}
function schemaFromText(text) {
  log(`↘️schemaFromText`);
  try {
    if (text == null) throw new Error(`Bit nothin`);
    const parsed = JSON.parse(text);
    return Array.isArray(parsed) ? parsed : []; // ensure array
  } 
  catch (e) {
    log(`❌ schemaFromText:`, ...arguments, e.message);
    return [];
  }
}
function schemaToText(schema) {
  log(`↘️schemaToText`);
  try {
    if (schema == null) throw new Error(`Bit nothin`);
    return JSON.stringify(schema, null, 2);
  } 
  catch (e) {
    log("❌schemaToText:", ...arguments, e.message);
    return '';
  }
}
function schemaGet() {
  log(`↘️schemaGet`);
  return schemaFromText(schemaText.value);
}
//<button onclick="schemaValidate()?log('✅ Schema is valid'):log('❌ Schema validation failed');">Learn</button>
/***** FILENAME OPERATIONS *****/
function metadataUUID() {
  log(`↘️metadataUUID`);
  const now = new Date();
  const year = now.getFullYear();                             //4
  const month = String(now.getMonth() + 1).padStart(2, '0');  //2
  const day = String(now.getDate()).padStart(2, '0');         //2
  const hour = String(now.getHours()).padStart(2, '0');       //2
  const min = String(now.getMinutes()).padStart(2, '0');      //2
  const sec = String(now.getSeconds()).padStart(2, '0');      //2
  const mil = String(now.getMilliseconds()).padStart(3, '0'); //3
  return `${year}${month}${day}${hour}${min}${sec}${mil}`;    //4+2+2+2+2+2+3 = 17
}
function metadataFromName(filename, schema=schemaGet()) {
  log(`↘️metadataFromName`);
  try {
    const parts = filename.split('_');
    const [uuid, ext] = parts.pop().split('.');
    if (!/^\d{17}$/.test(uuid)) {
      throw new Error("Bad UUID!");
    }
    const values = parts.map((part, index) => {
      // Default fallback handles full/incomplete/invalid schema
      if (schema?.[index]?.type === 'multiple') {
        return part.split(',');
      } else {  // 'single', 'text', or unknown/inferred
        return [part];
      }
    });
    return {
      data: { UUID: uuid, ext: ext },
      values: values
    };
  }
  catch (e) {
    log(`❌ metadataFromName: `, ...arguments, e.message);
    return null;
  }
}
// AI SLOP: NEEDS CHANGE: Should never fail - return minimal metadata with single empty field if schema loading fails
function metadataToName(metadata) {
  log(`↘️metadataToName`);
  const parts = metadata.values.map(valueArray => valueArray.join(','));
  return [...parts, metadata.data.UUID].join('_') + '.' + metadata.data.ext;
}
// Does this function even need to hold the same size? Leave values as empty array?

function metadataDefault() {
  log(`↘️metadataDefault`);
  return {
    data: { UUID: metadataUUID(), ext: "jpg" },
    values: schemaGet().map(() => [])
  };
}
/***** GALLERY *****/
let thumbnailCache = {};
const CACHENAME = 'cache.json';
async function galleryLoad() {
  log(`↘️galleryLoad`);
  try {
    const gallery = document.getElementById('gallery');
    gallery.innerHTML = '';
    await cacheLoad();
    for (const uuid in inventory) {
      await new Promise(resolve => requestAnimationFrame(resolve));
      if (uiCurrent !== 🖼️) return;
      const thumbnailDataURL = await galleryThumbnailGet(uuid);
      if (thumbnailDataURL) {
        const galleryImg = galleryImageElementCreate(uuid, thumbnailDataURL);
        gallery.appendChild(galleryImg);
      }
    }
    await cacheSave();
  } 
  catch (e) {
    log(`❌ galleryLoad: `, ...arguments, e.message);
  }
}
// prevent single image failure from breaking entire gallery
async function cacheLoad() {
  log(`↘️cacheLoad`);
  try {
    const cacheFile = await fileRead(CACHENAME);
    const cacheText = await cacheFile.text();
    const cacheData = JSON.parse(cacheText);
    for (const uuid in cacheData) {
      if (inventory[uuid]) {
        inventory[uuid].thumbnail = cacheData[uuid].thumbnail;
      }
    }
    return cacheData;
  } 
  catch (e) {
    log(`❌ cacheLoad: `, ...arguments, e.message);
    return {};
  }
}
async function cacheSave() {
  log(`↘️cameraSave`);
  try {
    const cacheData = {};
    for (const uuid in inventory) {
      if (inventory[uuid].thumbnail) {
        cacheData[uuid] = {
          thumbnail: inventory[uuid].thumbnail
        };
      }
    }
    const cacheJSON = JSON.stringify(cacheData);
    const blob = new Blob([cacheJSON], {type: 'application/json'});
    await fileWrite(CACHENAME, blob);
  } 
  catch (e) {
    log(`❌ cacheSave: `, ...arguments, e.message);
  }
}
// no error handling, let error fly
async function galleryThumbnailGet(uuid) {
  log(`↘️galleryThumbnailGet`);
  if (inventory[uuid] && inventory[uuid].thumbnail) {
    return inventory[uuid].thumbnail; //use existing thumbnail
  }
  const filename = await fileFind(uuid);
  if (!filename) {
    log(`galleryThumbnailGet: ${uuid}`);
    return null;
  }
  const file = await fileRead(filename);
  const thumbnailDataURL = await galleryThumbnailCreate(file);
  if (!inventory[uuid]) {
    inventory[uuid] = {};
  }
  inventory[uuid].thumbnail = thumbnailDataURL;
  return thumbnailDataURL;
}
async function galleryThumbnailCreate(file) {
  log(`↘️galleryThumbnailCreate`);
  const canvas = document.createElement('canvas');
  const ctx = canvas.getContext('2d');
  const img = new Image();
  const imgUrl = URL.createObjectURL(file);
  return new Promise((resolve) => {
    img.onload = () => {
      URL.revokeObjectURL(imgUrl);
      const size = 512;
      canvas.width = size;
      canvas.height = size;
      ctx.drawImage(img, 0, 0, size, size);
      const dataURL = canvas.toDataURL('image/jpeg', 0.8);
      resolve(dataURL);
    };
    img.src = imgUrl;
  });
}
function galleryImageElementCreate(uuid, thumbnailDataURL) {
  log(`↘️galleryImageElementCreate`);
  const galleryImg = document.createElement('img');
  galleryImg.src = thumbnailDataURL;
  galleryImg.className = 'gallery-item';
  galleryImg.loading = 'lazy';
  galleryImg.onclick = async () => {
    const filename = await fileFind(uuid);
    if (!filename) {
      log(`galleryImageElementCreate: ${uuid}`);
      return;
    }
    const file = await fileRead(filename);
    const originalUrl = URL.createObjectURL(file);
    const popup = window.open('', '_blank');
    popup.document.write(`<img src="${originalUrl}" style="max-width:100%;max-height:100%;">`);
  };
  return galleryImg;
}
// DISCUSSION: How to make components more robust?
// RESILIENT INPUT + NON-BLOCKING: Wizard failures return empty metadata rather than throwing. Input validation warns but doesn't prevent progression. Build functions handle missing schema gracefully.
/***** WIZARD OVERLAY *****/
const Wizard = (function() {
  let wizardOverlay;
  let wizardPanels;
  let navi;
  let schema;
  let values;
  let step;
  let resolve;
  let reject;
  function gridSizer(buttonCount, aspectRatio) {
    log(`↘️Wizard.gridSizer`);
    let bestRows = 1;
    let bestCols = buttonCount;
    let bestSkinny = 0.0;
    for (let rows = 1; rows <= buttonCount; rows++) {
      const cols = Math.ceil(buttonCount / rows);
      const width = aspectRatio / cols;
      const height = 1.0 / rows;
      const nowSkinny = Math.min(width, height);
      if (nowSkinny > bestSkinny) {
        bestSkinny = nowSkinny;
        bestRows = rows;
        bestCols = cols;
      }
    }
    return { rows: bestRows, cols: bestCols };
  }
  function refresh() {
    log(`↘️Wizard.refresh`);
    if (step < 0) {
      step = 0;
    } else if (step >= schema.length) {
      done();
      return;
    }
    const screens = Array.from(wizardPanels.children);
    screens.forEach((screen, index) => {
      if (index === step) {
        screen.classList.add('active');
      } else {
        screen.classList.remove('active');
      }
    });
  }
  function init() {
    log(`↘️Wizard.init`);
    wizardOverlay = document.getElementById('wizardOverlay');
    wizardPanels = document.getElementById('wizardPanels');
    wizardPanels.innerHTML = '';
    navi = document.getElementById('wizardNavi');
    navi.innerHTML = '';
    [
      {textContent: '⬅️', onclick: back},
      {textContent: '❌', onclick: kill},
      {textContent: '💾', onclick: done},
      {textContent: '➡️', onclick: next}
    ].forEach(config => {
      const button = document.createElement('button');
      Object.assign(button, config, { className: 'nav-button' });
      navi.appendChild(button);
    });
  }
  function build() {
    log(`↘️Wizard.build`);
    schema.forEach((_, index) => {
      const screen = document.createElement('div');
      screen.className = 'wizard-screen';
      buildScreenContent(screen, index);
      wizardPanels.appendChild(screen);
    });
  }
  // CHANGE?
  function buildScreenContent(screen, index) {
    log(`↘️Wizard.buildScreenContent`);
    const field = schema[index];
    if (field.type === 'text') {
      screen.style.cssText = 'grid-template-columns:1fr;grid-template-rows:1fr';
      const textarea = document.createElement('textarea');
      textarea.className = 'wizard-text';
      textarea.placeholder = `Enter ${field.name}`;
      textarea.value = values[index] ? values[index][0] : '';
      textarea.oninput = () => {
        values[index] = [textarea.value];
      };
      screen.appendChild(textarea);
    } else {
      const availableHeight = window.innerHeight - 80;
      const availableWidth = window.innerWidth;
      log(availableHeight);
      log(availableWidth);
      const grid = gridSizer(field.options.length, availableWidth/availableHeight);
      screen.style.cssText = `grid-template-columns:repeat(${grid.cols},1fr);grid-template-rows:repeat(${grid.rows},1fr)`;
      field.options.forEach((option, optionIndex) => {
        const input = document.createElement('input');
        input.value = option;
        input.className = 'wizard-input';
        input.name = `field-${index}`;
        const id = `field-${index}-${field.name}-${optionIndex}`;
        input.id = id;
        if (field.type === 'multiple') {
          input.type = 'checkbox';
          //if (values[index] && values[index].includes(option)) {input.checked = true;}
          input.onchange = () => {
            const checkedInputs = screen.querySelectorAll('input[type="checkbox"]:checked');
            values[index] = Array.from(checkedInputs).map(inp => inp.value);
          };
        } else if (field.type === 'single') {
          input.type = 'radio';
          //if (values[index] && values[index][0] === option) {input.checked = true;}
          input.onchange = () => {
            values[index] = [option];
            next();
          };
        }
        const button = document.createElement('button');
        button.type = 'button';
        button.className = 'wizard-label';
        button.textContent = option;
        button.onclick = () => {
          input.checked = !input.checked;
          input.onchange();
        };
        screen.appendChild(input);
        screen.appendChild(button);
      });
    }
  }
  function mergeMetadata(metadata1, metadata2) {
    log(`↘️mergeMetadata`);
    const mergedResult = { ...metadata1 , ...metadata2 };
    mergedResult.data = {...metadata1.data, ...metadata2.data};
    const isEmptyArray = x => Array.isArray(x) && x.length === 0;
    const maxLength = Math.max(metadata1.values?.length ?? 0, metadata2.values?.length ?? 0)
    mergedResult.values = Array.from({ length: maxLength }, (_, i) =>
      isEmptyArray(metadata2?.values[i]||[]) ?
        (isEmptyArray(metadata1?.values[i]||[]) ?
          []
          :
         metadata1.values[i])
        :
        metadata2.values[i]
    );
    return mergedResult;
  }
  function record() {
    log(`↘️Wizard.record`);
    values = [];
    step = 0;
    schema = schemaGet();
    init();
    build();
    wizardOverlay.style.display = 'flex';
    return new Promise((res, rej) => {
      resolve = res;
      reject = rej;
      refresh();
    });
  }
  // DISCUSSION: If the wizard fails for some reason, should it return an empty metadata? Should the calling function save the image with empty metadata?
  function kill() {
    log(`↘️Wizard.kill`);
    wizardOverlay.style.display = 'none';
    if (reject) {
      reject(new Error('WIZARD_KILLED'));
    }
  }
  function done() {
    log(`↘️Wizard.done`);
    wizardOverlay.style.display = 'none';
    if (resolve) {
      resolve(values);
    }
  }
  function back() {
    log(`↘️Wizard.back`);
    step--;
    refresh();
  }
  function next() {
    log(`↘️Wizard.next`);
    step++;
    refresh();
  }
  return { record, kill, done, back, next };
})();
log('script end');
  </script>
</body>
</html>
`````



`````real-command
Solve the problem in phases and start and end each phase with a set of 20 meditations. Before trying to solve the problem Try multiple times to elaborate in extensive detail on exactly what the problem is, making sure it is consistent with what's actually inside the prompt. There are some problems I'd like you to elaborate on AFTER DOING YOUR FUCKING MEDITATION EXERCISE, NOT BEFORE.
MEDITATE,
DEFINE PROBLEM,

The grand majority of this code is AI and human generated slop. It's hasn't actually been tested in a browser for 50 commits. Which is fine, it's more of a spec sheet than it is source code. Now I am testing out individual parts. I am now turning it into a fully functioning web app. We're going to keep running unit tests and making sure the components work working on each one until the whole thing works. You're not testing individual functions we are testing components. Now I'm testing this shit IRL.

Double check the navigation button system and the camera control button for any code anomalies or anything that might be off.

If you make code or solutions, provide it in snippets.

Use your meditation spacer tokens to figure out the answer.
`````



`````procedure
STEP 1: MEDITATE.
STEP 2: DISSECT QUESTION
STEP 3: MEDITATE
STEP 4: GAME PLAN THEN EXPLAINERS OR CODE SNIPPETS
`````
