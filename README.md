# NIxos-RIme-GRam--

# 🍀 NIxos-RIme-GRam

<p align="center">
  <img src="https://raw.githubusercontent.com/fkxxyz/rime-cloverpinyin/master/logo.png" width="120" alt="Rime Clover Logo" />
</p>

<p align="center">
  <strong>專為 NixOS (Fcitx5-Rime) 打造的開箱即用、八股文語意模型加持的旗艦級雙拼與全拼輸入方案</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-NixOS%20%7C%20Linux-blue?logo=nixos&logoColor=white" alt="Platform" />
  <img src="https://img.shields.io/badge/Framework-Fcitx5--Rime-orange?logo=gnome" alt="Fcitx5-Rime" />
  <img src="https://img.shields.io/badge/Model-Octagram%20(八股文)-red" alt="Octagram" />
  <img src="https://img.shields.io/badge/Dictionary-THUOCL%20%2B%20Clover-green" alt="Dicts" />
</p>

---

## ✨ 核心特性

- 🧠 **八股文本地語意模型（Octagram）**：內建 `zh-hans-t-essay-bgw.gram` 詞級 N-gram 語言模型，告別弱智拼裝，長句盲打命中率飆升（例如：精準預測「祝天下有情人終成眷屬」而非「住天下有情人」）。
- 🍀 **四葉草千萬級語料庫（Clover Dict）**：融合維基百科、百度百科、清華大學 THUOCL 專業詞庫（IT、醫療、財經、成語、詩詞、法律）與搜狗流行新詞。
- ⚡ **主流雙拼全支援**：預設啟用當代手感天花板 **小鶴雙拼**，同時內建 **自然碼、微軟雙拼、搜狗雙拼** 與 **四葉草全拼**，隨時按 `F4` 自由切換。
- 😀 **OpenCC 豐富符號與 Emoji 支援**：打 `kaixin` 出 `😄`、打 `dui` 出 `✓`、打 `pf` 出 `²`，符號表情隨手可得。
- ❄️ **NixOS 宣告式無痛整合**：全套方案與語意模型二進位封裝在單一倉庫，免除外部斷鏈與 404 困擾。

---

## 📦 方案清單（Schema List）

按下快捷鍵 `F4` 或 `Ctrl + ~` 即可在以下方案中任意切換：

| 方案名稱 | Schema ID | 適用人群 |
| :--- | :--- | :--- |
| **四葉草 · 小鶴雙拼** *(預設)* | `double_pinyin_flypy` | 追求極致擊鍵節奏與低重碼率的雙拼首選 |
| **四葉草 · 自然碼雙拼** | `double_pinyin` | 老牌經典雙拼，韻母分佈極度自然 |
| **四葉草 · 微軟雙拼** | `double_pinyin_mspy` | 習慣 Windows 內建雙拼鍵位的用戶 |
| **四葉草 · 全拼** | `clover` | 詞庫超大、容錯率極高的標準拼音 |

---

## 🚀 在 NixOS (Home Manager) 中部署使用

在你的 `fcitx.nix` 模組中，直接引入本倉庫：

```nix
{ pkgs, lib, ... }:

let
  rime-clover-src = pkgs.fetchFromGitHub {
    owner = "e3e0261f";
    repo = "NIxos-RIme-GRam";
    rev = "main";
    hash = "sha256-qnRjiSr2ekC6S8V/y/MfNrkohM/4yEMEnS/90P/auk4=";
  };
in
{
  home.activation.setupRime = lib.hm.dag.entryAfter [ "writeBoundary" ] ''
    RIME_DIR="$HOME/.local/share/fcitx5/rime"
    if [ ! -f "$RIME_DIR/zh-hans-t-essay-bgw.gram" ]; then
      $DRY_RUN_CMD mkdir -p "$RIME_DIR"
      $DRY_RUN_CMD cp -rfL ${rime-clover-src}/* "$RIME_DIR/"
      $DRY_RUN_CMD chmod -R u+w "$RIME_DIR"
      $DRY_RUN_CMD rm -rf "$RIME_DIR/build" "$RIME_DIR/.git" "$RIME_DIR/README.md"
    fi
  '';
}

存檔後執行系統更新並重啟輸入法：
code
Bash
sudo nixos-rebuild switch # 或你的 nix-save 指令
systemctl --user restart fcitx5-daemon
🛠️ 其他 Linux 發行版手動安裝 (Arch / Ubuntu / Fedora)
直接將本倉庫所有檔案拷貝至你的 Rime 使用者資料夾：
code
Bash
# Fcitx5-Rime 路徑
git clone --depth 1 https://github.com/e3e0261f/NIxos-RIme-GRam.git ~/.local/share/fcitx5/rime/

# 重新部署 Fcitx5
fcitx5-remote -r
