<h1> Practical Threat Modeling with MITRE ATT&CK </h1>

# Day 1 / Lab 1: Threat Modeling - Am I a Target?

Follow along using the MITRE ATT&CK Navigator https://mitre-attack.github.io/attack-navigator/enterprise/#

# Day 1 / Lab 2: Slicing and Dicing ATT&CK with MITRE Assistant

Mitre Assistant allows you to slice and dice the ATT&CK matrix quickly to get meaningful insights of what that matrix has to offer.

Mitre Assistant was created and is maintained by Carlos Diaz (@dfirence)

## Setup

1. Install Rust & Cargo: 

    https://doc.rust-lang.org/cargo/getting-started/installation.html

2. Install mitre assistant

    `cargo install mitre-assistant`

## Getting started & initializing Mitre-Assistant

3. You can find all the information on how to use mitre-assistant here: https://docs-ma.vercel.app/docs/project/Brief_Overview. 
    
4. Test that Mitre-Assistant is correctly installed:

    `mitre-assistant -h`

5. Download the ATT&CK Enterprise Matrix (notice that it does NOT support proxies)

    `mitre-assistant download -m enterprise`

6. Parse the downloaded matrix in JSON format so Mitre-Assistant can process it

    `mitre-assistant baseline -m enterprise`

7. Validate your setup with a simple query, searching for technique T1480

    `mitre-assistant search -m enterprise -t "t1480"`

## Statistics

Mitre Assistant is developed to be a batteries included utility for end-users who should only need to focus on asking questions, and finding answers from the ATT&CK dataset.

All queries that are prefixed with `stats:` support the way most professionals approach a dataset as they are exploring it to make sense of what the data may have to offer. When you are using Mitre Assistant, you are encouraged to use these queries as you are discovering ways to operationalize the ATT&CK matrix in your security program and support its defensive requirements.

8. Obtain stats by adversaries

    `mitre-assistant search -m enterprise -t "stats:adversaries"`

9. Obtain stats by malware

    `mitre-assistant search -m enterprise -t "stats:malware"`

10. Obtain stats by tools

    `mitre-assistant search -m enterprise -t "stats:tools"`

11. Obtain stats by platforms

    `mitre-assistant search -m enterprise -t "stats:platforms"`

12. Obtain stats by tactics

    `mitre-assistant search -m enterprise -t "stats:tactics"`

13. Obtain stats by data sources

    `mitre-assistant search -m enterprise -t "stats:datasources"`

14. Obtain stats by techniques

    `mitre-assistant search -m enterprise -t "stats:techniques"`

15. Obtain stats by sub-techniques 

    `mitre-assistant search -m enterprise -t "stats:subtechniques"`

## Searching queries

The Mitre Assistant cli uses the search subcommand. This subcommand mode is designed as a search engine. Users of ATT&CK who know the terminology of the matrix should just have to provide inputs (search terms), and get relevant answers they are seeking.

The subcommand accepts both single and multiple inputs with comma , to indicate their needs for searching are various types of input. 

16. Search for these terms

All techniques for the windows platform

   `mitre-assistant search -m enterprise -t "windows"` 

All techniques for persistence tactic

   `mitre-assistant search -m enterprise -t "persistence"`

All techniques whose name contains the "Exploitation" keyword

   `mitre-assistant search -m enterprise -t "Exploitation"`

All techniques whose datasource contains "wmi-creation"

   `mitre-assistant search -m enterprise -t "wmi-creation"`

All techniques for the adversary "apt1"

   `mitre-assistant search -m enterprise -t "apt1"`

All techniques for the malware "poisonivy"

   `mitre-assistant search -m enterprise -t "poisonivy"`


17. Profiling adversaries, malware and tools

    `mitre-assistant search -m enterprise -t "fin7"`

    The summary query result from above is very useful to get a high level understanding, you might have noticed the technique_id was provided, but you did not get the names of the techniques. So it would be annoying for you to do that analysis manually.

    To avoid this frustration the Mitre Assistant cli allows you to get these details by using the -c or --correlate switch parameter.

    The correlated details provide you a compact understanding of all of the previous items, plus the following:

    - The Tactic Name mapped to the Technique ID, and the Technique Name
    - The Technique Name mapped to its Data Sources
    - The Malware Name, and Tool Name mapped to the adversary and technique
    
    `mitre-assistant search -m enterprise -t "fin7" -c`   

    In cases where you want more fine-grained control over the adversary details, you can express semantics with an AND operator by using the colon : delimiter. This query answers the question: What are the techniques for FIN7 in the persistence Tactic?

    `mitre-assistant search -m enterprise -t "fin7:persistence" -c`

    You are not limited to one tactic with queries of adversaries focused on filters by tactics. You just need to use a : delimiter for each tactic.

    `mitre-assistant search -m enterprise -t "fin7:persistence:defense-evasion:exfiltration" -c`

    We can also leverage Mitre Assistant to give us the results for malware or tool entities by simply querying them by their name.

    `mitre-assistant search -m enterprise -t "griffon"`

    We can again use the `-c` or `--correlate` parameter like this:

    `mitre-assistant search -m enterprise -t "griffon" -c`

    Similar to filtering adversary objects by tactics, you can also filter techniques of malware and tool objects by tactic, like this:

    `mitre-assistant search -m enterprise -t "griffon:lateral-movement" -c`

    `mitre-assistant search -m enterprise -t "mimikatz:credential-dumping" -c`

## Exporting queries

Mitre Assistant can export both to CSV and JSON. The export functionality is provided by the -e or --export-to parameter switch.
    
Exporting to csv is done by -e csv, like this:

`mitre-assistant search -m enterprise -t "windows" -e csv` 

Exporting to CSV to a file of your choosing is done by adding the -f or --file switch, like this:

`mitre-assistant search -m enterprise -t "windows" -e csv -f the_windows_techniques.csv`

18. Export the statistics we produced earlier to CSV, open it on your spreadsheet application, and sort by fields like Platforms, Tactics, Techniques, Adversaries, Sub-techniques, Malware or Tools. For example:

    `mitre-assistant search -m enterprise -t "stats:adversaries" -e csv -f adversaries.csv`

    `mitre-assistant search -m enterprise -t "stats:malware" -e csv -f malware.csv`

    `mitre-assistant search -m enterprise -t "stats:tools" -e csv -f tools.csv`

    `mitre-assistant search -m enterprise -t "stats:platforms" -e csv -f platforms.csv`

    `mitre-assistant search -m enterprise -t "stats:tactics" -e csv -f tactics.csv`

    `mitre-assistant search -m enterprise -t "stats:datasources" -e csv -f datasources.csv`

    `mitre-assistant search -m enterprise -t "stats:techniques" -e csv -f techniques.csv`

    `mitre-assistant search -m enterprise -t "stats:subtechniques" -e csv -f subtechniques.csv`

19. Finally, aggregate the data for FIN6, FIN7, FIN8 and correlate their tactics and techniques with their malware and tools used (follow the weapons methodology). 

    `mitre-assistant search -m enterprise -t “fin6,fin7,fin8” -c -f fin6fin7fin8.csv`

At this time Mitre Assistant does not export in MITRE Navigator format. 

As you can see, Mitre Assistant helps you to slice and dice the ATT&CK matrix quickly to get meaningful insights of what that matrix has to offer.

# Day 2 / Lab 1 - Threat Modeling – Visibility & Coverage

## Setup

1. Install docker for your platform
    - https://docs.docker.com/get-docker/

2. Get the latest image for DeTT&CT from Docker Hub
    `docker pull rabobankcdc/dettect:latest`

3. Execute the appropriate command to create the container and mount the input and output directories

- Linux and MacOS: 
    `docker run -p 8080:8080 -v $(pwd)/output:/opt/DeTTECT/output -v $(pwd)/input:/opt/DeTTECT/input --name dettect -it rabobankcdc/dettect:latest /bin/bash`

- Windows (cmd.exe): 
    `docker run -p 8080:8080 -v %cd%/output:/opt/DeTTECT/output -v %cd%/input:/opt/DeTTECT/input --name dettect -it rabobankcdc/dettect:latest /bin/bash`
    
- PowerShell: 
    `docker run -p 8080:8080 -v ${PWD}/output:/opt/DeTTECT/output -v ${PWD}/input:/opt/DeTTECT/input --name dettect -it rabobankcdc/dettect:latest /bin/bash`

# Day 2 / Lab 2 - Cyballistics – Follow the Weapons with VirusTotal 

Follow the instructions given in class to obtain your temporary Virustotal Intelligence license. 



