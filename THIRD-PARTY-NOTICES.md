# Third-Party Notices

Rollingcats Transit Expansion (RTE) incorporates the following third-party software.
The notices below are reproduced as required by the respective licenses.

---

## Nemo's Transit Expansion (mtr-nte)

Portions of the rendering layer under `cn.jstxjf_.rte.gfx` and `cn.jstxjf_.rte.gfxext`
are derived from the `sowcer` and `sowcerext` packages of Nemo's Transit Expansion
(https://github.com/zbx1425/mtr-nte), used and modified under the MIT License.

**Code only.** No NTE models, textures or sounds are bundled with RTE. The D51 and
DK3 assets previously present were removed on 2026-08-18 — see `ASSET_AUDIT.md`.
NTE targets MTR 3.x and cannot be installed alongside MTR 4, so those vehicles are
simply not available in RTE.

```
MIT License

Copyright (c) 2022-present Zbx1425

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## Mozilla Rhino

Bundled and relocated to `cn.jstxjf_.rte.libs.rhino`. Licensed under the
Mozilla Public License 2.0 (https://www.mozilla.org/MPL/2.0/).
Source: https://github.com/mozilla/rhino

## GraalVM JavaScript

Bundled. Licensed under the Universal Permissive License v1.0.
Source: https://github.com/oracle/graaljs

## imgui-java

Bundled. Licensed under the MIT License.
Copyright (c) 2020 Aleksandr Popov.
Source: https://github.com/SpaiR/imgui-java

## Noto Sans CJK TC

Bundled as `assets/mtr/font/noto-sans-cjk-tc-medium.otf` (15.8 MB) for in-game
CJK text rendering. Licensed under the SIL Open Font License, Version 1.1.
Copyright 2014-2021 Adobe (https://adobe.com/), with Reserved Font Name 'Source'.
Source: https://github.com/notofonts/noto-cjk
License: https://scripts.sil.org/OFL

The SIL OFL requires that this notice accompany the font in any distribution.
The font is bundled unmodified and is not sold separately.
