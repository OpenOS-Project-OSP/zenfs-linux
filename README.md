# ZenFS Backends and Emulation of Linux

[![Built with Ona](https://ona.com/build-with-ona.svg)](https://app.ona.com/#https://github.com/OpenOS-Project-OSP/zenfs-linux) [![KDE Eco](https://img.shields.io/badge/KDE%20Eco-certified-brightgreen?logo=kde&logoColor=white&style=flat-square)](https://eco.kde.org/) [![Blue Angel](https://img.shields.io/badge/Blue%20Angel-DE--UZ%20215-0055a4?style=flat-square)](https://www.blauer-engel.de/en/certification/criteria)

> [!CAUTION]
> This is still being developed and the API is not completely stable yet.

This package serves as a best-effort emulation of Linux-specific behavior for ZenFS.

## Kernel modules

The foundational part of `@zenfs/linux` is it's kernel module API.

Modules are loaded with `init` and unloaded with `dispose`. Unlike Linux, both are
async, since a module may need to do something asynchronous before it is ready. While `init` runs,
the module is in the `init` state (Linux calls this `COMING`).

```ts
import { Module } from '@zenfs/linux';

const mod = new Module({
	name: 'example',
	version: '1.0.0',
	license: 'GPL',
	params: {
		debug: { value: false, changed: value => console.log('debug is now', value) },
	},
	init() {
		if (this.param('debug')) console.log('loading');
	},
	exit() {
		// clean up
	},
});

await mod.init();

await mod.dispose();
```

Modules can depend on each other using their `use` method, which takes a reference on the target and adds
a link in its `holders` directory. A module that is still referenced can't be unloaded without
forcing, which taints it with `F`.

## Filesystems

The following filesystems are (will be) provided:

- sysfs (`/sys`)
- debugfs (`/sys/kernel/debug`)
- configfs (`/sys/kernel/config`)
- procfs (`/proc`)
- devtmpfs (`/dev`)

## License

[@zenfs/linux](https://github.com/zen-fs/linux) is licensed under the [LGPL 3.0 or later](https://www.gnu.org/licenses/lgpl-3.0.html) and [COPYING.md](https://github.com/zen-fs/linux/blob/main/COPYING.md).
Copyright © James Prevett and other ZenFS contributors.
