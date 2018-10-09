# Description
This script queries and parses DMARC records for a list of given domains.

# A Use Case: Implementing DMARC Might Not Suck _That_ Bad
### Why DMARC Might Seem Intimidating
When implementing DMARC verification on incoming mail, it can be difficult to determine what mail will be effected before flipping the switch and enabling "quarantine" or "reject" mode. A good first step is to enable DMARC verification in 'report-only' or 'none' mode on your incoming mail filter (or mail server). Once this is enabled, you should be able to search for incoming mail that is failing DMARC without actually blocking anything. Chances are, after a couple weeks or months, this list will become pretty lengthy and daunting.

### Why It's Probably Not that Bad
The good news is, a significant portion of these domains probably still have their suggested policy set to _'none'_ in their DNS record (in a DMARC DNS record, the suggested policy is defined by the 'p' and 'sp' tags). That means that while they do _have_ a public DMARC record, they don't recommend you actually do anything in the case that DMARC verification fails. It may seem surprising that at the time of writing, a lot of major companies and email providers such as GMail still have their suggested policy set to 'none'. With that in mind, if you were to enable blocking and quarantining email _based on each email domain's suggested policy_, you're probably only going to  quarantine or block a small subset of the original list of emails failing DMARC checks.

### How this script could help
This script seeks to automate the process of going over the long list of domains failing DMARC and pick out the ones that may actually be impactful. It does this by importing the list of domains, querying the DMARC record for each one, parsing the results, and saving them to a CSV file. This way, you can focus on the domains with their 'p' tags set to 'quarantine' or 'reject'.

# Usage
```
python getdmarcrecord.py -h
Usage: python getdmarcrecords.py -i <infile> -o <outfile>

Query and parse DMARC records for a list of domains. Input a list
of domains with the '-i' option. To output results to a CSV file,
define an output file name with the '-o' option.

Example:
python getdmarcrecords.py -i domains.txt -o DMARCRecords.csv

Options:
  -h, --help  show this help message and exit
  -i INFILE   a file containing a list of domains
  -o OUTFILE  (OPTIONAL) an output file name
```

# Sample output
| Domain | v | p | sp | rua | ruf | pct | adkim | aspf | Raw |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| nytimes.com | DMARC1 | none | | mailto:paperboydmarcrua@<i></i>lga2.nytimes.com | mailto:paperboydmarcruf@<i></i>lga2.nytimes.com | | | | v=DMARC1; p=none; rua=mailto:paperboydmarcrua@<i></i>lga2.nytimes.com; ruf=mailto:paperboydmarcruf@<i></i>lga2.nytimes.com; |
| gmail.com | DMARC1 | none | quarantine | mailto:mailauth-reports@<i></i>google.com | | | | | v=DMARC1; p=none; sp=quarantine; rua=mailto:mailauth-reports@<i></i>google.com |
| aol.com | DMARC1 | reject | | mailto:d@<i></i>rua.agari.com | mailto:d@<i></i>ruf.agari.com | 100 | | | v=DMARC1; p=reject; pct=100; rua=mailto:d@<i></i>rua.agari.com; ruf=mailto:d@<i></i>ruf.agari.com; |
| hotmail.com | DMARC1 | none | quarantine | mailto:d@<i></i>rua.agari.com | mailto:d@<i></i>ruf.agari.com | 100 | | | v=DMARC1; p=none; sp=quarantine; pct=100; rua=mailto:d@<i></i>rua.agari.com; ruf=mailto:d@<i></i>ruf.agari.com; fo=1 |
| aim.com | DMARC1 | reject | | mailto:d@<i></i>rua.agari.com | mailto:d@<i></i>ruf.agari.com | 100 | | | v=DMARC1; p=reject; pct=100; rua=mailto:d@<i></i>rua.agari.com; ruf=mailto:d@<i></i>ruf.agari.com; |


