---
description: Feladat végrehajtása a Graphify MCP tudásgráf elsődleges használatával
allowed-tools: mcp__graphify__*, Read, Edit, Write, Bash
---

# Feladat végrehajtása Graphify MCP segítségével

Hajtsd végre az alábbi feladatot a projekt **Graphify MCP** tudásgráfjára támaszkodva:

**Feladat:** $ARGUMENTS

## Kötelező működési szabályok

1. **Gráf-alapú felfedezés (Graph-First):** A kódbázis feltérképezéséhez, a függőségek megértéséhez és az érintett modulok azonosításához **elsőként mindig a Graphify MCP eszközeit használd** (pl. `query_graph`, `get_node`, `get_neighbors`, `get_community`, `god_nodes`, `shortest_path`, `graph_stats`). Kerüld a felesleges, teljes kódbázisra kiterjedő `grep` vagy vak fájlkereséseket, amíg a gráfból nem nyerted ki a pontos kontextust.
2. **Hatásvizsgálat (Blast Radius):** Mielőtt bármilyen kódot módosítanál, a Graphify MCP segítségével ellenőrizd a módosítani kívánt csomópontok (függvények, osztályok, modulok) szomszédait és hívási láncait, hogy elkerüld a rejtett regressziókat.
3. **Célzott fájlműveletek:** Csak azokat a fájlokat olvasd be teljes egészében (`Read`) és szerkeszd (`Edit`), amelyeket a Graphify MCP relevánsként azonosított.
4. **Hivatkozás a struktúrára:** A megoldás végén röviden foglald össze, mely gráf-csomópontok és függőségek alapján hoztad meg a döntéseidet.

