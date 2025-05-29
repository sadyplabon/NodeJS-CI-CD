# Challenges and Solutions: Node.js CI/CD with GitHub Actions and Vagrant

## 1. ❌ Missing `test` Script in package.json
**Problem:**  
The GitHub Actions test job failed because `npm test` could not find a `test` script.

**Solution:**  
Added a `test` script in `package.json` that aliases the existing `check` script:

```json
"scripts": {
  "test": "npm run check"
}
```

---

## 2. ❌ Artifact Actions Not Downloading
**Problem:**  
`actions/upload-artifact@v3` and `actions/download-artifact@v3` failed with "Missing download info" on the self-hosted runner.

**Solution:**  
- Ensured the runner had internet access to GitHub.
- Upgraded to `@v4` for better reliability.
- Verified action names were typed correctly in the workflow YAML.

---

## 3. ❌ Workflow YAML Error – Unexpected Value
**Problem:**  
GitHub Actions showed an "Unexpected value 'deploy'" error.

**Solution:**  
Fixed the YAML structure by moving the `deploy:` block under the `jobs:` key, so it was properly nested alongside `test:`.

---

## 4. ❌ `test-results` Folder Missing Locally
**Problem:**  
The artifact `test-results` uploaded in CI was not visible in the local project directory.

**Solution:**  
Understood that the directory was created temporarily in the GitHub Actions environment and uploaded from there. It’s not part of the local project unless explicitly created.

---

## 5. ❌ App Not Reachable on Port 3000
**Problem:**  
The app deployed successfully with PM2, but `curl http://192.168.0.201:3000/` failed with “Connection refused”.

**Solution:**  
- Updated 'config.vm.network' file and restarted Vagrant.
- Restarted the PM2 app.
- Confirmed with `netstat` that port 3000 was listening on all interfaces.

---

## 6. ❌ PM2 Showed No Running Apps
**Problem:**  
After rebooting the VM, `pm2 list` showed no apps running.

**Solution:**  
- Restarted the app manually using:
  ```bash
  pm2 start ./src/server.js --name node-app
  ```
- Ran `pm2 save` and `pm2 startup` to persist the process across VM restarts.

---

## 7. ❌ Vagrant Port Not Reachable from Host
**Problem:**  
Even though the VM was running, the app wasn’t reachable via the browser.

**Solution:**  
- Configured **bridged networking** in Vagrant to expose the VM directly on the LAN.
- Ensured firewall allowed port 3000:
  ```bash
  sudo firewall-cmd --zone=public --add-port=3000/tcp --permanent
  sudo firewall-cmd --reload
  ```

---

## ✅ Result
Successfully implemented a full CI/CD pipeline using GitHub Actions and deployed the app using PM2 inside a CentOS Vagrant VM.