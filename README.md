# Email Alias Generator

A free, open-source tool to generate random email aliases for Gmail, Outlook, iCloud, ProtonMail, and other email providers that support plus addressing.

## Features

- **Random Alias Generation** — Generate unique email aliases using `username+random@domain` format
- **Configurable Length** — Set minimum suffix length (2-10 characters)
- **Bulk Generation** — Generate 10, 50, 500, or 5000 aliases at once
- **No Duplicates** — Guaranteed unique aliases using Set data structure
- **One-Click Copy** — Copy all generated aliases to clipboard
- **Theme Support** — Light, Dark, and Auto (system preference) modes
- **100% Private** — No data collection, storage, or transmission. Everything runs locally in your browser.

## Use Cases

- **Email Alias Generation** — Create unique aliases to organize your inbox and filter unwanted emails
- **Software Testing** — Generate bulk test email addresses for QA and development
- **Form Testing** — Populate email fields during website and app testing
- **Spam Prevention** — Use unique aliases to track and block spam sources
- **Privacy Protection** — Keep your primary email address private

## Tech Stack

- [Vue 3](https://vuejs.org/) — Progressive JavaScript framework
- [Vite](https://vitejs.dev/) — Next generation frontend tooling
- [TailwindCSS](https://tailwindcss.com/) — Utility-first CSS framework

## Getting Started

### Prerequisites

- Node.js 18+ 
- npm or yarn

### Installation

```bash
# Clone the repository
git clone https://github.com/qwegogo/email-alias-generator.git

# Navigate to project directory
cd email-alias-generator

# Install dependencies
npm install
```

### Development

```bash
# Start development server
npm run dev
```

Open `http://localhost:5173` in your browser.

### Build

```bash
# Build for production
npm run build
```

The built files will be in the `dist/` directory.

### Preview Production Build

```bash
npm run preview
```

## Deployment

### GitHub Pages

1. Update `vite.config.js` to set the correct base path:
   ```js
   export default defineConfig({
     base: '/your-repo-name/',
     plugins: [vue(), tailwindcss()],
   })
   ```

2. Build and deploy:
   ```bash
   npm run build
   ```

3. Push the `dist/` folder to the `gh-pages` branch or use GitHub Actions.

### Netlify / Vercel

1. Connect your GitHub repository
2. Set build command: `npm run build`
3. Set output directory: `dist`
4. Deploy!

## How It Works

Email aliasing (plus addressing) is a feature supported by many email providers:

- `bigcat@gmail.com` → `bigcat+shopping@gmail.com`
- `bigcat@gmail.com` → `bigcat+newsletter@gmail.com`

All emails sent to these aliases will be delivered to your original inbox. This allows you to:
- Filter and organize incoming emails
- Identify which services share your email
- Block specific aliases if they receive spam

## License

MIT License — feel free to use this project for any purpose.
