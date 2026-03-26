# 📨 ha-telegram-tematizado

A Home Assistant script for sending **themed Telegram notifications** to specific **thread topics** within a Telegram group, with support for inline keyboards, parse modes, and optional titles.

Built and battle-tested in a real smart home with 80+ automations.

---

## ✨ Features

- 📂 Routes messages to **specific Telegram group topics** (threads) by name
- ⌨️ Supports **inline keyboards** for actionable notifications
- 🎨 Supports **MarkdownV2, HTML, Markdown, and plain text** formatting
- 🏷️ Optional **message title**
- 🔄 Runs in **queued mode** — no message collisions under parallel automations
- 🧩 Simple to call from any automation, script or action

---

## 📋 Prerequisites

1. A **Telegram Bot** created via [@BotFather](https://t.me/BotFather)
2. The bot added to your Telegram **group with topic support enabled**
3. The [Telegram Bot integration](https://www.home-assistant.io/integrations/telegram_bot/) configured in Home Assistant
4. Your group **chat_id** and each topic's **message_thread_id**

---

## 🛠️ Installation

### 1. Add the Telegram Bot integration

In `configuration.yaml`:

```yaml
telegram_bot:
  - platform: polling
    api_key: "YOUR_BOT_TOKEN"
    allowed_chat_ids:
      - YOUR_CHAT_ID   # negative number for groups, e.g. -1001234567890
```

### 2. Find your chat_id and thread IDs

**chat_id:** Send a message to your group and check the event `telegram_bot.message_received` in **Developer Tools → Events**. The `chat_id` field is your group ID (negative number).

**message_thread_id:** Do the same but send the message inside a specific topic. The event will include `message_thread_id` for that topic.

### 3. Add the script to Home Assistant

**Option A — Via UI (recommended):**
1. Go to **Settings → Automations & Scenes → Scripts**
2. Click the three-dot menu → **Edit in YAML**
3. Paste the content of `script.yaml`
4. Replace `YOUR_CHAT_ID_HERE` and the thread IDs in `THREAD_TABLE`

**Option B — Via `scripts.yaml` file:**

If you use a separate `scripts.yaml`, paste the content directly (without the top-level key, starting from `alias:`).

In `configuration.yaml`, make sure you have:
```yaml
script: !include scripts.yaml
```

### 4. Customize topics

> ⚠️ **Important:** There are **two places** you need to update when adding or renaming topics — if they get out of sync the UI dropdown won't match the actual routing logic and messages may silently go to the wrong thread (or nowhere).

**4a. Update `THREAD_TABLE`** in the script variables — this is the actual routing map:

```yaml
THREAD_TABLE: >
  {{
    {
      "Alerts": 3,
      "Home": 5,
      "System": 7,
      "Appliances": 9
    }
  }}
```

**4b. Update the `selector` options** in `fields.tema` — this controls the dropdown in the HA UI:

```yaml
fields:
  tema:
    selector:
      select:
        options:
          - Alerts
          - Home
          - System
          - Appliances
```

Both lists must use **exactly the same names** (case-sensitive).

---

## 🚀 Usage

Call the script from any automation or action:

```yaml
action: script.telegram_tematizado
data:
  message: "💡 Motion detected in the living room!"
  tema: Alerts
```

### With inline keyboard:

```yaml
action: script.telegram_tematizado
data:
  message: "🌡️ AC is on. What do you want to do?"
  tema: Home
  inline_keyboard:
    - "/Turn off AC"
    - "/Leave it on"
```

### With MarkdownV2 formatting:

```yaml
action: script.telegram_tematizado
data:
  message: "*Washing machine* has finished\\."
  tema: Appliances
  parse_mode: markdownv2
```

### With title:

```yaml
action: script.telegram_tematizado
data:
  title: "📊 Daily Summary"
  message: "Washing machine ran 2 times today."
  tema: Home
```

> 💡 See [`examples/automations.yaml`](examples/automations.yaml) for more real-world examples including callback handling.

---

## ⚙️ Parameters

| Parameter | Required | Description |
|---|---|---|
| `message` | ✅ | The message text |
| `tema` | ❌ | Topic name (maps to `message_thread_id`). If omitted, sends to group root |
| `title` | ❌ | Optional bold title shown above the message |
| `inline_keyboard` | ❌ | List of callback command strings |
| `parse_mode` | ❌ | `plain_text` (default), `html`, `markdown`, `markdownv2` |

---

## 📁 File structure

```
ha-telegram-tematizado/
├── README.md
├── LICENSE
├── script.yaml              # The main script — copy this into HA
└── examples/
    └── automations.yaml     # Usage examples
```

---

## ⚠️ Important notes

- **MarkdownV2** requires escaping special characters (`.`, `-`, `!`, `(`, `)`, etc.) with a backslash. Use `plain_text` when in doubt.
- Inline keyboard callbacks must be handled in a separate automation listening to the `telegram_callback` event. See the example.
- The script runs in `queued` mode with `max: 10`. Adjust if you have very high notification volume.
- Topic names are **case-sensitive** — `"Alerts"` and `"alerts"` are treated as different keys.

---

## 🤝 Contributing

PRs and issues welcome. If you improve it or adapt it for your setup, share it back!

---

## ☕ Buy me a coffee

If this saved you time and you feel like it, a coffee is always welcome — but absolutely no pressure.

[→ paypal.me/javilopez83](https://www.paypal.me/javilopez83)

---

## 📄 License

MIT — do whatever you want with it, attribution appreciated.

---

*Made with ☕ and too many automations in Benidorm, Spain.*

---

## 📋 Prerequisites

1. A **Telegram Bot** created via [@BotFather](https://t.me/BotFather)
2. The bot added to your Telegram **group with topic support enabled**
3. The [Telegram Bot integration](https://www.home-assistant.io/integrations/telegram_bot/) configured in Home Assistant
4. Your group **chat_id** and each topic's **message_thread_id**

---

## 🛠️ Installation

### 1. Add the Telegram Bot integration

In `configuration.yaml`:

```yaml
telegram_bot:
  - platform: polling
    api_key: "YOUR_BOT_TOKEN"
    allowed_chat_ids:
      - YOUR_CHAT_ID   # negative number for groups, e.g. -1001234567890
```

### 2. Find your chat_id and thread IDs

**chat_id:** Send a message to your group and check the event `telegram_bot.message_received` in **Developer Tools → Events**. The `chat_id` field is your group ID (negative number).

**message_thread_id:** Do the same but send the message inside a specific topic. The event will include `message_thread_id` for that topic.

### 3. Add the script to Home Assistant

**Option A — Via UI (recommended):**
1. Go to **Settings → Automations & Scenes → Scripts**
2. Click the three-dot menu → **Edit in YAML**
3. Paste the content of `script.yaml`
4. Replace `YOUR_CHAT_ID_HERE` and the thread IDs in `THREAD_TABLE`

**Option B — Via `scripts.yaml` file:**

If you use a separate `scripts.yaml`, paste the content directly (without the top-level key, starting from `alias:`).

In `configuration.yaml`, make sure you have:
```yaml
script: !include scripts.yaml
```

### 4. Customize topics

Edit the `THREAD_TABLE` variable in the script to match your group's topics:

```yaml
THREAD_TABLE: >
  {{
    {
      "Alerts": 3,
      "Home": 5,
      "System": 7,
      "Appliances": 9
    }
  }}
```

And update the `selector` options in `fields.tema` to match.

---

## 🚀 Usage

Call the script from any automation or action:

```yaml
action: script.telegram_tematizado
data:
  message: "💡 Motion detected in the living room!"
  tema: Alerts
```

### With inline keyboard:

```yaml
action: script.telegram_tematizado
data:
  message: "🌡️ AC is on. What do you want to do?"
  tema: Home
  inline_keyboard:
    - "/Turn off AC"
    - "/Leave it on"
```

### With MarkdownV2 formatting:

```yaml
action: script.telegram_tematizado
data:
  message: "*Washing machine* has finished\\."
  tema: Appliances
  parse_mode: markdownv2
```

### With title:

```yaml
action: script.telegram_tematizado
data:
  title: "📊 Daily Summary"
  message: "Washing machine ran 2 times today."
  tema: Home
```

> 💡 See [`examples/automations.yaml`](examples/automations.yaml) for more real-world examples including callback handling.

---

## ⚙️ Parameters

| Parameter | Required | Description |
|---|---|---|
| `message` | ✅ | The message text |
| `tema` | ❌ | Topic name (maps to `message_thread_id`). If omitted, sends to group root |
| `title` | ❌ | Optional bold title shown above the message |
| `inline_keyboard` | ❌ | List of callback command strings |
| `parse_mode` | ❌ | `plain_text` (default), `html`, `markdown`, `markdownv2` |

---

## 📁 File structure

```
ha-telegram-tematizado/
├── README.md
├── LICENSE
├── script.yaml              # The main script — copy this into HA
└── examples/
    └── automations.yaml     # Usage examples
```

---

## ⚠️ Important notes

- **MarkdownV2** requires escaping special characters (`.`, `-`, `!`, `(`, `)`, etc.) with a backslash. Use `plain_text` when in doubt.
- Inline keyboard callbacks must be handled in a separate automation listening to the `telegram_callback` event. See the example.
- The script runs in `queued` mode with `max: 10`. Adjust if you have very high notification volume.

---

## 🤝 Contributing

PRs and issues welcome. If you improve it or adapt it for your setup, share it back!

---

## 📄 License

MIT — do whatever you want with it, attribution appreciated.

---

*Made with ☕ and too many automations in Benidorm, Spain.*
