# 🌬️ Mindful Breathing  
![Status](https://img.shields.io/badge/status-in_progress-yellow)

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=csswizardry&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)

A minimal, distraction‑free breathing timer for relaxation, focus, and mindfulness practice.

Mindful Breathing is a lightweight, single‑file HTML application that guides you through structured breathing cycles such as Relax, Box Breathing, and the 4‑7‑8 technique.  
It runs entirely in the browser, requires no backend, and works offline.

---

## 📌 Project Status

This project is currently **in progress**.  
New features, UI improvements, and refinements are actively being developed.

---

## 🔗 Live Demo

Try the app here:  
**[https://bielinskilukasz.github.io/mindful-breathing/](https://bielinskilukasz.github.io/mindful-breathing/)**

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

<img width="639" height="805" alt="image" src="https://github.com/user-attachments/assets/e492c052-1ad8-44f7-a865-865139bd0c2d" />

---

## 📦 Getting Started

1. **Clone the repository**

```bash
git clone https://github.com/BielinskiLukasz/mindful-breathing.git
cd mindful-breathing
```

2. **Open the app**

Simply open the HTML file in any modern browser:

```bash
open index.html
```

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

## 📝 TODO

Planned improvements and enhancements:

- [ ] Remove vibration feedback  
- [ ] Replace the sound checkbox with a speaker‑icon toggle button  
- [ ] Add support for screen rotation on mobile (dedicated landscape layout)  
- [ ] Add a “thought of the day” or inspirational quote (possibly via external API)  
- [ ] Hide the bottom information panel behind an **(i)** expandable button  
- [ ] Remove separators at the bottom of the page  
- [ ] Add a favicon (browser tab icon)  
- [ ] Improve the layout and spacing of the PAST SESSIONS section  
- [ ] Add a “Clear history” button for session logs  
- [ ] Refine the Start/Reset button styling for better visual consistency  
- [ ] Improve mobile spacing and responsive scaling of UI elements  
- [ ] Enhance readability of the status text (“Stopped 0:00”) 

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
