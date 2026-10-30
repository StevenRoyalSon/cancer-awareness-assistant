# Supabase Setup Guide - Step by Step

Follow these steps to configure Supabase for the Cancer Awareness Assistant.

---

## 📋 Prerequisites
- A web browser
- Email address (for Supabase account)
- Basic internet connection

---

## Step 1: Create a Supabase Account

1. Go to [https://supabase.com](https://supabase.com)
2. Click **"Start your project"** or **"Sign Up"**
3. Choose one of the sign-up options:
   - Sign up with GitHub (recommended)
   - Sign up with email
4. Fill in your details and create your account
5. Verify your email if required

---

## Step 2: Create a New Project

1. After logging in, you'll see the dashboard
2. Click **"New Project"** button
3. Fill in the project details:
   - **Name**: e.g., "cancer-awareness-app" (or any name you like)
   - **Database Password**: Create a strong password (save this somewhere safe!)
   - **Region**: Choose a region closest to you (e.g., "Southeast Asia" if you're in India)
   - **Pricing Plan**: Select **"Free"** (it's sufficient for this project)
4. Click **"Create new project"**
5. Wait for the project to be created (this takes 1-2 minutes)

---

## Step 3: Get Your API Credentials

Once the project is ready:

1. In the left sidebar, click on **Settings** (gear icon)
2. Click on **API** in the submenu
3. You'll see two important values:

### Copy the Project URL
- Look for **"Project URL"** field
- It looks like: `https://xxxxxxxxxxxxx.supabase.co`
- Click the copy button next to it
- Save this somewhere (you'll need it soon)

### Copy the API Key
- Look for **"anon public"** key (or "service_role" key)
- It looks like: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...`
- Click the copy button next to it
- Save this somewhere (you'll need it soon)

**⚠️ Keep these credentials secret! Never share them or commit to git.**

---

## Step 4: Create the .env File

Since I cannot create the `.env` file directly (it's in .gitignore for security), you need to create it manually:

### Option A: Using PowerShell (Easiest)

Open PowerShell in your project directory and run:
```powershell
Copy-Item .env.example .env
```

Then open `.env` in your text editor.

### Option B: Manual Creation

1. Open `.env.example` in your text editor
2. Go to **File → Save As**
3. Name it `.env` (remove `.example`)
4. Save it in the same folder

---

## Step 5: Configure .env with Your Credentials

Open the `.env` file you just created and replace the placeholder values:

```env
# Supabase Configuration
SUPABASE_URL=https://your-project-id.supabase.co
SUPABASE_KEY=your-anon-or-service-role-key-here

# OpenAI Configuration
OPENAI_API_KEY=your-openai-api-key-here
```

**Replace with your actual values:**
- Replace `https://your-project-id.supabase.co` with your Project URL from Step 3
- Replace `your-anon-or-service-role-key-here` with your API key from Step 3
- Replace `your-openai-api-key-here` with your OpenAI API key (or Groq key)

**Example:**
```env
# Supabase Configuration
SUPABASE_URL=https://abc123xyz.supabase.co
SUPABASE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImFiYzEyeHl6Iiwicm9sZSI6ImFub24iLCJpYXQiOjE2MzAwMDAwMDAsImV4cCI6MTk0NTU1NTU1NX0.your-key-here

# OpenAI Configuration
OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

**Save the file after making changes.**

---

## Step 6: Set Up the Database Schema

Now you need to create the database tables in Supabase:

1. Go back to your Supabase project dashboard in the browser
2. In the left sidebar, click on **SQL Editor** (icon looks like a terminal)
3. Click **"New query"** button
4. Open the file `sql/schema.sql` from your project folder (in VS Code or any text editor)
5. Copy ALL the SQL code from that file (lines 1-100)
6. Paste it into the Supabase SQL Editor
7. Click the **"Run"** button (or press Ctrl+Enter)
8. You should see a success message like: "Success. No rows returned"

This will create three tables:
- `hospital_profile` - Stores hospital information
- `chat_messages` - Stores conversation history
- `document_chunks` - Stores document text

---

## Step 7: Verify the Tables Were Created

1. In the left sidebar, click on **Table Editor** (icon looks like a grid)
2. You should see three tables:
   - `hospital_profile`
   - `chat_messages`
   - `document_chunks`
3. Click on each table to verify they exist (they will be empty initially)

---

## Step 8: Re-run the Document Ingestion

Now that Supabase is configured, you need to re-run the ingestion to upload documents:

1. Open PowerShell in your project directory
2. Make sure your virtual environment is activated:
```powershell
.venv\Scripts\activate
```
3. Run the ingestion script:
```powershell
python ingest.py
```

This will:
- Process documents in the `documents/` folder
- Create embeddings
- Store in local ChromaDB
- Upload documents to Supabase Storage
- Store document chunks in Supabase database

You should see output like:
```
Loading embedding model BAAI/bge-small-en-v1.5...
Indexed screening_basics.docx: 5 chunk(s)
Indexed symptoms_do_not_prove_cancer.pdf: 3 chunk(s)
...
Supabase storage bucket documents now has X file(s).
Supabase document_chunks now has X chunk(s).
```

---

## Step 9: Restart the Streamlit App

1. If Streamlit is currently running, stop it by pressing `Ctrl+C` in the terminal
2. Restart it:
```powershell
streamlit run app.py
```

3. The browser should open with the app
4. The Supabase error message should be gone
5. You should see:
   - Hospital profile information displayed
   - A message: "The hospital profile, indexed documents, and this chat are stored in Supabase."

---

## Step 10: Test the Setup

Try asking a question in the chat:
- "What is the hospital name?"
- "What is the hospital address?"
- "Tell me about cancer screening"

The app should:
- Answer from the hospital profile (for hospital info)
- Answer from documents (for screening info)
- Save the chat history to Supabase

---

## 🔍 Troubleshooting

### Problem: "Supabase is not configured" still appears

**Solution:**
1. Check that `.env` file exists (not `.env.example`)
2. Verify the URL and key are correct in `.env`
3. Make sure there are no extra spaces or quotes around the values
4. Restart Streamlit after saving `.env`

### Problem: "Supabase could not read or write the tables"

**Solution:**
1. Make sure you ran `sql/schema.sql` in the Supabase SQL Editor
2. Check that the tables exist in the Table Editor
3. Verify your API key has the right permissions (use anon or service_role key)

### Problem: "Supabase rejected the API key"

**Solution:**
1. Verify you copied the correct key (anon or service_role)
2. Make sure the key is not expired
3. Check that you're using the key from the correct project

### Problem: Documents not uploading to Supabase

**Solution:**
1. Make sure Supabase is configured before running `ingest.py`
2. Check that the `documents/` folder has files
3. Run `ingest.py` again after configuring Supabase

### Problem: Chat history not saving

**Solution:**
1. Verify the `chat_messages` table exists
2. Check that the SQL schema was run successfully
3. Look at the browser console for errors (F12)

---

## 📝 Quick Reference

### Your .env should look like:
```env
SUPABASE_URL=https://your-project-id.supabase.co
SUPABASE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
OPENAI_API_KEY=sk-proj-...
```

### Key Supabase URLs:
- Dashboard: https://supabase.com/dashboard
- SQL Editor: Project → SQL Editor
- API Settings: Project → Settings → API
- Table Editor: Project → Table Editor

### Commands to remember:
```powershell
# Activate virtual environment
.venv\Scripts\activate

# Ingest documents (also uploads to Supabase)
python ingest.py

# Run the app
streamlit run app.py
```

---

## ✅ Success Checklist

- [ ] Created Supabase account
- [ ] Created a new project
- [ ] Copied Project URL
- [ ] Copied API key
- [ ] Created `.env` file
- [ ] Configured `.env` with credentials
- [ ] Ran `sql/schema.sql` in Supabase SQL Editor
- [ ] Verified tables exist in Table Editor
- [ ] Re-ran `python ingest.py`
- [ ] Restarted Streamlit app
- [ ] Supabase error message is gone
- [ ] Hospital profile is displayed
- [ ] Chat messages are saving

---

## 🎯 Next Steps

Once Supabase is working:
1. Add your own documents to the `documents/` folder
2. Run `python ingest.py` to process them
3. Test different questions
4. Check chat history persists after restarting the app
5. Explore the Supabase dashboard to see stored data

---

## 🆘 Need Help?

If you're still having issues:
1. Check the terminal where Streamlit is running for error messages
2. Open browser console (F12) to see JavaScript errors
3. Verify your `.env` file has no syntax errors
4. Make sure you're using the correct Supabase project

Good luck! 🚀
