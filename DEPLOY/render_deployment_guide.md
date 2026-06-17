# How to Deploy MAXIS (Eris) to Render.com

Deploying Eris to the cloud ensures she is always active and doesn't rely on your local machine. Since the project includes a `Dockerfile` and a `render.yaml` file inside the `maxis-core` folder, the process is streamlined.

Here is your comprehensive, step-by-step guide to taking Eris live.

---

## Step 1: Set up the Cloud Database (Supabase)
Because Eris is moving to the cloud, she can no longer use local SQLite databases reliably (Render's free tier wipes local disk data on every restart). You must use a remote PostgreSQL database.

1. Go to [Supabase.com](https://supabase.com/) and create a new project.
2. Go to the **SQL Editor** and paste the contents of `Supabase\schema.sql` (the code we generated earlier) and run it. This will create all her memory tables.
3. Go to **Project Settings** -> **Database** and copy your **Connection String (URI)**.
   *It will look something like:* `postgresql://postgres.xxx:[YOUR-PASSWORD]@aws-0-us-west-1.pooler.supabase.com:6543/postgres`
   *Save this for Step 4.*

---

## Step 2: Push the Code to GitHub
Render pulls the code directly from a Git repository. You need to push your local `maxis-bot` folder to GitHub.

1. Create a new, empty repository on GitHub.
2. Open your terminal in the `c:\Users\HP\Desktop\ERIS\maxis-bot` folder and run:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/your-username/your-repo-name.git
   git push -u origin main
   ```

---

## Step 3: Connect to Render.com
You have two options here: using the automated Blueprint, or setting up a standard Web Service. The standard Web Service method is highly recommended for better control.

1. Log in to [Render.com](https://render.com/).
2. Click **New** -> **Web Service**.
3. Connect your GitHub account and select the repository you just pushed.
4. On the setup page, configure the following:
   - **Name:** `maxis-cloud` (or whatever you prefer)
   - **Root Directory:** `maxis-core` *(This is CRITICAL. It tells Render to look for the `Dockerfile` inside the `maxis-core` folder).*
   - **Environment:** `Docker` *(Render should automatically detect this based on the Dockerfile)*.
   - **Region:** Choose a region close to your Supabase database.
   - **Instance Type:** `Free` (or upgrade to Starter if you don't want the server to sleep).

---

## Step 4: Configure Environment Variables
Scroll down to the **Environment Variables** section on the Render setup page. You must configure these exactly so Eris knows she is in the cloud and how to connect to her APIs.

Add the following variables:
1. `MAXIS_ENV`
   - **Value:** `cloud` *(This tells Eris to use cloud API models instead of trying to look for a local Ollama instance).*
2. `MAXIS_CLOUD__DATABASE_URL`
   - **Value:** Your Supabase connection string from Step 1. Remember to replace `[YOUR-PASSWORD]` with your actual database password.
3. `MAXIS_GEMINI__API_KEY`
   - **Value:** Your Google Gemini API Key.
4. `MAXIS_XAI__API_KEY` *(Optional)*
   - **Value:** Your Grok API Key (needed if the Active Mind daemon uses Grok).
5. `PORT`
   - **Value:** `8420` *(Render dynamically assigns ports, but Eris is hardcoded to listen on 8420 inside the Docker container).*

---

## Step 5: Deploy and Ping
1. Click **Create Web Service**. 
2. Render will now start building the Docker image. Because Eris uses heavy machine learning libraries (like `sentence-transformers` and `chromadb`), this initial build will take **5 to 15 minutes**.
3. Watch the logs. Once you see `MAXIS — Online and ready` and `Uvicorn running on http://0.0.0.0:8420`, Eris is live!
4. Render will give you a public URL (e.g., `https://maxis-cloud-abc.onrender.com`). Navigating to this URL will load the Eris web UI!

---

## Important Considerations for the Free Tier
If you are using Render's Free tier, the server will "go to sleep" after 15 minutes of inactivity. When you send her a message after she is asleep, it will take ~50 seconds for the server to wake back up.

**To prevent this (Keep-Alive Hack):**
Eris has a lightweight `/ping` endpoint built exactly for this reason. You can set up a free account on [UptimeRobot](https://uptimerobot.com/), and tell it to send an HTTP GET request to `https://your-render-url.onrender.com/ping` every 10 minutes. This will artificially keep Eris awake 24/7.
