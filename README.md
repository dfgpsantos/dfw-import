# dfw-import

This bash script helps importing a lis of FW rules in a cvs file into the NSX DFW.
It should work in most of the linux distros available as it only uses curl and bash commands.
Please inform your environemnt variables such as NSX Manager IP Address/FQDN and user in the respective fields before using the script.

The rules.list structure should follow the guidelines below to run sucessful importing

Field #

1 - Section/Policy name/id - it creates a security policy with this id/name or update one that exists

2 - Rule Name - it creates a rule with this id/name or update one that exists inside the security-policy informed in the first field

3 - Source - One or Many host IP Addresses or CIDRs. It should be informed individualy between double quotes and separated by comas e.g. "192.168.10.11","192.168.0.0/24"

4 - Destination - One or Many host IP Addresses or CIDRs. It should be informed individualy between double quotes and separated by comas e.g. "192.168.10.11","192.168.0.0/24"

5 - Protocol - Choice of ANY, TCP or UDP

6 - Port - None, One or Many destination ports. Ranges are supported. It should be informed individualy between double quotes and separated by comas e.g. "161-162","53","514"

7 - Action - Choice of ALLOW, DROP or REJECT

8 - Log - Choice of log or empty

For each rule the script will generate a .txt with the json used to run the API call and inform the status as below

Creating DFW Rule for regra1 with the Source IP Address "192.168.10.0/24" and Destination IP Address "192.168.10.10","192.168.10.11" for TCP and "80","443","8080"

When finishing the script will prompt an option to keep or delete the created json files.

Happy importing

David Santos
