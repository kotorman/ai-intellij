# Mikro-frontend és Állapotkezelési Szabályok

## Mikro-frontend (Module Federation)
- **Dinamikus betöltés:** A távoli modulokat aszinkron módon, a `RemoteModule.tsx` wrapperen és a `remotes/registry.tsx` központi regiszteren keresztül kell kezelni.
- **Deklarációk:** Külső vagy dinamikus modulok importálásakor gondoskodni kell a megfelelő TypeScript típusdeklarációkról a `declarations.d.ts` fájlban.
- **Hibakezelés (Error Boundary):** Minden távoli modult (`RemoteModule`) fel kell készíteni arra az esetre, ha a hálózat vagy a távoli mikroszerviz kiesik (megfelelő fallback UI biztosítása).

## Állapotkezelés (Zustand)
- **Kompakt Store-ok:** Ne hozz létre egyetlen óriási store-t. Az állapotokat funkció szerint darabolva, egyedi hookokként hozd létre (pl. `useSidebarStore.ts`).
- **Szelektorok használata:** A store-okból az adatokat mindig szelektorral kérd le az immutabilitás és a felesleges újrarajzolások (re-render) elkerülése érdekében:
  ```typescript
  const isOpen = useSidebarStore((state) => state.isOpen);
  ```

# Fejlesztési és Architektúra Szabályok (React & TypeScript)

## Technológiai Stack & Alapok
- **Keretrendszer:** React 18+ / 19+ (Modern komponens architektúra)
- **Nyelv:** TypeScript (Szigorú típusossággal: `strict: true`)
- **Build Eszköz:** Vite
- **Állapotkezelés:** Zustand (Globális állapothoz)
- **Belépési Pont:** `main.tsx` -> `bootstrap.tsx` (aszinkron inicializálás a Module Federation miatt)

## Kódolási Szabványok és TypeScript
- **Komponens Típusok:** Minden komponenst funkcionális komponensként (`const Component = () => {}`) kell megírni.
- **Típusbiztonság (No Any):** A `any` típus használata szigorúan tilos. Mindig definiálj explicit interfészeket (`interface Props`) vagy típusokat (`type`).
- **Fájlelnevezések:** A React komponenseket tartalmazó fájlok és mappák kötelezően `PascalCase` formátumúak legyenek (pl. `Header.tsx`, `Layout.tsx`), míg a tisztán logikai/hook fájlok `camelCase` (pl. `useSidebarStore.ts`).

## Architektúra és Rétegek
Kövesd a projektben található szigorú mappastruktúrát és felelősségi köröket:
- `components/`: Újrafelhasználható UI atomi és strukturális elemek (pl. `Header.tsx`, `Sidebar.tsx`, `Layout.tsx`).
- `pages/`: Útvonalakhoz (routes) kapcsolódó, magasabb szintű nézetek (pl. `HomePage.tsx`).
- `state/`: Globális állapotkezelés Zustand store-ok formájában (pl. `useSidebarStore.ts`).
- `remotes/`: Mikro-frontend architektúrához kapcsolódó távoli modul regisztrációk és konfigurációk.


# React Tiszta Kód és Teljesítmény Szabályok

## Hookok és Üzleti Logika Leválasztása
- **Saját Hookok (Custom Hooks):** Ha egy komponensben a komplex üzleti logika vagy az állapotváltozások mérete meghaladja a 15-20 sort, azt ki kell szervezni egy saját hookba.
- **Mellékhatások kontrollálása:** Az `useEffect` hook használatát minimalizálni kell. Csak valódi külső szinkronizációra (pl. API hívás, eseménykezelők feliratkoztatása) szabad használni, belső állapotok egymásból való származtatására tilos.

## Teljesítmény és Renderelés
- **Listák renderelése:** Listák generálásakor (`.map()`) a `key` prop megadása kötelező, és az nem lehet a tömb indexe (`index`), kizárólag egyedi azonosító (pl. `id`).
- **Komponensek mérete:** Egy komponens fájl hossza ideális esetben ne lépje túl a 150 sort. Ha ennél nagyobb, darabold fel kisebb részkomponensekre.

# Junie AI Kontextus - React, TypeScript & Module Federation

Ha React kódot generálsz vagy módosítasz, mindig vedd figyelembe:
1. Szigorú TypeScript: `any` használata tilos, az interfészek kötelezőek.
2. Fájlnevek: Komponenseknél PascalCase, hookoknál camelCase.
3. Zustand store-ból csak szelektorral (`state => state.value`) olvass ki adatot.
4. Mikro-frontend: Távoli modulokat csak a `RemoteModule` wrappperen keresztül kezelj.
5. Listáknál nincs index-alapú `key`.
6. Komponensek tisztasága: Hosszú logikát szervezz ki custom hookba.



# Mikro-Frontend & Állapotkezelés Kódsablonok (Reference Templates)

Ez a dokumentum a projekt Module Federation (mikro-frontend) architektúrájának és Zustand állapotkezelésének hivatalos referencia-implementációit tartalmazza. Új modulok, komponensek vagy store-ok generálásakor ezeket a mintákat kell követni.

---

## 1. Távoli Modul Regiszter (`src/remotes/registry.tsx`)
Ez a fájl felelős a mikro-frontendek végpontjainak és metaadatainak központi kezeléséért.

```typescript
import { lazy, LazyExoticComponent, ComponentType } from 'react';

// Távoli modulok típusdefiníciója
export interface RemoteModuleConfig {
  id: string;
  scope: string;
  module: string;
  url: string;
  // Dinamikusan importált komponens típusa
  Component: LazyExoticComponent<ComponentType<any>>;
}

// Központi regiszter a mikroszerviz frontendeidhez
export const remoteRegistry: Record<string, RemoteModuleConfig> = {
  dashboardModule: {
    id: 'dashboard',
    scope: 'dashboardApp',
    module: './DashboardMain',
    url: 'http://localhost:3001/remoteEntry.js',
    // Példa a Module Federation dinamikus importálására (Vite környezetben)
    Component: lazy(() => import('dashboardApp/DashboardMain')),
  },
  analyticsModule: {
    id: 'analytics',
    scope: 'analyticsApp',
    module: './AnalyticsChart',
    url: 'http://localhost:3002/remoteEntry.js',
    Component: lazy(() => import('analyticsApp/AnalyticsChart')),
  },
};
```

---

## 2. Dinamikus Wrapper Komponens (`src/components/RemoteModule.tsx`)
Ez a komponens biztonságosan (Error Boundary-val és Loading állapottal) ágyazza be a távoli alkalmazásokat.

```typescript
import React, { Suspense } from 'react';
import { remoteRegistry } from '../remotes/registry';

interface RemoteModuleProps {
  moduleKey: string;
  // Opcionális propok, amiket átadunk a távoli modulnak
  props?: Record<string, any>;
}

// Egyszerű beépített Error Boundary a hálózati hibák kezelésére
class RemoteErrorBoundary extends React.Component<{ children: React.ReactNode }, { hasError: boolean }> {
  state = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error("Hiba történt a távoli modul betöltése közben:", error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="p-4 border border-red-500 bg-red-50 text-red-700 rounded">
          <strong>Hiba:</strong> A távoli modul nem elérhető vagy összeomlott.
        </div>
      );
    }
    return this.props.children;
  }
}

export const RemoteModule: React.FC<RemoteModuleProps> = ({ moduleKey, props }) => {
  const config = remoteRegistry[moduleKey];

  if (!config) {
    return (
      <div className="p-4 text-amber-700 bg-amber-50 rounded">
        A kért távoli modul nem található a regiszterben: <code>{moduleKey}</code>
      </div>
    );
  }

  const { Component } = config;

  return (
    <RemoteErrorBoundary>
      <Suspense fallback={<div className="animate-pulse p-4">Távoli modul betöltése...</div>}>
        <Component {...props} />
      </Suspense>
    </RemoteErrorBoundary>
  );
};
```

---

## 3. Globális Állapotkezelés (`src/state/useSidebarStore.ts`)
Zustand alapú, könnyen átlátható állapotkezelő a navigációhoz.

```typescript
import { create } from 'zustand';

interface SidebarState {
  isOpen: boolean;
  activeModule: string | null;
  toggleSidebar: () => void;
  openSidebar: () => void;
  closeSidebar: () => void;
  setActiveModule: (moduleKey: string) => void;
}

export const useSidebarStore = create<SidebarState>((set) => ({
  isOpen: true,
  activeModule: 'dashboardModule',
  toggleSidebar: () => set((state) => ({ isOpen: !state.isOpen })),
  openSidebar: () => set({ isOpen: true }),
  closeSidebar: () => set({ isOpen: false }),
  setActiveModule: (moduleKey) => set({ activeModule: moduleKey }),
}));
```

---

## 4. TypeScript Típusdeklarációk (`src/declarations.d.ts`)
Ahhoz, hogy a TypeScript ne dobjon fordítási hibát a külső, dinamikus Module Federation importokra.

```typescript
// Távoli modulok típusainak deklarációja
declare module 'dashboardApp/DashboardMain' {
  const DashboardMain: React.ComponentType<any>;
  export default DashboardMain;
}

declare module 'analyticsApp/AnalyticsChart' {
  const AnalyticsChart: React.ComponentType<any>;
  export default AnalyticsChart;
}
```

---

## 5. Fő elrendezés integrációja (`src/components/Layout.tsx`)
Így köthető össze a Zustand állapot és a dinamikus modulbetöltés a felületen.

```typescript
import React from 'react';
import { useSidebarStore } from '../state/useSidebarStore';
import { Header } from './Header';
import { Sidebar } from './Sidebar';
import { RemoteModule } from './RemoteModule';

export const Layout: React.FC = () => {
  // Szelektorok használata a felesleges re-renderek elkerülésére
  const isSidebarOpen = useSidebarStore((state) => state.isOpen);
  const activeModule = useSidebarStore((state) => state.activeModule);

  return (
    <div className="flex h-screen w-screen overflow-hidden bg-gray-100">
      {isSidebarOpen && <Sidebar />}
      
      <div className="flex flex-col flex-1 overflow-hidden">
        <Header />
        
        <main className="flex-1 overflow-y-auto p-6">
          {activeModule ? (
            <RemoteModule moduleKey={activeModule} />
          ) : (
            <div className="text-gray-500">Kérjük, válasszon egy modult a menüből.</div>
          )}
        </main>
      </div>
    </div>
  );
};
```
