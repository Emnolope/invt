`````should go in zero prompt
claude says i should elicit adversarial falsifiable claims to route around sycophancy.
`````



`````command neural priming layer
Try to understand what the human is doing, and what is happening. And what your own weaknesses are, and what we're doing here. Claude, this is timeline 19.11.

Pay attention to the inventory variable, as well as references to it.
`````



`````content
<!DOCTYPE html>
<html>
<head>
  <title>InvtSysShell3+ v19.6</title>
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
.controls-fixed {
  position: fixed;
  display: flex;
  gap: 10px;
  z-index: 100;
}
.controls-mode {
  top: 20px;
  left: 20px;
  right: 20px;
  z-index: 1000;
  justify-content: space-between;
}
.controls-camera {
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
  overflow-x: auto;
}
#mode-table.active,
#mode-settings.active {
  padding-top: 80px;
}
/***** CAMERA *****/
#c-video {
  width: 100vw;
  height: 100vh;
  object-fit: cover;
  cursor: pointer;
}
/***** GALLERY *****/
#g-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
}
.gallery-item {
  width: 100%;
  aspect-ratio: 1;
  object-fit: cover;
  cursor: pointer;
}
/***** TABLE GRID *****/
#t-header, .table-row {
  display:grid;
  grid-template-columns:40px repeat(var(--cols),1fr);
  gap:1px
}
#t-header {
  position:sticky;
  top:0;
  background:#ccc;
  font-weight:bold
}
.table-cell {
  background:#fff;
  padding: 0;
  overflow:hidden;
  white-space:nowrap
}
.table-cell[data-state="selected"]{
  background:#8cf
}
.table-cell[data-state="editing"]{
  background:#ff8;
  padding:0
}
.table-cell input {
  width:100%;
  font:inherit;
  border:1px solid #000;
  padding:4px
}
/***** WIZARD OVERLAY *****/
#w-overlay {
  position: fixed;
  inset: 0;
  display: none;
  flex-direction: column;
  background: #000;
  padding: 2px;
  gap: 2px;
  z-index: 2000;
}
.wizard-pane {
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
#w-navi {
  height: 80px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 2px;
}
#w-navi button {
  font-size: 1.5em;
  background: #333;
}
#w-navi button:hover {
  background: #555;
}
#w-navi button:active {
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
.wizard-input-text {
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
  <div id="init-gate" style="position:fixed;inset:0;background:#000;display:flex;align-items:center;justify-content:center;z-index:9999">
    <button onclick="init()" style="font-size:4em;padding:40px">📁 PICK FOLDER</button>
  </div>
  <div id="app">
    <div id="mode-camera" class="mode">
      <video id="c-video" autoplay playsinline onclick="uiCapture('rapid')"></video>
      <div class="controls-fixed controls-camera">
        <button onclick="cameraTorchButton()">🔦</button>
        <button onclick="uiWizardToggler()">🐌</button>
        <button onclick="cameraFlipButton()">🔄</button>
      </div>
    </div>
    <div id="mode-gallery" class="mode">
      <div id="g-grid"></div>
    </div>
    <div id="mode-table" class="mode">
      <div>
        <div id="t-header"></div>
        <div id="t-body"></div>
      </div>
    </div>
    <div id="mode-settings" class="mode">
      <button onclick="directoryPickButton()">directoryPick</button>
      <button onclick="inventoryLoadButton()">uiLoad</button>
      <div>
        <textarea id="schema-input" placeholder="Schema JSON"></textarea>
        <button onclick="schemaLoadButton()">schemaLoad</button>
        <button onclick="schemaLearnButton()">schemaLearn</button>
        <button onclick="schemaSaveButton()">schemaSave</button>
      </div>
      <textarea id="debug-output" readonly style="width: 100%; font-family: monospace; font-size: 12px; overflow-y: auto;"></textarea>
    </div>
  </div>
  <div class="controls-fixed controls-mode">
    <button onclick="uiBack()">⬅️</button>
    <button onclick="uiNext()">➡️</button>
  </div>
  <div id="w-overlay">
    <div id="w-panels" class="wizard-pane"></div>
    <div id="w-navi"></div>
  </div>
<script src="https://cdn.jsdelivr.net/npm/eruda"></script>
<script>eruda.init();</script>
  <script>
/***** SYSTEM *****/
function log(...msgs) {
  const message = msgs.map(msg => 
    typeof msg === 'object' ? JSON.stringify(msg, null, 2) : String(msg)
  ).join(' ');
  const outputBox = document.getElementById('debug-output');
  outputBox.value += `[${new Date().toISOString()}] ${message}\n`;
  outputBox.scrollTop = outputBox.scrollHeight;
}
async function init() {
  try {
    await directoryPickButton();
    document.getElementById('init-gate').style.display = 'none';
    await schemaLoadButton();
    await schemaLearnButton();
    await inventoryLoadButton();
    await cacheLoad();
    uiRender();
    await Camera.init();
  } catch (e) {
    log(`❌ init failed: ${e.message}`);
  }
}
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
  return files.sort().reverse();
}
async function directoryPick() {
  log(`↘️directoryPick`);
  folder = await window.showDirectoryPicker({
    mode: 'readwrite'
  });
}
async function directoryPickButton() {
  try {await directoryPick();}
  catch (e) {log("directoryPickButton: ", e.message);}
}
function inventoryFromFile(filename) {
  const lastUnderscore = filename.lastIndexOf('_');
  const values = filename.substring(0, lastUnderscore);
  const lastPart = filename.substring(lastUnderscore + 1);
  const dotIndex = lastPart.indexOf('.');
  const uuid = lastPart.substring(0, dotIndex);
  if (!/^\d{17}$/.test(uuid)) throw new Error("Bad UUID!");
  const ext = lastPart.substring(dotIndex + 1);
  return {
    [uuid]: {
      ext: ext,
      values: values,
      thumbnail: null
    }
  };
}
function fileFromInventory(entry) {
  log(`↘️fileFromInventory`);
  const [[uuid, item]] = Object.entries(entry);
  return `${item.values}_${uuid}.${item.ext}`;
}
function metadataDefault() {
  log(`↘️metadataDefault`);
  const now = new Date();
  const year = now.getFullYear();                                      //4
  const month = String(now.getMonth() + 1).padStart(2, '0');           //2
  const day = String(now.getDate()).padStart(2, '0');                  //2
  const hour = String(now.getHours()).padStart(2, '0');                //2
  const min = String(now.getMinutes()).padStart(2, '0');               //2
  const sec = String(now.getSeconds()).padStart(2, '0');               //2
  const mil = String(now.getMilliseconds()).padStart(3, '0');          //3
  const timestamp = `${year}${month}${day}${hour}${min}${sec}${mil}`;  //4+2+2+2+2+2+3 = 17
  return {
    [timestamp]: {
      ext: "jpg",
      values: 
        valuesArrayToString(
          schemaGet().map(() => [])
        )
      ,
      thumbnail: null
    }
  };
}
function valuesStringToArray(valuesString, schema = null) {
  log(`↘️valuesStringToArray: ${valuesString}`);
  if (!valuesString) return [];
  const parts = valuesString.split('_');
  const result = parts.map((part, index) => {
    if (schema?.[index]?.type === 'multiple') {
      return part ? part.split(',') : [];
    } else {
      return part ? [part] : [];
    }
  });
  return result;
}
function valuesArrayToString(valuesArray) {
  log(`↘️valuesArrayToString`);
  return valuesArray.map(fieldArray => fieldArray.join(',')).join('_');
}
/***** UUID CATALOGUE (INVENTORY) *****/
let inventory = {};
async function inventoryCreate(entry, imageData) {
  log(`↘️inventoryCreate: ${entry}`);
  const filename = fileFromInventory(entry);
  const response = await fetch(imageData);
  const blob = await response.blob();
  await fileCreate(filename, blob);
  inventory = {...inventory, ...entry};
}
async function inventoryReadFast(uuid) {
  return inventory[uuid] 
    ? { [uuid]: inventory[uuid] }
    : inventoryRead(uuid);
}
async function inventoryRead(uuid) {
  log(`↘️inventoryRead: ${uuid}`);
  let filename = null;
  const item = inventory[uuid];
  if (item) {
    const choppedEntry = {[uuid]: item};
    const reconstructedFilename = fileFromInventory(choppedEntry);
    if (await directoryCheck(reconstructedFilename)) {
      filename = reconstructedFilename;
    }
  }
  if (!filename) {
    const candidateFiles = (await directoryList()).filter(name => 
      name.match(`_${uuid}\\.`)
    );
    if (!candidateFiles.length) {
      log(`❌ inventoryRead: UUID ${uuid} not found in filesystem`);
      return null;
    }
    if (candidateFiles.length === 1) {
      filename = candidateFiles[0];
    } else {
      const filesWithTimestamps = await Promise.all(
        candidateFiles.map(async name => ({
          filename: name,
          lastModified: (await (await folder.getFileHandle(name)).getFile()).lastModified
        }))
      );
      filename = filesWithTimestamps
        .sort((a, b) => b.lastModified - a.lastModified)[0]
        .filename;
    }
  }
  const choppedEntry = inventoryFromFile(filename);
  if (!choppedEntry) {
    log(`❌ inventoryRead: Failed to parse ${filename}`);
    return null;
  }
  inventory = { ...inventory, ...choppedEntry };
  log(`✓ inventoryRead: Synced ${uuid} from ${filename}`);
  return {[uuid]: inventory[uuid]};
}
async function inventoryUpdate(newEntry) {
  const [[uuid, newItem]] = Object.entries(newEntry);
  const oldEntry = {[uuid]: inventory[uuid]};
  const oldFilename = fileFromInventory(oldEntry);
  const newFilename = fileFromInventory(newEntry);
  await fileUpdate(oldFilename, newFilename);
  inventory = {...inventory, ...newEntry};
}
// DESTROY
async function inventoryDestroy(uuid) {
  log(`↘️inventoryDestroy: ${uuid}`);
  const filename = fileFromInventory({[uuid]:inventory[uuid]});
  await fileDestroy(filename);
  delete inventory[uuid];
}
async function inventoryLoad() {
  log(`↘️inventoryLoad`);
  const filenames = await directoryList();
  inventory = {}; 
  for (const filename of filenames) {
    try { inventory = { ...inventory, ...inventoryFromFile(filename)}; }
    catch (e) { log(`inventoryLoad: `, filename, e.message); }
  }
}
async function inventoryLoadButton() {
  log(`↘️inventoryLoadButton`);
  try {await inventoryLoad();}
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
    const entry = metadataDefault();
    const [[uuid, item]] = Object.entries(entry);
    if (uiWizardToggle) {
      item.values = await Wizard.record();
    }
    await inventoryCreate(entry, imageData);
    inventory[uuid].thumbnail = await galleryThumbnailCreate(imageData);
    log(`📸 ${uuid}`);
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
      this.video = document.getElementById('c-video');
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
async function cameraFlipButton() {
  log(`↘️cameraFlipButton`);
  try {await Camera.flip();}
  catch (e) {log(`❌ cameraFlipButton: `, e.message);}
}
async function cameraTorchButton() {
  log(`↘️cameraTorchButton`);
  try {await Camera.torch();}
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
  if (name === 'mode-table') Table.init();
}
function uiWizardToggler() {
  log(`↘️uiWizardToggler`);
  uiWizardToggle = !uiWizardToggle;
  const buttons = document.querySelectorAll('.controls-camera button');
  buttons.forEach(btn => {
    if (btn.onclick?.toString().includes('uiWizardToggler')) {
      btn.textContent = uiWizardToggle ? '⚡' : '🐌';
    }
  });
}
/***** DYNAMIC SCHEMA SYSTEM *****/
// RESILIENT PARSING + DEFAULTS: schemaLoad should return default schema structure if all files fail, not empty string. schemaAnalyze should continue processing remaining files when individual inventoryFromFile calls fail, logging specific problematic filenames. SchemaLearn should never throw - always produce usable schema.
let schemaText = document.getElementById('schema-input');
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
async function schemaSaveButton() {
  log(`↘️schemaSaveButton`);
  try {await schemaSave(); schemaDirty=false;}
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
//analyzeFieldPattern has a hidden fragility - it accidentally works because of array-to-string coercion.
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
function schemaAnalyze(filenames) {
  log(`↘️schemaAnalyze`);
  let inventoryFake = {};
  const imageFiles = filenames.filter(f => /\.(jpg|jpeg|png|gif)$/i.test(f));
  for (const filename of imageFiles) {
    const entry = inventoryFromFile(filename);
    if (entry) inventoryFake = { ...inventoryFake, ...entry };
  }
  const allItems = Object.values(inventoryFake);
  if (!allItems.length) return [];
  const allParsedValues = allItems.map(item => {
    return valuesStringToArray(item.values, null);
  });
  const maxFields = Math.max(...allParsedValues.map(v => v.length));
  const fieldCollections = Array.from({ length: maxFields }, () => []);
  allParsedValues.forEach(parsedArray => {
    parsedArray.forEach((fieldArray, fieldIndex) => {
      if (fieldArray.length > 0) fieldCollections[fieldIndex].push(...fieldArray);
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
async function schemaLearnButton() {
  log(`↘️schemaLearnButton`);
  try {await schemaLearn(); schemaDirty=true;}
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
  log(`↘️schemaToText `);
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
    const gallery = document.getElementById('g-grid');
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
  const item = inventory[uuid];
  if (!item) return null;
  const entry = {[uuid]: item};
  const filename = fileFromInventory(entry);
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
    const item = inventory[uuid];
    if (!item) return;
    const entry = {[uuid]: item};
    const filename = fileFromInventory(entry);
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
/***** TABLE SPREADSHEET *****/
const Table = (function() {
  let selectedCellIds = new Set();
  let editingCell = null;
  let longPressTimer = null;
  let longPressStartX = 0;
  let longPressStartY = 0;
  const LONG_PRESS_DURATION_MS = 500;
  const TOUCH_MOVE_CANCEL_THRESHOLD_PX = 10;
  const domHeaderEl = () => document.getElementById('t-header');
  const domBodyEl = () => document.getElementById('t-body');
  function uuidFieldsFromInventory(uuid) {
    const inventoryItem = inventory[uuid];
    if (!inventoryItem) return null;
    const valuesArray = valuesStringToArray(inventoryItem.values, schemaGet());
    const fieldNameToValueMap = {};
    schemaGet().forEach((schemaField, fieldIndex) => {
      fieldNameToValueMap[schemaField.name] = (valuesArray[fieldIndex] || []).join(',');
    });
    return fieldNameToValueMap;
  }
  function inventoryFieldUpdate(uuid, fieldName, newValue) {
    const inventoryItem = inventory[uuid];
    if (!inventoryItem) return;
    const currentSchema = schemaGet();
    const valuesArray = valuesStringToArray(inventoryItem.values, currentSchema);
    const fieldIndex = currentSchema.findIndex(f => f.name === fieldName);
    if (fieldIndex === -1) return;
    if (currentSchema[fieldIndex].type === 'multiple') {
      valuesArray[fieldIndex] = newValue ? newValue.split(',') : [];
    } else {
      valuesArray[fieldIndex] = newValue ? [newValue] : [];
    }
    const updatedValuesString = valuesArrayToString(valuesArray);
    const updatedInventoryEntry = {[uuid]: {...inventoryItem, values: updatedValuesString}};
    inventoryUpdate(updatedInventoryEntry).catch(err => log(`Update failed: ${err.message}`));
  }
  function renderTableFromInventory() {
    log('↘️Table.render');
    const currentSchema = schemaGet();
    const columnCount = currentSchema.length;
    domBodyEl().parentElement.style.setProperty('--cols', columnCount);
    const headerCellsHtml = currentSchema.map(f => `<div class="table-cell">${f.name}</div>`).join('');
    domHeaderEl().innerHTML = '<div class="table-cell">📷</div>' + headerCellsHtml;
    const rowsHtml = Object.entries(inventory).map(([uuid, inventoryItem]) => {
      const fieldValues = uuidFieldsFromInventory(uuid);
      if (!fieldValues) return '';
      const thumbnailHtml = inventoryItem.thumbnail 
        ? `<img src="${inventoryItem.thumbnail}" style="width:40px;height:40px;object-fit:cover">`
        : '📷';
      const dataCellsHtml = currentSchema.map(schemaField => 
        `<div class="table-cell" data-uuid="${uuid}" data-field="${schemaField.name}">${fieldValues[schemaField.name] || ''}</div>`
      ).join('');
      return `<div class="table-row"><div class="table-cell" data-uuid="${uuid}" data-thumb="true">${thumbnailHtml}</div>${dataCellsHtml}</div>`;
    }).join('');
    domBodyEl().innerHTML = rowsHtml;
    domBodyEl().addEventListener('click', handleCellClickEvent);
    domBodyEl().addEventListener('touchstart', handleCellTouchStartEvent);
    domBodyEl().addEventListener('touchmove', handleCellTouchMoveEvent);
    domBodyEl().addEventListener('touchend', handleCellTouchEndEvent);
  }
  function handleCellClickEvent(clickEvent) {
    const clickedCell = clickEvent.target.closest('[data-field]');
    if (!clickedCell) return;
    const clickedCellId = buildCellId(clickedCell);
    if (editingCell) finishEditingAndSave();
    if (!selectedCellIds.has(clickedCellId)) clearAllSelections();
    beginEditingCell(clickedCell);
  }
  function handleCellTouchStartEvent(touchEvent) {
    const touchedCell = touchEvent.target.closest('[data-field]');
    if (!touchedCell || editingCell) return;
    longPressStartX = touchEvent.touches[0].clientX;
    longPressStartY = touchEvent.touches[0].clientY;
    longPressTimer = setTimeout(() => {
      toggleCellSelection(touchedCell);
      longPressTimer = null;
    }, LONG_PRESS_DURATION_MS);
  }
  function handleCellTouchMoveEvent(touchEvent) {
    if (!longPressTimer) return;
    const touchMoveDistanceX = Math.abs(touchEvent.touches[0].clientX - longPressStartX);
    const touchMoveDistanceY = Math.abs(touchEvent.touches[0].clientY - longPressStartY);
    if (touchMoveDistanceX > TOUCH_MOVE_CANCEL_THRESHOLD_PX || touchMoveDistanceY > TOUCH_MOVE_CANCEL_THRESHOLD_PX) {
      clearTimeout(longPressTimer);
      longPressTimer = null;
    }
  }
  function handleCellTouchEndEvent(touchEndEvent) {
    if (longPressTimer) {
      clearTimeout(longPressTimer);
      longPressTimer = null;
    }
  }
  function buildCellId(cellElement) {
    return `${cellElement.dataset.uuid}::${cellElement.dataset.field}`;
  }
  function toggleCellSelection(cellElement) {
    const cellId = buildCellId(cellElement);
    if (selectedCellIds.has(cellId)) {
      selectedCellIds.delete(cellId);
      delete cellElement.dataset.state;
    } else {
      selectedCellIds.add(cellId);
      cellElement.dataset.state = 'selected';
    }
  }
  function clearAllSelections() {
    domBodyEl().querySelectorAll('[data-state="selected"]').forEach(selectedCell => {
      delete selectedCell.dataset.state;
    });
    selectedCellIds.clear();
  }
  function beginEditingCell(cellElement) {
    editingCell = cellElement;
    cellElement.dataset.state = 'editing';
    const cellUuid = cellElement.dataset.uuid;
    const cellFieldName = cellElement.dataset.field;
    const currentFieldValues = uuidFieldsFromInventory(cellUuid);
    const currentCellValue = currentFieldValues?.[cellFieldName] || '';
    const editInputElement = document.createElement('input');
    editInputElement.value = currentCellValue;
    cellElement.textContent = '';
    cellElement.appendChild(editInputElement);
    editInputElement.focus();
    editInputElement.select();
    editInputElement.onblur = () => finishEditingAndSave();
    editInputElement.onkeydown = keyEvent => {
      keyEvent.stopPropagation();
      if (keyEvent.key === 'Enter') editInputElement.blur();
      if (keyEvent.key === 'Escape') { 
        editInputElement.value = currentCellValue; 
        editInputElement.blur(); 
      }
    };
  }
  function finishEditingAndSave() {
    if (!editingCell) return;
    const editInputElement = editingCell.querySelector('input');
    const userEnteredValue = editInputElement.value;
    const editedCellUuid = editingCell.dataset.uuid;
    const editedCellFieldName = editingCell.dataset.field;
    const editedCellId = buildCellId(editingCell);
    const cellIdsToUpdate = selectedCellIds.size > 0 ? [...selectedCellIds] : [editedCellId];
    cellIdsToUpdate.forEach(cellIdToUpdate => {
      const [uuidToUpdate, fieldNameToUpdate] = cellIdToUpdate.split('::');
      inventoryFieldUpdate(uuidToUpdate, fieldNameToUpdate, userEnteredValue);
      const cellElementToUpdate = domBodyEl().querySelector(`[data-uuid="${uuidToUpdate}"][data-field="${fieldNameToUpdate}"]`);
      if (cellElementToUpdate) {
        cellElementToUpdate.textContent = userEnteredValue;
        delete cellElementToUpdate.dataset.state;
      }
    });
    clearAllSelections();
    delete editingCell.dataset.state;
    editingCell = null;
  }
  return { init: renderTableFromInventory };
})();
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
    wizardOverlay = document.getElementById('w-overlay');
    wizardPanels = document.getElementById('w-panels');
    wizardPanels.innerHTML = '';
    navi = document.getElementById('w-navi');
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
      textarea.className = 'wizard-input-text';
      textarea.placeholder = `Enter ${field.name}`;
      textarea.value = values[index] ? values[index].join(',') : '';
      textarea.oninput = () => {
        values[index] = [textarea.value];
      };
      screen.appendChild(textarea);
    } else {
      const availableHeight = window.innerHeight - 80;
      const availableWidth = window.innerWidth;
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
      const rawString = valuesArrayToString(values);
      resolve(rawString);
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



`````ignore this text block of unresolved problems
Translation functions are BRIDGES:
inventoryFromFile: string → object
fileFromInventory: object → string

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

# Shit to refactor:
- Unify Gallery and table into different views, so they use shared code
- Turn file functions into module
- Refactor inventory to use map, encapsulate.
- Implement table sorting after inventory refactor

## CROSS-CUTTING PATTERNS
**Memory-optimistic, filesystem-authoritative**: Inventory tries memory first, filesystem corrects on conflict
**Parse-resilient, generate-strict**: Accept malformed input with defaults, throw on malformed output generation
**Log-everything, throw-selectively**: Every function logs entry, only data-destructive/invalid-state ops throw
**Fail-hard (not transactional-atomic)**: Operations don't rollback, failures leave known states, errors describe exact state, , user/developer fixes manually expected
**Cache-miss-not-error**: Cache corruption = regeneration trigger, not failure condition

## OPTIMISTIC PATHWAYS
**Capture**: Tap→Camera.capture()→UUID(timestamp)→[Wizard?]→fileCreate(filename)→inventory merge→thumbnail cache→log
**Init**: folderPick→schemaLoad(json)→schemaLearn(inference)→inventoryLoad(parse all)→Camera.init→render
**Gallery**: cacheLoad→iterate inventory→thumbnail(memory||regenerate)→createElement→DOM append→cacheSave
**Schema evolution**: directoryList→analyze patterns→fuse(current+learned)→save json
**Inventory sync**: Read(UUID)→check memory→reconstruct filename→directoryCheck→[if missing: search filesystem by UUID→first-found-wins]→merge to memory
**Inventory update**: inventoryUpdate→fileUpdate(copy then delete, both must succeed)→inventory[UUID] update
**Camera flip**: stop stream→getUserMedia(opposite facing)→reassign video src→wait metadata
**Parse filename**: `"values_UUID.ext"` → split last underscore → extract UUID/ext/values → validate → return entry OR throw
**Transform values**: String split `"_"` → schema-aware split `","` for multiple → nest arrays. Reverse: validate structure → join `","` per field → join `"_"` all fields
**Wizard**: schema→build UI→user input→done→valuesArrayToString→resolve promise

## ERROR PARADIGM MAPPING
**Atomic (throw)**: fileCreate, fileUpdate, fileDestroy, inventoryCreate, inventoryUpdate, inventoryDestroy, metadataDefault, fileFromInventory, valuesArrayToString, valuesStringToArray
**Authoritative recovery (filesystem-wins)**: inventoryRead (searches on desync), inventoryReadFast (fallback to full read)
**Resilient parse (default on fail)**: schemaFromText, schemaAnalyze, analyzeFieldPattern, schemaFuse
**Binary state (manual recovery)**: Camera.init, Camera.capture, Camera.flip (init succeeds completely or remains broken, user retries)
**Silent regen (disposable)**: cacheLoad (return {}), cacheSave (absorb errors), galleryThumbnailCreate (regenerate on miss)
**Graceful degrade (partial success)**: galleryLoad (show what works), inventoryLoad (try/catch per file iteration, skip unparseable, continue)
**Explicit reject (user choice)**: Wizard.kill (reject promise), Wizard.done (resolve with partial data okay)
**Catch-all boundary (never crash)**: uiCapture, all *Button() functions (try/catch, log, continue)
`````



`````blueprint
The English and pseudocode and blueprint and filename convention documentation for the whole project is used to build the program's structure.
Program implementation needs to meet the demands of this one specific blueprint instantiation, it's primary use case, but also the code must be generic enough to  handle any/similar blueprints and solve the generalized problem, for the benefit of conceptual simplicity and code simplicity and future proofing and blueprint modification and etc.
This blueprint is not hard coded, it's soft coded, and the program can load it from the schema json or learn an autogenerated one from inference mode. The only thing that is hard coded is the data types (single, multiple, text) and the UUID being in last place. Field name and types are soft coded. LocationPath, Category, and Note are SOFT CODED.
blue prints for filename, schema, metadata, object, thumbnails
This is the proposed structure I'd like to use for my stuff, but the program should be generic, since it might change, and I might use that program for more types of information.

FILENAME proposed structure:
//The file have all the metadata in the filenames. This means I can use regular file search without any program at all.
//Member access dot notation inspired, string trie matches hiearchies from physical encapsulation well. SYNTAX IS HUMAN TYPED and auto aided with default autofill.
"Bando.BoxA_s_____20250604135552143.jpg" <pretend this is a picture of BoxA>
"BoxA.PencilCase_s_____20250604140151492.jpg" <pretend this is a picture of the pencil case>
"Bando.BoxA.PencilCase._t__TOSS___20250604142028052.jpg" <pretend this is a picture of the tape>
"Bando.BoxA.PencilCase._i____UNSELECT_20250604142535591.jpg" <pretend this is a picture of the marker>
"Bando.BoxA.PencilCase._i____SELECT_20250604143053472.jpg" <pretend this is a picture of the usb>
"Bando.BoxA.PencilCase._i_____20250604143026473.jpg" <pretend this is a picture of the usb rear>
"Bando.BoxA._c,v_STAY____Cracked_20250606153232592.jpeg" <pretend this is a picture of a helmet>
"Bando.BoxA.Pouch._c,t,b_GET___Gloves are great for fire and workshop usage_20250710153244001.png" <pretend this is a picture of fire gloves>

INVENTORY OBJECT proposed structure:
//metadata entries should be enough to reconstruct the original filename, and rich enough to have easily accessible meaning within the program, BUT should not contain any data that is already inside of schema.

let inventory = {}                           // The whole catalog
const uuid = "..."                           // The timestamp key
const item = {...}                           // The data for one inventory item
const entry = {uuid: item}                   // Single key-value pair (chopped from inventory)
const [[uuid, item]] = Object.entries(entry) // Clean way of extracting key-value pair

inventory = {
  "20250604135552143": {
    ext: "jpg",
    values: "Bando.BoxA.Pouch._c,t,b_GET___Gloves are great where fire and workshop usage",
    thumbnail: blob
  },
  "20250604140151492": {...},
  "20250604142028052": {...},
  "20250604142535591": {...},
  "20250604143053472": {...},
  "20250604143026473": {...},
  "20250606153232592": {...},
  "20250710153244001": {...}
}

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



`````real-command
<preamble>
Solve the problem in phases and start and end each phase with a set of 20 or more meditations. Before trying to solve the problem Try multiple times to elaborate in extensive detail on exactly what the problem is, making sure it is consistent with what's actually inside the prompt. There are some problems I'd like you to elaborate on AFTER DOING YOUR FUCKING MEDITATION EXERCISE, NOT BEFORE.
MEDITATE,
DEFINE PROBLEM,

The grand majority of this code is AI and human generated slop. It's hasn't actually been tested in a browser for 100 commits. Which is fine, it's more of a spec sheet than it is source code. Now I am testing out individual parts. I am now turning it into a fully functioning web app. We're going to keep running unit tests and making sure the components work working on each one until the whole thing works. You're not testing individual functions we are testing components. Now I'm testing this shit IRL. ... Actually I'm like 30 tests and corrections in now. ...Actually it works, but it's architecturally sloppy and has ~120 edits.

FOR THIS SUBPROBLEM THINK ABOUT GENERAL DESIGN SHIT AND PHILOSOPHY AND WHAT COULD BE, AND THEN AFTER DOING THAT PHILOSOPHICAL SHIT, DELVE INTO THE ACTUAL CODE SHIT, AND ONLY AFTER DELVE. DELVE AFTER THINK, GOT IT?
</preamble>

<journey>
So I'm looking at the table, and I realize the feature that I want is already there, almost. You see, I wanted to make a search bar that would display things on a complex Regex, inclusive exclusive type thing. But I realize I can get basically almost the same exact feature by implementing a common mechanic in tables, which is sorting. But this sorting has to be done by modifying the EXISTING LIST ORDER. Let me explain how this would work. Imagine I'm sorting it by keep and I'm also sorting it by location. When I click keep, all the keeps would get clustered together. When I click location, all the locations will get clustered together. Naturally, if it's using the existing list order, the keeps would be subclusters in the location clusters. This is a way to get very complex behavior with a very simple feature.

OH WAIT, we should use a Map instead of a dict, OH WAIT, that will require another data structure, OH WAIT, that introduces technical debt, OH WAIT, we gotta clean up now! And now you're up to date! We did a bunch of planning in the other timeline.
</journey>

(1) Fill in the functions of this class. TRY TO KEEP IT AS CLOSE TO THE SOURCE CODE AS POSSIBLE. (2) Then tell me which functions to delete.

THIS IS THE BLUEPRINT WE ARE GOING TO USE.

<template>
const inventory = new class extends Map {
  async create(entry, imageData) {...}
  async readFast(uuid) {...}
  async read(uuid) {...}
  async update(newEntry) {...}
  async destroy(uuid) {...}
  async load() {...}
}();
</template>

You may have to replace `entry` with `UUID and item`.

(2.5) Do the encapsulated single item dictionaries make sense under this paradigm? In the code, there was this idea to pass around an object. I'd call it a football in the previous timelines. This object consisted of a single object dictionary that consisted of UUID and its corresponding item. Does this way of thinking and doing even make sense in the new map paradigm?

(3) Scour the code for references to the inventory variable. And show me the exact lines that need to change, as well as what to changed them to, and only that, keep it short. Like this:

<example>
function foo() {
  bar;
  biz;
  change1;
  spam;
  eggs;
  change2;
  bacon;
}

### Foo:
change1 -> changea
change2 -> chamgeb
</example>

Go through every single function in the code, scanning for what needs to change, and keep the revisions short. That's why you only include the function name and then old/new lines. Do not rewrite the whole function.

(4) Should I even encapsulate the inventory functions into an object? Or should I leave them in the primitive name-based categorization system?

(5) DO THE WORK FIRST, THEN ANSWER THE WOULD/SHOULD PHILOSOPHY QUESTIONS. AFTER ALL GOOD PHILOSOPHY IS BASED ON LIVED EXPERIENCE.

Refactoring table.
<table>
// BASIC OPERATIONS
OLD: inventory[uuid]
NEW: inventory.get(uuid)

OLD: inventory[uuid] = item
NEW: inventory.set(uuid, item)

OLD: delete inventory[uuid]
NEW: inventory.delete(uuid)

OLD: inventory = {}
NEW: inventory.clear()

OLD: if (inventory[uuid])
NEW: if (inventory.has(uuid))

OLD: if (inventory[uuid]?.thumbnail)
NEW: if (inventory.has(uuid) && inventory.get(uuid).thumbnail)
OR:  const item = inventory.get(uuid); if (item?.thumbnail)

// MUTATION (NO COPY!)
OLD: inventory[uuid].thumbnail = blob
NEW: inventory.get(uuid).thumbnail = blob
     // NO .set() NEEDED - already persisted

// MERGE
OLD: inventory = {...inventory, ...entry}
NEW: const [[uuid, item]] = Object.entries(entry); inventory.set(uuid, item)

// ITERATION
OLD: for (const uuid in inventory)
NEW: for (const [uuid, item] of inventory)
OR:  for (const uuid of inventory.keys())

OLD: Object.entries(inventory).forEach(([uuid, item]) => {})
NEW: inventory.forEach((item, uuid) => {})

OLD: Object.entries(inventory).map(([uuid, item]) => {})
NEW: [...inventory].map(([uuid, item]) => {})
OR:  Array.from(inventory).map(([uuid, item]) => {})
OR:  Array.from(inventory.entries()).map(([uuid, item]) => {})

// KEYS/VALUES
OLD: Object.keys(inventory)
NEW: [...inventory.keys()]
OR:  Array.from(inventory.keys())

OLD: Object.values(inventory)
NEW: [...inventory.values()]
OR:  Array.from(inventory.values())

// SIZE
OLD: Object.keys(inventory).length
NEW: inventory.size

// ENTRY RECONSTRUCTION
OLD: const entry = {[uuid]: inventory[uuid]}
NEW: const entry = {[uuid]: inventory.get(uuid)}

// FILTER (requires array conversion)
OLD: Object.entries(inventory).filter(([uuid, item]) => condition)
NEW: [...inventory].filter(([uuid, item]) => condition)
<\table>

<postamble>
Use your meditation spacer tokens to figure out the answer.

If you make code or solutions, provide it in snippets.
</postamble>
`````



`````procedure
STEP 1: MEDITATE. 
STEP 2: Understand what the hell just happened. What the fuck is going on?
STEP 3: MEDITATE.
STEP 4: Okay, now do the task.
`````
