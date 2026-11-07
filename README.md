## A project alatti AI folderek


### /general folder tartma

Az AGENTS.md file-ok a project típusától függő folderekbe vannak rendezve.

Jelen pillandatban két féle típus project folder van. Ezek:

**/spring-project**

ez SringBoot/java projecthez tartozik.

**/react-porject**

React/javascript/css projecthez tartozó agents.md file

Ha egyéb más típusú projethez is szükséges fájlokat felvenni akkor az szabadon megteheteő. Pl /vaadin-project, /racher-project, stb


## General folder

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

A Junie chat esetén nem adható meg command. Az AI Chat-ben AI Assitant estén a .aiassistant/commands alá kell tenni. Azonban az többi agent esetén a saját foldere alatt lévő commands könyvtárba kell 


### Rule-ok kezelése


### Skill-ek kezelése

A skill-ek telepíése az intelliJ-ben egyszerűbb git repóból telepíteni, ezért ha szükség van skill-re akkor érdemes egy git repót kialakítani erre a célra.
