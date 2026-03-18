# Saint John: Find and stop the process writing to `/var/log/bad.log`

## 🧩 Scenario
A developer created a test program that continuously writes to `/var/log/bad.log`, causing the disk to fill up. The program is no longer needed.

> ✅ Goal: Identify the process writing to `/var/log/bad.log` and terminate it.
> 
> ⚠️ Do **not** delete the log file.

## 🧪 Test Condition
The solution is valid when the size of `/var/log/bad.log` stops growing (observe for a duration longer than the file's write frequency).

The check script is at:

```sh
sudo /home/admin/agent/check.sh
```

## 🛠️ Prerequisites
- OS: Debian 11
- You have root (sudo) access

---

## ✅ Step-by-step Solution

### 1) Identify which process is writing to the log

#### Option A: `lsof`

```sh
sudo lsof /var/log/bad.log
```

Look for the process ID (PID) and the command that has the file open.

#### Option B: `fuser`

```sh
sudo fuser /var/log/bad.log
```

This prints the PID(s) of processes using the file.

---

### 2) (Optional) Verify the process is the right one

```sh
ps -fp <PID>
```

Replace `<PID>` with the process ID you found.

---

### 3) Terminate the process

Start with a polite termination:

```sh
sudo kill <PID>
```

If the process does not stop, use a forceful kill:

```sh
sudo kill -9 <PID>
```

> Tip: Use `tail -f /var/log/bad.log` while you kill it to confirm writes stop.

---

## ✅ Verify the fix

Confirm the log file size stabilizes. Then run the provided check script:

```sh
sudo /home/admin/agent/check.sh
```

---

## 🧰 Quick command (one-liner)

If you want to kill any process using the file in one go (requires `fuser`):

```sh
sudo fuser -k /var/log/bad.log
```
