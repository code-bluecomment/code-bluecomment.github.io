---
layout: post
title: "Setting Up TypeScript: A Step-by-Step Journey"
date: 2020-06-05 10:50:00 +0200
comments: true
published: true
categories: ["blog", "archives"]
tags: ["Typescript", "install node", "node js install", "node package manager", "npm", "install typescript"]
permalink: /post/how-to-install-typescript
---

Imagine stepping into the world of web development and discovering a tool that can enhance JavaScript with powerful static typing, making your code more scalable and error-free. That’s where **TypeScript** comes in! But before we can wield this mighty tool, we need to prepare our system—starting with **Node.js**.

## What is Node.js and Why Do You Need It?

Think of **Node.js** as the bridge that allows JavaScript to transcend its browser-bound existence. Traditionally, JavaScript was confined to web browsers, but Node.js unlocked its potential for **server-side development**, opening doors to high-speed backend applications.
So, why does TypeScript need Node.js? Simple! **Node.js comes with npm (Node Package Manager)**, which helps in managing the packages we need—including TypeScript itself.

## Installing Node.js on Windows & macOS

To begin your journey, follow these steps to install Node.js:

### Download Node.js
- Visit the official [**Node.js** website](https://nodejs.org/en) and grab the latest **LTS (Long-Term Support)** version for your OS.

### Install Node.js
- Windows: Run the `.msi` installer and follow the simple wizard.
- macOS: Open the `.pkg` file and complete the setup process

### Verify Installation
- Open a terminal (Command Prompt for Windows, Terminal for macOS) and type: `node -v`
- If you see a version number, congratulations! Node.js is successfully installed.

### Check npm Version
- Run: `npm -v`
- This ensures npm is set up correctly, as we’ll need it next.

## Installing TypeScript Using npm

Now that Node.js is ready, let’s install TypeScript:

### Global Installation
- Type the following command in the terminal:
`npm install -g typescript`
- The `-g` flag ensures TypeScript is available throughout the system

### Verify Installation
- Check the TypeScript version with: `tsc -v`
- If you see a version number, TypeScript is now part of your toolkit!

### Project-Specific Installation (Optional)

- If you want TypeScript installed only within a specific project, navigate to the project folder and run:

`npm install --save-dev typescript`

- This keeps TypeScript as a development dependency.

## Common Issues & Their Solutions

Even seasoned developers stumble upon issues during installation. Here are the most common ones:

### 1. npm Command Not Found

✅ Solution: Ensure Node.js was installed correctly. If issues persist, reinstall Node.js and try again.

### 2. Permission Errors While Installing TypeScript

✅ Solution: Use the administrator mode in Windows or `sudo` in macOS:
`sudo npm install -g typescript`

### 3. 'tsc' Not Recognized

✅ Solution: Restart the terminal or add npm’s global path manually:
- Windows: Add `C:\Users\YourUsername\AppData\Roaming\npm` to your system’s PATH.
- macOS/Linux: Add `export PATH=$PATH:$(npm bin -g)` to your `.bashrc` or `.zshrc`.

### 4. Incorrect Node.js Version

✅ Solution: Update Node.js using:


```bash
npm install -g n
n latest
```

## Final Thoughts
Setting up TypeScript is like crafting the perfect foundation for a grand structure. With Node.js as the backbone and TypeScript adding clarity and robustness, developers can build more reliable, scalable projects.

Would love to hear from you—what’s been your experience installing TypeScript? Have you run into any surprises along the way?