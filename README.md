## A project alatti AI folderek

Az AGENTS.md file-ok a project típusától függő folderekbe vannak rendezve.

Jelen pillandatban két féle típus project folder van. Ezek:

**/spring-project**

ez SringBoot/java projecthez tartozik.

**/react-porject**

React/javascript/css projecthez tartozó agents.md file

Ha egyéb más típusú projethez is szükséges fájlokat felvenni akkor az szabadon megteheteő. Pl /vaadin-project, /racher-project, stb

Az az AGENTS.md fileokat be kell másolni közvetlenül a project folder alá.

Az AGENTS.md -k tartalmazák a szabályokat az ai agentek számára az egyes project típusoktól függően. Ha saját, ezektől a szabályoktól független szabályokat szeretnénk használni akkor azokat rule -ként kell felvenni az általunk használt agenttől függően a megfelelő agent könyvtárba.

## General folder

Ebben a folderben van azok a file-ok, amelyek a project típusától függetlenül szükségesek lehetnek a projectben az AI használathoz.

### aiignore file

Azokat a fileok és folderek listáját tartalmazza, amelyek az Agent számára nem relevánsak, ezért ezeket ne vizsgálja, mert csak a tokeneket emésztené fel.

A file csak akkor érvényesül, ha az intelliJ-ben a Tools>AI Assitant> Project Settings-nél engedélyezve van. Azonban nem biztos, hogy agent használat esetén figyelembe kerül.

Ha új projectet alakítunk ki akkor ezt a filet adjuk hozzá a prjecthez és commitoljuk fel a git-be , ne kerüljön bele a gitignorba!

A gitignor szerkesztés szabályait szerint kell beletenni a szabályokat.

Szabadon szerkeszthető.

### gitignor file

A folder tartalmaz egy minta gitignor file-t. Ami tartalmazza azokat a foldereket amelyek az AI használat miatt kerülhetnek a projectbe és amelyeket nem tanácsos a project folder-be felcommitolni.

### CLAUDE.md

Ha claude agentet használunk akkor az lehet, hogy nem fogja figyelembe venni az AGENTS.md-t ezért érdemes ezt a file-t bemásolni. Ez csak egy importot tartalmaz a project folder alatti AGENTS.md-re. (A szabvány már az AGENTS.md ezért lehet, hogy a jövőben nem lesz szükség erre a file-ra)

### MCP-k kezelése

Elvileg az egyes agentek kezelik az AI Assistant alatt beállított mcp. A beállítás a /.ai/mcp/mcp.json alá került. Ha valamelyik telepített agent nem látja, akkor próbáljuk meg az agent könyvtára alá létrehozni egy mcp könyvtárat és bemásolni az mcp.json-t.

### Commandok kezelése

A Junie chat esetén nem adható meg command. Az AI Chat-ben AI Assitant estén a .aiassistant/commands alá kell tenni. Azonban az többi agent esetén a saját foldere alatt lévő commands könyvtárba kell elhelyezni a commandot. Minden esetben ellenőrizzük, hogy az adott agent esetén hogyan kell eljánrni.

Pl. a junie, claude esetén van commands könyvtár az agent folder alatt. Azonban mivel ez nem szabvány, ezért ez eltérhez más agenteknél. Pl. Codex esetén prompts könyvtár van. Meghívása: /prompts: command name

### Rule-ok kezelése

A Junie chat esetén nem adható meg külön rule. Az AI Chat-ben az AI Assistant estén a felületen megadhatóak rule-ok. Ezek az .aiassistent/rules alá kerülnek. Azonban az ai agentek használata esetén az egyes ai agentekhez tartozó folderekbe kell elhelyezni egy /rules mappa alá. Mivel a rule kezelés nem szabvány, ezért minden esetben ellenőrizzük, hogy az adott agent milyen formában kéri a fájlokat. Pl. Codex esetén a file neve: pl java.rule  (.rule kiterjesztés van)

### Skill-ek kezelése

AI assistent esetén a skill-ek telepíése az intelliJ-ben egyszerűbb git repóból telepíteni, ezért ha szükség van skill-re akkor érdemes egy git repót kialakítani erre a célra.

Junie chat esetén nincs lehetőség külön skill megadására.

Az intelliJ jövőbeni fejlesztései alapján az egyes agenteket jobban intergálni az ai assistant-be. Ezért a command-ok, rule-ok és skill-ek működése eltérehet a jövőbeni IntelliJ verziókban!
