# Electron Reference

## Architecture

- **Main process**: Node.js -- manages windows, system access, app lifecycle
- **Renderer process**: Chromium -- renders UI (one per BrowserWindow)
- **Preload script**: Bridge with controlled API exposure via contextBridge

## Project Structure

```
src/
  main/index.ts          # Main process entry
  preload/index.ts       # Context bridge
  renderer/              # Frontend app (React/Vue/Svelte)
electron-builder.yml
```

## BrowserWindow

```ts
import { app, BrowserWindow } from 'electron';
import path from 'node:path';

function createWindow() {
  const win = new BrowserWindow({
    width: 1200, height: 800,
    webPreferences: {
      preload: path.join(__dirname, '../preload/index.js'),
      contextIsolation: true,   // REQUIRED
      nodeIntegration: false,   // REQUIRED
      sandbox: true,
    },
  });
  if (process.env.VITE_DEV_SERVER_URL) win.loadURL(process.env.VITE_DEV_SERVER_URL);
  else win.loadFile(path.join(__dirname, '../renderer/index.html'));
}

app.whenReady().then(createWindow);
app.on('window-all-closed', () => { if (process.platform !== 'darwin') app.quit(); });
```

## Preload Script (Context Bridge)

```ts
import { contextBridge, ipcRenderer } from 'electron';
contextBridge.exposeInMainWorld('electronAPI', {
  saveFile: (content: string) => ipcRenderer.send('file:save', content),
  openFile: () => ipcRenderer.invoke('dialog:openFile'),
  onMenuAction: (cb: (action: string) => void) => {
    const handler = (_e: any, action: string) => cb(action);
    ipcRenderer.on('menu:action', handler);
    return () => ipcRenderer.removeListener('menu:action', handler);
  },
});
```

## IPC Communication

```ts
import { ipcMain, dialog } from 'electron';
import fs from 'node:fs/promises';

// Two-way (invoke/handle)
ipcMain.handle('dialog:openFile', async () => {
  const { canceled, filePaths } = await dialog.showOpenDialog({
    filters: [{ name: 'Text', extensions: ['txt', 'md'] }],
  });
  if (canceled) return null;
  return fs.readFile(filePaths[0], 'utf-8');
});

// One-way (send/on)
ipcMain.on('file:save', async (_event, content: string) => {
  const { filePath } = await dialog.showSaveDialog({});
  if (filePath) await fs.writeFile(filePath, content, 'utf-8');
});
```

## Native Menus

```ts
import { Menu } from 'electron';
Menu.setApplicationMenu(Menu.buildFromTemplate([
  { label: 'File', submenu: [
    { label: 'New', accelerator: 'CmdOrCtrl+N', click: () => mainWindow?.webContents.send('menu:action', 'new') },
    { type: 'separator' },
    { role: 'quit' },
  ]},
  { role: 'editMenu' },
]));
```

## System Tray

```ts
import { Tray, Menu, nativeImage } from 'electron';
const icon = nativeImage.createFromPath('tray-icon.png').resize({ width: 16, height: 16 });
const tray = new Tray(icon);
tray.setContextMenu(Menu.buildFromTemplate([
  { label: 'Show', click: () => mainWindow?.show() },
  { label: 'Quit', click: () => app.quit() },
]));
```

## Auto Updates

```ts
import { autoUpdater } from 'electron-updater';
autoUpdater.checkForUpdatesAndNotify();
autoUpdater.on('update-downloaded', () => mainWindow?.webContents.send('update:ready'));
// Install: autoUpdater.quitAndInstall();
```

## Packaging (electron-builder)

```yaml
appId: com.example.myapp
productName: My App
mac:
  target: [dmg, zip]
  hardenedRuntime: true
win:
  target: [nsis]
linux:
  target: [AppImage, deb]
publish:
  provider: github
```

## Security Checklist

- `contextIsolation: true` -- never disable
- `nodeIntegration: false` -- never enable
- `sandbox: true` -- enable when possible
- Validate all IPC inputs in main process
- Never `shell.openExternal()` with unvalidated URLs
- Set CSP in renderer: `default-src 'self'; script-src 'self'`
- Do not load remote content without request filtering
