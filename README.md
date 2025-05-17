#!/bin/bash

# Filename to store output
OUTPUT_FILE="system_report_$(date +%F_%T).txt"

# Start report
echo "===== System Information Report =====" > "$OUTPUT_FILE"
echo "Generated on: $(date)" >> "$OUTPUT_FILE"
echo "=====================================" >> "$OUTPUT_FILE"
echo "" >> "$OUTPUT_FILE"

# 1. System Hostname
echo "Hostname: $(hostname)" >> "$OUTPUT_FILE"

# 2. Operating System
echo "Operating System: $(lsb_release -d | cut -f2-)" >> "$OUTPUT_FILE"

# 3. System Uptime
echo "Uptime: $(uptime -p)" >> "$OUTPUT_FILE"

# 4. Linux Kernel Version
echo "Kernel Version: $(uname -r)" >> "$OUTPUT_FILE"

# 5. CPU Information
echo "CPU Info:" >> "$OUTPUT_FILE"
lscpu | grep -E 'Model name|Socket|Thread|Core|Architecture' >> "$OUTPUT_FILE"

# 6. Memory Info
echo "" >> "$OUTPUT_FILE"
echo "Memory Info:" >> "$OUTPUT_FILE"
free -h >> "$OUTPUT_FILE"

# 7. IP and MAC Addresses
echo "" >> "$OUTPUT_FILE"
echo "Network Interfaces:" >> "$OUTPUT_FILE"
ip -brief address >> "$OUTPUT_FILE"
echo "" >> "$OUTPUT_FILE"
ip link | awk '/ether/ {print "Interface:", iface, "MAC:", $2} {iface=$2}' >> "$OUTPUT_FILE"

# 8. Filesystem Usage
echo "" >> "$OUTPUT_FILE"
echo "Filesystem Usage:" >> "$OUTPUT_FILE"
df -hT >> "$OUTPUT_FILE"

# 9. Last five "error" log entries (case-insensitive)
echo "" >> "$OUTPUT_FILE"
echo "Last 5 lines in syslog containing the word 'error':" >> "$OUTPUT_FILE"
grep -i 'error' /var/log/syslog | tail -n 5 >> "$OUTPUT_FILE"

# Completion message
echo "" >> "$OUTPUT_FILE"
echo "Report saved to: $OUTPUT_FILE"
echo "Done."
