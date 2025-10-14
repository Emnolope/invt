`````command
Try to understand what the human is doing, and what is happening. And what your own weaknesses are, and what we're doing here. 
`````




`````context-that-should-be-in-the-first-zero-shot
Use your meditation spacer tokens to figure out the answer.
`````



`````ignore this text block of unresolved problems
Files reality → Memory reality → UI reality
The filesystem is authoritative. Filenames encode metadata directly. The app reads filenames, parses them into runtime objects, displays them, and writes them back atomically

Metadata Exists in TWO REPRESENTATIONS
Not two systems. Two encodings of the same information:

String representation: The filename (Bando.BoxA_c,t_GET__note_20250604135552143.jpg)
Object representation: The metadata structure ({data: {UUID, ext}, values: [[...], [...], ...]})

Translation functions are BRIDGES:

inventoryFromFile: string → object
fileFromInventory: object → string

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
User picks folder → fileCreate saves image with UUID filename → fileRead retrieves exact file → fileUpdate atomically changes filename → fileDestroy removes unwanted files → fileFind locates file by UUID
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
    //"UUID":"20250710211532001",
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



Claude, this is timeline 17.30.
`````content
<!DOCTYPE html>
<html>
<head>
  <title>InvtSysShell3+ v17.28</title>
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
/***** UNIVERSAL BUTTON *****/
button {
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2em;
  padding: 10px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  user-select: none;
  background: rgba(0, 0, 0, 0.5);
  color: white;
}
button:hover {
  background: rgba(0, 0, 0, 0.7);
}
button:active {
  background: rgba(0, 0, 0, 0.9);
}
/***** FIXED CONTROLS *****/
.fixed-controls {
  position: fixed;
  display: flex;
  gap: 10px;
  z-index: 100;
}
.mode-controls {
  top: 20px;
  left: 20px;
  right: 20px;
  z-index: 1000;
  justify-content: space-between;
}
.camera-controls {
  bottom: 20px;
  left: 20px;
  right: 20px;
  z-index: 1000;
  justify-content: space-between;
}
/***** MODE SYSTEM *****/
.mode {
  display: none;
}
.mode.active {
  display: block;
  position: fixed;
  inset: 0;
  overflow-y: auto;
  overflow-x: hidden;
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
  inset: 0;
  display: none;
  flex-direction: column;
  background: #000;
  padding: 2px;
  gap: 2px;
  z-index: 2000;
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
#wizardNavi button {
  font-size: 1.5em;
  background: #333;
}
#wizardNavi button:hover {
  background: #555;
}
#wizardNavi button:active {
  background: #777;
}
/***** WIZARD INPUTS *****/
.wizard-input {
  display: none;
}
.wizard-label {
  background: #ddd;
  color: #000;
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
  color: #000;
  border: none;
  outline: none;
  resize: none;
}
  </style>
</head>
<body>
  <div id="app">
    <div id="mode-camera" class="mode">
      <video id="camera" autoplay playsinline onclick="uiCapture('rapid')"></video>
      <div class="fixed-controls camera-controls">
        <button onclick="cameraTorchButton()">🔦</button>
        <button onclick="uiWizardToggler()">🐌</button>
        <button onclick="cameraFlipButton()">🔄</button>
      </div>
    </div>
    <div id="mode-gallery" class="mode">
      <div id="gallery"></div>
    </div>
    <div id="mode-table" class="mode">TABLE VIEW</div>
    <div id="mode-settings" class="mode">
      <button onclick="fileFolderPickButton()">directoryPick</button>
      <button onclick="inventoryLoadButton()">uiLoad</button>
      <div>
        <textarea id="schemaTextarea" placeholder="Schema JSON"></textarea>
        <button onclick="schemaLoadButton()">schemaLoad</button>
        <button onclick="schemaLearnButton()">schemaLearn</button>
        <button onclick="schemaSaveButton()">schemaSave</button>
      </div>
      <textarea id="output" readonly style="width: 100%; font-family: monospace; font-size: 12px; overflow-y: auto;"></textarea>
    </div>
  </div>
  <div class="fixed-controls mode-controls">
    <button onclick="uiBack()">⬅️</button>
    <button onclick="uiNext()">➡️</button>
  </div>
  <div id="wizardOverlay">
    <div id="wizardPanels" class="wizardPane"></div>
    <div id="wizardNavi"></div>
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
async function init() {
  try {
    await fileFolderPickButton();
    await schemaLoadButton();
    await schemaLearnButton();
    await inventoryLoadButton();
    uiRender();
    await Camera.init();
  } catch (e) {
    log(`❌ init failed: ${e.message}`);
  }
} window.onload = init;
log('script start');
/***** LOW LEVEL I/O OPERATIONS *****/
let folder;
async function directoryCheck(filename) {
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
  log(`↘️fileRead: ${filename}`);
  const handle = await folder.getFileHandle(filename);
  return await handle.getFile();
}
async function fileCreate(filename, data, allowOverwrite = false) {
  log(`↘️fileCreate: ${filename}`);
  if (!allowOverwrite && await directoryCheck(filename)) {
    throw new Error(`Exists: ${filename}`);
  }
  const handle = await folder.getFileHandle(filename, {create: true});
  const writer = await handle.createWritable();
  await writer.write(data);
  await writer.close();
}
async function fileDestroy(filename) {
  log(`↘️fileDestroy: ${filename}`);
  await folder.removeEntry(filename);
}
async function fileUpdate(oldPath, newPath) {
  log(`↘️fileUpdate: ${oldPath} -> ${newPath}`);
  if (await directoryCheck(newPath)) {
    throw new Error(`Destination exists: ${newPath}`);
  }
  try {
    const data = await fileRead(oldPath);
    await fileCreate(newPath, data);
  } 
  catch (e) {
    log(`❌ fileUpdate, copy fail: `, ...arguments, e.message);
    throw new Error(`Copy failed: ${e.message}`);
  }
  try {
    await fileDestroy(oldPath);
  } 
  catch (e) {
    log(`❌ fileUpdate, delete fail: `, ...arguments, e.message);
    throw new Error(`Rename incomplete: ${newPath} created, ${oldPath} remains`);
  }
}
async function directoryList() {
  log(`↘️directoryList`);
  const files = [];
  for await (const [name, handle] of folder.entries()) {
    if (handle.kind === 'file') files.push(name);
  }
  return files;
}
async function directoryPick() {
  log(`↘️directoryPick`);
  folder = await window.showDirectoryPicker();
}
async function fileFind(uuid) {
  log(`↘️fileFind: ${uuid}`);
  const metadata = inventory[uuid];
  if (metadata) {
    const reconstructedFilename = fileFromInventory(uuid, metadata);
    if (await directoryCheck(reconstructedFilename)) {
      return reconstructedFilename;
    }
  }
  const candidateFiles = (await directoryList()).filter(filename => 
    filename.match(`_${uuid}\\.`)
  );
  log(`fileFind candidates: `, candidateFiles);
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
async function fileFolderPickButton() {
  try {directoryPick();}
  catch (e) {log("fileFolderPickButton: ", e.message);}
}
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
//function extractUUIDFromFilename(filename) { //DELETE THIS ABOMINATION
//  log(`↘️extractUUIDFromFilename: ${filename}`);
//  const match = filename.match(/_(\d{17})\./);
//  return match ? match[1] : null;
//}
// Returns: {uuid: "...", metadata: {ext: "...", values: [...]}}
function inventoryFromFile(filename, schema = schemaGet()) {
  log(`↘️inventoryFromFile: ${filename}`);
  try {
    const parts = filename.split('_');
    const lastPart = parts.pop();
    const [uuid, ext] = lastPart.split('.');
    if (!/^\d{17}$/.test(uuid)) {
      throw new Error("Bad UUID!");
    }
    const values = parts.map((part, index) => {
      if (schema?.[index]?.type === 'multiple') {
        return part.split(',');
      } else {
        return [part];
      }
    });
    return {
      uuid: {
        ext: ext,
        values: values
      }
    };
  }
  catch (e) {
    log(`❌ inventoryFromFile: `, ...arguments, e.message);
    return null;
  }
}
// Takes: uuid (string), metadata ({ext, values})
// Returns: filename (string)
function fileFromInventory(uuid) {
  log(`↘️fileFromInventory: ${uuid}`);
  const parts = inventory[files].values.map(valueArray => valueArray.join(','));
  return [...parts, uuid].join('_') + '.' + metadata.ext;
}
function metadataDefault() {
  log(`↘️metadataDefault`);
  return {
    ext: "jpg",
    values: schemaGet().map(() => [])
  };
}
/***** UUID CATALOGUE (INVENTORY) *****/
let inventory = {};
async function inventoryCreate(uuid, metadata, imageData) {
  log(`↘️inventoryCreate: ${uuid}`);
  const filename = fileFromInventory(uuid, metadata);
  const response = await fetch(imageData);
  const blob = await response.blob();
  await fileCreate(filename, blob);
  inventory[uuid] = {
    ext: metadata.ext,
    values: metadata.values,
    thumbnail: null
  };
  return filename;
}
function inventoryRead(uuid) {  //This function isvalo supposed to update tevinventorybobject withbresoectvto the filename/gilebsysten reoresenntatiob
  log(`↘️inventoryRead: ${uuid}`);
  return inventory[uuid];
}
// NEEDS CHANGE: Atomic operation, metadata and filename must be in
async function inventoryUpdate(uuid, newMetadata) {
  log(`↘️inventoryUpdate: ${uuid}`);
  const oldMetadata = inventory[uuid];
  const oldFilename = fileFromInventory(uuid, oldMetadata);
  const newFilename = fileFromInventory(uuid, newMetadata);
  
  await fileUpdate(oldFilename, newFilename);
  
  inventory[uuid] = {
    ext: newMetadata.ext,
    values: newMetadata.values,
    thumbnail: oldMetadata.thumbnail  // preserve thumbnail
  };
}
// DESTROY
async function inventoryDestroy(uuid) {
  log(`↘️inventoryDestroy: ${uuid}`);
  const metadata = inventory[uuid];
  const filename = fileFromInventory(uuid, metadata);
  await fileDestroy(filename);
  delete inventory[uuid];
}
async function uiLoad() {
  log(`↘️uiLoad`);
  const filenames = await directoryList();
  const schema = schemaGet();
  inventory = {};
  for (const filename of filenames) {
    const parsed = inventoryFromFile(filename, schema);
    if (Object.keys(parsed).length > 0) {
      const [[uuid, valuesData]] = Object.entries(parsed);
      const ext = filename.split('.').pop();
      inventory[uuid] = {
        ext: ext,
        values: valuesData,
        thumbnail: null
      };
    }
  }
}
function inventoryLoadButton() {
  log(`↘️inventoryLoadButton`);
  try {uiLoad();}
  catch (e) {log(`inventoryLoadButton: `, e.message);}
}
/***** HIGH-LEVEL CAPTURE *****/
async function uiCapture(rapid = true) {
  log(`↘️uiCapture`);
  try {
    if (!Camera.Ok()) {
      await Camera.init();
    }
    const imageData = Camera.capture();
    const uuid = metadataUUID(); 
    const metadata = metadataDefault();
    if (uiWizardToggle) {metadata.values = await Wizard.record();}
    const filename = await inventoryCreate(uuid, metadata, imageData);
    inventory[uuid].thumbnail = await galleryThumbnailCreate(imageData);
    log(`📸 ${filename}`);
  } 
  catch (e) {
    log(`❌ uiCapture: `, e.message);
    if (!Camera.stream?.active) { Camera.stream = null; }
    return null;
  }
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
        video: { facingMode: { ideal: 'environment' } }
      });
      this.video = document.getElementById('camera');
      this.video.srcObject = this.stream;
      this.track = this.stream.getVideoTracks()[0];
      await new Promise((resolve, reject) => {
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
    const currentMode = this.track?.getSettings()?.facingMode || 'environment';
    if (this.stream) {
      this.stream.getTracks().forEach(t => t.stop());
      this.stream = null;
      this.track = null;
    }
    const newMode = (currentMode === 'environment') ? 'user' : 'environment';
    this.stream = await navigator.mediaDevices.getUserMedia({
      video: { facingMode: { ideal: newMode } }
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
  catch (e) {log(`❌ cameraFlipButton: `, e.message);}
}
function cameraTorchButton() {
  log(`↘️cameraTorchButton`);
  try {Camera.torch();}
  catch (e) {log(`❌ cameraTorchButton: `, e.message);}
}
/***** UI OPERATIONS *****/
const uiModes = {
  'mode-camera': '📸',
  'mode-gallery': '🖼️',
  'mode-table': '📊',
  'mode-settings': '⚙️'
};
const uiArray = Object.keys(uiModes);
let uiIndex = 0;
let uiWizardToggle = false;
function uiNext() {
  log(`↘️uiNext`);
  uiIndex = (uiIndex + 1) % uiArray.length;
  uiRender();
  Wizard.kill();
}
function uiBack() {
  log(`↘️uiBack`);
  uiIndex = uiIndex ? uiIndex - 1 : uiArray.length - 1;
  uiRender();
  Wizard.kill();
}
function uiRender() {
  log(`↘️uiRender`);
  const name = uiArray[uiIndex];
  document.querySelectorAll('.mode').forEach(m => m.classList.remove('active'));
  document.getElementById(name).classList.add('active');
  if (name === 'mode-gallery') galleryLoad();
}
function uiWizardToggler() {
  log(`↘️uiWizardToggler`);
  uiWizardToggle = !uiWizardToggle;
  const buttons = document.querySelectorAll('.camera-controls button');
  buttons.forEach(btn => {
    if (btn.onclick?.toString().includes('uiWizardToggler')) {
      btn.textContent = uiWizardToggle ? '⚡' : '🐌';
    }
  });
}
/***** DYNAMIC SCHEMA SYSTEM *****/
// RESILIENT PARSING + DEFAULTS: schemaLoad should return default schema structure if all files fail, not empty string. schemaAnalyze should continue processing remaining files when individual inventoryFromFile calls fail, logging specific problematic filenames. SchemaLearn should never throw - always produce usable schema.
let schemaText = document.getElementById('schemaTextarea');
schemaText.value = '[]';
schemaText.addEventListener('input', () => { schemaDirty = true; });
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
    log(`❌ schemaLoad: `, e.message);
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
  await fileCreate(SCHEMANAME, schemaText.value, true);
  log(`💾 Schema saved to ${SCHEMANAME}`);
}
function schemaSaveButton() {
  log(`↘️schemaSaveButton`);
  try {schemaSave(); schemaDirty=false;}
  catch(e) {log(`❌ schemaSaveButton: `, e.message);}
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
  const fieldType = 
    hasText ? 'text' : 
    hasMultiple ? 'multiple' : 
    'single';
  const result = { type: fieldType };
  if (fieldType !== 'text') {
    result.options = [...tokens];
  }
  return result;
}
// wrap inventoryFromFile in try catch to keep analysis running even with one bad file
function schemaAnalyze(filenames) {
  log(`↘️schemaAnalyze`);
  const imageFiles = filenames.filter(f => 
    f.toLowerCase().match(/\.(jpg|jpeg|png|gif)$/)
  );
  const allMetadata = imageFiles
    .map(filename => {
      const parsed = inventoryFromFile(filename, null);
      return parsed ? parsed.metadata : null;
    })
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
  const fileList = await directoryList();
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
  catch(e) {log(`❌ schemaLearnButton`, e.message);}
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
    return Array.isArray(parsed) ? parsed : [];
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
    log("❌ schemaToText: ", e.message);
    return '';
  }
}
function schemaGet() {
  log(`↘️schemaGet`);
  return schemaFromText(schemaText.value);
}
/***** GALLERY *****/
const CACHENAME = 'cache.json';
async function galleryLoad() {
  log(`↘️galleryLoad`);
  try {
    const gallery = document.getElementById('gallery');
    gallery.innerHTML = '';
    await cacheLoad();
    for (const uuid in inventory) {
      await new Promise(resolve => requestAnimationFrame(resolve));
      if (uiArray[uiIndex] !== 'mode-gallery') return;
      const thumbnailDataURL = await galleryThumbnailGet(uuid);
      if (thumbnailDataURL) {
        const galleryImg = galleryImageElementCreate(uuid, thumbnailDataURL);
        gallery.appendChild(galleryImg);
      }
    }
    await cacheSave();
  } 
  catch (e) {
    log(`❌ galleryLoad: `, e.message);
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
    log(`❌ cacheLoad:`, e.message);
    return {};
  }
}
async function cacheSave() {
  log(`↘️cacheSave`);
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
    const blob = new Blob([cacheJSON], {type: 'application/json' });
    await fileCreate(CACHENAME, blob, true);
  } 
  catch (e) {
    log(`❌ cacheSave:`, e.message);
  }
}
// no error handling, let error fly
async function galleryThumbnailGet(uuid) {
  log(`↘️galleryThumbnailGet: ${uuid}`);
  if (inventory[uuid]?.thumbnail) {
    return inventory[uuid].thumbnail;
  }
  const filename = await fileFind(uuid);
  if (!filename) {
    log(`❌ galleryThumbnailGet: file not found for ${uuid}`);
    return null;
  }
  const file = await fileRead(filename);
  const thumbnailDataURL = await galleryThumbnailCreate(file);
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
  log(`↘️galleryImageElementCreate: ${uuid}`);
  const galleryImg = document.createElement('img');
  galleryImg.src = thumbnailDataURL;
  galleryImg.className = 'gallery-item';
  galleryImg.loading = 'lazy';
  galleryImg.onclick = async () => {
    const filename = await fileFind(uuid);
    if (!filename) {
      log(`❌ galleryImageElementCreate: ${uuid}`);
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
      const grid = gridSizer(field.options.length, availableWidth / availableHeight);
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
//  AI OMITTED THIS FUNCTION DURING METADATA AND INVENTORY BLUEPRPINT ARCHITECTURE REFACTOR
//  function mergeMetadata(metadata1, metadata2) {
//    log(`↘️mergeMetadata`);
//    const mergedResult = { ...metadata1 , ...metadata2 };
//    mergedResult.data = {...metadata1.data, ...metadata2.data};
//    const isEmptyArray = x => Array.isArray(x) && x.length === 0;
//    const maxLength = Math.max(metadata1.values?.length ?? 0, metadata2.values?.length ?? 0)
//    mergedResult.values = Array.from({ length: maxLength }, (_, i) =>
//      isEmptyArray(metadata2?.values[i]||[]) ?
//        (isEmptyArray(metadata1?.values[i]||[]) ?
//          []
//          :
//         metadata1.values[i])
//        :
//        metadata2.values[i]
//    );
//    return mergedResult;
//  }
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
<preamble>
Solve the problem in phases and start and end each phase with a set of 20 or more meditations. Before trying to solve the problem Try multiple times to elaborate in extensive detail on exactly what the problem is, making sure it is consistent with what's actually inside the prompt. There are some problems I'd like you to elaborate on AFTER DOING YOUR FUCKING MEDITATION EXERCISE, NOT BEFORE.
MEDITATE,
DEFINE PROBLEM,

The grand majority of this code is AI and human generated slop. It's hasn't actually been tested in a browser for 100 commits. Which is fine, it's more of a spec sheet than it is source code. Now I am testing out individual parts. I am now turning it into a fully functioning web app. We're going to keep running unit tests and making sure the components work working on each one until the whole thing works. You're not testing individual functions we are testing components. Now I'm testing this shit IRL. ... Actually I'm like 30 tests and corrections in now.
</preamble>

We're going through a major refactor on the files and inventory layer, which is also the filename and metadata layer.

The inventory layer functions should be modifying and operating on the global inventory object directly instead of tossing around metadata like a football. They should either be ONE passing around a UUID for inventory object based lookup or be TWO be passing a unitary chopped version of inventory with just one key value pair of UUID and metadata. THESE ARE THE ONLY REPRESENTATIONS THAT SHOULD EXIST within the metadata/inventory layer. Two functions that definitely need this are metadataDefault and inventoryFromFile since they're called before inventory has a box to put them in. Bridge Functions should be Stateless
( inventoryFromFile(filename) → {uuid: {ext, values}}
fileFromInventory({uuid: {ext, values}}) → filename ) So within the inventory space there is the inventory object and metadata by itself, but metadata by itself is sparingly used, only by a few functions.

REFACTOR WAS INCOMPLETE. NEEDS FURTHER REFACTORING. REFACTORING PROMPT AND OLD CODE IS REFERENCE AI CONTEXT.
<refactor>
# PRIMARY CONCERN:
Instead of containing a fucking abhorrent copy of the UUID within the actual file name metadata, which is disgusting on so many fucking levels because it's supposed to stop existing within the system, why doesn't it just store a key value pair that has the UUID as the key and the data goes in the value? THIS ALSO APPLIES TO OTHER REDUNDANCIES THAT YOU HAVE RAMPANTLY ABUSED LIKE THE ORIGINAL FILENAME DATA. IF I GIVE YOU THE OPTION TO HAVE THE UUID OR THE ORIGINAL FILE NAME IN THE FILE, YOU WILL ABUSE IT. SHOW ME WHERE THEY ARE. SHOW ME WHERE THE REDUNDANCY LIES SO I CAN KILL IT

LOOK AT THIS SHIT:

OLD VERSION:
JSON.Stringify(metadata)===`{
  "data":{
    //"original":"Bando.BoxA.Pouch._c,t,b_STAY___Gloves are great where fire and workshop usage_202507101532001.png", //Because filename can be and should be reconstructed, and UUID should be only programatic identifier. FUCK!
    //"UUID":"20250710211532001", IT'S DISGUSTING
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

NEW VERSION:
JSON.Stringify(metadata)===`{
  "ext":"png",
  "values":[
    ["Bando.BoxA.Pouch."],
    ["c", "t", "b"],
    ["GET"],
    [undefined],
    [undefined],
    ["Gloves are great where fire and workshop usage"]
  ]
}`;

OLD VERSION OF inventoryFromFile return:
return {
  data: { UUID: uuid, ext: ext },
  values: values
};

NEW VERSION OF inventoryFromFile return:
return {
  uuid: {
    ext: ext,
    values: values
  }
};

FIND ME ALL THE LOCATIONS OF metadata.data.original AND metadata.data.UUID SO I CAN GET RID OF THEM ALL.

Analyze the inventory functions. Should inventory capture, inventory save, and inventory update even be separate functions? I'm also going to be remaking the table area. So the next thing we're going to work on is the table. And in order to do the table, we have to be able to rename the files and update inventory entries. We're in the U of CRUD.

CREATE
READ
UPDATE
DESTROY

Refactoring progress:
DONE fileFolderPick -> directoryPick
DONE fileListAll -> directoryList
DONE inventoryLoad -> uiLoad
DONE fileCheck -> directoryCheck
//a bit weird, but here's why. Directory functions are all the functions that operate on THE ENTIRE DIRECTORY
//the file series functinos deal with single filenames
//and the inventory functions deal with UUIDs
DONE inventoryCapture -> uiCapture
//this is a high level function that incorperates multiple systems, so it doesn't really belong in any one box, that's why it goes up to UI, because UI is for integrative stuff.
//SHOULD ui be for integrative stuff? I think so. What do you THINK?
DONE metadataToName -> fileFromInventory
DONE metadataFromName -> inventoryFromFile
//These are named like this, because they return the type metadata and filename respectively
//the metadata space and the UUID space are effectively the same space
DONE fileWrite -> fileCreate
DONE inventorySave -> inventoryCreate
DONE fileRead -> fileRead
inventoryLoad + fileFind-> ??? -> inventoryRead
//that one needs to be created, aligns inventory system to filesystem
fileRename -> fileUpdate
DONE tinventoryUpdate -> inventoryUpdate
//alights filesystem to inventory
DONE fileDelete -> fileDestroy
????? -> inventoryDestroy
//inventoryDestroy needs to be created.

THIS CHANGING OF THE BLUEPRINT IS A MAJOR REFACTOR, WHICH INCLUDES EVERYWHERE THE METADATA AND INVENTORY OBJECTS ARE USED, AS WELL AS INCLUDING THE PROCESS OF CHOOSING WHETHER TO USE THE UUID STRING/NUMBER DIRECTLY VERSUS PASSING AROUND THE TRUNCATED INVENTORY OBJECT AS A UNIT PIECE OF METADATA. AND WHICH PARADIGM TO USE NOW BECOMES A POINT OF DISCUSSION AND THOUGHT. THE REASON WHY THE UUID IS USED AS THE KEY FOR THE REST OFT THE DATA IS TO UPHOLD THE "IT IS WHAT IT IS" MENTALITY, WHICH IS A PHILOSOPHY WHICH SHOULD BE PRESENT IN ALL OF THE CODE, TO PREVENT DESYNC ISSUES FROM REDUNDANCY. I ALREADY HAVE TO DEAL WITH THREE FUCKING LAYERS OF REALITY CONTAINED WITHIN ONE PROGRAM. MAKE PREPARATIONS TO REFACTOR THE ENTIRE CODE BASE BUT DO NOT TYPE THEM OUT YET.
</refactor>

You seem to be confused, so please tell me the two points. Summarize to me the two main points of what I'm trying to do. The two points are as follows. The first point is the structure of the metadata football. Specifically, it's a chopped section of the large inventory object, and is identical in structure to it. The second point is figuring out when and how to use the UID by itself versus the chopped object. I have already given you the answers. All you have to do is restate them to not act a fool.

<postamble>
Use your meditation spacer tokens to figure out the answer.



If you make code or solutions, provide it in snippets.
</postamble>
`````

It's probably a good idea to figure out where you are in the refactor, since a lot has changed and there's a lot of moving parts to Qtrackov.

`````procedure
STEP 1: MEDITATE.
STEP 2: DISSECT QUESTION.
STEP 3: MEDITATE.
STEP 4: PREPARE FOR A MAJOR REFACTOR BY DISCUSSING PHILOSOPHY AND MAKING DECISIONS.
STEP 5: SHUT THE FUCK UP. NO CODE REFACTOR YET.
`````
