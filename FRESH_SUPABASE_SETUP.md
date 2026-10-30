# Fresh Supabase Setup - Complete Guide

Follow these steps to create a brand new Supabase project and connect it to your Cancer Awareness Assistant.

---

## STEP 1: Create New Supabase Project

### 1.1 Go to Supabase
Open your browser and go to: [https://supabase.com](https://supabase.com)

### 1.2 Sign In or Sign Up
- If you have an account, click **"Sign In"**
- If you don't, click **"Start your project"** to create an account

### 1.3 Create New Project
1. After logging in, you'll see the dashboard
2. Click the **"New Project"** button (usually top-right)
3. Fill in the project details:

**Project Information:**
- **Name**: `cancer-awareness-app` (or any name you like)
- **Database Password**: Create a strong password
  - Example: `MySecurePass123!`
  - ⚠️ **Save this password somewhere safe!**
- **Region**: Choose the region closest to you
  - If in India: Select "Southeast Asia" (Singapore)
  - If in USA: Select "East US" or "West US"
  - If in Europe: Select "EU West"
- **Pricing Plan**: Select **"Free"** (sufficient for this project)

4. Click **"Create new project"**
5. Wait for the project to be created (takes 1-2 minutes)
6. You'll see a "Your project is ready" message when done

---

## STEP 2: Get Project URL and Secret Key

### 2.1 Navigate to API Settings
1. In your new project dashboard, look at the **left sidebar**
2. Scroll down and click on **Settings** (gear icon ⚙️)
3. In the submenu that appears, click on **API**

### 2.2 Copy Project URL
On the API settings page, you'll see:

```
Project Reference: easdghpynuzgbpzpouti
Project URL: https://easdghpynuzgbpzpouti.supabase.co
```

1. Find the field labeled **"Project URL"**
2. Click the **Copy button** (📋) next to it
3. Paste it somewhere safe (Notepad, etc.)

**✅ Correct format:**
```
https://easdghpynuzgbpzpouti.supabase.co
```

### 2.3 Copy Secret Key
Scroll down to the **"Project API keys"** section. You'll see:

```
Publishable key
sb_publishable_xxxxxxxxxxxxxxxxxxxxxxxxxxxx

Secret key
sb_secret_xxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

1. Find the field labeled **"Secret key"** (starts with `sb_secret_`)
2. Click the **Copy button** (📋) next to it
3. Paste it somewhere safe

**⚠️ Important:**
- Use the **Secret key** (NOT the Publishable key)
- This key has full access to your project
- Keep it secure and don't share it

---

## STEP 3: Run SQL Schema

### 3.1 Open SQL Editor
1. In the left sidebar of your Supabase dashboard
2. Click on **SQL Editor** (icon looks like a terminal `>_`)
3. Click **"New query"** button

### 3.2 Copy the SQL Schema
1. Open the file `sql/schema.sql` from your project folder
2. Select ALL the code (Ctrl+A)
3. Copy it (Ctrl+C)

The SQL code creates three tables:
- `hospital_profile` - Hospital information
- `chat_messages` - Chat history
- `document_chunks` - Document text storage

### 3.3 Paste and Execute
1. Go back to the Supabase SQL Editor
2. Paste the SQL code (Ctrl+V)
3. Click the **"Run"** button (or press Ctrl+Enter)
4. You should see a success message: "Success. No rows returned"

### 3.4 Verify Tables Were Created
1. In the left sidebar, click on **Table Editor** (grid icon)
2. You should see three tables listed:
   - `hospital_profile`
   - `chat_messages`
   - `document_chunks`
3. Click on each table to verify they exist (they'll be empty initially)

---

## STEP 4: Update .env File

### 4.1 Create .env File (if not exists)
Open PowerShell in your project directory and run:

```powershell
# Check if .env exists
Test-Path .env

# If False, create it from .env.example
Copy-Item .env.example .env
```

### 4.2 Edit .env File
1. Open `.env` in your text editor (VS Code, Notepad, etc.)
2. Replace the placeholder values with your actual credentials

**Your .env should look like this:**

```env
# Supabase Configuration
SUPABASE_URL=https://easdghpynuzgbpzpouti.supabase.co
SUPABASE_KEY=sb_secret_eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImVhc2RnaHdueW51emdicHpwb3V0aSIsInJvbGUiOiJzZXJ2aWNlX3JvbGUiLCJpYXQiOjE2MzAwMDAwMDAsImV4cCI6MTk0NTU1NTU1NTB9.your-full-secret-key-here

# OpenAI Configuration
OPENAI_API_KEY=sk-proj-your-openai-api-key-here
```

**Important:**
- Replace `https://easdghpynuzgbpzpouti.supabase.co` with your actual Project URL
- Replace the key with your actual Secret key (the one starting with `sb_secret_`)
- Replace `your-openai-api-key-here` with your OpenAI API key
- **NO quotes around the values**
- **NO `/rest/v1/` at the end of the URL**
- **NO trailing slash `/` at the end of the URL**

### 4.3 Save the File
Save the `.env` file after making changes.

---

## STEP 5: Test the Connection

### 5.1 Stop Streamlit (if running)
If Streamlit is currently running, stop it by pressing `Ctrl+C` in the terminal.

### 5.2 Restart Streamlit
```powershell
streamlit run app.py
```

### 5.3 Check for Success
The browser should open and you should see:
- ✅ Hospital profile information displayed (name, address, phone, etc.)
- ✅ Message: "The hospital profile, indexed documents, and this chat are stored in Supabase."
- ✅ No error message about Supabase not being configured

### 5.4 Test Chat Functionality
Try asking a question:
- "What is the hospital name?"
- "What is the hospital address?"

The app should answer from the hospital profile and save the chat to Supabase.

---

## STEP 6: Run Document Ingestion (Optional but Recommended)

Now that Supabase is configured, upload your documents:

### 6.1 Activate Virtual Environment
```powershell
.venv\Scripts\activate
```

### 6.2 Run Ingestion
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
Indexed warning_signs.txt: 2 chunk(s)
Collection cancer_awareness now has 10 chunk(s).
Supabase storage bucket documents now has 4 file(s).
Supabase document_chunks now has 10 chunk(s).
```

---

## 🔍 Troubleshooting

### Problem: "Supabase is not configured" error

**Solution:**
1. Verify `.env` file exists (not `.env.example`)
2. Check the URL format: `https://xxxxx.supabase.co` (no `/rest/v1/`, no trailing slash)
3. Verify the key is the Secret key (starts with `sb_secret_`)
4. Make sure there are no quotes around values
5. Restart Streamlit after saving `.env`

### Problem: "Supabase could not read or write the tables"

**Solution:**
1. Make sure you ran the SQL schema in Step 3
2. Verify tables exist in Supabase Table Editor
3. Check that you're using the correct project (URL matches your project)

### Problem: "Invalid URL" error

**Solution:**
1. Check URL format: must start with `https://`
2. No `/rest/v1/` at the end
3. No trailing slash `/` at the end
4. No quotes around the URL
5. No spaces around the `=` sign

### Problem: "Invalid API key" error

**Solution:**
1. Verify you're using the Secret key (not Publishable)
2. Make sure the key is copied completely (no truncation)
3. Check you're using the key from the correct project

---

## ✅ Success Checklist

- [ ] Created new Supabase project
- [ ] Copied Project URL
- [ ] Copied Secret key
- [ ] Opened SQL Editor
- [ ] Ran the SQL schema from `sql/schema.sql`
- [ ] Verified 3 tables exist in Table Editor
- [ ] Created `.env` file
- [ ] Updated `.env` with Project URL
- [ ] Updated `.env` with Secret key
- [ ] Updated `.env` with OpenAI key
- [ ] Saved `.env` file
- [ ] Restarted Streamlit
- [ ] Hospital profile is displayed
- [ ] No Supabase error messages
- [ ] Ran `python ingest.py` (optional)
- [ ] Documents uploaded to Supabase

---

## 📝 Quick Reference

### .env File Template:
```env
SUPABASE_URL=https://your-project-id.supabase.co
SUPABASE_KEY=sb_secret_your-full-secret-key
OPENAI_API_KEY=sk-proj-your-openai-key
```

### Key Commands:
```powershell
# Create .env from template
Copy-Item .env.example .env

# Activate virtual environment
.venv\Scripts\activate

# Ingest documents
python ingest.py

# Run the app
streamlit run app.py
```

### Supabase Dashboard URLs:
- Dashboard: https://supabase.com/dashboard
- SQL Editor: Project → SQL Editor
- API Settings: Project → Settings → API
- Table Editor: Project → Table Editor

---

## 🎯 You're Done!

Once you complete all steps, your Cancer Awareness Assistant will:
- ✅ Connect to Supabase
- ✅ Store hospital profile
- ✅ Save chat history
- ✅ Upload documents to cloud storage
- ✅ Persist data across sessions

Good luck! 🚀
