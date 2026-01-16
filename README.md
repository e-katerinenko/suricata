<h1>Network Intrusion Detection with Suricata</h1>

<p>
This project documents the installation, configuration, and validation of
<strong>Suricata</strong> as a network-based Intrusion Detection System (IDS)
on Ubuntu. The goal is to deploy Suricata, enable packet inspection on a
network interface, load detection rules, and verify alert and log generation
in a SOC-style environment.
</p>

<hr>

<h2>Lab Environment</h2>
<ul>
  <li><strong>Ubuntu Server</strong> — Suricata IDS</li>
  <li><strong>Rule Source</strong> — Suricata Emerging Threats ruleset, Custom rules</li>
</ul>

<hr>

<h2>Installation and Configuration</h2>

<h3>1. Install Required Packages</h3>

<pre>
sudo apt -y install libnetfilter-queue-dev libnetfilter-queue1 libnfnetlink-dev libnfnetlink0 software-properties-common jq
</pre>

<hr>

<h3>2. Add Suricata Stable Repository</h3>

<pre>
sudo add-apt-repository ppa:oisf/suricata-stable
</pre>

<hr>

<h3>3. Install Suricata</h3>

<pre>
sudo apt install -y suricata suricata-update
</pre>

<hr>

<h3>4. Verify Installed Version</h3>

<pre>
suricata -V
</pre>
<img width="1404" height="150" alt="0 Version check" src="https://github.com/user-attachments/assets/d44adfbe-71f8-40ab-be82-6fe48e88a83d" />

<hr>

<h3>5. Identify Network Interface</h3>

<p>
Determine the interface Suricata will monitor.
</p>

<pre>
ip a
</pre>
<img width="1645" height="740" alt="01 Interface name" src="https://github.com/user-attachments/assets/031c0a67-47fe-4e8d-9a4b-d25edf951e7d" />

<hr>

<h3>6. Update Suricata Configuration</h3>

<p>
Edit the Suricata configuration file:
</p>

<pre>
sudo nano /etc/suricata/suricata.yaml
</pre>

<p>
Update the <strong>AF_PACKET</strong> section to reflect the correct interface:
</p>

<pre>
af-packet:
  - interface: enp0s3
</pre>

<hr>

<h3>7. Download Detection Rules</h3>

<pre>
sudo suricata-update
</pre>

<p>
This creates the rule file:
</p>

<pre>
/var/lib/suricata/rules/suricata.rules
</pre>
<img width="1638" height="270" alt="02 Downloading rules" src="https://github.com/user-attachments/assets/6816bd73-124d-4eba-884d-2a65635f2a09" />

<hr>

<h3>8. Verify Rule Path</h3>

<pre>
grep -n "default-rule-path" /etc/suricata/suricata.yaml
</pre>

<p>
Expected value:
</p>

<pre>
default-rule-path: /var/lib/suricata/rules
</pre>

<hr>

<h3>9. Verify Rule File Inclusion</h3>

<pre>
grep -n "suricata.rules" /etc/suricata/suricata.yaml
</pre>

<hr>

<h3>10. Test Suricata Configuration</h3>

<pre>
sudo suricata -T -c /etc/suricata/suricata.yaml
</pre>

<p>
Expected output:
</p>

<pre>
Configuration provided was successfully loaded.
</pre>
<img width="1686" height="216" alt="1 Config check" src="https://github.com/user-attachments/assets/1658e090-10c5-4cb3-ba8f-ef48bfc17970" />

<hr>

<h3>11. Set Required Capabilities</h3>

<p>
Allow Suricata to capture packets without running as root.
</p>

<pre>
sudo setcap cap_net_raw,cap_net_admin=eip /usr/bin/suricata
</pre>

<hr>

<h3>12. Verify Capabilities</h3>

<pre>
getcap /usr/bin/suricata
</pre>

<p>
Expected output:
</p>

<pre>
/usr/bin/suricata = cap_net_raw,cap_net_admin+eip
</pre>

<hr>

<h3>13. Enable and Start Suricata Service</h3>

<pre>
sudo systemctl daemon-reload
sudo systemctl enable suricata
sudo systemctl restart suricata
</pre>

<p>
Verify service status:
</p>

<pre>
sudo systemctl status suricata
</pre>

<p>
Expected state:
</p>

<pre>
Active: active (running)
</pre>
<img width="1646" height="632" alt="3 Status check" src="https://github.com/user-attachments/assets/d536641e-4945-4630-a646-0358706b221d" />

<hr>

<h3>14. Confirm Log Generation</h3>

<pre>
ls /var/log/suricata/
</pre>

<p>
Typical log files include:
</p>
<ul>
  <li><code>eve.json</code> — alerts, flows, and metadata</li>
  <li><code>fast.log</code> — human-readable alerts</li>
  <li><code>stats.log</code> — performance statistics</li>
</ul>
<img width="1516" height="82" alt="4 log files" src="https://github.com/user-attachments/assets/d7871526-a0cb-4c96-ba38-8b55a7d439a8" />

<hr>
