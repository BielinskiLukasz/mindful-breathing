# 🌬️ Mindful Breathing

A minimal, distraction‑free breathing timer for relaxation, focus, and mindfulness practice.

Mindful Breathing is a lightweight, single‑file HTML application that guides you through structured breathing cycles such as Relax, Box Breathing, and the 4‑7‑8 technique.  
It runs entirely in the browser, requires no backend, and works offline.

---

## 🔗 Live Demo

Try the app here:  
**https://bielinskilukasz.github.io/mindful-breathing/**

---

## 🚀 Features

- Three breathing modes  
  - **Relax** — gentle inhale/exhale rhythm  
  - **Box Breathing** — balanced 4‑4‑4‑4 pattern  
  - **4‑7‑8** — scientifically backed calming technique  
- Visual circular timer with phase labels (Inhale / Hold / Exhale)  
- Configurable cycle count (default: 8 cycles ≈ 2 minutes)  
- Sound and vibration feedback (when supported by the device)  
- Keyboard shortcuts  
  - **Space** — start/stop  
  - **R** — reset  
  - **F** — fullscreen  
- Session history stored locally in the browser  
- Fully offline — no external dependencies  
- Single‑file HTML — easy to host, embed, or modify  

---

## 🖼️ Preview

*(Screenshot from the app — breathing timer with cycle indicator, mode selection, and session history.)*

---

## 📦 Getting Started

1. **Clone the repository**

```bash
git clone https://github.com/BielinskiLukasz/mindful-breathing.git
cd mindful-breathing
```

2. **Open the app**

Simply open the HTML file in any modern browser:

`bash
open index.html
`

Or double‑click it in your file explorer.

No build steps. No dependencies. No installation.

---

## 🧠 How It Works

Each breathing mode defines a sequence of timed phases:

- Inhale  
- Hold  
- Exhale  

The circular timer animates through these phases while the UI displays the current step and remaining seconds.  
Session results (timestamp + duration) are stored using `localStorage`.

---

## 🛠️ Customization

You can easily modify:

- Breathing patterns  
- Cycle count  
- Colors and animations  
- Sound/vibration behavior  

All logic is contained in a single HTML file for maximum simplicity.

---

## 📱 Compatibility

- Works on desktop and mobile browsers  
- Supports vibration on devices implementing the Vibration API  
- Supports sound cues when user interaction allows audio playback  

Tested on:

- Chrome  
- Firefox  
- Safari  
- Edge  

---

## 📄 License

This project is released under the **MIT License**.  
You are free to use, modify, and distribute it.

---

## 🤝 Contributing

Contributions, ideas, and improvements are welcome.  
Feel free to open an issue or submit a pull request.

---

## 💛 Acknowledgements

Created to support simple, accessible mindfulness practice — without ads, accounts, or distractions.
