# 📨 ha-telegram-tematizado

A Home Assistant script for sending **themed Telegram notifications** to specific **thread topics** within a Telegram group, with support for inline keyboards, parse modes, optional titles, **and photo/video attachments**.

Built and battle-tested in a real smart home with 80+ automations.

---

## ✨ Features

* 📂 Routes messages to **specific Telegram group topics** (threads) by name
* 📸 Supports **photo and video** attachments via local path or URL
* ⌨️ Supports **inline keyboards** for actionable notifications
* 🎨 Supports **MarkdownV2, HTML, Markdown, and plain text** formatting
* 🏷️ Optional **message title** (text messages only)
* 🔒 Configurable **SSL verification** (defaults to `false` for local `http://` sources)
* 🔄 Runs in **queued mode** — no message collisions under parallel automations
* 🧩 Simple to call from any automation, script or action

---

## 📋 Prerequisites

1. A **Telegram Bot** created via [@BotFather](https://t.me/BotFather)
2. The bot added to your Telegram **group with topic support enabled**
3. The [Telegram Bot integration](https://www.home-assistant.io/integrations/telegram_bot/) configured in Home Assistant
4. Your group **chat\_id** and each topic's **message\_thread\_id**

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

### 2. Find your chat\_id and thread IDs

**chat\_id:** Send a message to your group and check the event `telegram_bot.message_received` in **Developer Tools → Events**. The `chat_id` field is your group ID (negative number).

**message\_thread\_id:** Do the same but send the message inside a specific topic. The event will include `message_thread_id` for that topic.

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

### With inline keyboard

```yaml
action: script.telegram_tematizado
data:
  message: "🌡️ AC is on. What do you want to do?"
  tema: Home
  inline_keyboard:
    - "/Turn off AC"
    - "/Leave it on"
```

### With MarkdownV2 formatting

```yaml
action: script.telegram_tematizado
data:
  message: "*Washing machine* has finished\\."
  tema: Appliances
  parse_mode: markdownv2
```

### With title

```yaml
action: script.telegram_tematizado
data:
  title: "📊 Daily Summary"
  message: "Washing machine ran 2 times today."
  tema: Home
```

### With photo (local file)

```yaml
action: script.telegram_tematizado
data:
  photo: /config/www/images/snapshot.jpg
  message: "🖨️ Print started"
  tema: Printer
```

### With photo (local http URL)

```yaml
action: script.telegram_tematizado
data:
  photo: http://192.168.1.x/webcam/snapshot
  message: "📷 Camera snapshot"
  tema: Alerts
```

### With video

```yaml
action: script.telegram_tematizado
data:
  video: http://192.168.1.x/webcam/clip.mp4
  message: "🎥 Motion clip"
  tema: Alerts
```

### With SSL verification (HTTPS sources only)

```yaml
action: script.telegram_tematizado
data:
  photo: https://your-domain.com/snapshot.jpg
  message: "📸 External camera"
  tema: Alerts
  verify_ssl: true
```

> 💡 See [`examples/automations.yaml`](examples/automations.yaml) for more real-world examples including callback handling and media sending.

---

## ⚙️ Parameters

| Parameter | Required | Default | Description |
| --- | --- | --- | --- |
| `message` | ❌ | — | Message text or media caption |
| `tema` | ❌ | — | Topic name (maps to `message_thread_id`). If omitted, sends to group root |
| `title` | ❌ | — | Optional bold title (text messages only, ignored for photo/video) |
| `inline_keyboard` | ❌ | — | List of callback command strings |
| `parse_mode` | ❌ | `plain_text` | `plain_text`, `html`, `markdown`, `markdownv2` |
| `photo` | ❌ | — | Local path (`/config/www/...`) or URL of image to send |
| `video` | ❌ | — | Local path or URL of video to send |
| `verify_ssl` | ❌ | `false` | Set to `true` only for HTTPS sources with valid certificates |

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

* **MarkdownV2** requires escaping special characters (`.`, `-`, `!`, `(`, `)`, etc.) with a backslash. Use `plain_text` when in doubt.
* Inline keyboard callbacks must be handled in a separate automation listening to the `telegram_callback` event. See the example.
* The script runs in `queued` mode. Adjust `max` if you have very high notification volume.
* `verify_ssl` defaults to `false` — appropriate for local `http://` sources like cameras or Klipper. Set to `true` only for external HTTPS endpoints with valid certificates.
* When sending **photo or video**, `title` is ignored by the Telegram Bot API. Use `message` as the caption instead.

---

## 📋 Changelog

### v1.1.0
- Added `photo` field — send images from local path or URL
- Added `video` field — send videos from local path or URL
- Added `verify_ssl` field — boolean, defaults to `false`
- `message` is now optional (used as caption when sending media)
- Fully backward compatible — no changes needed in existing automations

### v1.0.0
- Initial release
- Text messages routed to Telegram group topics by name
- Fields: `message`, `tema`, `title`, `inline_keyboard`, `parse_mode`

---

*Made with ☕ and too many automations in Benidorm, Spain.*

## ☕ Buy me a coffee

If this saved you time and you feel like it, a coffee is always welcome — but absolutely no pressure.

[→ paypal.me/javilopez83](https://www.paypal.me/javilopez83)

---

## 📄 License

MIT — do whatever you want with it, attribution appreciated.

---

*Made with ☕ and too many automations in Benidorm, Spain.*

---