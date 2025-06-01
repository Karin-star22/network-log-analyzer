import re
from collections import defaultdict

def read_log_file(filename):
    try:
        with open(filename, 'r') as f:
            return f.readlines()
    except FileNotFoundError:
        print(f"File {filename} not found.")
        return []

def parse_log_lines(lines):
    """
    Exemplo de log esperado (você pode adaptar):
    2025-06-01 10:00:00 - IP: 192.168.1.5 - Login Success
    2025-06-01 10:01:00 - IP: 192.168.1.5 - Login Failed
    """
    log_entries = []
    pattern = re.compile(r'IP:\s*(\d+\.\d+\.\d+\.\d+)\s*-\s*Login\s*(Success|Failed)', re.IGNORECASE)
    for line in lines:
        match = pattern.search(line)
        if match:
            ip = match.group(1)
            status = match.group(2).lower()
            log_entries.append((ip, status))
    return log_entries

def analyze_logs(log_entries):
    total_attempts = len(log_entries)
    success_count = sum(1 for ip, status in log_entries if status == 'success')
    fail_count = total_attempts - success_count

    fails_by_ip = defaultdict(int)
    for ip, status in log_entries:
        if status == 'failed':
            fails_by_ip[ip] += 1

    print(f"Total login attempts: {total_attempts}")
    print(f"Successful logins: {success_count}")
    print(f"Failed logins: {fail_count}")

    print("\nIPs with multiple failed attempts (possible suspicious activity):")
    for ip, fails in fails_by_ip.items():
        if fails > 3:
            print(f" - {ip}: {fails} failed attempts")

def main():
    filename = input("Enter the log filename: ")
    lines = read_log_file(filename)
    if not lines:
        return

    log_entries = parse_log_lines(lines)
    if not log_entries:
        print("No valid log entries found.")
        return

    analyze_logs(log_entries)

if __name__ == "__main__":
    main()
