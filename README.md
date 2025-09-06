# Solar System Clock

Solar System Clock is an interactive web app that displays the current time on different planets in our solar system, along with fun facts and quirky anecdotes about each one. You can also create custom clocks for any time zone on Earth!

---

## 🌟Features

- Planet Clocks: See the current time on Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, and Neptune.
- Planet Pop-ups: Click any planet to learn its nickname, description, a funny fact, and a link to its Wikipedia page.
- Custom Clocks: Create your own clocks for any UTC offset.
- Customizable Settings: Switch between 12-hour and 24-hour formats, and choose from multiple clock backgrounds.
- Solar System Animation: Watch the planets rotate in real time.
- Local Storage: Your preferences and custom clocks are saved in your browser.
- Current version can be seen here <https://stwompy-solar-clock.netlify.app/>

---

## 🚀 Inspiration

This project was inspired by a Canadian friend who decided to live on Mars time, yes you read that right! Since a Martian day (or "sol") is about 24 hours and 40 minutes long, they adjusted their daily schedule to sync with the Red Planet. This app lets you do the same, or explore the time on any planet in our solar system.

---

## 🛠 Installation

- Clone this repository:
  git clone <https://github.com/LevasseurMickael/Solar-clock.git>
- Navigate to the project directory:
  cd solar-system-clock
- Install dependencies:
  npm install
- Run the app in development mode:
  npm run dev
- Open your browser to <http://localhost:5173>.
  
---

## 📂 Project Structure

solar-system-clock/\
├── public/                # Static assets (images, etc.)\
│   ├── line-no-number.png\
│   ├── no-number-point.png\
│   ├── number-point.png\
│   └── ramon-number-line.png\
├── src/\
│   ├── assets/             # Local assets (e.g., Svelte logo)\
│   ├── lib/\
│   │   └── Counter.svelte  # Example Svelte component\
│   ├── App.svelte          # Main app component\
│   └── main.js             # App entry point\
├── package.json            # npm dependencies and scripts\
└── README.md               # Project documentation\

---

## 🎮 Usage

- Check the planets you want to display in the menu.
- Click any planet in the animation to see its details in a pop-up.

### Creating Custom Clocks

- Go to the Settings tab.
- Fill out the form to create a new clock:
  - Clock Name: Give your clock a name.
  - Time Zone: Select the UTC offset (+ or -).
  - Color: Choose a color for the clock display.
- Click Create to add your custom clock.
  
### Settings

- Clock Convert: Toggle between 12-hour and 24-hour formats.
- Clock Watch: Select a background for your clocks.
  
---
  
## ⏳ Time Calculation

### Earth Time
  
  Earth time is displayed according to your local time zone.

### Mars Time

    Mars time is calculated using the Julian Date (JD) formula. The current Mars time is derived by converting the Julian Date to Mars Sol Date (MSD) and then to Mars Coordinated Time (MTC). This method ensures accuracy in representing a sol (Martian day).

### Other Planets

    For all other planets, the time is calculated using a simple ratio based on the length of their day relative to Earth's day. This provides an approximate but fun representation of what time it might be on those planets.

---

## 🛠 Customization

- Add New Planets: Edit the allPlanetsOfSolarSystem array in App.svelte to include more celestial bodies.
- Change Styles: Modify the CSS in the style section of App.svelte to tweak the app’s appearance.
  
  Happy time-traveling across the solar system! 🚀🌌
