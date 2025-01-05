# LINE-Chrome-Extension-clone
Free messaging any time, anywhere
LINE reshapes communication around the globe, allowing you to enjoy free messaging wherever you find yourself. Install LINE now and stay close with your favorite people.

Show your emotions through expressive LINE stickers
- Choose from an extensive variety of LINE stickers that perfectly capture a wide range of emotions.

Large files? No problem!
- Send photos and even large files up to 1 GB quickly and easily. 

Stay closer by creating groups
- Keep in better touch with your family, school friends, and other people important to you. 
- Share exciting news and interesting conversations with multiple friends at once. 
- Easily organize your groups in a separate list.
----
Building a Chrome extension like the LINE extension with the features you described would require several components working together. This would involve integrating messaging, file sharing, group chats, and a variety of other features that allow the user to interact with contacts and media in an intuitive way. The features you've mentioned, such as "free messaging," "sticker support," and "file sharing," are core to the LINE experience.

Below is a conceptual outline of how you might approach building such an extension, along with sample code snippets to guide you through the process of building these features.
Chrome Extension with LINE-like Features
1. Manifest File (manifest.json)

This file defines the extension and its permissions.

{
  "manifest_version": 3,
  "name": "LINE Messaging Extension",
  "description": "Free messaging, stickers, and large file sharing.",
  "version": "1.0",
  "permissions": [
    "activeTab",
    "storage",
    "notifications"
  ],
  "background": {
    "service_worker": "background.js"
  },
  "action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "icons/icon16.png",
      "48": "icons/icon48.png",
      "128": "icons/icon128.png"
    }
  },
  "host_permissions": [
    "http://*/",
    "https://*/"
  ],
  "content_scripts": [
    {
      "matches": ["https://line.me/*", "chrome-extension://*/*"],
      "js": ["content.js"]
    }
  ]
}

2. Background Script (background.js)

The background script handles ongoing tasks, such as managing messages, storing data, and triggering notifications.

chrome.runtime.onInstalled.addListener(() => {
  console.log("LINE Messaging Extension Installed");

  // Example: Background event to notify a new message
  chrome.notifications.create({
    type: "basic",
    iconUrl: "icons/icon48.png",
    title: "New Message",
    message: "You have a new message in LINE!",
    priority: 2
  });
});

// Listen for messages from content script or popup
chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
  if (message.type === "sendMessage") {
    // Code to send message through LINE API
    console.log("Sending message:", message.data);
    sendResponse({ success: true });
  }
});

3. Popup HTML (popup.html)

This is the main UI that users interact with in the extension's popup. You will have sections to send messages, view stickers, and upload files.

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>LINE Messaging</title>
  <style>
    /* Basic styling for the popup */
    body {
      font-family: Arial, sans-serif;
      padding: 10px;
      width: 300px;
    }
    .chat-container {
      margin-top: 20px;
    }
    .message-box {
      width: 100%;
      height: 50px;
    }
    .send-btn {
      width: 100%;
      height: 30px;
      background-color: #00c300;
      color: white;
      border: none;
      cursor: pointer;
    }
    .sticker-container {
      display: flex;
      flex-wrap: wrap;
    }
    .sticker {
      width: 30px;
      height: 30px;
      margin: 5px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <div class="chat-container">
    <textarea class="message-box" placeholder="Type your message..."></textarea>
    <button class="send-btn">Send Message</button>
    
    <h3>Stickers</h3>
    <div class="sticker-container">
      <img class="sticker" src="stickers/happy.png" alt="Happy Sticker">
      <img class="sticker" src="stickers/sad.png" alt="Sad Sticker">
      <img class="sticker" src="stickers/angry.png" alt="Angry Sticker">
    </div>

    <h3>Send Large Files</h3>
    <input type="file" id="fileInput">
  </div>

  <script src="popup.js"></script>
</body>
</html>

4. Popup JavaScript (popup.js)

This script handles the user interaction, such as sending messages, choosing stickers, and uploading files.

document.querySelector(".send-btn").addEventListener("click", function() {
  const message = document.querySelector(".message-box").value;
  if (message.trim()) {
    // Send the message to the background script for processing
    chrome.runtime.sendMessage({ type: "sendMessage", data: message }, (response) => {
      if (response.success) {
        alert("Message sent successfully!");
      } else {
        alert("Failed to send message.");
      }
    });
  }
});

document.querySelectorAll(".sticker").forEach(sticker => {
  sticker.addEventListener("click", function() {
    // Add sticker to message or send as sticker
    console.log("Sticker chosen:", sticker.alt);
  });
});

document.querySelector("#fileInput").addEventListener("change", function(event) {
  const file = event.target.files[0];
  if (file) {
    if (file.size <= 1024 * 1024 * 1024) { // File is less than 1GB
      console.log("Uploading file:", file.name);
      // Handle file upload to LINE or another system
    } else {
      alert("File is too large! Please choose a file smaller than 1GB.");
    }
  }
});

5. Content Script (content.js)

This script interacts with the LINE web app, allowing the extension to scrape or push data if needed.

window.addEventListener("load", () => {
  console.log("LINE Web app loaded");

  // Example: Scraping message data from the LINE web app
  const messages = document.querySelectorAll('.message-text'); // Update the selector as needed
  messages.forEach((msg) => {
    console.log("Message:", msg.textContent);
  });
});

6. Icons and Stickers

You’ll also need icons for your extension and stickers for the UI. Here is an example folder structure:

/assets
  /stickers
    happy.png
    sad.png
    angry.png
  /icons
    icon16.png
    icon48.png
    icon128.png

7. Backend for Storing Data (Optional)

If you need a backend to store messages, users, or product data, you can use Firebase or Node.js to set up APIs. For example, using Firebase:

import firebase from "firebase/app";
import "firebase/firestore";

const firebaseConfig = {
  apiKey: "your-api-key",
  authDomain: "your-auth-domain",
  projectId: "your-project-id",
};

firebase.initializeApp(firebaseConfig);

const db = firebase.firestore();

db.collection("messages").add({
  message: "Hello World",
  timestamp: firebase.firestore.FieldValue.serverTimestamp(),
});

Key Features to Implement

    Free Messaging: Send messages in real-time between users.
    Stickers: Let users select stickers to send with messages.
    Large File Sharing: Support for file uploads (up to 1GB).
    Group Chats: Create and manage group chats.
    Notifications: Alert users of new messages and interactions.

Conclusion

This is a simple setup for a Chrome extension that replicates some of the core features of LINE. The key elements include a messaging system, sticker support, and file sharing. Depending on your needs, you can extend the system by adding more advanced features like user authentication, real-time messaging (using WebSockets), and a backend database to store user and message data securely.

Make sure to thoroughly test the extension, especially with handling large files, managing multiple messages, and ensuring that stickers are rendered properly. Also, consider implementing robust error handling and validation to ensure the best user experience.

====
Scraping data from the LINE Chrome Web Extension is a more complex task because the data you're looking for is behind a Chrome extension, and accessing it is restricted for privacy and security reasons. Chrome extensions are sandboxed from other web pages, and they often use internal APIs to interact with the browser, which makes direct scraping difficult. However, I can suggest a few approaches you could explore, depending on your requirements and how the data is structured.
Approach Overview

There are two main approaches you can consider for scraping data from a Chrome extension like LINE:

    Using a Custom Chrome Extension with a Content Script: You can inject a script directly into the LINE extension's popup or page using your own Chrome extension. This will allow you to access the HTML structure and pull data, but it requires interaction with the Chrome Extension APIs.

    Using Tampermonkey or Greasemonkey: These are user script managers that allow you to inject custom JavaScript into web pages and browser extensions. You can use these tools to inject scripts that access the LINE extension popup or its internal data.

    Automation with Selenium WebDriver: If the data you need to scrape is accessible in a web view or the extension creates an HTML document that you can interact with, you might be able to use Selenium for automation. However, this is more suited to websites rather than Chrome extensions directly.

Approach 1: Using a Custom Chrome Extension with a Content Script

A Chrome extension can have content scripts that run in the context of a webpage (or extension popup). This is the most seamless way to interact with the LINE extension popup and scrape data, provided that the data is publicly accessible.
Steps:

    Create a Chrome Extension: You’ll need to create your own extension that injects a content script into the LINE extension popup or page.

    Access the Popup Window: The LINE extension uses a popup that you can try to target with your content script. This is where you can interact with the HTML structure of the popup.

    Scrape the Data: Once you have access to the HTML, you can use JavaScript to extract the message data you need (e.g., last sent message, timestamps, etc.).

Code Example for Chrome Extension with Content Script

manifest.json: This file specifies your extension’s behavior, including the content script that targets the LINE popup or page.

{
  "manifest_version": 3,
  "name": "LINE Data Scraper",
  "version": "1.0",
  "permissions": [
    "activeTab",
    "storage"
  ],
  "background": {
    "service_worker": "background.js"
  },
  "content_scripts": [
    {
      "matches": ["chrome-extension://*/*"], 
      "js": ["content.js"]
    }
  ],
  "host_permissions": [
    "chrome-extension://ophjlpahpchlmihnnnihgmmeilfjmjjc/*"
  ]
}

background.js: The background script to handle the popup interaction.

chrome.runtime.onInstalled.addListener(() => {
  console.log("LINE Data Scraper Extension Installed!");
});

content.js: The content script that interacts with the LINE extension popup.

// This script will run inside the popup of the LINE extension
window.addEventListener("load", () => {
  console.log("LINE popup loaded.");

  // Example: Find elements related to messages
  const messageElements = document.querySelectorAll('.message-text'); // Update with actual selector
  const messageData = [];

  messageElements.forEach(element => {
    const messageText = element.textContent.trim();
    const timestamp = element.parentNode.querySelector('.timestamp').textContent.trim(); // Update with actual selector
    messageData.push({ messageText, timestamp });
  });

  console.log("Message Data: ", messageData);
  // Store or process the data as needed
});

Approach 2: Using Tampermonkey or Greasemonkey

Tampermonkey or Greasemonkey allows you to inject custom JavaScript into pages. This approach could be useful if the LINE extension is running as a web page in the background, and you can access it through the chrome-extension:// URL.
Steps:

    Create a Tampermonkey Script: Write a user script that targets the LINE extension’s popup page.
    Inject JavaScript to Scrape Data: Use JavaScript to scrape the content once it loads.

Example Tampermonkey Script:

// ==UserScript==
// @name         LINE Data Scraper
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  Scrape data from LINE extension popup
// @author       You
// @match        chrome-extension://ophjlpahpchlmihnnnihgmmeilfjmjjc/*
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    // Run the script when the popup has loaded
    window.addEventListener("load", function() {
        const messageElements = document.querySelectorAll('.message-text'); // Adjust the selector to target message text
        const messageData = [];

        messageElements.forEach(element => {
            const messageText = element.textContent.trim();
            const timestamp = element.parentNode.querySelector('.timestamp').textContent.trim(); // Adjust to the timestamp selector
            messageData.push({ messageText, timestamp });
        });

        console.log("Scraped Message Data: ", messageData);

        // Optional: Send data to a server or save to local storage
    });
})();

Approach 3: Using Selenium WebDriver

Selenium WebDriver is great for automating interaction with a web page. If the LINE extension shows its messages in a web-based view (not in a Chrome extension popup), you can automate the extraction of data by controlling the browser.
Steps:

    Install Selenium: Set up a Python or Node.js environment with Selenium WebDriver.
    Navigate to the LINE Web App or Popup: Automate the navigation to the LINE web app or the extension’s page.
    Scrape Data: Extract the message details.

Example Selenium (Python) Code:

from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options
import time

# Set up Chrome options
chrome_options = Options()
chrome_options.add_argument("--load-extension=/path/to/your/line/extension")  # Path to the LINE extension

# Set up the WebDriver
driver = webdriver.Chrome(service=Service('/path/to/chromedriver'), options=chrome_options)
driver.get("chrome-extension://ophjlpahpchlmihnnnihgmmeilfjmjjc/popup.html")

time.sleep(3)  # Wait for the popup to load

# Extract message data
messages = driver.find_elements(By.CSS_SELECTOR, '.message-text')  # Update with the correct selector
timestamps = driver.find_elements(By.CSS_SELECTOR, '.timestamp')  # Update with the correct selector

for msg, timestamp in zip(messages, timestamps):
    print(f"Message: {msg.text} | Timestamp: {timestamp.text}")

driver.quit()

Conclusion

    Best Approach: If you are looking for direct interaction with the LINE extension popup, I recommend using Chrome Extensions with Content Scripts (Approach 1). This approach allows for seamless interaction and data scraping directly from the extension’s DOM.

    Alternative Approaches: Tampermonkey (Approach 2) can also work, especially if you prefer a script-based solution that runs in your browser. Selenium (Approach 3) is more suited for automating browser interactions with websites but may not be as efficient for browser extension interactions.

Important Note:

    Be sure to respect the privacy and terms of service of LINE and other platforms. Scraping or interacting with their services may violate their terms, especially if you're bypassing security measures. Always check their terms and seek permission if necessary.
