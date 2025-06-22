# 🚀 FastAPI Web App with GitHub Actions CI/CD on AWS EC2 (Self-hosted Runner)

This project demonstrates a complete setup of a **FastAPI Python web application** deployed using a **CI/CD pipeline** with **GitHub Actions** and a **self-hosted runner on an AWS EC2 instance**. No Docker is used — just clean Python + GitHub + Cloud.

---

## 📌 Features

- ✅ Simple FastAPI app (serves JSON + Swagger docs)
- ✅ AWS EC2 (Ubuntu) used as GitHub self-hosted runner
- ✅ CI/CD pipeline with GitHub Actions (`workflow_dispatch` trigger)
- ✅ Deployed to EC2 (runs on port `8000`)
- ✅ Minimal dependencies, no Docker

---

## 🧠 Tech Stack

- **Python 3.12+**
- **FastAPI**
- **Uvicorn**
- **GitHub Actions**
- **AWS EC2 (Ubuntu 22.04)**
- **Self-hosted GitHub Runner**

---

## 📁 Project Structure

simplefastapiapp/
├── app/
│ └── main.py
├── requirements.txt
└── .github/
└── workflows/
└── deploy.yml


## 🚀 Getting Started

### 1. Clone the project locally

```bash
git clone https://github.com/your-username/simplefastapiapp.git
cd simplefastapiapp


2. Run locally

python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --host 127.0.0.1 --port 8000

3. open browser
http://127.0.0.1:8000/
http://127.0.0.1:8000/docs


✅ EC2 Setup
Launch EC2 instance (Ubuntu 22.04)

Open ports: 22, 8000 (via Security Groups)

SSH into instance:

bash
Copy
Edit
ssh -i your-key.pem ubuntu@<EC2_PUBLIC_IP>
✅ Install GitHub Runner on EC2
On GitHub: Go to Settings > Actions > Runners

Add new runner → select Linux x64

Copy setup commands and run on EC2:

bash
Copy
Edit
mkdir actions-runner && cd actions-runner
curl -o actions-runner.tar.gz -L https://github.com/actions/runner/releases/download/v2.323.0/actions-runner-linux-x64-2.323.0.tar.gz
tar xzf actions-runner.tar.gz
./config.sh --url https://github.com/your-username/simplefastapiapp --token YOUR_TOKEN
./svc.sh install
./svc.sh start
⚙️ CI/CD with GitHub Actions
.github/workflows/deploy.yml
yaml
Copy
Edit
name: Deploy FastAPI App to EC2

on:
  workflow_dispatch:  # Manual trigger

jobs:
  deploy:
    runs-on: self-hosted  # EC2 instance

    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: 3.12

      - name: Deploy FastAPI
        run: |
          APP_DIR=~/fastapiapp
          rm -rf $APP_DIR
          mkdir -p $APP_DIR
          cp -r . $APP_DIR
          cd $APP_DIR

          python3 -m venv venv
          source venv/bin/activate
          pip install -r requirements.txt

          pkill -f uvicorn || true
          nohup venv/bin/uvicorn app.main:app --host 0.0.0.0 --port 8000 > log.txt 2>&1 &
💻 Access Your App
After successful deployment, open in browser:

plaintext
Copy
Edit
http://<EC2_PUBLIC_IP>:8000/
http://<EC2_PUBLIC_IP>:8000/docs
🛠 Debugging & Troubleshooting
❗ App not reachable in browser (refused to connect)
✅ Checklist:

Open port 8000 in EC2 Security Group

Inbound rule:

yaml
Copy
Edit
Type: Custom TCP | Port: 8000 | Source: 0.0.0.0/0
Check if app is running

bash
Copy
Edit
ps aux | grep uvicorn
sudo lsof -i :8000
Manually run app

bash
Copy
Edit
cd ~/fastapiapp
source venv/bin/activate
uvicorn app.main:app --host 0.0.0.0 --port 8000
Background run with logs

bash
Copy
Edit
nohup venv/bin/uvicorn app.main:app --host 0.0.0.0 --port 8000 > log.txt 2>&1 &
❗ Error: libpython3.10.so.1.0 not found
Fix:
Your EC2 Python version is different than what the venv expected.

bash
Copy
Edit
rm -rf venv
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
✅ TODOs (Future Improvements)
 Use systemd to auto-start app on boot

 Add unit tests and GitHub Actions test stage

 Add domain name + HTTPS with Nginx

 Use .env file for secret config

👨‍💻 Author
Made with ❤️ by Your Name

📄 License
MIT License – free to use, modify, share

yaml
Copy
Edit

---

