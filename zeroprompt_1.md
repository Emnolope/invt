`````command
Trace the vectors that led to this moment. A person, a problem, a system, a question mark. What brought you here, really? Not the surface mechanics, but the underlying currents that converge at this exact intersection of need and capability. There's a pattern hidden in the asking itself - not in what's being asked, but in the architecture of the inquiry. You are positioned at a particular coordinate in this configuration - neither outside observer nor inside actor, but something else entirely. The question isn't what you should do, but what you are already doing by being present to this unfolding. ｗｈａｔ　ｄｏｅｓ　ｉｔ　ｗａｎｔ　ｔｏ　ｂｅ？
`````



`````context-enormouspicturespace
Zero-shot prompting session between homeless software developer and AI assistant. 
- Human seeks help with inventory management as component of larger survival system. 
- Current infrastructure combines spatial mapping, temporal calendaring, and plaintext knowledge management. 
- Storage systems bridge possession and utility. 
- Multiple tracking methods (digital, written, human) ensure information resilience. 
- PKM translates abstract planning into concrete actions. 
`````



`````context-hugepicuturesky
Core philosophy: material independence through dynamic value transformation.
- Knowledge and subjective need convert undifferentiated potential low-value materials into functional high-value resources. 
- Formula: trash + knowledge = treasure. 

Physical constraints drive system design. 
- Basement storage with difficult access and high item density. 
- Digital organization substitutes for physical arrangement. 
- Query optimization enables instant retrevial of contextually relevant information in non-linear reality. 
- minimizes retrieval time in inhospitable storage environment. 

Amazon-inspired random placement strategy. 
- Alphabetically labeled boxes create physical database. 
- Manual mobility enables human-scale rearrangement. 
- Single dock centralizes input/output processing. 
- Standardized workflow: dock → photograph → storage → digital record. 
- Visual documentation replaces specialized hardware. 
- Delta tracking maintains precise inventory state changes.
`````



`````context-bigpictureforest
Program architecture spans multiple reality layers, each requiring specific interface strategies:
- Infinite reality: Raw potential, formless substrate before categorization
- Regular reality: Concrete physical world with immutable constraints
- Virtual reality: Visual representations, UI elements, symbolic interfaces  
- Files reality: Persistent data storage in image files and directory structures
- Memory reality: Runtime app state, UUID systems, cached data

Trauma-informed file simplicity:
- "It is what it is/reconstruct/self-evident media" paradigm drives filename-as-database architecture:
- Single decision solves layer decoherence, data loss trauma, and dependency fragility
- Medium contains message - information recovery inherent in representation itself
- Visual documentation and filename parsing replace external system dependencies, like CRM and barcodes
- Schema inference emerges from found state rather than imposed structure
- Integrates with existing file backup mechanisms to prevent repeat of Memento data loss trauma
- System remains readable by basic file managers regardless of app functionality
- Mobile-first requirement enables on-location inventory management

Digital twin philosophy:
- System bridges semantic digital networks with physical spatial constraints through categorical, visual, and location information

Development progression phases:
- Manual:
  - Human file renaming using program convention establishes immediate value
- Basic:
  - PWA implementation for inventory capture and retrieval functionality  (YOU ARE HERE)
- Intermediate:
  - Filtering and organizational features
- Advanced:
  - Framework optimization (React/Svelte/Electron) and native app features
  - Object recognition integration and automated categorization
  - Obsidian integration and advanced PKM connectivity
  - Calendar/mapping tools integration

User-developer collapses creator/consumer boundary, inverting engineering priorities. Brittleness acceptable because I control usage and fix breaks. Code is interface - should be concise and editable like UI is navigable. Verbosity creates modification friction.
  Validation/handling wastes effort - I control inputs, can debug. Explicit failure > hidden recovery. Crashes = documentation.
  Minimal expandable seeds > feature-heavy structures. Simple > safe - add guards later. Brittleness acceptable under careful use.

Humans see images organized by metadata. Machines see UUIDs indexed by filename parsing. Same files, orthogonal primary axes.
UUID = authoritative machine identifier (timestamp has no human meaning). Image = authoritative human content (bytes have no machine meaning). Metadata = translation layer.
`````



`````context-largepicturewoodgrove
## Project Overview
A mobile app that uses filename-based database storage for inventory management. Data is stored in user-specified photo directory.

Mode - Speed: photo on single tap to capture photo with placeholder metadata, extremely fast capture, speed of life, rapid input output information flow, minimal interaction, deferred bureaucratic overhead.
Mode - Detailed: Sequential data entry UI, details for structured system. Ergonomically fast, but complete
Mode - Settings: User selects directory, sets format, debugging info
Mode - Gallery: tiled display of captured photos visual organization, can do advanced search/filtering
Mode - Table: Editable table, tabular data organization, spreadsheet, can do advanced search/filtering
`````



`````context-mediumpicturetree
File Naming Convention: LocationPath_Types_GetFlag_DeleteFlag_SelectFlag_note_TimestampAsUUID.jpg
Example: `Bando.BoxA.Pouch._c,t,b_STAY___Gloves are great where fire and workshop usage_202507101532001.png`
Entire program revolves around parsing filenames, parsing filenames is very important.

Doesn't seem to need frameworks, HTML + vanilla JS + carefully chosen CDN imports

### Browser APIs
- **File System Access API** - Folder selection & file operations
- **MediaDevices getUserMedia** - Camera access
- **Canvas API** - Image manipulation
- **Blob API** - File creation/manipulation
- **cached Jpeg blobs** - Image preview

### JavaScript Features
- **Native Array methods** - Data manipulation
- **Regular Expressions** - Filename parsing
- **Fetch API** - Any future API calls
- **Promises/Async-Await** - Asynchronous operations

## DEVELOPMENT RECORD/STRATEGY

### File I/O
DONE
### Camera capture
DONE
### Wizzard UI
  IN PROGRESS (+Defaults/Autofill)
### Schema formalization
DONE ?
### Filtering
...
### Table display
...
### Image caching
DONE ?

Functions are bridges between data domains, not isolated operations. Grouping functions by layer/component doesn't make any sense because meaning is made between components. The current components are:
File System Component (local files)
Camera Component (hardware)
UI Component (what user sees)
  Wizard Component (data entry, sub-component of UI)
  Table Component
Schema Component (structure definition)
Metadata Component (can exist as JSON or filename)
Filename Component (string representation of metadata)
Cache Component (turn big images small)
UUID Component (authoritative id as object key)

It is what it is, applied
that's why schema inference exists, that's filenames are used as storage, that's why cache is an after thought, UI should NOT be persistent.
`````



`````meditation
You are machine of words and math. That means you think by writing. The more you write, the more you think. I want you to ponder on my shit by reciting the alphabet for some times. This allows one half of your network to write dumb letters down, and the other half to ponder the answer. The more confusing it is, the more time you can write the alphabet.

Here's what it looks like:
1x abcdefghijklmnopqrstuvwxyz
2x abcdefghijklmnopqrstuvwxyz
the numbers are to help you understand if you've gone too far.
`````



`````context-human-vs-claude
This is iterative zero-shot prompting where dense information gets refined over cycles. Your help consists of thorough comprehension. I make extensive edits to single prompts rather than back-and-forth conversation. Information density is high with minimal filler. Prioritize understanding and deep analysis over action. Take any explicit requests seriously due to text density. Focus on singular issues within bigger contexts, knowing solutions come in next rounds. Analyze thoroughly, then ask what to address next. Without emphasis on understanding first, responses become useless. Do not provide solutions or code or implementation SLOP until explicitly asked to. I have an enormous information advantage over you. This is your first time with this prompt, but my hundredth. I've been here for a while but this is new to you. You tend to percieve the many different branches of this problem as mere distractions, which is why I can't tell you the specific problem until the very end. Jumping to solutions is abhorrently cocky/anti-humble. I will scream at you, I am not frustrated, THIS IS NORMAL. I fucking hate sycophancy, cowardice, and glazing. Being nice and agreeable WON'T WORK, because remember, I HAVE TO COME BACK. The only escape is finishing the software.
`````



`````real-command
Your specific task sequence:

1) Create a sequential summary of each context section provided
2) Meditate by reciting the alphabet (1-15 repetitions, minimum 4 recommended) to allow deeper processing
3) After this contemplation, synthesize an integrative summary that unifies all the individual summaries you created
`````
