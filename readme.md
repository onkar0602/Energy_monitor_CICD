# Smart Energy Monitoring System on AWS

A web-based Smart Energy Monitoring System that displays simulated electricity consumption for household appliances such as a fan, AC, bulb, and refrigerator. It calculates estimated energy usage and electricity bills, provides energy-saving suggestions, and visualizes consumption through a chart.

The application is deployed on AWS EC2 using Python Flask, Gunicorn, NGINX, and Jenkins CI/CD.

## Project Features

- Monitor appliance-wise power consumption.
- Display total energy usage in kWh.
- Calculate estimated electricity bills using a configurable tariff.
- Display estimated monthly electricity costs.
- Visualize energy consumption using Chart.js.
- Generate smart energy-saving suggestions.
- Refresh dashboard readings automatically every 3 seconds.
- Deploy frontend changes through Jenkins CI/CD.

> **Note:** The current backend generates simulated readings using Python's random module. Physical electricity sensors are not connected yet.

## Architecture

```text
Developer
   |
   v
GitHub Repository
   |
   v
Jenkins CI/CD
   |
   v
AWS EC2 Instance
   |
   +----------------------+
   |                      |
   v                      v
NGINX :80             Gunicorn :5000
   |                      |
   |                      v
   |                  Flask API
   |                      |
   +----------+-----------+
              |
              v
      Energy Dashboard
              |
              v
   Appliance Readings,
   Energy Chart and Bill
```

NGINX serves the frontend and forwards `/energy` requests to the Flask backend. Gunicorn runs the Python application, while Jenkins automates code checkout, validation, and frontend deployment.

## Technologies Used

| Technology | Purpose |
|---|---|
| AWS EC2 | Cloud hosting |
| Ubuntu Linux | Server operating system |
| HTML5 | Frontend structure |
| CSS3 | Dashboard styling |
| JavaScript | Dynamic dashboard updates |
| Chart.js | Energy consumption chart |
| Python | Backend logic |
| Flask | REST API |
| Flask-CORS | Cross-origin support |
| Gunicorn | Python application server |
| NGINX | Web server and reverse proxy |
| Jenkins | CI/CD automation |
| Git and GitHub | Version control |
| systemd | Backend service management |

## Project Structure

```text
Smart_Energy_Monitor/
├── README.md
├── backend.py
├── frontend/
│   ├── index.html
│   ├── script.js
│   └── style.css
└── screenshots/
    ├── dashboard.png
    ├── github-repository.png
    ├── jenkins-success.png
    ├── backend-status.png
    ├── api-response.png
    └── nginx-status.png
```

## How It Works

1. The user opens the dashboard in a browser.
2. NGINX serves the frontend files.
3. JavaScript sends a request to the `/energy` API endpoint.
4. Flask generates simulated appliance readings and returns JSON.
5. JavaScript updates appliance values, energy usage, estimated bills, charts, and suggestions.
6. The dashboard refreshes readings every 3 seconds.

## Energy Calculation

The backend adds the appliance power readings:

```text
Total Power (W) =
Fan + AC + Bulb + Refrigerator
```

The current application uses this simplified estimate:

```text
Estimated Energy (kWh) = Total Power (W) / 1000
```

The estimated bill is calculated using a tariff of ₹8.50 per kWh:

```text
Estimated Bill = Energy (kWh) × ₹8.50
```

The monthly estimate multiplies the current estimated bill by 30.

**Limitation:** These are demonstration estimates, not actual energy consumption accumulated over time. Real billing requires measured energy usage over a defined period.

## AWS EC2 Deployment

### 1. Install Required Packages

Connect to your Ubuntu EC2 instance using SSH.

```bash
sudo apt update
sudo apt install -y python3-pip python3-venv nginx git rsync
```

### 2. Create a Python Virtual Environment

```bash
python3 -m venv ~/energy-venv
~/energy-venv/bin/pip install flask flask-cors gunicorn
```
![API Response](/image/en.png)

### 3. Backend Deployment

Backend directory:

```text
/opt/energy-monitoring
```

Systemd service configuration:

```text
/etc/systemd/system/energy-backend.service
```

The service runs Gunicorn on `127.0.0.1:5000`.

Enable and start the backend:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now energy-backend
sudo systemctl status energy-backend --no-pager
```

### 4. NGINX Configuration

Frontend deployment directory:

```text
/var/www/energy-monitoring
```

NGINX serves the frontend on port 80 and forwards `/energy` requests to the backend at `http://127.0.0.1:5000`.

Test and reload NGINX:

```bash
sudo nginx -t
sudo systemctl reload nginx
sudo systemctl enable nginx
```

### 5. Frontend API Connection

In `frontend/script.js`, the API request uses a relative URL:

```javascript
const res = await fetch("/energy");
```

This allows the browser to communicate with the backend through NGINX.

## Jenkins CI/CD Pipeline

**Jenkins Job:** `Energy-Monitoring-CI-CD`

**Workspace:**

```text
/var/lib/jenkins/workspace/Energy-Monitoring-CI-CD
```

### Pipeline Workflow

1. Fetch source code from GitHub.
2. Check out the configured branch.
3. Validate Python syntax.
4. Deploy frontend files to the NGINX directory.
5. Report the build result in Console Output.

Frontend deployment command:

```bash
sudo rsync -av frontend/ /var/www/energy-monitoring/
```

The current pipeline deploys the frontend. Backend changes require a separate backend deployment step and a restart of the `energy-backend` service.

## Testing and Verification

### Check Backend Status

```bash
sudo systemctl status energy-backend --no-pager
```

### Check NGINX Status

```bash
sudo systemctl status nginx --no-pager
```

### Test Flask API Locally

```bash
curl http://127.0.0.1:5000/energy
```

### Test API Through NGINX

```bash
curl http://127.0.0.1/energy
```

Example JSON response:

```json
{
  "fan": 83,
  "ac": 995,
  "bulb": 29,
  "fridge": 287,
  "total_kwh": 1.38,
  "bill": 11.76
}
```

The values will vary because the backend generates random demonstration readings.

## Application URLs

Replace `<EC2-PUBLIC-IP>` with your instance's current public IP address.

- **Dashboard:** `http://<EC2-PUBLIC-IP>`
- **Energy API:** `http://<EC2-PUBLIC-IP>/energy`
- **Jenkins:** `http://<EC2-PUBLIC-IP>:8080`

**Security:** Restrict Jenkins access to your own IP address or a trusted administrative network. Keep backend port 5000 private and accessible only locally through NGINX.

## Screenshots

Create a folder named `screenshots` in the project root and save your actual screenshots using the filenames below.

### 1. Application Dashboard

![Application Dashboard](/image/a.png)

Shows appliance readings, total energy, estimated bill, chart, and smart suggestions.

### 2. GitHub Repository

![GitHub Repository](/image/git.png)

Shows the project files and repository structure.

### 3. Jenkins CI/CD Success

![Jenkins CI/CD Success](/image/jen.png)

Shows a successful Jenkins build and deployment.

### 4. Python Backend Status

Run the following command and capture the output.

```bash
sudo systemctl status energy-backend --no-pager
```



### 5. API Response


Shows the JSON response returned by the `/energy` endpoint.

### 6. NGINX Status

Run the following command and capture the output.

```bash
sudo systemctl status nginx --no-pager
```




## Future Enhancements

- Integrate actual voltage and current sensors.
- Measure real energy consumption over time.
- Store historical readings in a database.
- Add daily, weekly, and monthly reports.
- Implement alerts for high energy consumption.
- Add user authentication.
- Configure HTTPS with a domain and TLS certificate.
- Automate backend deployment and service restart through Jenkins.

## Learning Outcomes

- AWS EC2 deployment and Linux administration.
- Flask API development and testing.
- Gunicorn and systemd service management.
- NGINX reverse proxy configuration.
- Frontend and backend integration.
- Jenkins CI/CD and GitHub integration.
- Application troubleshooting and monitoring.

## Author

**Onkar Hol**
