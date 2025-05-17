#!/bin/bash

# Define output file
OUTPUT_FILE="/var/log/ubuntu_system_info_$(date +%Y%m%d_%H%M%S).txt"

# Function to print  headers
print() {
    echo "=====================================" >> "$OUTPUT_FILE"
    echo "$1" >> "$OUTPUT_FILE"
    echo "=====================================" >> "$OUTPUT_FILE"
}

# Start writing to output file
{
    # System Hostname
    print "System Hostname"
    hostnamectl | grep "Static hostname" >> "$OUTPUT_FILE"
    echo "" >> "$OUTPUT_FILE"

    # Operating System
    print "Operating System"
    lsb_release -a 2>/dev/null | grep -E "Description|Release|Codename" >> "$OUTPUT_FILE"
    echo "" >> "$OUTPUT_FILE"

    # System Uptime
    print "System Uptime"
    uptime -p >> "$OUTPUT_FILE"
    echo "" >> "$OUTPUT_FILE"

    # Linux Kernel Version
    print "Linux Kernel Version"
    uname -r >> "$OUTPUT_FILE"
    echo "" >> "$OUTPUT_FILE"

    # CPU Information
    print "CPU Information"
    lscpu | grep -E "Model name|Architecture|CPU\(s\)|Vendor ID" >> "$OUTPUT_FILE"
    echo "" >> "$OUTPUT_FILE"

    # Memory Information
    print "Memory Information"
    free -h >> "$OUTPUT_FILE"
    echo "" >> "$OUTPUT_FILE"

    # Network Information
    print "Network Information"
    for interface in $(ip link show | grep '^[0-9]' | cut -d: -f2 | awk '{print $1}'); do
        echo "Interface: $interface" >> "$OUTPUT_FILE"
        ip addr show "$interface" | grep -E "inet |link/ether" | sed 's/^[ \t]*//' >> "$OUTPUT_FILE"
        echo "" >> "$OUTPUT_FILE"
    done

    # Filesystem Utilization
    print "Filesystem Utilization"
    df -h --output=source,fstype,size,used,avail,pcent,target >> "$OUTPUT_FILE"
    echo "" >> "$OUTPUT_FILE"

    # Last 5 Error Lines from General Log
    print "Recent Errors in System Log"
    grep -i "error" /var/log/ubuntu_system_info | tail -n 5 >> "$OUTPUT_FILE"
    echo "" >> "$OUTPUT_FILE"
    echo "System information has been saved to $OUTPUT_FILE"

} > "$OUTPUT_FILE"

